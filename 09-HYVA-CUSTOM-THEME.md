# Building a Custom Hyvä Theme - Complete Guide

**Step-by-Step Guide to Creating Your Own Hyvä Theme**

---

## Table of Contents

1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Theme Setup](#theme-setup)
4. [Directory Structure](#directory-structure)
5. [Tailwind Configuration](#tailwind-configuration)
6. [Template Customization](#template-customization)
7. [Layout XML Customization](#layout-xml-customization)
8. [Adding Custom Components](#adding-custom-components)
9. [Styling with Tailwind](#styling-with-tailwind)
10. [Development Workflow](#development-workflow)
11. [Best Practices](#best-practices)
12. [Troubleshooting](#troubleshooting)

---

## Overview

### What is a Custom Hyvä Theme?

A custom Hyvä theme is a **child theme** that inherits from `Hyva/default` and allows you to:

- ✅ Customize branding (colors, fonts, logo)
- ✅ Override specific templates
- ✅ Extend layouts without modifying core
- ✅ Add custom components
- ✅ Maintain upgrade compatibility

### Why Create a Child Theme?

**Never modify vendor files directly.** Always create a child theme to:

- Preserve customizations during upgrades
- Maintain clean separation of concerns
- Enable easy version control
- Share themes across projects

---

## Prerequisites

### Required

- ✅ Magento 2.4.6+ installed
- ✅ Hyvä Themes installed (`Hyva/default` or `Hyva/default-csp`)
- ✅ Node.js 18+ (for Tailwind compilation)
- ✅ Composer 2.x
- ✅ Basic knowledge of Magento theme structure

### Verify Hyvä Installation

```bash
# Check Hyvä theme is installed
php bin/magento theme:list | grep Hyva

# Expected output:
# frontend/Hyva/default | Hyvä Default Theme

# Check Hyvä module
php bin/magento module:status Hyva_Theme

# Expected: Module is enabled
```

---

## Theme Setup

### Step 1: Create Theme Directory

```bash
# Create theme directory
mkdir -p app/design/frontend/MyCompany/hyva-custom

# Navigate to theme
cd app/design/frontend/MyCompany/hyva-custom
```

**Directory naming convention:**
- `MyCompany` - Your company/vendor name (PascalCase)
- `hyva-custom` - Theme name (kebab-case)

### Step 2: Create registration.php

**File:** `app/design/frontend/MyCompany/hyva-custom/registration.php`

```php
<?php
/**
 * Theme Registration
 *
 * @category  Design
 * @package   MyCompany_HyvaCustom
 * @author    Your Name <your.email@company.com>
 * @copyright Copyright (c) 2025 MyCompany
 */

use Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(
    ComponentRegistrar::THEME,
    'frontend/MyCompany/hyva-custom',
    __DIR__
);
```

### Step 3: Create theme.xml

**File:** `app/design/frontend/MyCompany/hyva-custom/theme.xml`

```xml
<?xml version="1.0"?>
<!--
/**
 * Theme Configuration
 *
 * @category  Design
 * @package   MyCompany_HyvaCustom
 * @author    Your Name <your.email@company.com>
 * @copyright Copyright (c) 2025 MyCompany
 */
-->
<theme xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/theme.xsd">
    <title>MyCompany Hyvä Custom Theme</title>
    <parent>Hyva/default</parent>
    <media>
        <preview_image>media/preview.png</preview_image>
    </media>
</theme>
```

**Key points:**
- `<parent>Hyva/default</parent>` - Inherit from Hyvä default
- Use `Hyva/default-csp` if you installed CSP version
- Preview image is PNG format (not JPG) in Hyvä

### Step 4: Create composer.json

**File:** `app/design/frontend/MyCompany/hyva-custom/composer.json`

```json
{
    "name": "mycompany/hyva-custom-theme",
    "description": "Custom Hyvä Theme for MyCompany",
    "type": "magento2-theme",
    "version": "1.0.0",
    "license": "proprietary",
    "authors": [
        {
            "name": "Your Name",
            "email": "your.email@company.com",
            "role": "Developer"
        }
    ],
    "require": {
        "php": "^8.1|^8.2",
        "magento/framework": "^103.0",
        "hyva-themes/magento2-default-theme": "^1.3",
        "hyva-themes/magento2-theme-module": "^1.3"
    },
    "autoload": {
        "files": [
            "registration.php"
        ]
    }
}
```

### Step 5: Create etc/view.xml

**File:** `app/design/frontend/MyCompany/hyva-custom/etc/view.xml`

```xml
<?xml version="1.0"?>
<view xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/view.xsd">
    <media>
        <images module="Magento_Catalog">
            <!-- Product Listing Images -->
            <image id="category_page_grid" type="small_image">
                <width>240</width>
                <height>300</height>
            </image>

            <!-- Product Detail Images -->
            <image id="product_page_image_large" type="image">
                <width>700</width>
                <height>875</height>
            </image>

            <!-- Cart Images -->
            <image id="mini_cart_product_thumbnail" type="small_image">
                <width>78</width>
                <height>78</height>
            </image>
        </images>
    </media>
</view>
```

### Step 6: Create Preview Image

```bash
# Create media directory
mkdir -p app/design/frontend/MyCompany/hyva-custom/media

# Add preview image (200x200px recommended)
# Copy your preview.png to:
# app/design/frontend/MyCompany/hyva-custom/media/preview.png
```

### Step 7: Register Theme

```bash
# Clear cache
php bin/magento cache:flush

# Run setup upgrade (registers theme)
php bin/magento setup:upgrade

# Verify theme is registered
php bin/magento theme:list

# Expected output:
# frontend/MyCompany/hyva-custom | MyCompany Hyvä Custom Theme

# Deploy static content
php bin/magento setup:static-content:deploy -f en_US

# Reindex
php bin/magento indexer:reindex
```

### Step 8: Activate Theme

**Via Admin Panel:**

1. Navigate to **Content > Design > Configuration**
2. Click **Edit** for your store view
3. Under **Applied Theme**, select "MyCompany Hyvä Custom Theme"
4. Click **Save Configuration**
5. Flush cache: `php bin/magento cache:flush`

**Via CLI (alternative):**

```bash
# Set theme for store ID 1
php bin/magento config:set design/theme/theme_id 5

# Flush cache
php bin/magento cache:flush
```

---

## Directory Structure

### Complete Theme Structure

```
app/design/frontend/MyCompany/hyva-custom/
├── registration.php                     # Theme registration
├── theme.xml                            # Theme configuration
├── composer.json                        # Composer package definition
│
├── etc/
│   └── view.xml                         # Image sizes, gallery config
│
├── media/
│   └── preview.png                      # Theme preview (200x200px)
│
├── web/                                 # Static assets
│   ├── css/
│   │   └── styles.css                   # Compiled Tailwind CSS (generated)
│   ├── images/                          # Theme images
│   │   └── logo.svg                     # Logo
│   ├── js/                              # Custom JavaScript
│   │   └── theme.js                     # Theme-specific JS
│   └── tailwind/                        # Tailwind configuration
│       ├── package.json                 # NPM dependencies
│       ├── package-lock.json            # Locked dependencies
│       ├── hyva.config.json             # Hyvä Tailwind config
│       ├── tailwind-source.css          # Tailwind source file
│       ├── components/                  # Custom Tailwind components
│       │   ├── buttons.css
│       │   ├── cards.css
│       │   └── forms.css
│       └── utilities/                   # Custom utilities
│           └── custom.css
│
├── Magento_Theme/                       # Theme module overrides
│   ├── layout/
│   │   └── default.xml                  # Default layout
│   └── templates/
│       └── html/
│           ├── header.phtml             # Header template
│           └── footer.phtml             # Footer template
│
├── Magento_Catalog/                     # Catalog module overrides
│   ├── layout/
│   │   ├── catalog_product_view.xml     # Product page layout
│   │   └── catalog_category_view.xml    # Category page layout
│   └── templates/
│       └── product/
│           ├── list/item.phtml          # Product list item
│           └── view/gallery.phtml       # Product gallery
│
├── Magento_Checkout/                    # Checkout module overrides
│   └── layout/
│       └── checkout_cart_index.xml
│
└── Magento_Customer/                    # Customer module overrides
    └── templates/
        └── account/
            └── dashboard.phtml
```

---

## Tailwind Configuration

### Step 1: Copy Web Directory

```bash
# From theme root
cp -r vendor/hyva-themes/magento2-default-theme/web/ \
      app/design/frontend/MyCompany/hyva-custom/web/
```

This copies:
- `web/tailwind/` - Tailwind configuration
- `web/css/` - CSS directory
- `web/images/` - Images directory
- `web/js/` - JavaScript directory

### Step 2: Configure Parent Theme Path

**File:** `app/design/frontend/MyCompany/hyva-custom/web/tailwind/hyva.config.json`

```json
{
    "tailwind": {
        "include": [
            { "src": "vendor/hyva-themes/magento2-default-theme" }
        ]
    },
    "tokens": {
        "values": {
            "color": {
                "primary": {
                    "lighter": "oklch(52% 0.2 265)",
                    "DEFAULT": "oklch(46% 0.2 265)",
                    "darker": "oklch(28% 0.2 265)"
                },
                "secondary": {
                    "lighter": "oklch(72% 0.2 150)",
                    "DEFAULT": "oklch(53% 0.15 150)",
                    "darker": "oklch(39% 0.1 153)"
                }
            }
        }
    }
}
```

**For CSP theme:**

```json
{
    "tailwind": {
        "include": [
            { "src": "vendor/hyva-themes/magento2-default-theme-csp" }
        ]
    }
}
```

### Step 3: Customize Colors and Branding

**File:** `web/tailwind/hyva.config.json`

```json
{
    "tailwind": {
        "include": [
            { "src": "vendor/hyva-themes/magento2-default-theme" }
        ]
    },
    "tokens": {
        "values": {
            "color": {
                "primary": {
                    "lighter": "#60a5fa",
                    "DEFAULT": "#3b82f6",
                    "darker": "#1e40af"
                },
                "secondary": {
                    "lighter": "#f59e0b",
                    "DEFAULT": "#d97706",
                    "darker": "#92400e"
                },
                "accent": {
                    "DEFAULT": "#ec4899",
                    "hover": "#db2777"
                },
                "success": "#10b981",
                "warning": "#f59e0b",
                "error": "#ef4444",
                "info": "#3b82f6"
            },
            "font": {
                "family": {
                    "sans": "Inter, ui-sans-serif, system-ui, sans-serif",
                    "heading": "Montserrat, ui-sans-serif, system-ui, sans-serif"
                }
            },
            "spacing": {
                "container": "1280px"
            },
            "form": {
                "radius": "0.5rem",
                "stroke": "#d1d5db",
                "active-color": "var(--color-primary)"
            }
        }
    }
}
```

### Step 4: Install Node Dependencies

```bash
# Navigate to tailwind directory
cd app/design/frontend/MyCompany/hyva-custom/web/tailwind

# Install dependencies
npm install

# Verify installation
npm list
```

### Step 5: Compile Tailwind CSS

```bash
# Development (watch mode)
npm run watch

# Production build (minified)
npm run build

# Generate sources
npm run generate
```

**package.json scripts:**

```json
{
  "scripts": {
    "start": "npm run watch",
    "generate": "npx hyva-sources && npx hyva-tokens",
    "prewatch": "npm run generate",
    "watch": "npx tailwindcss -i tailwind-source.css -o ../css/styles.css --watch",
    "prebuild": "npm run generate",
    "build": "npx tailwindcss -i tailwind-source.css -o ../css/styles.css --minify"
  }
}
```

---

## Template Customization

### Override Templates

To override a template, **copy it from parent theme** to your theme with the same path:

#### Example 1: Customize Product List Item

**Source:** `vendor/hyva-themes/magento2-default-theme/Magento_Catalog/templates/product/list/item.phtml`

**Destination:** `app/design/frontend/MyCompany/hyva-custom/Magento_Catalog/templates/product/list/item.phtml`

```bash
# Create directory
mkdir -p app/design/frontend/MyCompany/hyva-custom/Magento_Catalog/templates/product/list

# Copy template
cp vendor/hyva-themes/magento2-default-theme/Magento_Catalog/templates/product/list/item.phtml \
   app/design/frontend/MyCompany/hyva-custom/Magento_Catalog/templates/product/list/item.phtml
```

**Customize the template:**

```php
<?php
/**
 * Custom Product List Item Template
 */
declare(strict_types=1);

use Magento\Framework\Escaper;
use Hyva\Theme\ViewModel\ProductListItem;
use Hyva\Theme\ViewModel\HeroiconsOutline;

/** @var Escaper $escaper */
/** @var ProductListItem $itemViewModel */
/** @var HeroiconsOutline $heroicons */

$product = $block->getData('product');
?>

<div class="product-item group bg-white rounded-lg shadow-md hover:shadow-xl transition-shadow duration-300">
    <!-- Product Image -->
    <div class="product-image-container relative overflow-hidden">
        <a href="<?= $escaper->escapeUrl($itemViewModel->getProductUrl($product)) ?>"
           class="block">
            <img src="<?= $escaper->escapeUrl($itemViewModel->getImageUrl($product, 'product_list_image')) ?>"
                 alt="<?= $escaper->escapeHtmlAttr($product->getName()) ?>"
                 class="w-full h-auto transform group-hover:scale-105 transition-transform duration-300">
        </a>

        <!-- Badges -->
        <?php if ($product->getSpecialPrice()): ?>
            <span class="absolute top-2 right-2 bg-red-600 text-white px-2 py-1 text-sm font-bold rounded">
                SALE
            </span>
        <?php endif; ?>

        <!-- Quick Actions -->
        <div class="absolute bottom-2 right-2 flex gap-2 opacity-0 group-hover:opacity-100 transition-opacity">
            <button class="bg-white p-2 rounded-full shadow-md hover:bg-gray-100">
                <?= $heroicons->renderHtml('heart', 'w-5 h-5') ?>
            </button>
            <button class="bg-white p-2 rounded-full shadow-md hover:bg-gray-100">
                <?= $heroicons->renderHtml('eye', 'w-5 h-5') ?>
            </button>
        </div>
    </div>

    <!-- Product Info -->
    <div class="p-4">
        <h3 class="text-lg font-semibold mb-2 line-clamp-2">
            <a href="<?= $escaper->escapeUrl($itemViewModel->getProductUrl($product)) ?>"
               class="hover:text-primary">
                <?= $escaper->escapeHtml($product->getName()) ?>
            </a>
        </h3>

        <!-- Price -->
        <div class="mb-3">
            <?php if ($product->getSpecialPrice()): ?>
                <span class="text-gray-400 line-through text-sm mr-2">
                    <?= $priceViewModel->format($product->getPrice()) ?>
                </span>
                <span class="text-primary text-xl font-bold">
                    <?= $priceViewModel->format($product->getSpecialPrice()) ?>
                </span>
            <?php else: ?>
                <span class="text-gray-900 text-xl font-bold">
                    <?= $priceViewModel->format($product->getPrice()) ?>
                </span>
            <?php endif; ?>
        </div>

        <!-- Add to Cart -->
        <button @click="addToCart(<?= (int)$product->getId() ?>)"
                class="w-full bg-primary text-white py-2 px-4 rounded-lg hover:bg-primary-darker transition-colors flex items-center justify-center gap-2">
            <?= $heroicons->renderHtml('shopping-cart', 'w-5 h-5') ?>
            <span>Add to Cart</span>
        </button>
    </div>
</div>
```

#### Example 2: Customize Header

**Create:** `app/design/frontend/MyCompany/hyva-custom/Magento_Theme/templates/html/header.phtml`

```php
<?php
/**
 * Custom Header Template
 */
use Hyva\Theme\ViewModel\HeroiconsOutline;
use Hyva\Theme\ViewModel\Store;

/** @var HeroiconsOutline $heroicons */
/** @var Store $storeViewModel */

$heroicons = $block->getData('heroicons');
$storeViewModel = $block->getData('storeViewModel');
?>

<header class="page-header sticky top-0 z-50 bg-white shadow-md" x-data="initHeader()">
    <!-- Top Bar -->
    <div class="bg-gray-100 py-2">
        <div class="container mx-auto px-4">
            <div class="flex items-center justify-between text-sm">
                <div>
                    <span>Free shipping on orders over $50</span>
                </div>
                <div class="flex items-center gap-4">
                    <?= $block->getChildHtml('store.switcher') ?>
                    <?= $block->getChildHtml('customer.links') ?>
                </div>
            </div>
        </div>
    </div>

    <!-- Main Header -->
    <div class="container mx-auto px-4 py-4">
        <div class="flex items-center justify-between gap-4">
            <!-- Mobile Menu Button -->
            <button @click="mobileMenuOpen = true"
                    class="lg:hidden">
                <?= $heroicons->renderHtml('bars-3', 'w-6 h-6') ?>
            </button>

            <!-- Logo -->
            <div class="logo">
                <?= $block->getChildHtml('logo') ?>
            </div>

            <!-- Search (Desktop) -->
            <div class="hidden lg:block flex-1 max-w-2xl mx-8">
                <?= $block->getChildHtml('header-search') ?>
            </div>

            <!-- Icons -->
            <div class="flex items-center gap-4">
                <!-- Search (Mobile) -->
                <button @click="searchOpen = true" class="lg:hidden">
                    <?= $heroicons->renderHtml('magnifying-glass', 'w-6 h-6') ?>
                </button>

                <!-- Wishlist -->
                <a href="/wishlist" class="relative">
                    <?= $heroicons->renderHtml('heart', 'w-6 h-6') ?>
                    <span x-show="wishlistCount > 0"
                          x-text="wishlistCount"
                          class="absolute -top-2 -right-2 bg-primary text-white rounded-full w-5 h-5 flex items-center justify-center text-xs">
                    </span>
                </a>

                <!-- Cart -->
                <button @click="openCart()" class="relative">
                    <?= $heroicons->renderHtml('shopping-cart', 'w-6 h-6') ?>
                    <span x-show="cartQty > 0"
                          x-text="cartQty"
                          class="absolute -top-2 -right-2 bg-primary text-white rounded-full w-5 h-5 flex items-center justify-center text-xs">
                    </span>
                </button>
            </div>
        </div>
    </div>

    <!-- Navigation -->
    <div class="hidden lg:block bg-gray-50 border-t">
        <div class="container mx-auto px-4">
            <?= $block->getChildHtml('topmenu') ?>
        </div>
    </div>

    <!-- Mobile Menu -->
    <div x-show="mobileMenuOpen"
         x-cloak
         @click.away="mobileMenuOpen = false"
         class="fixed inset-0 z-50 lg:hidden">
        <div class="absolute inset-0 bg-black bg-opacity-50"></div>
        <div class="absolute left-0 top-0 bottom-0 w-80 bg-white shadow-xl">
            <?= $block->getChildHtml('mobile.menu') ?>
        </div>
    </div>
</header>

<script>
function initHeader() {
    return {
        mobileMenuOpen: false,
        searchOpen: false,
        cartQty: 0,
        wishlistCount: 0,

        openCart() {
            this.$dispatch('open-cart-drawer');
        }
    }
}
</script>
```

---

## Layout XML Customization

### Add Custom Block

**File:** `app/design/frontend/MyCompany/hyva-custom/Magento_Catalog/layout/catalog_product_view.xml`

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <!-- Add custom product badge -->
        <referenceContainer name="product.info.main">
            <block name="custom.product.badge"
                   template="Magento_Catalog::product/custom-badge.phtml"
                   before="product.info.price">
                <arguments>
                    <argument name="badge_text" xsi:type="string">New Arrival</argument>
                </arguments>
            </block>
        </referenceContainer>

        <!-- Move stock info -->
        <move element="product.info.stock.sku"
              destination="product.info.main"
              after="product.info.price"/>

        <!-- Remove block -->
        <referenceBlock name="product.info.review" remove="true"/>
    </body>
</page>
```

### Extend Default Layout

**File:** `app/design/frontend/MyCompany/hyva-custom/Magento_Theme/layout/default.xml`

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <!-- Add custom CSS -->
        <referenceContainer name="head.additional">
            <block name="custom.css" template="Magento_Theme::html/custom-css.phtml"/>
        </referenceContainer>

        <!-- Add footer newsletter -->
        <referenceContainer name="footer">
            <block name="footer.newsletter"
                   template="Magento_Theme::html/footer/newsletter.phtml"
                   before="footer-content"/>
        </referenceContainer>

        <!-- Add trust badges -->
        <referenceContainer name="footer">
            <block name="footer.trust.badges"
                   template="Magento_Theme::html/footer/trust-badges.phtml"
                   after="footer-content"/>
        </referenceContainer>
    </body>
</page>
```

---

## Adding Custom Components

### Custom Button Component

**File:** `web/tailwind/components/buttons.css`

```css
/* Custom Button Styles */
@layer components {
    .btn {
        @apply inline-flex items-center justify-center px-6 py-3
               font-medium rounded-lg transition-all duration-200
               focus:outline-none focus:ring-2 focus:ring-offset-2;
    }

    .btn-primary {
        @apply bg-primary text-white hover:bg-primary-darker
               focus:ring-primary shadow-md hover:shadow-lg;
    }

    .btn-secondary {
        @apply bg-secondary text-white hover:bg-secondary-darker
               focus:ring-secondary shadow-md hover:shadow-lg;
    }

    .btn-outline {
        @apply border-2 border-primary text-primary hover:bg-primary hover:text-white
               focus:ring-primary;
    }

    .btn-ghost {
        @apply text-primary hover:bg-primary hover:bg-opacity-10
               focus:ring-primary;
    }

    .btn-sm {
        @apply px-4 py-2 text-sm;
    }

    .btn-lg {
        @apply px-8 py-4 text-lg;
    }

    .btn-full {
        @apply w-full;
    }
}
```

**Import in tailwind-source.css:**

```css
/* File: web/tailwind/tailwind-source.css */
@import '@hyva-themes/hyva-modules/tailwindcss/tailwind-source.css';

/* Custom components */
@import './components/buttons.css';
@import './components/cards.css';
@import './components/forms.css';

/* Custom utilities */
@import './utilities/custom.css';
```

---

## Styling with Tailwind

### Using Custom Colors

```html
<!-- Using custom primary color -->
<button class="bg-primary text-white hover:bg-primary-darker">
    Buy Now
</button>

<!-- Using custom secondary color -->
<div class="border-2 border-secondary text-secondary">
    Special Offer
</div>

<!-- Using accent color -->
<span class="bg-accent text-white px-4 py-2 rounded">
    New
</span>
```

### Custom Utility Classes

**File:** `web/tailwind/utilities/custom.css`

```css
@layer utilities {
    .text-balance {
        text-wrap: balance;
    }

    .animation-fade-in {
        animation: fadeIn 0.3s ease-in;
    }

    @keyframes fadeIn {
        from { opacity: 0; transform: translateY(10px); }
        to { opacity: 1; transform: translateY(0); }
    }

    .line-clamp-3 {
        display: -webkit-box;
        -webkit-line-clamp: 3;
        -webkit-box-orient: vertical;
        overflow: hidden;
    }
}
```

---

## Development Workflow

### Daily Development

```bash
# 1. Start Tailwind watch mode
cd app/design/frontend/MyCompany/hyva-custom/web/tailwind
npm run watch

# Keep this terminal open - it will watch for changes

# 2. In another terminal, work on templates
# Edit templates in:
# app/design/frontend/MyCompany/hyva-custom/Magento_*/templates/

# 3. After layout XML changes
php bin/magento cache:clean layout

# 4. After template changes (developer mode)
# No cache clean needed, just refresh browser

# 5. Before committing/deploying
npm run build  # Minified production CSS
php bin/magento cache:flush
```

### Making Changes

**CSS/Tailwind changes:**
1. Edit `hyva.config.json` for colors/tokens
2. Edit component CSS files
3. Changes auto-compile (watch mode)
4. Refresh browser

**Template changes:**
1. Edit `.phtml` files
2. Clear layout cache: `php bin/magento cache:clean layout`
3. Refresh browser

**Layout XML changes:**
1. Edit `.xml` files
2. Clear layout cache: `php bin/magento cache:clean layout`
3. Refresh browser

---

## Best Practices

### 1. Never Modify Vendor Files

❌ **Don't:**
```bash
# Never edit vendor files
vim vendor/hyva-themes/magento2-default-theme/Magento_Catalog/templates/product/list/item.phtml
```

✅ **Do:**
```bash
# Copy to your theme first
cp vendor/hyva-themes/magento2-default-theme/Magento_Catalog/templates/product/list/item.phtml \
   app/design/frontend/MyCompany/hyva-custom/Magento_Catalog/templates/product/list/item.phtml
# Then edit your copy
```

### 2. Use Child Theme Inheritance

Your theme should always inherit from `Hyva/default`:

```xml
<parent>Hyva/default</parent>
```

### 3. Override Only What You Need

Don't copy entire directories. Only override specific files you're customizing.

### 4. Use Design Tokens

Instead of hardcoding colors:

❌ **Don't:**
```html
<button class="bg-blue-600 hover:bg-blue-700">
```

✅ **Do:**
```html
<button class="bg-primary hover:bg-primary-darker">
```

### 5. Keep Tailwind Config Clean

```json
{
    "tokens": {
        "values": {
            "color": {
                "primary": {
                    "DEFAULT": "#3b82f6",
                    "darker": "#1e40af"
                }
            }
        }
    }
}
```

### 6. Version Control

```.gitignore
# Ignore generated files
web/css/styles.css
web/tailwind/node_modules/
web/tailwind/package-lock.json
```

Commit:
- Source files (templates, layouts, config)
- Tailwind source CSS
- `package.json`
- `hyva.config.json`

### 7. Document Your Changes

```php
<?php
/**
 * Custom Product List Item Template
 *
 * Changes:
 * - Added quick view button
 * - Changed hover effect
 * - Added sale badge
 *
 * @category  Design
 * @package   MyCompany_HyvaCustom
 * @author    Your Name
 * @date      2025-01-15
 */
```

---

## Troubleshooting

### Issue 1: CSS Changes Not Appearing

**Solution:**

```bash
# 1. Restart Tailwind watch
cd web/tailwind
npm run watch

# 2. Clear browser cache (Ctrl+Shift+R)

# 3. Clear Magento cache
php bin/magento cache:flush

# 4. Rebuild CSS
npm run build
php bin/magento setup:static-content:deploy -f
```

### Issue 2: Template Changes Not Showing

**Solution:**

```bash
# 1. Clear layout cache
php bin/magento cache:clean layout full_page

# 2. Check developer mode
php bin/magento deploy:mode:show

# 3. If not developer mode:
php bin/magento deploy:mode:set developer

# 4. Verify template path is correct
ls -la app/design/frontend/MyCompany/hyva-custom/Magento_Catalog/templates/product/list/item.phtml
```

### Issue 3: Theme Not Appearing in Admin

**Solution:**

```bash
# 1. Verify registration
php bin/magento theme:list | grep MyCompany

# 2. If not listed, run:
php bin/magento setup:upgrade
php bin/magento cache:flush

# 3. Check registration.php is correct
cat app/design/frontend/MyCompany/hyva-custom/registration.php
```

### Issue 4: Parent Theme Path Error

**Solution:**

Check `web/tailwind/hyva.config.json`:

```json
{
    "tailwind": {
        "include": [
            { "src": "vendor/hyva-themes/magento2-default-theme" }
        ]
    }
}
```

For CSP theme, use:
```json
{ "src": "vendor/hyva-themes/magento2-default-theme-csp" }
```

### Issue 5: Node/NPM Errors

**Solution:**

```bash
# 1. Clear node_modules
cd web/tailwind
rm -rf node_modules package-lock.json

# 2. Reinstall
npm install

# 3. Verify Node version (18+)
node --version

# 4. If wrong version, use nvm:
nvm install 18
nvm use 18
```

---

## Quick Reference

### Essential Commands

```bash
# Theme registration
php bin/magento setup:upgrade
php bin/magento cache:flush
php bin/magento theme:list

# Tailwind compilation
cd web/tailwind
npm run watch      # Development
npm run build      # Production

# Cache management
php bin/magento cache:clean layout
php bin/magento cache:clean full_page
php bin/magento cache:flush

# Static content
php bin/magento setup:static-content:deploy -f
```

### Directory Quick Reference

| Purpose | Path |
|---------|------|
| **Theme Root** | `app/design/frontend/MyCompany/hyva-custom/` |
| **Templates** | `Magento_*/templates/` |
| **Layouts** | `Magento_*/layout/` |
| **Tailwind Config** | `web/tailwind/` |
| **Compiled CSS** | `web/css/styles.css` |
| **Images** | `web/images/` |

---

**Last Updated:** November 2025
**Hyvä Version:** 1.3+
**Tailwind Version:** 4.1+
**Document Version:** 1.0.0
