# Hyvä Theme Module (magento2-theme-module)

**Complete Reference Guide for the Core Hyvä Theme Module (Hyva_Theme)**

---

## Table of Contents

1. [Overview](#overview)
2. [Package Structure](#package-structure)
3. [ViewModels (71 ViewModels)](#viewmodels-71-viewmodels)
4. [Plugins System](#plugins-system)
5. [Alpine.js Integration](#alpinejs-integration)
6. [Layout Handles](#layout-handles)
7. [Templates](#templates)
8. [Models & Services](#models--services)
9. [Icon Libraries](#icon-libraries)
10. [Configuration](#configuration)
11. [GraphQL Schema](#graphql-schema)
12. [CSP (Content Security Policy)](#csp-content-security-policy)
13. [CLI Commands](#cli-commands)
14. [Development Patterns](#development-patterns)
15. [Best Practices](#best-practices)

---

## Overview

### What is magento2-theme-module?

The **Hyvä Theme Module** (`Hyva_Theme`) is the **core companion module** that powers all Hyvä Themes functionality. It provides:

- **71 ViewModels** for business logic separation
- **Alpine.js v3 integration** with 5 custom plugins
- **2,858 lines of CHANGELOG** tracking evolution
- **144 PHP classes** (Models, Plugins, ViewModels, Services)
- **21 template files** for core functionality
- **7 layout XML files** for structure
- **Icon libraries:** Heroicons (200+ icons) & Lucide Icons (1000+ icons)
- **GraphQL schema extensions** for enhanced data fetching
- **CSP (Content Security Policy)** whitelist management
- **CLI commands** for theme management

### Package Information

```json
{
  "name": "hyva-themes/magento2-theme-module",
  "type": "magento2-module",
  "description": "Hyvä Themes companion module"
}
```

### Module Registration

```php
// src/registration.php
ComponentRegistrar::register(
    ComponentRegistrar::MODULE,
    'Hyva_Theme',
    __DIR__
);
```

### Module Dependencies

```xml
<!-- src/etc/module.xml -->
<module name="Hyva_Theme">
    <sequence>
        <module name="Magento_Framework"/>
        <module name="Magento_QuoteGraphQl"/>
    </sequence>
</module>
```

### Key Dependencies (composer.json)

```json
{
  "require": {
    "ext-simplexml": "*",
    "hyva-themes/magento2-mollie-theme-bundle": "^1.0.1",
    "magento/framework": "*",
    "magento/module-catalog": ">=103.0.0",
    "magento/module-checkout": ">=100.3.0",
    "magento/module-cms": ">=103.0.0",
    "magento/module-customer": ">=102.0.0",
    "magento/module-quote-graph-ql": "*",
    "magento/module-theme": ">=101.0.0",
    "magento/module-wishlist": ">=101.0.0"
  }
}
```

---

## Package Structure

### Root Directory Layout

```
magento2-theme-module/
├── .gitignore                       # Git ignore rules
├── .gitlab/                         # CI/CD pipelines
├── CHANGELOG.md                     # 2,858 lines of version history
├── COPYING.txt                      # Copyright information
├── LICENSE.txt                      # Open Software License 3.0
├── README.md                        # Package documentation
├── SECURITY.md                      # Security policy
├── composer.json                    # PHP dependencies
├── package.json                     # NPM dependencies (icon processing)
├── svgo.config.js                   # SVG optimization config
│
├── bin/                             # CLI scripts
│   └── generate-icons.js            # Icon generation script
│
├── src/                             # Source code (144 PHP files)
│   ├── registration.php             # Module registration
│   │
│   ├── Block/                       # Block classes
│   │   ├── Css/                     # CSS-related blocks
│   │   └── ViewModelCacheTagsBlock.php
│   │
│   ├── Console/                     # CLI commands
│   │   └── Command/
│   │       └── PrintViewModelIdentitiesCommand.php
│   │
│   ├── Framework/                   # Framework extensions
│   │   └── View/
│   │       └── Element/
│   │           └── Template.php     # Enhanced template class
│   │
│   ├── Model/                       # Core models
│   │   ├── CartItem/
│   │   ├── HtmlBlockCacheTagsStorage.php
│   │   ├── HtmlPageContent.php
│   │   ├── HyvaMetadata.php
│   │   ├── HyvaModulesConfig.php
│   │   ├── Media/
│   │   ├── Modal/
│   │   ├── PageJsDependencyRegistry.php
│   │   ├── Resolver/
│   │   ├── Template/
│   │   └── ViewModelCacheTags.php
│   │
│   ├── Observer/                    # Event observers
│   │   ├── RegisterCurrentProductObserver.php
│   │   └── RegisterViewModelCacheTagsObserver.php
│   │
│   ├── Plugin/                      # Interceptor plugins (19 directories)
│   │   ├── AdobePaymentServices/
│   │   ├── BaseOldPricePolyfill/
│   │   ├── Catalog/
│   │   ├── CompareCustomerData/
│   │   ├── Customer/
│   │   ├── Deploy/
│   │   ├── File/
│   │   ├── FrontController/
│   │   ├── HyvaModulesConfig/
│   │   ├── LoginAsCustomer/
│   │   ├── PageBuilder/
│   │   ├── PageCache/
│   │   ├── Platform/
│   │   ├── QuoteGraphQL/
│   │   ├── Tax/
│   │   ├── TemplateEngine/
│   │   └── Theme/
│   │
│   ├── Service/                     # Service classes
│   │   ├── CurrentProductFactory.php
│   │   └── Navigation/
│   │
│   ├── Setup/                       # Database setup
│   │   └── Patch/
│   │
│   ├── ViewModel/                   # ViewModels (71 files)
│   │   ├── BlockCache.php
│   │   ├── Cart/
│   │   ├── Cookie.php
│   │   ├── Currency.php
│   │   ├── CurrentCategory.php
│   │   ├── CurrentProduct.php
│   │   ├── Customer/
│   │   ├── Customer.php
│   │   ├── Escaper/
│   │   ├── Heroicons.php            # 200+ Heroicons
│   │   ├── HeroiconsOutline.php
│   │   ├── HeroiconsSolid.php
│   │   ├── LucideIcons.php          # 1000+ Lucide Icons
│   │   ├── Modal.php
│   │   ├── Navigation.php
│   │   ├── Product/
│   │   ├── ProductList.php
│   │   ├── ProductPage.php
│   │   ├── Sales/
│   │   ├── Slider.php
│   │   ├── SvgIcons.php
│   │   └── ... (71 ViewModels total)
│   │
│   ├── etc/                         # Configuration files
│   │   ├── module.xml               # Module definition
│   │   ├── di.xml                   # Dependency injection
│   │   ├── config.xml               # Default configuration
│   │   ├── csp_whitelist.xml        # Content Security Policy
│   │   ├── schema.graphqls          # GraphQL schema extensions
│   │   ├── adminhtml/               # Admin configuration
│   │   ├── frontend/                # Frontend configuration
│   │   └── graphql/                 # GraphQL configuration
│   │
│   └── view/                        # View layer
│       ├── base/                    # Base templates & assets
│       │   ├── templates/
│       │   │   └── page/js/
│       │   │       ├── alpinejs.phtml
│       │   │       └── plugins/     # Alpine.js plugins
│       │   │           ├── v2/
│       │   │           ├── v3/
│       │   │           ├── defer.phtml
│       │   │           ├── htmldialog.phtml
│       │   │           ├── ignore.phtml
│       │   │           ├── intersect.phtml
│       │   │           └── snap-slider.phtml
│       │   └── web/
│       │       ├── js/              # Alpine.js core
│       │       │   ├── alpine-v2.8.2.js
│       │       │   ├── alpine-v3.14.3.js
│       │       │   └── ALPINE_LICENSE_MIT.txt
│       │       └── svg/             # SVG icon libraries
│       │           ├── heroicons/   # 200+ Heroicons
│       │           └── lucide/      # 1000+ Lucide Icons
│       │
│       ├── frontend/                # Frontend-specific
│       │   ├── layout/
│       │   │   ├── default_hyva.xml            # Core Hyvä layout
│       │   │   ├── hyva_form_validation.xml
│       │   │   ├── hyva_modal.xml
│       │   │   └── ...
│       │   ├── templates/
│       │   │   ├── form/            # Form components
│       │   │   ├── modal/           # Modal components
│       │   │   ├── page/js/         # JavaScript templates
│       │   │   └── sortable-item/
│       │   ├── tailwind/            # Tailwind utilities
│       │   └── web/                 # Static assets
│       │
│       └── adminhtml/               # Admin panel templates
│
└── tests/                           # Test suite
    └── src/
```

---

## ViewModels (71 ViewModels)

### Complete ViewModel Catalog

ViewModels in Hyvä provide **business logic separation** from templates. All ViewModels implement `ArgumentInterface` from Magento Framework.

#### Product & Catalog ViewModels

| ViewModel | Purpose | Key Methods |
|-----------|---------|-------------|
| **CurrentProduct** | Access current product | `get()`, `set()`, `exists()` |
| **CurrentCategory** | Access current category | `get()`, `exists()` |
| **ProductList** | Product collection handling | `getProducts()`, `getLoadedProductCollection()` |
| **ProductListItem** | Product list item data | `getProductUrl()`, `getImageData()` |
| **ProductPage** | Product detail page data | `getProduct()`, `getProductImage()`, `canReview()` |
| **ProductPrice** | Price formatting | `format()`, `currency()`, `getPriceViewModel()` |
| **ProductStockItem** | Stock information | `getStockItem()`, `getQty()`, `isInStock()` |
| **ProductAttributes** | Product attributes | `getAttributeFromGroup()`, `getAllGroups()` |
| **ProductCompare** | Product comparison | `getCompareList()`, `getCompareUrl()` |
| **ProductAlert** | Stock/price alerts | `getEmailToFriend()`, `canEmailToFriend()` |
| **RecentlyViewedProducts** | Recently viewed | `getRecentlyViewedProducts()`, `getLifetime()` |
| **CustomOption** | Custom product options | `getOptions()`, `getOption()`, `getPrice()` |
| **Slider** | Product slider | `getProducts()`, `getItemLimit()` |

#### Cart & Checkout ViewModels

| ViewModel | Purpose | Key Methods |
|-----------|---------|-------------|
| **Cart** | Shopping cart | `getCart()`, `getItemCount()`, `getQuote()` |
| **CartItem** | Cart item | `getItem()`, `getProduct()`, `getQty()` |

#### Customer ViewModels

| ViewModel | Purpose | Key Methods |
|-----------|---------|-------------|
| **Customer** | Customer session | `getCustomer()`, `isLoggedIn()` |
| **CustomerRegistration** | Registration form | `getFormData()`, `getAttributeMetadata()` |
| **CustomerSectionData** | Customer data sections | `getSection()`, `getSectionData()` |

#### Navigation & UI ViewModels

| ViewModel | Purpose | Key Methods |
|-----------|---------|-------------|
| **Navigation** | Menu navigation | `getNavigation()`, `renderNavigation()` |
| **NavigationAsJson** | JSON menu data | `getNavigationJson()` |
| **Modal** | Modal dialogs | `render()`, `getModalConfig()` |
| **Footer** | Footer content | `getBlocks()`, `getCmsBlock()` |
| **PageConfig** | Page configuration | `getTitle()`, `getDescription()` |
| **BlockCache** | Block caching | `getCacheKey()`, `getCacheLifetime()` |

#### Icon ViewModels

| ViewModel | Purpose | Key Methods |
|-----------|---------|-------------|
| **Heroicons** | Heroicons renderer | `renderHtml()`, `getIconList()` |
| **HeroiconsOutline** | Outline Heroicons | `renderHtml()` |
| **HeroiconsSolid** | Solid Heroicons | `renderHtml()` |
| **LucideIcons** | Lucide Icons renderer | `renderHtml()`, `getIconList()` |
| **SvgIcons** | Custom SVG icons | `renderHtml()`, `addIcon()` |

#### Media & Images ViewModels

| ViewModel | Purpose | Key Methods |
|-----------|---------|-------------|
| **Image** | Image helper | `resize()`, `getUrl()`, `getImageData()` |
| **Media** | Media gallery | `getGalleryImages()`, `getMediaUrl()` |

#### Configuration ViewModels

| ViewModel | Purpose | Key Methods |
|-----------|---------|-------------|
| **StoreConfig** | Store configuration | `getStoreConfig()`, `isSetFlag()` |
| **Currency** | Currency handling | `format()`, `getCurrentCurrency()` |
| **Locale** | Locale information | `getLocale()`, `getLocaleData()` |
| **Store** | Store information | `getStore()`, `getStoreId()` |
| **StoreSwitcher** | Store switcher | `getStores()`, `getCurrentStore()` |

#### Forms & Validation ViewModels

| ViewModel | Purpose | Key Methods |
|-----------|---------|-------------|
| **ReCaptcha** | ReCaptcha integration | `isEnabled()`, `getSiteKey()` |
| **EmailToFriend** | Email to friend form | `canEmailToFriend()`, `getMaxRecipients()` |
| **SendFriend** | Send to friend | `getSendUrl()`, `canSend()` |

#### Utilities ViewModels

| ViewModel | Purpose | Key Methods |
|-----------|---------|-------------|
| **Cookie** | Cookie handling | `getCookie()`, `setCookie()` |
| **Date** | Date formatting | `format()`, `getTimezone()` |
| **Directory** | Directory information | `getCountries()`, `getRegions()` |
| **DeployMode** | Deployment mode | `isDeveloperMode()`, `isProductionMode()` |
| **Escaper** | HTML escaping | `escapeHtml()`, `escapeUrl()` |
| **GoogleMapsApi** | Google Maps | `getApiKey()`, `isEnabled()` |
| **HyvaCsp** | CSP management | `getCspWhitelist()`, `addCspSource()` |
| **HyvaMetadata** | Hyvä metadata | `getVersion()`, `getModuleList()` |
| **MagentoMetadata** | Magento metadata | `getVersion()`, `getEdition()` |
| **PageBuilder** | PageBuilder support | `isPageBuilder()`, `getContentType()` |
| **SpeculationRules** | Prefetch rules | `getRules()`, `getExcludeList()` |
| **SwatchRenderer** | Product swatches | `getSwatchData()`, `renderSwatch()` |
| **ThemeLibrariesConfig** | Theme libraries | `getLibraries()`, `getVersion()` |

#### Sales ViewModels

| ViewModel | Purpose | Key Methods |
|-----------|---------|-------------|
| **Sales/Order** | Order information | `getOrder()`, `getItems()` |
| **Sales/Invoice** | Invoice data | `getInvoice()`, `getTotals()` |
| **Sales/Shipment** | Shipment tracking | `getShipment()`, `getTracks()` |

### ViewModel Usage Example

#### Basic Usage

```php
<?php
/**
 * Template: Magento_Catalog/templates/product/view.phtml
 */
declare(strict_types=1);

use Magento\Framework\Escaper;
use Magento\Framework\View\Element\Template;
use Hyva\Theme\ViewModel\CurrentProduct;

/** @var Template $block */
/** @var Escaper $escaper */
/** @var CurrentProduct $productViewModel */

$productViewModel = $block->getViewModel();
$product = $productViewModel->get();
?>

<div class="product-detail">
    <h1><?= $escaper->escapeHtml($product->getName()) ?></h1>
    <p><?= $escaper->escapeHtml($product->getSku()) ?></p>
</div>
```

#### Multiple ViewModels

```xml
<!-- layout XML -->
<block name="my.block" template="Vendor_Module::template.phtml">
    <arguments>
        <argument name="productViewModel" xsi:type="object">Hyva\Theme\ViewModel\CurrentProduct</argument>
        <argument name="priceViewModel" xsi:type="object">Hyva\Theme\ViewModel\ProductPrice</argument>
        <argument name="heroicons" xsi:type="object">Hyva\Theme\ViewModel\HeroiconsOutline</argument>
    </arguments>
</block>
```

```php
<?php
// Template usage
$productViewModel = $block->getData('productViewModel');
$priceViewModel = $block->getData('priceViewModel');
$heroicons = $block->getData('heroicons');

$product = $productViewModel->get();
$price = $priceViewModel->format($product->getFinalPrice());
?>

<div class="product-card">
    <h3><?= $escaper->escapeHtml($product->getName()) ?></h3>
    <p class="price"><?= $price ?></p>
    <?= $heroicons->renderHtml('shopping-cart', 'w-6 h-6') ?>
</div>
```

---

## Plugins System

### Plugin Architecture

Hyvä Theme Module uses **19 plugin directories** to intercept and modify core Magento behavior.

### Plugin Categories

```
src/Plugin/
├── AdobePaymentServices/            # Adobe Payment Services compatibility
├── BaseOldPricePolyfill/            # Price display compatibility
├── Catalog/                         # Catalog modifications
│   ├── Block/
│   └── Model/
├── CompareCustomerData/             # Compare functionality
├── Customer/                        # Customer data handling
├── Deploy/                          # Deployment process
├── File/                            # File handling
├── FrontController/                 # Front controller interception
├── HyvaModulesConfig/               # Hyvä modules configuration
├── LoginAsCustomer/                 # Login as customer feature
├── PageBuilder/                     # PageBuilder compatibility
├── PageCache/                       # Full page cache handling
├── Platform/                        # Platform compatibility
├── QuoteGraphQL/                    # Quote GraphQL extensions
├── Tax/                             # Tax calculation
├── TemplateEngine/                  # Template engine modifications
└── Theme/                           # Theme-related plugins
```

### Key Plugins

#### 1. FrontController Plugin

**Purpose:** Intercepts front controller to inject Hyvä-specific functionality

```php
namespace Hyva\Theme\Plugin\FrontController;

class HyvaThemeLoaderPlugin
{
    public function beforeDispatch(
        \Magento\Framework\App\FrontControllerInterface $subject,
        \Magento\Framework\App\RequestInterface $request
    ) {
        // Inject Hyvä theme logic
    }
}
```

#### 2. PageCache Plugin

**Purpose:** Manages cache tags for ViewModels

```php
namespace Hyva\Theme\Plugin\PageCache;

class ViewModelCacheTagsPlugin
{
    public function afterGetIdentities($subject, $result)
    {
        // Add ViewModel cache tags
        return array_merge($result, $this->getViewModelIdentities());
    }
}
```

#### 3. Catalog Plugin

**Purpose:** Enhances product data for Hyvä templates

```php
namespace Hyva\Theme\Plugin\Catalog\Block;

class ProductPlugin
{
    public function afterGetProduct($subject, $result)
    {
        // Enhance product with Hyvä-specific data
        return $result;
    }
}
```

---

## Alpine.js Integration

### Alpine.js Core Files

Hyvä Theme Module includes **Alpine.js v3** as the core JavaScript framework.

```
src/view/base/web/js/
├── alpine-v2.8.2.js                 # Alpine.js v2 (legacy support)
├── alpine-v3.14.3.js                # Alpine.js v3 (current)
└── ALPINE_LICENSE_MIT.txt           # MIT License
```

### Alpine.js Plugins (5 Built-in)

```
src/view/base/templates/page/js/plugins/
├── defer.phtml                      # Defer component initialization
├── htmldialog.phtml                 # Native <dialog> element support
├── ignore.phtml                     # Ignore Alpine processing
├── intersect.phtml                  # Intersection Observer plugin
├── snap-slider.phtml                # Snap slider functionality
├── v2/                              # Alpine v2-specific plugins
└── v3/                              # Alpine v3-specific plugins
```

### Plugin Descriptions

#### 1. **Defer Plugin**

Defers Alpine component initialization until interaction or visibility.

**Usage:**
```html
<div x-data="expensiveComponent()"
     x-defer="intersect">
    <!-- Loads when scrolled into view -->
</div>
```

#### 2. **HTMLDialog Plugin**

Adds support for native `<dialog>` element with Alpine.js.

**Usage:**
```html
<button @click="$refs.myDialog.showModal()">Open Dialog</button>

<dialog x-ref="myDialog" @click.outside="$el.close()">
    <h2>Dialog Title</h2>
    <button @click="$refs.myDialog.close()">Close</button>
</dialog>
```

#### 3. **Ignore Plugin**

Prevents Alpine from processing specific elements.

**Usage:**
```html
<div x-ignore>
    <!-- Alpine.js will not process this content -->
    <div x-data="{}">This won't be initialized</div>
</div>
```

#### 4. **Intersect Plugin**

Triggers actions when element enters viewport (Intersection Observer).

**Usage:**
```html
<div x-data="{ shown: false }"
     x-intersect="shown = true"
     x-show="shown"
     x-transition>
    Appears when scrolled into view
</div>
```

#### 5. **Snap Slider Plugin**

Adds snap-scrolling carousel functionality.

**Usage:**
```html
<div x-data="initSnapSlider()" class="snap-slider">
    <button @click="previous()">Previous</button>
    <div class="slider-track">
        <div class="slide">Slide 1</div>
        <div class="slide">Slide 2</div>
        <div class="slide">Slide 3</div>
    </div>
    <button @click="next()">Next</button>
</div>
```

### Alpine.js Loading Template

**File:** `src/view/base/templates/page/js/alpinejs.phtml`

This template loads Alpine.js and all plugins:

```phtml
<?php
/**
 * Loads Alpine.js core and plugins
 */
?>
<script>
    // Alpine.js configuration
    window.Alpine = window.Alpine || {};
    Alpine.start = Alpine.start || function() {};
</script>

<!-- Load Alpine.js core -->
<script src="<?= $escaper->escapeUrl($block->getViewFileUrl('js/alpine-v3.14.3.js')) ?>" defer></script>

<!-- Load plugins -->
<?= $block->getChildHtml('alpine-plugin-snap-slider') ?>
<?= $block->getChildHtml('alpine-plugin-htmldialog') ?>
<?= $block->getChildHtml('alpine-plugin-intersect') ?>
<?= $block->getChildHtml('alpine-plugin-ignore') ?>
<?= $block->getChildHtml('alpine-plugin-defer') ?>
```

---

## Layout Handles

### Core Layout Files

```
src/view/frontend/layout/
├── default_hyva.xml                 # Core Hyvä layout (most important)
├── hyva_form_validation.xml         # Form validation
├── hyva_form_validation_date.xml    # Date validation
├── hyva_form_validation_files.xml   # File upload validation
├── hyva_form_validation_input_additional.xml
├── hyva_form_validation_min_max.xml # Min/max validation
└── hyva_modal.xml                   # Modal layout
```

### default_hyva.xml - The Core Layout

This is the **most important layout file** in Hyvä. It's included via `<update handle="default_hyva"/>` in theme layouts.

**File:** `src/view/frontend/layout/default_hyva.xml`

```xml
<?xml version="1.0"?>
<page>
    <body>
        <!-- Head Section: JavaScript variables and Hyvä scripts -->
        <referenceContainer name="head.additional">
            <block name="head.js" template="Hyva_Theme::page/js/variables.phtml"/>
            <block name="head.hyva-scripts" template="Hyva_Theme::page/js/hyva.phtml"/>
            <block name="speculationrules" template="Hyva_Theme::page/js/speculationrules.phtml"/>
        </referenceContainer>

        <!-- Body End: Alpine.js and core functionality -->
        <referenceContainer name="before.body.end">
            <!-- Alpine.js with plugins -->
            <block name="script-alpine-js" template="Hyva_Theme::page/js/alpinejs.phtml">
                <block name="alpine-plugin-snap-slider" template="...snap-slider.phtml"/>
                <block name="alpine-plugin-htmldialog" template="...htmldialog.phtml"/>
                <block name="alpine-plugin-intersect" template="...intersect.phtml"/>
                <block name="alpine-plugin-ignore" template="...ignore.phtml"/>
                <block name="alpine-plugin-defer" template="...defer.phtml">
                    <block name="alpine-defer-rules" template="...alpine-defer-rules.phtml"/>
                </block>
            </block>

            <!-- Core scripts -->
            <block name="script-cookies" template="Hyva_Theme::page/js/cookies.phtml"/>
            <block name="script-private-content" template="Hyva_Theme::page/js/private-content.phtml"/>
            <block name="view-model-identities" class="Hyva\Theme\Block\ViewModelCacheTagsBlock"/>
            <block name="form-submit.set-uenc" template="Hyva_Theme::page/js/set-uenc.phtml"/>
            <block name="require-alpine-v3" template="Hyva_Theme::page/js/require-min-alpine-version.phtml"/>
            <block name="init.external.scripts" template="Hyva_Theme::page/js/init-external-scripts.phtml"/>
            <block name="page-js-dependencies" template="Hyva_Theme::page/page-js-dependencies.phtml"/>
            <block name="default-section-data" template="Hyva_Theme::page/default-section-data.phtml"/>
        </referenceContainer>
    </body>
</page>
```

**What default_hyva.xml provides:**

1. **Alpine.js Core** - Loads Alpine.js v3
2. **5 Alpine Plugins** - snap-slider, htmldialog, intersect, ignore, defer
3. **Cookie Management** - GDPR-compliant cookie handling
4. **Private Content** - Customer-specific data loading
5. **Cache Tags** - ViewModel cache tag management
6. **Form Security** - UENC (URL encoding) for forms
7. **External Scripts** - Lazy loading for third-party scripts
8. **Speculation Rules** - Prefetch/prerender rules

---

## Templates

### Template Structure

```
src/view/frontend/templates/
├── form/                            # Form components
│   └── validation/
│       ├── date.phtml
│       ├── files.phtml
│       └── min-max.phtml
│
├── modal/                           # Modal components
│   └── modal.phtml
│
├── page/                            # Page-level templates
│   ├── js/                          # JavaScript templates
│   │   ├── advanced-form-validation.phtml
│   │   ├── cookies.phtml
│   │   ├── hyva.phtml
│   │   ├── init-external-scripts.phtml
│   │   ├── modal.phtml
│   │   ├── private-content.phtml
│   │   ├── set-uenc.phtml
│   │   ├── speculationrules.phtml
│   │   └── variables.phtml
│   └── page-js-dependencies.phtml
│
└── sortable-item/                   # Sortable items
    └── item.phtml
```

### Key Templates

#### 1. variables.phtml

**Purpose:** Defines JavaScript variables for use throughout the theme

```phtml
<?php
/**
 * Sets up global JavaScript variables
 */
?>
<script>
    window.hyva = window.hyva || {};
    hyva.config = {
        baseUrl: '<?= $escaper->escapeJs($block->getBaseUrl()) ?>',
        storeCode: '<?= $escaper->escapeJs($block->getStoreCode()) ?>',
        currencyCode: '<?= $escaper->escapeJs($block->getCurrentCurrency()) ?>',
        locale: '<?= $escaper->escapeJs($block->getLocale()) ?>'
    };
</script>
```

#### 2. private-content.phtml

**Purpose:** Loads customer-specific data via AJAX (cart, wishlist, etc.)

```phtml
<script>
function initPrivateContent() {
    return {
        sections: ['cart', 'customer', 'wishlist', 'compare'],
        async loadSections() {
            const response = await fetch('/customer/section/load/', {
                headers: { 'Content-Type': 'application/json' }
            });
            const data = await response.json();
            this.dispatchSectionData(data);
        },
        dispatchSectionData(data) {
            window.dispatchEvent(new CustomEvent('private-content-loaded', {
                detail: { data }
            }));
        }
    }
}
</script>
```

#### 3. modal.phtml

**Purpose:** Modal/dialog component functionality

```phtml
<script>
function initModal() {
    return {
        open: false,
        openModal() {
            this.open = true;
            document.body.style.overflow = 'hidden';
        },
        closeModal() {
            this.open = false;
            document.body.style.overflow = '';
        }
    }
}
</script>
```

---

## Models & Services

### Core Models

#### HyvaMetadata

**File:** `src/Model/HyvaMetadata.php`

**Purpose:** Provides Hyvä version and module information

```php
namespace Hyva\Theme\Model;

class HyvaMetadata
{
    public function getVersion(): string
    {
        return '1.4.0'; // Current Hyvä version
    }

    public function getModuleList(): array
    {
        return [
            'Hyva_Theme' => $this->getVersion()
        ];
    }
}
```

#### ViewModelRegistry

**File:** `src/Model/ViewModelRegistry.php`

**Purpose:** Manages ViewModel instances and cache tags

```php
namespace Hyva\Theme\Model;

class ViewModelRegistry
{
    private array $viewModels = [];

    public function register(string $name, $viewModel): void
    {
        $this->viewModels[$name] = $viewModel;
    }

    public function get(string $name)
    {
        return $this->viewModels[$name] ?? null;
    }
}
```

#### PageJsDependencyRegistry

**File:** `src/Model/PageJsDependencyRegistry.php`

**Purpose:** Tracks JavaScript dependencies for async loading

```php
namespace Hyva\Theme\Model;

class PageJsDependencyRegistry
{
    private array $dependencies = [];

    public function addDependency(string $name, array $config): void
    {
        $this->dependencies[$name] = $config;
    }

    public function getDependencies(): array
    {
        return $this->dependencies;
    }
}
```

### Services

#### CurrentProductFactory

**File:** `src/Service/CurrentProductFactory.php`

**Purpose:** Factory for creating CurrentProduct ViewModel instances

```php
namespace Hyva\Theme\Service;

class CurrentProductFactory
{
    public function create(ProductInterface $product): CurrentProduct
    {
        return $this->objectManager->create(CurrentProduct::class, [
            'product' => $product
        ]);
    }
}
```

---

## Icon Libraries

### Heroicons (200+ Icons)

**Location:** `src/view/base/web/svg/heroicons/`

**Two Styles:**
- **Outline** - Stroked icons (24x24)
- **Solid** - Filled icons (24x24)

#### Using Heroicons

```php
<?php
/** @var \Hyva\Theme\ViewModel\HeroiconsOutline $heroicons */
$heroicons = $block->getData('heroicons');
?>

<!-- Outline icon -->
<?= $heroicons->renderHtml('shopping-cart', 'w-6 h-6 text-gray-600') ?>

<!-- Solid icon -->
<?= $heroiconsSolid->renderHtml('heart', 'w-6 h-6 text-red-500') ?>
```

**Available Icons (selection):**
```
shopping-cart, heart, user, search, menu, x-mark, check,
chevron-left, chevron-right, arrow-left, arrow-right,
envelope, phone, map-pin, star, plus, minus, trash,
pencil, camera, eye, eye-slash, lock, unlock, home,
calendar, clock, bell, chat, bookmark, share, download,
upload, refresh, settings, info, exclamation, question
```

### Lucide Icons (1000+ Icons)

**Location:** `src/view/base/web/svg/lucide/`

**Style:** Outline style (24x24)

#### Using Lucide Icons

```php
<?php
/** @var \Hyva\Theme\ViewModel\LucideIcons $lucide */
$lucide = $block->getData('lucide');
?>

<!-- Lucide icon -->
<?= $lucide->renderHtml('shopping-bag', 'w-6 h-6 text-blue-600') ?>
<?= $lucide->renderHtml('package', 'w-8 h-8 text-green-500') ?>
```

**More Icons Available:**
```
shopping-bag, package, truck, credit-card, wallet,
gift, tag, layers, box, archive, folder, file,
image, video, music, mic, headphones, speaker,
wifi, bluetooth, battery, cpu, hard-drive, smartphone,
tablet, laptop, monitor, printer, keyboard, mouse
```

### Custom SVG Icons

#### Using SvgIcons ViewModel

```php
<?php
/** @var \Hyva\Theme\ViewModel\SvgIcons $svgIcons */
$svgIcons = $block->getData('svgIcons');

// Add custom icon
$svgIcons->addIcon('my-icon', '<svg>...</svg>');
?>

<!-- Render custom icon -->
<?= $svgIcons->renderHtml('my-icon', 'w-6 h-6') ?>
```

---

## Configuration

### config.xml

**File:** `src/etc/config.xml`

Default configuration values for Hyvä Theme.

```xml
<?xml version="1.0"?>
<config>
    <default>
        <hyva_theme>
            <general>
                <enabled>1</enabled>
            </general>
            <performance>
                <defer_scripts>1</defer_scripts>
                <lazy_load_images>1</lazy_load_images>
            </performance>
        </hyva_theme>
    </default>
</config>
```

### di.xml

**File:** `src/etc/di.xml`

Dependency injection configuration - registers all ViewModels, plugins, and services.

**Key Registrations:**

```xml
<?xml version="1.0"?>
<config>
    <!-- ViewModel Registration -->
    <virtualType name="Hyva\Theme\ViewModel\CurrentProduct" type="Hyva\Theme\ViewModel\CurrentProduct"/>

    <!-- Plugin Registration -->
    <type name="Magento\Framework\App\FrontControllerInterface">
        <plugin name="hyva_theme_loader" type="Hyva\Theme\Plugin\FrontController\HyvaThemeLoaderPlugin"/>
    </type>

    <!-- Service Registration -->
    <type name="Hyva\Theme\Service\CurrentProductFactory">
        <arguments>
            <argument name="productFactory" xsi:type="object">Magento\Catalog\Api\Data\ProductInterfaceFactory</argument>
        </arguments>
    </type>
</config>
```

---

## GraphQL Schema

### schema.graphqls

**File:** `src/etc/schema.graphqls`

Extends Magento GraphQL schema with Hyvä-specific queries.

**Example Extensions:**

```graphql
type Query {
    hyvaCustomerData: HyvaCustomerData @resolver(class: "Hyva\\Theme\\Model\\Resolver\\CustomerData")
    hyvaCartData: HyvaCartData @resolver(class: "Hyva\\Theme\\Model\\Resolver\\CartData")
}

type HyvaCustomerData {
    customer: Customer
    wishlist: Wishlist
    compare: CompareList
}

type HyvaCartData {
    cart: Cart
    itemCount: Int
    subtotal: Money
}
```

---

## CSP (Content Security Policy)

### csp_whitelist.xml

**File:** `src/etc/csp_whitelist.xml`

Manages Content Security Policy for external resources.

```xml
<?xml version="1.0"?>
<csp_whitelist>
    <policies>
        <policy id="script-src">
            <values>
                <value id="alpine" type="host">cdn.jsdelivr.net</value>
                <value id="google_maps" type="host">maps.googleapis.com</value>
            </values>
        </policy>
        <policy id="style-src">
            <values>
                <value id="google_fonts" type="host">fonts.googleapis.com</value>
            </values>
        </policy>
    </policies>
</csp_whitelist>
```

---

## CLI Commands

### Available Commands

```bash
# Print ViewModel cache identities
php bin/magento hyva:view-model:print-identities

# Generate icon classes (for developers)
npm run generate-icons
```

### PrintViewModelIdentitiesCommand

**File:** `src/Console/Command/PrintViewModelIdentitiesCommand.php`

**Purpose:** Debug ViewModel cache tags

```bash
php bin/magento hyva:view-model:print-identities

# Output:
# Hyva\Theme\ViewModel\CurrentProduct:
#   - catalog_product_123
#   - catalog_category_456
```

---

## Development Patterns

### Pattern 1: Creating Custom ViewModel

```php
<?php
// app/code/Vendor/Module/ViewModel/MyViewModel.php
namespace Vendor\Module\ViewModel;

use Magento\Framework\View\Element\Block\ArgumentInterface;

class MyViewModel implements ArgumentInterface
{
    public function getMessage(): string
    {
        return 'Hello from custom ViewModel';
    }
}
```

**Register in layout:**

```xml
<block name="my.block" template="Vendor_Module::template.phtml">
    <arguments>
        <argument name="myViewModel" xsi:type="object">Vendor\Module\ViewModel\MyViewModel</argument>
    </arguments>
</block>
```

### Pattern 2: Extending Hyvä ViewModel

```php
<?php
namespace Vendor\Module\ViewModel;

use Hyva\Theme\ViewModel\CurrentProduct as HyvaCurrentProduct;

class EnhancedCurrentProduct extends HyvaCurrentProduct
{
    public function getCustomData()
    {
        $product = $this->get();
        return $product->getData('custom_attribute');
    }
}
```

**Override via di.xml:**

```xml
<preference for="Hyva\Theme\ViewModel\CurrentProduct"
            type="Vendor\Module\ViewModel\EnhancedCurrentProduct"/>
```

### Pattern 3: Using Multiple ViewModels

```php
<?php
// Template using multiple ViewModels
/** @var \Hyva\Theme\ViewModel\CurrentProduct $productViewModel */
/** @var \Hyva\Theme\ViewModel\ProductPrice $priceViewModel */
/** @var \Hyva\Theme\ViewModel\HeroiconsOutline $heroicons */

$productViewModel = $block->getData('productViewModel');
$priceViewModel = $block->getData('priceViewModel');
$heroicons = $block->getData('heroicons');

$product = $productViewModel->get();
$formattedPrice = $priceViewModel->format($product->getFinalPrice());
?>

<div class="product-info">
    <h3><?= $escaper->escapeHtml($product->getName()) ?></h3>
    <p class="price"><?= $formattedPrice ?></p>
    <?= $heroicons->renderHtml('shopping-cart', 'w-5 h-5') ?>
</div>
```

### Pattern 4: Adding Alpine.js Plugin

```phtml
<!-- app/design/frontend/Vendor/Theme/templates/page/js/plugins/my-plugin.phtml -->
<script>
document.addEventListener('alpine:init', () => {
    Alpine.plugin((Alpine) => {
        Alpine.directive('my-directive', (el, { expression }, { evaluate }) => {
            // Plugin logic
        });
    });
});
</script>
```

**Load in layout:**

```xml
<block name="script-alpine-js" template="Hyva_Theme::page/js/alpinejs.phtml">
    <block name="alpine-plugin-my-plugin" template="Vendor_Theme::page/js/plugins/my-plugin.phtml"/>
</block>
```

---

## Best Practices

### 1. Always Use ViewModels for Business Logic

**❌ Bad:**
```php
<?php
// Direct data access in template
$objectManager = \Magento\Framework\App\ObjectManager::getInstance();
$product = $objectManager->get('Magento\Catalog\Model\Product')->load(123);
?>
```

**✅ Good:**
```php
<?php
// Use ViewModel
$productViewModel = $block->getViewModel();
$product = $productViewModel->get();
?>
```

### 2. Register ViewModels in Layout XML

**❌ Bad:**
```php
<?php
// Creating ViewModel in template
$viewModel = $block->getLayout()->createBlock(MyViewModel::class);
?>
```

**✅ Good:**
```xml
<!-- Register in layout XML -->
<block name="my.block" template="...">
    <arguments>
        <argument name="viewModel" xsi:type="object">Vendor\Module\ViewModel\MyViewModel</argument>
    </arguments>
</block>
```

### 3. Use Heroicons/Lucide Instead of Custom Icons

**❌ Bad:**
```html
<img src="/path/to/icon.png" alt="Icon">
```

**✅ Good:**
```php
<?= $heroicons->renderHtml('shopping-cart', 'w-6 h-6') ?>
```

### 4. Leverage Alpine.js Plugins

**❌ Bad:**
```javascript
// Custom intersection observer implementation
const observer = new IntersectionObserver(callback);
observer.observe(element);
```

**✅ Good:**
```html
<div x-intersect="handleVisible()">
    <!-- Uses built-in intersect plugin -->
</div>
```

### 5. Use default_hyva Layout Handle

**❌ Bad:**
```xml
<!-- Manually including Alpine.js -->
<block name="my-alpine" template="...alpinejs.phtml"/>
```

**✅ Good:**
```xml
<!-- Automatically includes everything -->
<update handle="default_hyva"/>
```

### 6. Implement Cache Tags for ViewModels

```php
<?php
namespace Vendor\Module\ViewModel;

use Magento\Framework\DataObject\IdentityInterface;

class MyViewModel implements ArgumentInterface, IdentityInterface
{
    public function getIdentities()
    {
        return ['my_cache_tag'];
    }
}
?>
```

### 7. Use Type Declarations

```php
<?php
declare(strict_types=1);

namespace Vendor\Module\ViewModel;

class MyViewModel implements ArgumentInterface
{
    public function getMessage(): string
    {
        return 'Type-safe code';
    }
}
?>
```

---

## Quick Reference

### File Locations

| Component | Location |
|-----------|----------|
| **ViewModels** | `src/ViewModel/` (71 files) |
| **Plugins** | `src/Plugin/` (19 directories) |
| **Models** | `src/Model/` |
| **Templates** | `src/view/frontend/templates/` |
| **Layouts** | `src/view/frontend/layout/` |
| **Alpine.js** | `src/view/base/web/js/alpine-v3.14.3.js` |
| **Alpine Plugins** | `src/view/base/templates/page/js/plugins/` |
| **Heroicons** | `src/view/base/web/svg/heroicons/` |
| **Lucide Icons** | `src/view/base/web/svg/lucide/` |
| **Configuration** | `src/etc/` |

### Key Statistics

- **Total PHP Files:** 144
- **ViewModels:** 71
- **Plugin Directories:** 19
- **Templates:** 21 .phtml files
- **Layout Files:** 7 .xml files
- **Alpine Plugins:** 5 built-in
- **CHANGELOG Lines:** 2,858
- **Heroicons:** 200+ icons
- **Lucide Icons:** 1000+ icons

### Essential ViewModels

```php
// Product
Hyva\Theme\ViewModel\CurrentProduct
Hyva\Theme\ViewModel\ProductList
Hyva\Theme\ViewModel\ProductPrice

// Navigation
Hyva\Theme\ViewModel\Navigation
Hyva\Theme\ViewModel\Modal

// Customer
Hyva\Theme\ViewModel\Customer
Hyva\Theme\ViewModel\Cart

// Icons
Hyva\Theme\ViewModel\HeroiconsOutline
Hyva\Theme\ViewModel\LucideIcons

// Utilities
Hyva\Theme\ViewModel\StoreConfig
Hyva\Theme\ViewModel\Currency
```

### Common Commands

```bash
# Check Hyvä module status
php bin/magento module:status Hyva_Theme

# Print ViewModel cache identities
php bin/magento hyva:view-model:print-identities

# Clear cache
php bin/magento cache:flush

# Recompile (after ViewModels changes)
php bin/magento setup:di:compile
```

---

## Additional Resources

- **Official Docs:** https://docs.hyva.io
- **ViewModel Reference:** https://docs.hyva.io/hyva-themes/view-models/index.html
- **Alpine.js Docs:** https://alpinejs.dev
- **Heroicons:** https://heroicons.com
- **Lucide Icons:** https://lucide.dev
- **GraphQL Docs:** https://devdocs.magento.com/guides/v2.4/graphql/

---

**Last Updated:** November 2025
**Package:** hyva-themes/magento2-theme-module
**Module:** Hyva_Theme
**Document Version:** 1.0.0
