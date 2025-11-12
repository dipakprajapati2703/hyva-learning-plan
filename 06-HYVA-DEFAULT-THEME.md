# Hyvä Default Theme (magento2-default-theme)

**Complete Reference Guide for the Hyvä Default Theme Package**

---

## Table of Contents

1. [Overview](#overview)
2. [Package Structure](#package-structure)
3. [Module Overrides (55+ Modules)](#module-overrides-55-modules)
4. [Tailwind CSS Configuration](#tailwind-css-configuration)
5. [Layout System](#layout-system)
6. [Template Architecture](#template-architecture)
7. [Web Assets](#web-assets)
8. [Theme Configuration](#theme-configuration)
9. [Development Workflow](#development-workflow)
10. [Customization Patterns](#customization-patterns)
11. [PageBuilder Integration](#pagebuilder-integration)
12. [ReCaptcha Integration](#recaptcha-integration)
13. [Third-Party Module Support](#third-party-module-support)
14. [Best Practices](#best-practices)

---

## Overview

### What is magento2-default-theme?

The **Hyvä Default Theme** (`frontend/Hyva/default`) is the reference implementation of Hyvä Themes. It provides:

- **Complete Magento 2 frontend implementation** using Tailwind CSS and Alpine.js
- **307 template files** (.phtml) covering all core Magento functionality
- **165 layout XML files** for structure and block configuration
- **55+ module overrides** for Magento core modules
- **Tailwind CSS v4** configuration with custom components and utilities
- **GraphQL-based data fetching** for improved performance
- **Responsive design** with mobile-first approach
- **Accessibility (WCAG 2.1)** compliant markup

### Package Information

```json
{
  "name": "hyva-themes/magento2-default-theme",
  "type": "magento2-theme",
  "version": "3.0.0",
  "registration": "frontend/Hyva/default"
}
```

### Key Dependencies

```json
{
  "php": ">=7.3.0",
  "magento/framework": ">=102.0.0",
  "hyva-themes/magento2-base-layout-reset": "^2.0.0",
  "hyva-themes/magento2-theme-module": ">=1.4.0-beta2",
  "hyva-themes/magento2-email-module": "*",
  "hyva-themes/magento2-graphql-tokens": "*",
  "hyva-themes/magento2-graphql-view-model": ">=1.0.1",
  "tailwindcss": "^4.1.15",
  "@hyva-themes/hyva-modules": "^1.2.2"
}
```

---

## Package Structure

### Root Directory Layout

```
magento2-default-theme/
├── .editorconfig                    # Code style configuration
├── .gitignore                       # Git ignore rules
├── .gitlab/                         # CI/CD pipelines
├── CHANGELOG.md                     # 4,890 lines of version history
├── COPYING.txt                      # License information
├── LICENSE.txt                      # Open Software License 3.0
├── LICENSE_AFL.txt                  # Academic Free License 3.0
├── README.md                        # Package documentation
├── SECURITY.md                      # Security policy
├── composer.json                    # PHP dependencies
├── grumphp.yml                      # Code quality checks
├── registration.php                 # Theme registration
├── theme.xml                        # Theme metadata
│
├── etc/                             # Theme configuration
│   └── view.xml                     # Image sizes, gallery config
│
├── i18n/                            # Translations
│   └── en_US.csv                    # English translations
│
├── media/                           # Theme preview
│   └── preview.png                  # Admin panel preview image
│
├── web/                             # Static assets
│   ├── css/                         # Compiled CSS
│   │   ├── styles.css               # Main stylesheet (Tailwind output)
│   │   └── print.css                # Print styles
│   ├── images/                      # Images
│   ├── js/                          # JavaScript files
│   └── tailwind/                    # Tailwind configuration (see below)
│
├── Hyva_Theme/                      # Hyva-specific templates
│   ├── layout/
│   ├── page_layout/
│   └── templates/
│
├── Magento_AdvancedSearch/          # Module override
├── Magento_Bundle/                  # Module override
├── Magento_Catalog/                 # Module override (largest)
├── Magento_CatalogInventory/        # Module override
├── Magento_CatalogSearch/           # Module override
├── Magento_CatalogWidget/           # Module override
├── Magento_Checkout/                # Module override
├── Magento_Cms/                     # Module override
├── Magento_ConfigurableProduct/     # Module override
├── Magento_Contact/                 # Module override
├── Magento_Cookie/                  # Module override
├── Magento_Customer/                # Module override
├── Magento_Directory/               # Module override
├── Magento_Downloadable/            # Module override
├── Magento_GiftMessage/             # Module override
├── Magento_GoogleAnalytics/         # Module override
├── Magento_GoogleGtag/              # Module override
├── Magento_GoogleTagManager/        # Module override
├── Magento_GroupedProduct/          # Module override
├── Magento_LayeredNavigation/       # Module override
├── Magento_LoginAsCustomer*/        # Module overrides
├── Magento_Msrp/                    # Module override
├── Magento_Newsletter/              # Module override
├── Magento_OrderCancellationUi/     # Module override
├── Magento_PageBuilder/             # Module override (6 directories)
├── Magento_Paypal/                  # Module override
├── Magento_ProductAlert/            # Module override
├── Magento_ReCaptcha*/              # 8 ReCaptcha modules
├── Magento_Review/                  # Module override
├── Magento_Robots/                  # Module override
├── Magento_Sales/                   # Module override
├── Magento_Search/                  # Module override
├── Magento_SendFriend/              # Module override
├── Magento_Shipping/                # Module override
├── Magento_Sitemap/                 # Module override
├── Magento_Store/                   # Module override
├── Magento_Swatches/                # Module override
├── Magento_Tax/                     # Module override
├── Magento_Theme/                   # Module override (core theme)
├── Magento_Vault/                   # Module override
├── Magento_Weee/                    # Module override
├── Magento_Wishlist/                # Module override
└── PayPal_Braintree/                # Third-party module override
```

### Module Override Structure

Each module directory follows this pattern:

```
Magento_[ModuleName]/
├── layout/                          # Layout XML files
│   ├── default.xml                  # Applied to all pages
│   ├── catalog_product_view.xml     # Product detail page
│   ├── catalog_category_view.xml    # Category page
│   └── ...
│
├── page_layout/                     # Page layouts (rare)
│   └── override/
│       └── base/
│           ├── 1column.xml
│           ├── 2columns-left.xml
│           └── ...
│
├── templates/                       # Template files (.phtml)
│   ├── product/
│   │   ├── view.phtml
│   │   ├── list.phtml
│   │   └── ...
│   └── ...
│
└── web/                             # Module-specific assets
    ├── css/
    ├── js/
    └── ...
```

---

## Module Overrides (55+ Modules)

### Complete Module List with Template Counts

| Module | Templates | Key Features |
|--------|-----------|--------------|
| **Magento_Catalog** | 80+ | Product lists, PDP, categories, compare, search |
| **Magento_Checkout** | 30+ | Cart, checkout, success page |
| **Magento_Customer** | 40+ | Account dashboard, login, registration |
| **Magento_Theme** | 25+ | Header, footer, navigation, breadcrumbs |
| **Magento_Wishlist** | 20+ | Wishlist pages, email, cart integration |
| **Magento_Cms** | 10+ | CMS pages, widgets |
| **Magento_Sales** | 15+ | Order history, invoice, shipment |
| **Magento_Review** | 12+ | Product reviews, ratings |
| **Magento_Newsletter** | 8+ | Subscription forms |
| **Magento_Contact** | 5+ | Contact form |
| **Magento_CatalogSearch** | 8+ | Search results, advanced search |
| **Magento_ConfigurableProduct** | 10+ | Configurable product options |
| **Magento_Bundle** | 8+ | Bundle product options |
| **Magento_Downloadable** | 6+ | Downloadable product links |
| **Magento_GroupedProduct** | 5+ | Grouped product display |
| **Magento_Swatches** | 8+ | Color/image swatches |
| **Magento_LayeredNavigation** | 6+ | Filters, faceted navigation |
| **Magento_PageBuilder** | 15+ | Page Builder content types |
| **Magento_ReCaptcha*** | 8 modules | ReCaptcha integration |
| **Magento_Paypal** | 5+ | PayPal payment integration |
| **PayPal_Braintree** | 4+ | Braintree payment integration |
| **Magento_Vault** | 5+ | Saved payment methods |
| **Magento_ProductAlert** | 4+ | Price/stock alerts |
| **Magento_GiftMessage** | 3+ | Gift message forms |
| **Magento_Tax** | 3+ | Tax display |
| **Magento_Weee** | 2+ | Fixed Product Tax (FPT) |
| **Magento_Msrp** | 3+ | Minimum Advertised Price |
| **Magento_SendFriend** | 3+ | Email to friend |
| **Magento_Shipping** | 4+ | Tracking popup |
| **Magento_GoogleAnalytics** | 2+ | GA tracking |
| **Magento_GoogleGtag** | 2+ | Google Tag tracking |
| **Magento_GoogleTagManager** | 2+ | GTM integration |
| **Others** | 10+ | Various utility modules |

### Most Important Module Overrides

#### 1. Magento_Catalog (Product Catalog)

**Key Templates:**

```
Magento_Catalog/templates/
├── product/
│   ├── view.phtml                        # Product detail page
│   ├── list.phtml                        # Product list
│   ├── list/item.phtml                   # Product list item
│   ├── slider/
│   │   ├── product-slider.phtml          # Product slider
│   │   └── product-slider-container.phtml
│   ├── view/
│   │   ├── addtocart.phtml               # Add to cart button
│   │   ├── gallery.phtml                 # Image gallery
│   │   ├── details.phtml                 # Product details
│   │   └── attributes.phtml              # Product attributes
│   ├── compare/
│   │   ├── list.phtml                    # Compare products
│   │   └── sidebar.phtml                 # Compare sidebar
│   └── image.phtml                       # Product image component
│
└── category/
    └── view.phtml                        # Category page
```

**Key Layouts:**

```xml
<!-- catalog_product_view.xml -->
<page>
    <update handle="default_hyva"/>
    <body>
        <referenceContainer name="content">
            <block name="product.info.main" template="Magento_Catalog::product/view.phtml">
                <!-- Product ViewModels -->
                <argument name="productViewModel" xsi:type="object">Hyva\Theme\ViewModel\CurrentProduct</argument>
                <argument name="productImageViewModel" xsi:type="object">Hyva\Theme\ViewModel\ProductImage</argument>
                <argument name="productPriceViewModel" xsi:type="object">Hyva\Theme\ViewModel\ProductPrice</argument>
            </block>
        </referenceContainer>
    </body>
</page>
```

#### 2. Magento_Checkout (Cart & Checkout)

**Key Templates:**

```
Magento_Checkout/templates/
├── cart/
│   ├── form.phtml                        # Shopping cart form
│   ├── item/
│   │   └── default.phtml                 # Cart item
│   └── totals.phtml                      # Cart totals
│
└── onepage/
    ├── success.phtml                     # Order success page
    └── failure.phtml                     # Order failure page
```

**Cart Drawer Template:**

```
Magento_Theme/templates/html/cart/
└── cart-drawer.phtml                     # Mini cart drawer
```

#### 3. Magento_Customer (Customer Account)

**Key Templates:**

```
Magento_Customer/templates/
├── form/
│   ├── login.phtml                       # Login form
│   ├── register.phtml                    # Registration form
│   └── edit.phtml                        # Account edit
│
├── account/
│   ├── dashboard.phtml                   # Customer dashboard
│   └── navigation.phtml                  # Account navigation
│
└── address/
    ├── list.phtml                        # Address book
    └── edit.phtml                        # Address edit form
```

#### 4. Magento_Theme (Core Theme Elements)

**Key Templates:**

```
Magento_Theme/templates/
├── html/
│   ├── header.phtml                      # Main header
│   ├── footer.phtml                      # Main footer
│   ├── header/
│   │   ├── topmenu.phtml                 # Top menu
│   │   ├── menu/
│   │   │   ├── mobile.phtml              # Mobile menu
│   │   │   └── desktop.phtml             # Desktop menu
│   │   ├── search-form.phtml             # Search form
│   │   └── compare.phtml                 # Compare link
│   ├── breadcrumbs.phtml                 # Breadcrumbs
│   ├── title.phtml                       # Page title
│   ├── notices.phtml                     # Global notices
│   └── skip.phtml                        # Skip to content
│
└── messages.phtml                        # Flash messages
```

**Key Layout (default.xml):**

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <update handle="default_hyva"/>
    <body>
        <referenceContainer name="header.container">
            <block name="header-content" template="Magento_Theme::html/header.phtml">
                <block name="logo" class="Magento\Theme\Block\Html\Header\Logo"/>
                <block name="header-search" template="Magento_Theme::html/header/search-form.phtml"/>
                <block name="header-compare" template="Magento_Theme::html/header/compare.phtml"/>
                <block name="cart-drawer" template="Magento_Theme::html/cart/cart-drawer.phtml"/>
                <block name="topmenu_generic" template="Magento_Theme::html/header/topmenu.phtml">
                    <block name="topmenu_mobile" template="Magento_Theme::html/header/menu/mobile.phtml"/>
                    <block name="topmenu_desktop" template="Magento_Theme::html/header/menu/desktop.phtml"/>
                </block>
            </block>
        </referenceContainer>

        <referenceContainer name="footer">
            <block name="footer-content" template="Magento_Theme::html/footer.phtml">
                <block name="footer-static-links" template="Magento_Theme::html/footer/links.phtml"/>
            </block>
        </referenceContainer>
    </body>
</page>
```

#### 5. Magento_Wishlist (Wishlist Functionality)

**Key Templates:**

```
Magento_Wishlist/templates/
├── view.phtml                            # Wishlist page
├── item/
│   ├── list.phtml                        # Wishlist items
│   └── column/
│       ├── image.phtml                   # Item image
│       ├── info.phtml                    # Item info
│       └── cart.phtml                    # Add to cart
│
├── email/
│   ├── items.phtml                       # Email items
│   └── rss.phtml                         # RSS feed
│
└── share.phtml                           # Share wishlist form
```

---

## Tailwind CSS Configuration

### Tailwind Directory Structure

```
web/tailwind/
├── .nvmrc                                # Node version (20.0.0+)
├── package.json                          # NPM dependencies
├── package-lock.json                     # Locked dependencies
├── hyva.config.json                      # Hyvä configuration
├── browser-sync.config.js                # BrowserSync config
├── tailwind-source.css                   # Source CSS file
│
├── base/                                 # Base layer customizations
│   └── typography.css                    # Typography base styles
│
├── components/                           # Component layer
│   ├── actions-toolbar.css               # Action toolbars
│   ├── button.css                        # Button styles
│   ├── card.css                          # Card components
│   ├── forms.css                         # Form elements
│   ├── messages.css                      # Flash messages
│   ├── rating-summary.css                # Star ratings
│   ├── slider.css                        # Slider styles
│   ├── swatches.css                      # Product swatches
│   ├── wrapper.css                       # Page wrapper
│   └── index.css                         # Component imports
│
├── utilities/                            # Utility layer
│   ├── fallback.css                      # Luma fallback styles
│   ├── icons.css                         # Icon utilities
│   └── index.css                         # Utility imports
│
├── theme/                                # Theme customizations
│   └── (empty - for your custom styles)
│
└── generated/                            # Auto-generated files
    ├── hyva-source.css                   # Generated source
    └── hyva-tokens.css                   # Design tokens
```

### Package.json Scripts

```json
{
  "name": "@hyva-themes/magento2-default-theme",
  "version": "3.0.0",
  "dependencies": {
    "@hyva-themes/hyva-modules": "^1.2.2",
    "@tailwindcss/cli": "^4.1.12",
    "tailwindcss": "^4.1.15"
  },
  "scripts": {
    "start": "npm run watch",
    "generate": "npx hyva-sources && npx hyva-tokens",
    "prewatch": "npm run generate",
    "watch": "npx tailwindcss -i tailwind-source.css -o ../css/styles.css --watch",
    "browser-sync": "npx browser-sync start --config ./browser-sync.config.js",
    "prebuild": "npm run generate",
    "build": "npx tailwindcss -i tailwind-source.css -o ../css/styles.css --minify"
  }
}
```

### Usage

```bash
# Navigate to tailwind directory
cd /path/to/theme/web/tailwind

# Install dependencies
npm install

# Watch mode (development)
npm run watch

# Build for production
npm run build

# With BrowserSync (live reload)
npm run browser-sync
```

### hyva.config.json (Design Tokens)

```json
{
  "tailwind": {
    "include": [],
    "exclude": []
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
        },
        "on": {
          "primary": "#fff",
          "secondary": "#fff"
        }
      },
      "form": {
        "radius": "var(--radius-lg)",
        "stroke": "var(--color-slate-400)",
        "active-color": "var(--color-primary)"
      }
    }
  }
}
```

**Using Design Tokens in Templates:**

```html
<!-- Primary color button -->
<button class="bg-primary text-on-primary hover:bg-primary-darker">
    Click Me
</button>

<!-- Secondary color -->
<div class="bg-secondary text-on-secondary">
    Secondary content
</div>

<!-- Form with token-based styling -->
<input type="text" class="border-[--form-stroke] focus:border-[--form-active-color]">
```

### Tailwind Components

#### Button Component (components/button.css)

```css
.btn {
    @apply inline-flex items-center justify-center px-6 py-3
           font-medium transition-colors duration-200
           focus:outline-none focus:ring-2 focus:ring-offset-2;
}

.btn-primary {
    @apply bg-primary text-on-primary hover:bg-primary-darker
           focus:ring-primary;
}

.btn-secondary {
    @apply bg-secondary text-on-secondary hover:bg-secondary-darker
           focus:ring-secondary;
}
```

#### Form Component (components/forms.css)

```css
.form-input {
    @apply w-full px-4 py-2 border rounded-lg
           focus:ring-2 focus:ring-primary focus:border-transparent
           transition-colors duration-200;
}

.form-select {
    @apply w-full px-4 py-2 border rounded-lg
           bg-white appearance-none
           focus:ring-2 focus:ring-primary focus:border-transparent;
}

.form-checkbox, .form-radio {
    @apply w-5 h-5 text-primary
           focus:ring-2 focus:ring-primary focus:ring-offset-2;
}
```

#### Card Component (components/card.css)

```css
.card {
    @apply bg-white rounded-lg shadow-md overflow-hidden;
}

.card-header {
    @apply px-6 py-4 border-b;
}

.card-body {
    @apply p-6;
}

.card-footer {
    @apply px-6 py-4 border-t bg-gray-50;
}
```

---

## Layout System

### Page Layouts (page_layout/)

Hyvä Default Theme overrides base page layouts:

```
Magento_Theme/page_layout/override/base/
├── 1column.xml                          # Single column layout
├── 2columns-left.xml                    # Left sidebar layout
├── 2columns-right.xml                   # Right sidebar layout
└── 3columns.xml                         # Three column layout
```

**Example: 1column.xml**

```xml
<?xml version="1.0"?>
<layout xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <update handle="default_hyva"/>
    <container name="root">
        <container name="page.wrapper" htmlClass="page-wrapper" htmlTag="div">
            <container name="global.notices" htmlClass="message global" htmlTag="div"/>
            <container name="page.top">
                <container name="header.container" htmlTag="header" htmlClass="page-header"/>
            </container>
            <container name="main.content" htmlTag="main" htmlClass="page-main">
                <container name="columns">
                    <container name="main"/>
                </container>
            </container>
            <container name="page.bottom">
                <container name="footer" htmlTag="footer" htmlClass="page-footer"/>
            </container>
        </container>
    </container>
</layout>
```

### Layout Handles

**Common Layout Handles:**

| Handle | Purpose |
|--------|---------|
| `default_hyva` | Core Hyvä functionality (Alpine.js, plugins) |
| `default` | Applied to all pages |
| `default_head_blocks` | Head blocks (meta, CSS, JS) |
| `catalog_product_view` | Product detail page |
| `catalog_category_view` | Category page |
| `catalog_category_view_type_default` | Default category type |
| `checkout_cart_index` | Shopping cart page |
| `checkout_index_index` | Checkout page |
| `customer_account` | Customer account pages |
| `cms_index_index` | Homepage |
| `cms_page_view` | CMS page |

### Container Names

**Root Containers:**

```
root
└── page.wrapper
    ├── global.notices
    ├── page.top
    │   └── header.container
    │       ├── header.panel
    │       └── header-content
    │           ├── logo.container
    │           ├── search.container
    │           └── minicart
    ├── main.content
    │   ├── page.messages
    │   ├── top.container
    │   ├── columns.top
    │   ├── columns
    │   │   ├── div.sidebar.main
    │   │   ├── main
    │   │   └── div.sidebar.additional
    │   └── page.bottom
    └── footer
        └── footer-content
```

---

## Template Architecture

### Template File Conventions

#### Standard Template Pattern

```php
<?php
/**
 * Hyvä Themes - https://hyva.io
 * Copyright © Hyvä Themes. All rights reserved.
 * See COPYING.txt for license details.
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

<div class="product-detail" x-data="productDetail()">
    <h1 class="text-3xl font-bold">
        <?= $escaper->escapeHtml($product->getName()) ?>
    </h1>

    <!-- Product content -->
</div>

<script>
function productDetail() {
    return {
        quantity: 1,
        addToCart() {
            // Add to cart logic
        }
    }
}
</script>
```

### Key Template Patterns

#### 1. Product List Item

**File:** `Magento_Catalog/templates/product/list/item.phtml`

```php
<?php
use Hyva\Theme\ViewModel\ProductListItem;
use Hyva\Theme\ViewModel\ProductImage;

/** @var ProductListItem $productListItemViewModel */
/** @var ProductImage $productImageViewModel */

$product = $block->getData('product');
$imageDisplayArea = 'category_page_list';
?>

<div class="product-item group relative"
     x-data="initPriceBox()"
     x-intersect="loadProduct()">

    <!-- Product Image -->
    <div class="product-image-container">
        <a href="<?= $escaper->escapeUrl($product->getProductUrl()) ?>">
            <?= $productImageViewModel->getImage($product, $imageDisplayArea)->toHtml() ?>
        </a>
    </div>

    <!-- Product Info -->
    <div class="product-info">
        <h3 class="product-name">
            <a href="<?= $escaper->escapeUrl($product->getProductUrl()) ?>">
                <?= $escaper->escapeHtml($product->getName()) ?>
            </a>
        </h3>

        <!-- Price -->
        <div class="price-box" x-html="$dispatch('update-price', {productId: <?= (int)$product->getId() ?>})"></div>

        <!-- Add to Cart -->
        <button @click="addToCart()" class="btn-primary">
            Add to Cart
        </button>
    </div>
</div>
```

#### 2. Product Detail Page

**File:** `Magento_Catalog/templates/product/view.phtml`

```php
<?php
use Hyva\Theme\ViewModel\CurrentProduct;
use Hyva\Theme\ViewModel\ProductPage;

/** @var CurrentProduct $productViewModel */
/** @var ProductPage $productPageViewModel */

$product = $productViewModel->get();
?>

<div x-data="initProductDetail()"
     x-init="init()"
     class="product-detail-page">

    <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
        <!-- Left: Gallery -->
        <div>
            <?= $block->getChildHtml('product.info.media') ?>
        </div>

        <!-- Right: Product Info -->
        <div>
            <h1 class="text-3xl font-bold mb-4">
                <?= $escaper->escapeHtml($product->getName()) ?>
            </h1>

            <?= $block->getChildHtml('product.info.price') ?>
            <?= $block->getChildHtml('product.info.review') ?>
            <?= $block->getChildHtml('product.info.form') ?>
            <?= $block->getChildHtml('product.info.addtocart') ?>
        </div>
    </div>

    <!-- Description Tabs -->
    <?= $block->getChildHtml('product.info.details') ?>
</div>
```

#### 3. Mini Cart Drawer

**File:** `Magento_Theme/templates/html/cart/cart-drawer.phtml`

```php
<div x-data="initCartDrawer()"
     x-show="isOpen"
     @private-content-loaded.window="onPrivateContentLoaded($event.detail.data)"
     class="fixed inset-0 z-modal"
     x-cloak>

    <!-- Backdrop -->
    <div class="absolute inset-0 bg-black bg-opacity-50"
         @click="close()"></div>

    <!-- Drawer -->
    <div class="absolute right-0 top-0 h-full w-full max-w-md bg-white shadow-xl"
         x-transition:enter="transform transition ease-out duration-300"
         x-transition:enter-start="translate-x-full"
         x-transition:enter-end="translate-x-0">

        <!-- Header -->
        <div class="flex items-center justify-between p-4 border-b">
            <h2 class="text-xl font-bold">Shopping Cart</h2>
            <button @click="close()" class="text-gray-500 hover:text-gray-700">
                <?= $heroicons->xMarkHtml('w-6 h-6') ?>
            </button>
        </div>

        <!-- Cart Items -->
        <div class="flex-1 overflow-y-auto p-4">
            <template x-if="cart && cart.items && cart.items.length">
                <div class="space-y-4">
                    <template x-for="item in cart.items" :key="item.item_id">
                        <?= $block->getChildHtml('cart.item.renderer') ?>
                    </template>
                </div>
            </template>

            <template x-if="!cart || !cart.items || cart.items.length === 0">
                <p class="text-center text-gray-500">Your cart is empty</p>
            </template>
        </div>

        <!-- Footer -->
        <div class="border-t p-4">
            <div class="flex items-center justify-between mb-4">
                <span class="text-lg font-semibold">Subtotal:</span>
                <span class="text-xl font-bold" x-text="cart.subtotal"></span>
            </div>
            <a href="<?= $escaper->escapeUrl($block->getUrl('checkout/cart')) ?>"
               class="btn-primary w-full text-center block mb-2">
                View Cart
            </a>
            <a href="<?= $escaper->escapeUrl($block->getUrl('checkout')) ?>"
               class="btn-secondary w-full text-center block">
                Checkout
            </a>
        </div>
    </div>
</div>
```

#### 4. Header with Navigation

**File:** `Magento_Theme/templates/html/header.phtml`

```php
<header class="page-header sticky top-0 z-header bg-white shadow-md" x-data="initHeader()">
    <!-- Top Bar -->
    <div class="bg-gray-100 py-2">
        <div class="container mx-auto px-4">
            <div class="flex items-center justify-between text-sm">
                <div>Welcome to our store!</div>
                <div>
                    <?= $block->getChildHtml('customer.links') ?>
                </div>
            </div>
        </div>
    </div>

    <!-- Main Header -->
    <div class="container mx-auto px-4 py-4">
        <div class="flex items-center justify-between gap-4">
            <!-- Logo -->
            <div class="logo-container">
                <?= $block->getChildHtml('logo') ?>
            </div>

            <!-- Search -->
            <div class="flex-1 max-w-2xl">
                <?= $block->getChildHtml('header-search') ?>
            </div>

            <!-- Icons -->
            <div class="flex items-center gap-4">
                <?= $block->getChildHtml('header-compare') ?>
                <?= $block->getChildHtml('header-wishlist') ?>
                <button @click="openCart()" class="relative">
                    <?= $heroicons->shoppingCartHtml('w-6 h-6') ?>
                    <span x-show="cartQty > 0"
                          x-text="cartQty"
                          class="absolute -top-2 -right-2 bg-primary text-on-primary rounded-full w-5 h-5 flex items-center justify-center text-xs">
                    </span>
                </button>
            </div>
        </div>
    </div>

    <!-- Navigation -->
    <div class="bg-gray-50 border-t">
        <?= $block->getChildHtml('topmenu') ?>
    </div>

    <!-- Cart Drawer -->
    <?= $block->getChildHtml('cart-drawer') ?>
</header>
```

---

## Web Assets

### CSS Files

```
web/css/
├── styles.css              # Main compiled Tailwind CSS (minified in production)
└── print.css               # Print-specific styles
```

**styles.css** is generated by Tailwind CLI from `web/tailwind/tailwind-source.css`

### JavaScript Files

```
web/js/
├── theme.js                # Theme-specific JavaScript
└── (module-specific JS)    # Additional JS as needed
```

Most JavaScript is handled by:
1. **Alpine.js** (loaded via `default_hyva.xml`)
2. **Inline component scripts** in templates
3. **ViewModels** providing data

### Images

```
web/images/
└── (theme images)          # Logo, icons, etc.
```

### SVG Icons

Icons are provided by:
- **Heroicons** (via `magento2-theme-module`)
- **Lucide Icons** (via `magento2-theme-module`)

Access via ViewModels:

```php
<?php
/** @var \Hyva\Theme\ViewModel\HeroiconsOutline $heroicons */
$heroicons = $block->getData('heroicons');
?>

<!-- Outline icon -->
<?= $heroicons->renderHtml('shopping-cart', 'w-6 h-6') ?>

<!-- Solid icon -->
<?= $heroiconsSolid->renderHtml('heart', 'w-6 h-6 text-red-500') ?>
```

---

## Theme Configuration

### theme.xml

```xml
<?xml version="1.0"?>
<theme xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/theme.xsd">
    <title>Hyvä Default</title>
    <media>
        <preview_image>media/preview.png</preview_image>
    </media>
</theme>
```

### registration.php

```php
<?php
use \Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(
    ComponentRegistrar::THEME,
    'frontend/Hyva/default',
    __DIR__
);
```

### etc/view.xml

Configures image sizes, gallery settings, and other theme parameters:

```xml
<?xml version="1.0"?>
<view xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/view.xsd">
    <media>
        <images module="Magento_Catalog">
            <!-- Category page list -->
            <image id="category_page_list" type="small_image">
                <width>240</width>
                <height>300</height>
            </image>

            <!-- Product page main image -->
            <image id="product_page_image_large" type="image">
                <width>700</width>
                <height>700</height>
            </image>

            <!-- Product page thumbnails -->
            <image id="product_page_image_small" type="thumbnail">
                <width>120</width>
                <height>120</height>
            </image>
        </images>
    </media>

    <vars module="Magento_Catalog">
        <var name="gallery">
            <var name="nav">thumbs</var>
            <var name="loop">false</var>
            <var name="keyboard">true</var>
            <var name="arrows">true</var>
            <var name="allowfullscreen">true</var>
        </var>
    </vars>
</view>
```

---

## Development Workflow

### Setting Up for Development

1. **Install the theme** (if not using child theme):

```bash
composer require hyva-themes/magento2-default-theme
```

2. **Navigate to Tailwind directory**:

```bash
cd vendor/hyva-themes/magento2-default-theme/web/tailwind
# OR for your child theme:
cd app/design/frontend/YourVendor/YourTheme/web/tailwind
```

3. **Install Node dependencies**:

```bash
npm install
```

4. **Start watch mode**:

```bash
npm run watch
```

### Tailwind Watch Commands

```bash
# Basic watch (recommended)
npm run watch

# Watch with BrowserSync (live reload)
npm run browser-sync

# Production build (minified)
npm run build

# Generate tokens and sources
npm run generate
```

### Development Mode Best Practices

1. **Enable developer mode**:

```bash
php bin/magento deploy:mode:set developer
```

2. **Disable caches** (optional):

```bash
php bin/magento cache:disable layout full_page block_html
```

3. **Watch Tailwind changes**:

```bash
cd vendor/hyva-themes/magento2-default-theme/web/tailwind
npm run watch
```

4. **After template changes**:

```bash
php bin/magento cache:clean layout full_page
```

5. **After layout XML changes**:

```bash
php bin/magento cache:clean layout
```

### File Watching Best Practice

**Watch these locations:**

```
vendor/hyva-themes/magento2-default-theme/
├── Magento_*/templates/*.phtml    # Template changes (hot reload in dev mode)
├── Magento_*/layout/*.xml         # Layout changes (cache:clean layout)
└── web/tailwind/**/*.css          # CSS changes (auto-compiled by npm watch)
```

---

## Customization Patterns

### Pattern 1: Child Theme Inheritance

**Create a child theme** that extends Hyvä default:

**Your theme structure:**

```
app/design/frontend/YourVendor/YourTheme/
├── registration.php
├── theme.xml
├── composer.json
└── Magento_Catalog/
    └── templates/
        └── product/
            └── view.phtml            # Override single template
```

**theme.xml:**

```xml
<?xml version="1.0"?>
<theme xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/theme.xsd">
    <title>Your Custom Hyvä Theme</title>
    <parent>Hyva/default</parent>
    <media>
        <preview_image>media/preview.png</preview_image>
    </media>
</theme>
```

### Pattern 2: Template Override

**Override only what you need:**

```
YourTheme/Magento_Catalog/templates/product/list/item.phtml
```

This inherits all other templates from Hyvä default.

### Pattern 3: Layout Customization

**Extend layouts without copying:**

```xml
<?xml version="1.0"?>
<!-- YourTheme/Magento_Catalog/layout/catalog_product_view.xml -->
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <!-- Add custom block -->
        <referenceContainer name="product.info.main">
            <block name="custom.product.badge"
                   template="YourVendor_YourModule::product/badge.phtml"
                   after="product.info.price"/>
        </referenceContainer>

        <!-- Remove default block -->
        <referenceBlock name="product.info.review" remove="true"/>

        <!-- Move block -->
        <move element="product.info.stock.sku"
              destination="product.info.main"
              before="product.info.price"/>
    </body>
</page>
```

### Pattern 4: Tailwind Customization

**Create custom Tailwind config in your child theme:**

```
YourTheme/web/tailwind/
├── package.json                      # Copy and customize
├── hyva.config.json                  # Custom design tokens
├── tailwind-source.css               # Import default + custom
└── theme/
    └── custom.css                    # Your custom styles
```

**hyva.config.json (custom colors):**

```json
{
  "tokens": {
    "values": {
      "color": {
        "primary": {
          "DEFAULT": "#007bff",
          "darker": "#0056b3"
        }
      }
    }
  }
}
```

**tailwind-source.css:**

```css
/* Import Hyvä default styles */
@import '../../../../../../vendor/hyva-themes/magento2-default-theme/web/tailwind/tailwind-source.css';

/* Your custom styles */
@import './theme/custom.css';
```

### Pattern 5: Alpine.js Component Extension

**Extend existing Alpine components:**

```php
<!-- YourTheme/Magento_Catalog/templates/product/view/addtocart.phtml -->

<div x-data="Object.assign(initAddToCart(), {
    customMethod() {
        console.log('Custom logic');
    },
    addToCart() {
        // Call custom method first
        this.customMethod();

        // Call parent method
        this.$dispatch('add-to-cart', {
            productId: <?= (int)$product->getId() ?>
        });
    }
})">
    <button @click="addToCart()" class="btn-primary">
        Add to Cart
    </button>
</div>
```

---

## PageBuilder Integration

### PageBuilder Module Override

```
Magento_PageBuilder/
├── layout/
│   └── (PageBuilder layout overrides)
├── page_layout/
│   └── (PageBuilder page layouts)
├── templates/
│   ├── content-type/
│   │   ├── banner/
│   │   ├── slider/
│   │   ├── products/
│   │   ├── block/
│   │   └── ...
│   └── ...
└── web/
    └── css/
        └── (PageBuilder styles)
```

### Key PageBuilder Content Types

Hyvä default theme provides templates for:

- **Banner** - Hero banners with images and text
- **Slider** - Image/content sliders
- **Products** - Product grids/sliders
- **Block** - CMS block embedding
- **Row/Column** - Grid layout
- **Text** - Rich text content
- **HTML** - Custom HTML
- **Video** - Video embedding
- **Map** - Google Maps
- **Buttons** - Call-to-action buttons

### PageBuilder Template Example

**File:** `Magento_PageBuilder/templates/content-type/banner/default.phtml`

```php
<?php
/** @var \Magento\PageBuilder\Block\Template $block */
$bannerData = $block->getData('banner_data');
?>

<div class="pagebuilder-banner relative overflow-hidden"
     style="min-height: <?= $escaper->escapeHtmlAttr($bannerData['min_height'] ?? '300px') ?>">

    <!-- Background Image -->
    <?php if ($bannerData['background_image']): ?>
    <img src="<?= $escaper->escapeUrl($bannerData['background_image']) ?>"
         class="absolute inset-0 w-full h-full object-cover"
         alt="<?= $escaper->escapeHtmlAttr($bannerData['alt_text'] ?? '') ?>">
    <?php endif; ?>

    <!-- Overlay -->
    <div class="relative z-10 h-full flex items-<?= $escaper->escapeHtmlAttr($bannerData['content_align'] ?? 'center') ?>
                justify-<?= $escaper->escapeHtmlAttr($bannerData['content_justify'] ?? 'center') ?> p-8">
        <div class="text-center max-w-2xl">
            <?php if ($bannerData['heading']): ?>
            <h2 class="text-4xl font-bold mb-4 text-white">
                <?= $escaper->escapeHtml($bannerData['heading']) ?>
            </h2>
            <?php endif; ?>

            <?php if ($bannerData['description']): ?>
            <p class="text-lg mb-6 text-white">
                <?= $block->filterOutput($bannerData['description']) ?>
            </p>
            <?php endif; ?>

            <?php if ($bannerData['button_text']): ?>
            <a href="<?= $escaper->escapeUrl($bannerData['button_link'] ?? '#') ?>"
               class="btn-primary inline-block">
                <?= $escaper->escapeHtml($bannerData['button_text']) ?>
            </a>
            <?php endif; ?>
        </div>
    </div>
</div>
```

---

## ReCaptcha Integration

Hyvä default theme includes integration for **8 ReCaptcha modules**:

### ReCaptcha Modules

```
Magento_ReCaptchaCheckout/         # Checkout forms
Magento_ReCaptchaContact/          # Contact form
Magento_ReCaptchaCustomer/         # Customer login/registration
Magento_ReCaptchaFrontendUi/       # Frontend UI components
Magento_ReCaptchaNewsletter/       # Newsletter subscription
Magento_ReCaptchaReview/           # Product reviews
Magento_ReCaptchaSendFriend/       # Email to friend
```

### ReCaptcha Template Example

**File:** `Magento_ReCaptchaCustomer/templates/recaptcha.phtml`

```php
<div class="g-recaptcha"
     data-sitekey="<?= $escaper->escapeHtmlAttr($block->getPublicKey()) ?>"
     data-size="<?= $escaper->escapeHtmlAttr($block->getSize()) ?>"
     data-theme="<?= $escaper->escapeHtmlAttr($block->getTheme()) ?>">
</div>

<script>
function initRecaptcha() {
    return {
        verified: false,
        verify(response) {
            this.verified = !!response;
        }
    }
}
</script>
```

---

## Third-Party Module Support

### PayPal Braintree

```
PayPal_Braintree/
├── layout/
│   └── vault_cards_listaction.xml
└── templates/
    └── (Braintree payment templates)
```

### Google Analytics & GTM

```
Magento_GoogleAnalytics/           # Google Analytics tracking
Magento_GoogleGtag/                # Google Global Site Tag
Magento_GoogleTagManager/          # Google Tag Manager
```

### Login as Customer

```
Magento_LoginAsCustomerAssistance/  # Customer assistance
Magento_LoginAsCustomerFrontendUi/  # Frontend UI
```

---

## Best Practices

### 1. Always Use Child Themes

**Never modify vendor files directly.** Always create a child theme:

```xml
<!-- theme.xml -->
<theme>
    <title>Your Theme</title>
    <parent>Hyva/default</parent>
</theme>
```

### 2. Override Only What You Need

Don't copy entire templates. Override only specific files:

```
YourTheme/
└── Magento_Catalog/
    └── templates/
        └── product/
            └── list/
                └── item.phtml       # Override only product list item
```

### 3. Use Layout XML for Customization

Prefer layout XML over template copying:

```xml
<!-- Add block without copying template -->
<referenceContainer name="product.info.main">
    <block name="custom.block" template="YourModule::custom.phtml"/>
</referenceContainer>

<!-- Remove block -->
<referenceBlock name="unwanted.block" remove="true"/>

<!-- Move block -->
<move element="product.info.price" destination="product.info.main" before="-"/>
```

### 4. Extend Alpine Components

Extend existing Alpine.js components instead of replacing:

```javascript
Object.assign(initProductPage(), {
    customMethod() {
        // Your custom logic
    }
})
```

### 5. Use Design Tokens

Customize colors via `hyva.config.json`:

```json
{
  "tokens": {
    "values": {
      "color": {
        "primary": {
          "DEFAULT": "#your-brand-color"
        }
      }
    }
  }
}
```

### 6. Leverage ViewModels

Always use ViewModels for business logic:

```php
/** @var \Hyva\Theme\ViewModel\CurrentProduct $productViewModel */
$productViewModel = $block->getViewModel();
$product = $productViewModel->get();
```

### 7. Follow Hyvä Naming Conventions

- **Layouts:** Use `hyva_` prefix for Hyvä-specific layouts
- **Templates:** Use `/hyva/` subdirectory for Hyvä-specific templates
- **CSS:** Use Tailwind utilities, avoid custom CSS when possible

### 8. Optimize Images

Use proper image sizes via `etc/view.xml`:

```xml
<image id="category_page_list" type="small_image">
    <width>240</width>
    <height>300</height>
</image>
```

### 9. Test Responsiveness

Always test on multiple devices:

```bash
# Use BrowserSync for live testing
cd web/tailwind
npm run browser-sync
```

### 10. Keep Updated

Regularly update Hyvä default theme:

```bash
composer update hyva-themes/magento2-default-theme
php bin/magento setup:upgrade
```

---

## Quick Reference

### File Locations

| Type | Location |
|------|----------|
| **Theme Root** | `vendor/hyva-themes/magento2-default-theme/` |
| **Templates** | `Magento_[Module]/templates/` |
| **Layouts** | `Magento_[Module]/layout/` |
| **Tailwind Config** | `web/tailwind/` |
| **Compiled CSS** | `web/css/styles.css` |
| **Theme Config** | `theme.xml`, `etc/view.xml` |

### Common Commands

```bash
# Tailwind watch
cd vendor/hyva-themes/magento2-default-theme/web/tailwind && npm run watch

# Tailwind build (production)
cd vendor/hyva-themes/magento2-default-theme/web/tailwind && npm run build

# Clear layout cache
php bin/magento cache:clean layout full_page

# Apply theme changes
php bin/magento setup:upgrade && php bin/magento cache:flush
```

### Module Statistics

- **Total Modules:** 55+
- **Total Templates:** 307 .phtml files
- **Total Layouts:** 165 .xml files
- **Total Lines (CHANGELOG):** 4,890 lines
- **Tailwind Components:** 10 component files
- **Tailwind Utilities:** 3 utility files

---

## Additional Resources

- **Official Docs:** https://docs.hyva.io
- **GitHub:** https://gitlab.hyva.io/hyva-themes/magento2-default-theme
- **Tailwind CSS:** https://tailwindcss.com/docs
- **Alpine.js:** https://alpinejs.dev
- **Magento DevDocs:** https://devdocs.magento.com

---

**Last Updated:** November 2025
**Hyvä Default Theme Version:** 3.0.0
**Tailwind CSS Version:** 4.1.15
**Document Version:** 1.0.0
