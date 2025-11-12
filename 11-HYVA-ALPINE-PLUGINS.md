# Hyvä Alpine.js Plugins - Complete Guide

**Complete Reference for Alpine.js Integration and 5 Built-in Plugins**

---

## Table of Contents

1. [Overview](#overview)
2. [Alpine.js in Hyvä](#alpinejs-in-hyv)
3. [Built-in Plugins (5 Total)](#built-in-plugins-5-total)
4. [Plugin 1: Defer](#plugin-1-defer)
5. [Plugin 2: HTMLDialog](#plugin-2-htmldialog)
6. [Plugin 3: Intersect](#plugin-3-intersect)
7. [Plugin 4: Ignore](#plugin-4-ignore)
8. [Plugin 5: Snap Slider](#plugin-5-snap-slider)
9. [Creating Custom Plugins](#creating-custom-plugins)
10. [Alpine.js Best Practices](#alpinejs-best-practices)
11. [Common Patterns](#common-patterns)

---

## Overview

Hyvä Themes uses **Alpine.js v3** as its core JavaScript framework, providing a lightweight alternative to heavy frameworks like jQuery, React, or Vue.

### Key Features

- ✅ **Alpine.js v3.14.3** included (3.7KB minified + gzipped)
- ✅ **5 built-in plugins** for enhanced functionality
- ✅ **Reactive data binding** without virtual DOM
- ✅ **Declarative syntax** directly in HTML
- ✅ **No build step required** for basic usage
- ✅ **Legacy support** with Alpine.js v2.8.2

### File Locations

```
vendor/hyva-themes/magento2-theme-module/
├── src/view/base/web/js/
│   ├── alpine-v3.14.3.js            # Alpine.js v3 (current)
│   ├── alpine-v2.8.2.js             # Alpine.js v2 (legacy)
│   └── ALPINE_LICENSE_MIT.txt       # MIT License
│
└── src/view/base/templates/page/js/
    ├── alpinejs.phtml               # Alpine.js loader
    └── plugins/                     # Plugin templates
        ├── defer.phtml              # Defer plugin
        ├── htmldialog.phtml         # HTMLDialog plugin
        ├── intersect.phtml          # Intersect plugin
        ├── ignore.phtml             # Ignore plugin
        ├── snap-slider.phtml        # Snap Slider plugin
        ├── v2/                      # Alpine v2 versions
        └── v3/                      # Alpine v3 versions
```

---

## Alpine.js in Hyvä

### How Alpine.js is Loaded

Alpine.js is loaded via the `default_hyva.xml` layout handle:

```xml
<!-- src/view/frontend/layout/default_hyva.xml -->
<page>
    <body>
        <referenceContainer name="before.body.end">
            <block name="script-alpine-js" template="Hyva_Theme::page/js/alpinejs.phtml">
                <block name="alpine-plugin-snap-slider" template="...snap-slider.phtml"/>
                <block name="alpine-plugin-htmldialog" template="...htmldialog.phtml"/>
                <block name="alpine-plugin-intersect" template="...intersect.phtml"/>
                <block name="alpine-plugin-ignore" template="...ignore.phtml"/>
                <block name="alpine-plugin-defer" template="...defer.phtml"/>
            </block>
        </referenceContainer>
    </body>
</page>
```

### Alpine.js Loader Template

**File:** `src/view/base/templates/page/js/alpinejs.phtml`

```phtml
<?php
/**
 * Loads Alpine.js core and all plugins
 */
?>
<script>
    window.Alpine = window.Alpine || {};
</script>

<!-- Load Alpine.js v3 -->
<script src="<?= $escaper->escapeUrl($block->getViewFileUrl('js/alpine-v3.14.3.js')) ?>" defer></script>

<!-- Load all plugins -->
<?= $block->getChildHtml() ?>
```

### Basic Alpine.js Usage

```html
<!-- Counter example -->
<div x-data="{ count: 0 }">
    <button @click="count++">Increment</button>
    <span x-text="count"></span>
</div>

<!-- Toggle example -->
<div x-data="{ open: false }">
    <button @click="open = !open">Toggle</button>
    <div x-show="open">
        This content can be toggled
    </div>
</div>

<!-- Form example -->
<div x-data="{ email: '' }">
    <input type="email" x-model="email" placeholder="Enter email">
    <p>You entered: <span x-text="email"></span></p>
</div>
```

---

## Built-in Plugins (5 Total)

### Plugin Overview

| Plugin | Purpose | File |
|--------|---------|------|
| **defer** | Delay component initialization | defer.phtml |
| **htmldialog** | Native `<dialog>` element support | htmldialog.phtml |
| **intersect** | Intersection Observer integration | intersect.phtml |
| **ignore** | Prevent Alpine processing | ignore.phtml |
| **snap-slider** | Snap-scrolling carousels | snap-slider.phtml |

---

## Plugin 1: Defer

**Purpose:** Delays Alpine component initialization until interaction or visibility

**File:** `src/view/base/templates/page/js/plugins/defer.phtml`

### Use Cases

- Defer heavy components until needed
- Lazy-load interactive features
- Improve initial page load performance
- Load components when scrolled into view

### Syntax

```html
<!-- Defer until intersect (scroll into view) -->
<div x-data="heavyComponent()" x-defer="intersect">
    <!-- Component loads when visible -->
</div>

<!-- Defer until interaction -->
<div x-data="expensiveLogic()" x-defer>
    <!-- Component loads on first interaction -->
</div>
```

### Configuration in default_hyva.xml

```xml
<block name="alpine-plugin-defer" template="Hyva_Theme::page/js/plugins/defer.phtml">
    <block name="alpine-defer-rules" template="Hyva_Theme::page/js/alpine-defer-rules.phtml">
        <arguments>
            <argument name="deferred_components" xsi:type="array">
                <item name=".product-slider > div > section[x-data]" xsi:type="string">intersect</item>
                <item name=".heavy-component[x-data]" xsi:type="string">idle</item>
            </argument>
        </arguments>
    </block>
</block>
```

### Examples

#### Example 1: Defer Product Slider

```html
<div class="product-slider">
    <div x-data="productSlider()" x-defer="intersect">
        <!-- Slider loads when scrolled into viewport -->
        <div class="slider-track">
            <!-- Products -->
        </div>
    </div>
</div>

<script>
function productSlider() {
    return {
        currentSlide: 0,
        init() {
            console.log('Product slider initialized!');
        },
        next() {
            this.currentSlide++;
        },
        previous() {
            this.currentSlide--;
        }
    }
}
</script>
```

#### Example 2: Defer Until User Interaction

```html
<div x-data="reviewForm()" x-defer class="review-form">
    <button type="button">Write a Review</button>
    <!-- Form only initializes when user clicks -->
</div>

<script>
function reviewForm() {
    return {
        rating: 5,
        comment: '',
        init() {
            console.log('Review form initialized');
        },
        submitReview() {
            // Submit logic
        }
    }
}
</script>
```

#### Example 3: Defer Options

```html
<!-- Defer strategies -->

<!-- Load when visible (default) -->
<div x-defer="intersect">...</div>

<!-- Load when browser is idle -->
<div x-defer="idle">...</div>

<!-- Load on media query match -->
<div x-defer="media (min-width: 1024px)">...</div>

<!-- Load after delay (ms) -->
<div x-defer="100">...</div>
```

---

## Plugin 2: HTMLDialog

**Purpose:** Adds support for native HTML `<dialog>` element with Alpine.js

**File:** `src/view/base/templates/page/js/plugins/htmldialog.phtml`

### Benefits

- ✅ Native browser dialog (no custom CSS needed)
- ✅ Built-in backdrop
- ✅ Keyboard support (Escape to close)
- ✅ Focus management
- ✅ Accessibility features

### Basic Usage

```html
<div x-data="{}">
    <!-- Trigger button -->
    <button @click="$refs.myDialog.showModal()">
        Open Dialog
    </button>

    <!-- Dialog element -->
    <dialog x-ref="myDialog" @click.outside="$el.close()">
        <h2>Dialog Title</h2>
        <p>Dialog content goes here</p>
        <button @click="$refs.myDialog.close()">Close</button>
    </dialog>
</div>
```

### Advanced Examples

#### Example 1: Product Quick View

```html
<div x-data="productQuickView()">
    <button @click="open(<?= (int)$product->getId() ?>)" class="quick-view-btn">
        Quick View
    </button>

    <dialog x-ref="quickView" @click.outside="close()" class="product-dialog">
        <div class="dialog-content">
            <button @click="close()" class="close-btn" aria-label="Close">
                <?= $heroicons->renderHtml('x-mark', 'w-6 h-6') ?>
            </button>

            <div x-show="loading" class="loading">
                Loading product...
            </div>

            <div x-show="!loading && product" x-cloak>
                <h2 x-text="product?.name"></h2>
                <img :src="product?.image" :alt="product?.name">
                <p x-text="product?.price"></p>
                <button @click="addToCart()">Add to Cart</button>
            </div>
        </div>
    </dialog>
</div>

<script>
function productQuickView() {
    return {
        loading: false,
        product: null,

        async open(productId) {
            this.$refs.quickView.showModal();
            this.loading = true;

            try {
                const response = await fetch(`/catalog/product/view/id/${productId}`);
                this.product = await response.json();
            } finally {
                this.loading = false;
            }
        },

        close() {
            this.$refs.quickView.close();
            this.product = null;
        },

        addToCart() {
            // Add to cart logic
            this.close();
        }
    }
}
</script>

<style>
dialog {
    max-width: 600px;
    width: 90%;
    padding: 0;
    border: none;
    border-radius: 0.5rem;
    box-shadow: 0 10px 25px rgba(0,0,0,0.3);
}

dialog::backdrop {
    background: rgba(0, 0, 0, 0.5);
}

dialog[open] {
    animation: fadeIn 0.2s ease-out;
}

@keyframes fadeIn {
    from { opacity: 0; transform: scale(0.95); }
    to { opacity: 1; transform: scale(1); }
}
</style>
```

#### Example 2: Confirmation Dialog

```html
<div x-data="confirmDialog()">
    <button @click="openConfirm('delete')" class="btn-danger">
        Delete Item
    </button>

    <dialog x-ref="confirmDialog" class="confirm-dialog">
        <h3>Confirm Action</h3>
        <p x-text="message"></p>
        <div class="actions">
            <button @click="$refs.confirmDialog.close()" class="btn-secondary">
                Cancel
            </button>
            <button @click="confirm()" class="btn-primary">
                Confirm
            </button>
        </div>
    </dialog>
</div>

<script>
function confirmDialog() {
    return {
        message: '',
        action: null,

        openConfirm(actionType) {
            this.action = actionType;
            this.message = `Are you sure you want to ${actionType} this item?`;
            this.$refs.confirmDialog.showModal();
        },

        confirm() {
            if (this.action === 'delete') {
                this.deleteItem();
            }
            this.$refs.confirmDialog.close();
        },

        deleteItem() {
            console.log('Item deleted');
        }
    }
}
</script>
```

#### Example 3: Form Dialog

```html
<div x-data="newsletterDialog()">
    <button @click="$refs.newsletter.showModal()">
        Subscribe to Newsletter
    </button>

    <dialog x-ref="newsletter" @click.outside="$el.close()">
        <form @submit.prevent="subscribe()">
            <h2>Subscribe to our Newsletter</h2>

            <input type="email"
                   x-model="email"
                   placeholder="Enter your email"
                   required>

            <div class="actions">
                <button type="button" @click="$refs.newsletter.close()">
                    Cancel
                </button>
                <button type="submit" :disabled="!email">
                    Subscribe
                </button>
            </div>

            <p x-show="success" class="text-green-600">
                Successfully subscribed!
            </p>
        </form>
    </dialog>
</div>

<script>
function newsletterDialog() {
    return {
        email: '',
        success: false,

        async subscribe() {
            try {
                await fetch('/newsletter/subscriber/new/', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ email: this.email })
                });

                this.success = true;
                setTimeout(() => {
                    this.$refs.newsletter.close();
                    this.email = '';
                    this.success = false;
                }, 2000);
            } catch (error) {
                console.error('Subscription failed', error);
            }
        }
    }
}
</script>
```

---

## Plugin 3: Intersect

**Purpose:** Triggers actions when elements enter the viewport using Intersection Observer

**File:** `src/view/base/templates/page/js/plugins/intersect.phtml`

### Use Cases

- Lazy load images
- Animate elements on scroll
- Load content when visible
- Track element visibility
- Infinite scroll

### Basic Syntax

```html
<!-- Basic intersection -->
<div x-data="{ shown: false }"
     x-intersect="shown = true"
     x-show="shown"
     x-transition>
    Appears when scrolled into view
</div>

<!-- Call function on intersect -->
<div x-intersect="loadContent()">
    Content loads when visible
</div>
```

### Advanced Options

```html
<!-- Intersect once (doesn't re-trigger) -->
<div x-intersect.once="loadData()">
    Loads only first time
</div>

<!-- Intersect with margin -->
<div x-intersect.margin.200px="loadContent()">
    Triggers 200px before entering viewport
</div>

<!-- Intersect with threshold -->
<div x-intersect.threshold.50="handleIntersect()">
    Triggers when 50% visible
</div>

<!-- Combined options -->
<div x-intersect.once.margin.100px.threshold.75="init()">
    Advanced intersection
</div>
```

### Examples

#### Example 1: Lazy Load Images

```html
<div x-data="{ loaded: false }"
     x-intersect.once="loaded = true"
     class="image-container">

    <img x-show="!loaded" src="/placeholder.jpg" alt="Loading...">

    <img x-show="loaded"
         x-transition
         :src="'<?= $escaper->escapeUrl($product->getImage()) ?>'"
         alt="<?= $escaper->escapeHtmlAttr($product->getName()) ?>">
</div>
```

#### Example 2: Animate on Scroll

```html
<div x-data="{ visible: false }"
     x-intersect.once="visible = true"
     :class="{ 'opacity-0 translate-y-10': !visible, 'opacity-100 translate-y-0': visible }"
     class="transition-all duration-700">

    <h2>Animated Heading</h2>
    <p>This content animates when scrolled into view</p>
</div>
```

#### Example 3: Infinite Scroll

```html
<div x-data="infiniteScroll()" x-init="loadMore()">
    <!-- Product list -->
    <div class="product-grid">
        <template x-for="product in products" :key="product.id">
            <div class="product-item">
                <h3 x-text="product.name"></h3>
            </div>
        </template>
    </div>

    <!-- Load more trigger -->
    <div x-intersect="loadMore()" class="load-more-trigger">
        <span x-show="loading">Loading more products...</span>
    </div>
</div>

<script>
function infiniteScroll() {
    return {
        products: [],
        loading: false,
        page: 1,
        hasMore: true,

        async loadMore() {
            if (this.loading || !this.hasMore) return;

            this.loading = true;

            try {
                const response = await fetch(`/products?page=${this.page}`);
                const data = await response.json();

                this.products = [...this.products, ...data.products];
                this.page++;
                this.hasMore = data.hasMore;
            } finally {
                this.loading = false;
            }
        }
    }
}
</script>
```

#### Example 4: Visibility Tracking

```html
<div x-data="visibilityTracker()"
     x-intersect:enter="onEnter()"
     x-intersect:leave="onLeave()">

    <p>This element tracks when it's visible</p>
    <p x-text="`Visible: ${isVisible}`"></p>
    <p x-text="`View count: ${viewCount}`"></p>
</div>

<script>
function visibilityTracker() {
    return {
        isVisible: false,
        viewCount: 0,

        onEnter() {
            this.isVisible = true;
            this.viewCount++;
            console.log('Element entered viewport');
        },

        onLeave() {
            this.isVisible = false;
            console.log('Element left viewport');
        }
    }
}
</script>
```

---

## Plugin 4: Ignore

**Purpose:** Prevents Alpine.js from processing specific elements and their children

**File:** `src/view/base/templates/page/js/plugins/ignore.phtml`

### Use Cases

- Exclude third-party widgets
- Prevent Alpine processing in server-rendered content
- Preserve content with `x-` attributes that shouldn't be processed
- Work with other JavaScript frameworks

### Basic Usage

```html
<!-- Alpine ignores this entire section -->
<div x-ignore>
    <div x-data="{ count: 0 }">
        <!-- This won't be initialized by Alpine -->
        <button @click="count++">This won't work</button>
    </div>
</div>
```

### Examples

#### Example 1: Third-Party Widget

```html
<div x-ignore>
    <!-- Google Maps widget -->
    <div id="google-map" data-lat="40.7128" data-lng="-74.0060">
        <!-- Alpine won't process this -->
    </div>
</div>

<script>
// Non-Alpine JavaScript
function initGoogleMap() {
    const mapEl = document.getElementById('google-map');
    // Initialize Google Maps
}
</script>
```

#### Example 2: Server-Rendered Content

```html
<div x-data="app()">
    <h1>Alpine App</h1>

    <div x-ignore>
        <!-- Server-rendered content that shouldn't be touched -->
        <?= $block->getChildHtml('server.rendered.content') ?>
    </div>

    <button @click="doSomething()">Alpine works here</button>
</div>
```

#### Example 3: Mixed Framework Usage

```html
<div x-data="{ tab: 'alpine' }">
    <!-- Alpine-controlled tabs -->
    <button @click="tab = 'alpine'">Alpine Tab</button>
    <button @click="tab = 'other'">Other Framework</button>

    <div x-show="tab === 'alpine'">
        <p>This uses Alpine.js</p>
    </div>

    <div x-show="tab === 'other'" x-ignore>
        <!-- React/Vue component here -->
        <div id="react-root"></div>
    </div>
</div>
```

---

## Plugin 5: Snap Slider

**Purpose:** Creates snap-scrolling carousels with native scroll-snap CSS

**File:** `src/view/base/templates/page/js/plugins/snap-slider.phtml`

### Features

- ✅ Smooth snap scrolling
- ✅ Touch/swipe support
- ✅ Keyboard navigation
- ✅ Auto-scroll option
- ✅ Pagination dots
- ✅ Previous/Next buttons

### Basic Usage

```html
<div x-data="initSnapSlider()" class="snap-slider">
    <button @click="previous()" class="slider-btn">←</button>

    <div class="slider-track">
        <div class="slide">Slide 1</div>
        <div class="slide">Slide 2</div>
        <div class="slide">Slide 3</div>
    </div>

    <button @click="next()" class="slider-btn">→</button>
</div>

<style>
.slider-track {
    display: flex;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    scroll-behavior: smooth;
    -webkit-overflow-scrolling: touch;
}

.slide {
    flex: 0 0 100%;
    scroll-snap-align: start;
}
</style>
```

### Examples

#### Example 1: Product Slider

```html
<div x-data="productSlider()" class="product-slider">
    <div class="slider-header">
        <h2>Featured Products</h2>
        <div class="slider-controls">
            <button @click="previous()" :disabled="!canGoPrevious">
                <?= $heroicons->renderHtml('chevron-left', 'w-6 h-6') ?>
            </button>
            <button @click="next()" :disabled="!canGoNext">
                <?= $heroicons->renderHtml('chevron-right', 'w-6 h-6') ?>
            </button>
        </div>
    </div>

    <div class="slider-track" x-ref="track" @scroll.passive="updatePosition()">
        <?php foreach ($products as $product): ?>
            <div class="product-slide">
                <img src="<?= $product->getImage() ?>" alt="<?= $product->getName() ?>">
                <h3><?= $product->getName() ?></h3>
                <p class="price"><?= $product->getPrice() ?></p>
            </div>
        <?php endforeach; ?>
    </div>

    <div class="slider-dots">
        <template x-for="(slide, index) in totalSlides" :key="index">
            <button @click="goToSlide(index)"
                    :class="{ 'active': currentSlide === index }"
                    class="dot">
            </button>
        </template>
    </div>
</div>

<script>
function productSlider() {
    return {
        currentSlide: 0,
        totalSlides: <?= count($products) ?>,

        get canGoPrevious() {
            return this.currentSlide > 0;
        },

        get canGoNext() {
            return this.currentSlide < this.totalSlides - 1;
        },

        previous() {
            if (this.canGoPrevious) {
                this.currentSlide--;
                this.scrollToSlide(this.currentSlide);
            }
        },

        next() {
            if (this.canGoNext) {
                this.currentSlide++;
                this.scrollToSlide(this.currentSlide);
            }
        },

        goToSlide(index) {
            this.currentSlide = index;
            this.scrollToSlide(index);
        },

        scrollToSlide(index) {
            const track = this.$refs.track;
            const slideWidth = track.querySelector('.product-slide').offsetWidth;
            track.scrollLeft = slideWidth * index;
        },

        updatePosition() {
            const track = this.$refs.track;
            const slideWidth = track.querySelector('.product-slide').offsetWidth;
            this.currentSlide = Math.round(track.scrollLeft / slideWidth);
        }
    }
}
</script>

<style>
.product-slider {
    position: relative;
}

.slider-track {
    display: flex;
    gap: 1rem;
    overflow-x: auto;
    scroll-snap-type: x mandatory;
    scroll-behavior: smooth;
    scrollbar-width: none;
}

.slider-track::-webkit-scrollbar {
    display: none;
}

.product-slide {
    flex: 0 0 calc(25% - 0.75rem);
    scroll-snap-align: start;
}

@media (max-width: 768px) {
    .product-slide {
        flex: 0 0 calc(50% - 0.5rem);
    }
}

.slider-dots {
    display: flex;
    gap: 0.5rem;
    justify-content: center;
    margin-top: 1rem;
}

.dot {
    width: 0.5rem;
    height: 0.5rem;
    border-radius: 50%;
    background: #ccc;
}

.dot.active {
    background: #333;
}
</style>
```

#### Example 2: Auto-Play Slider

```html
<div x-data="autoPlaySlider()" x-init="startAutoPlay()">
    <div class="slider-track" x-ref="track">
        <div class="slide" v-for="slide in slides">
            Slide content
        </div>
    </div>
</div>

<script>
function autoPlaySlider() {
    return {
        currentSlide: 0,
        totalSlides: 5,
        autoPlayInterval: null,
        isPaused: false,

        startAutoPlay() {
            this.autoPlayInterval = setInterval(() => {
                if (!this.isPaused) {
                    this.next();
                }
            }, 3000);
        },

        next() {
            this.currentSlide = (this.currentSlide + 1) % this.totalSlides;
            this.scrollToSlide(this.currentSlide);
        },

        pause() {
            this.isPaused = true;
        },

        resume() {
            this.isPaused = false;
        },

        scrollToSlide(index) {
            // Scroll logic
        }
    }
}
</script>
```

---

## Creating Custom Plugins

### Plugin Structure

```phtml
<!-- app/design/frontend/Vendor/Theme/templates/page/js/plugins/my-plugin.phtml -->
<script>
document.addEventListener('alpine:init', () => {
    Alpine.plugin((Alpine) => {
        // Register custom directive
        Alpine.directive('my-directive', (el, { expression }, { evaluate }) => {
            // Plugin logic
            console.log('Custom directive initialized');
        });

        // Register custom magic
        Alpine.magic('myMagic', () => {
            return (value) => {
                return `Processed: ${value}`;
            };
        });
    });
});
</script>
```

### Load Custom Plugin in Layout

```xml
<referenceBlock name="script-alpine-js">
    <block name="alpine-plugin-custom" template="Vendor_Theme::page/js/plugins/my-plugin.phtml"/>
</referenceBlock>
```

### Example: Custom Tooltip Plugin

```phtml
<script>
document.addEventListener('alpine:init', () => {
    Alpine.directive('tooltip', (el, { expression }, { effect, evaluateLater }) => {
        let getContent = evaluateLater(expression);

        effect(() => {
            getContent(content => {
                el.setAttribute('title', content);

                // Enhanced tooltip
                el.addEventListener('mouseenter', () => {
                    const tooltip = document.createElement('div');
                    tooltip.textContent = content;
                    tooltip.className = 'custom-tooltip';
                    document.body.appendChild(tooltip);

                    const rect = el.getBoundingClientRect();
                    tooltip.style.left = rect.left + 'px';
                    tooltip.style.top = (rect.top - tooltip.offsetHeight - 5) + 'px';

                    el._tooltip = tooltip;
                });

                el.addEventListener('mouseleave', () => {
                    if (el._tooltip) {
                        el._tooltip.remove();
                    }
                });
            });
        });
    });
});
</script>

<style>
.custom-tooltip {
    position: absolute;
    background: #333;
    color: white;
    padding: 0.5rem;
    border-radius: 0.25rem;
    font-size: 0.875rem;
    z-index: 9999;
    pointer-events: none;
}
</style>

<!-- Usage -->
<button x-tooltip="'This is a custom tooltip'">
    Hover me
</button>
```

---

## Alpine.js Best Practices

### 1. Keep Data Reactive

```html
<!-- ✅ Good -->
<div x-data="{ count: 0 }">
    <button @click="count++">Count: <span x-text="count"></span></button>
</div>

<!-- ❌ Bad -->
<div x-data="{ count: 0 }">
    <button @click="document.querySelector('span').textContent = ++count">
        Count: <span>0</span>
    </button>
</div>
```

### 2. Use x-cloak for Flash Prevention

```html
<style>
[x-cloak] { display: none !important; }
</style>

<div x-data="{ loaded: false }" x-init="loaded = true" x-cloak>
    <div x-show="loaded">Content</div>
</div>
```

### 3. Extract Complex Logic

```html
<!-- ❌ Bad -->
<div x-data="{ items: [], async load() { const r = await fetch('/api'); this.items = await r.json(); } }">
</div>

<!-- ✅ Good -->
<div x-data="itemLoader()">
</div>

<script>
function itemLoader() {
    return {
        items: [],
        loading: false,

        async load() {
            this.loading = true;
            try {
                const response = await fetch('/api');
                this.items = await response.json();
            } finally {
                this.loading = false;
            }
        }
    }
}
</script>
```

### 4. Use Modifiers

```html
<!-- Prevent default -->
<form @submit.prevent="handleSubmit()">

<!-- Stop propagation -->
<button @click.stop="handleClick()">

<!-- Only once -->
<button @click.once="initialize()">

<!-- Debounce -->
<input @input.debounce.500ms="search()">

<!-- Throttle -->
<div @scroll.throttle.100ms="handleScroll()">
```

### 5. Leverage Alpine Stores

```html
<script>
document.addEventListener('alpine:init', () => {
    Alpine.store('cart', {
        items: [],

        add(item) {
            this.items.push(item);
        },

        get count() {
            return this.items.length;
        }
    });
});
</script>

<!-- Access from anywhere -->
<div x-data>
    <span x-text="$store.cart.count"></span>
</div>
```

---

## Common Patterns

### Pattern 1: Tabs

```html
<div x-data="{ tab: 'description' }">
    <div class="tab-buttons">
        <button @click="tab = 'description'" :class="{ 'active': tab === 'description' }">
            Description
        </button>
        <button @click="tab = 'reviews'" :class="{ 'active': tab === 'reviews' }">
            Reviews
        </button>
    </div>

    <div x-show="tab === 'description'" x-transition>
        Description content
    </div>

    <div x-show="tab === 'reviews'" x-transition>
        Reviews content
    </div>
</div>
```

### Pattern 2: Dropdown

```html
<div x-data="{ open: false }" @click.away="open = false">
    <button @click="open = !open">Menu</button>

    <div x-show="open"
         x-transition:enter="transition ease-out duration-200"
         x-transition:enter-start="opacity-0 transform scale-95"
         x-transition:enter-end="opacity-100 transform scale-100"
         class="dropdown">
        <a href="#">Link 1</a>
        <a href="#">Link 2</a>
    </div>
</div>
```

### Pattern 3: Accordion

```html
<div x-data="{ active: null }">
    <template x-for="(item, index) in items" :key="index">
        <div class="accordion-item">
            <button @click="active = active === index ? null : index">
                <span x-text="item.title"></span>
            </button>

            <div x-show="active === index"
                 x-collapse>
                <div x-html="item.content"></div>
            </div>
        </div>
    </template>
</div>
```

---

**Last Updated:** November 2025
**Alpine.js Version:** v3.14.3
**Document Version:** 1.0.0
