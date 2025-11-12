# Hyvä Themes - Complete Learning Plan (Beginner to Advanced)

**Project:** Magento 2.4.6-p8 with Hyvä Themes
**Location:** `/var/www/html/m246p8`
**Date Created:** 2025-11-12

---

## Overview

Hyvä Themes is a modern, performance-focused frontend theme for Magento 2 that replaces:
- **RequireJS** → Alpine.js (lightweight, reactive framework)
- **KnockoutJS** → Alpine.js
- **jQuery** → Vanilla JavaScript
- **LESS** → Tailwind CSS

**Key Benefits:**
- 50-60% faster page loads
- 95+ Lighthouse scores
- Simpler frontend development
- Better Core Web Vitals

---

## Installed Packages

Your installation includes:

1. **magento2-default-theme** - Base Hyvä theme with all templates
2. **magento2-theme-module** - Core functionality module (Alpine.js, utilities)
3. **magento2-theme-fallback** - Fallback mechanism for missing templates
4. **magento2-compat-module-fallback** - Compatibility with third-party modules
5. **magento2-base-layout-reset** - Resets Magento's default layout XML
6. **magento2-email-module** - Email template support
7. **magento2-graphql-tokens** - GraphQL authentication tokens
8. **magento2-graphql-view-model** - GraphQL integration for frontend
9. **magento2-mollie-theme-bundle** - Mollie payment integration
10. **magento2-order-cancellation-webapi** - Order cancellation API

---

## Learning Path

### **Phase 1: Foundation & Setup** (Understanding the Basics)

#### 1.1 Understanding Hyvä Architecture
**Objective:** Learn what makes Hyvä different from Luma/Blank themes

**Topics:**
- Theme structure and file organization
- Alpine.js vs KnockoutJS comparison
- Tailwind CSS vs LESS
- Module structure (Hyva_Theme)
- Template inheritance and fallback mechanism

**Hands-on:**
- Explore `vendor/hyva-themes/magento2-default-theme/` structure
- Compare with Luma theme structure
- Understand registration.php and theme.xml

**Files to Explore:**
```
vendor/hyva-themes/magento2-default-theme/
├── theme.xml                 # Theme metadata
├── registration.php          # Theme registration
├── composer.json            # Dependencies
├── etc/
│   └── view.xml            # Theme configuration
├── web/
│   ├── css/                # Tailwind source files
│   ├── js/                 # Custom JavaScript
│   └── tailwind/           # Tailwind config
├── Magento_Catalog/        # Module overrides
├── Magento_Checkout/
└── Magento_Customer/
```

---

#### 1.2 Enable and Activate Hyvä Theme
**Objective:** Get Hyvä theme running on your store

**Steps:**
1. Check if Hyva_Theme module is enabled
2. Configure Hyvä as active theme
3. Deploy static content
4. Test frontend

**Commands:**
```bash
# Check module status
php bin/magento module:status | grep Hyva

# Enable if needed
php bin/magento module:enable Hyva_Theme

# Run setup
php bin/magento setup:upgrade
php bin/magento setup:di:compile
php bin/magento setup:static-content:deploy -f
php bin/magento cache:flush

# Set Hyvä as theme (via Admin or CLI)
# Admin: Content > Design > Configuration
```

**Verification:**
- Check frontend appearance
- Inspect HTML source (should see Alpine.js, Tailwind classes)
- Verify no JS errors in console

---

#### 1.3 Tailwind CSS Fundamentals
**Objective:** Master Tailwind utility-first CSS

**Topics:**
- Utility classes (spacing, colors, typography)
- Responsive design (breakpoints: sm, md, lg, xl, 2xl)
- Flexbox and Grid utilities
- State variants (hover, focus, active)
- Tailwind configuration file
- Custom color palettes
- JIT (Just-In-Time) compiler

**Hands-on:**
- Explore `web/tailwind/tailwind.config.js`
- Modify spacing/colors
- Create custom utility classes
- Build responsive layouts

**Files to Study:**
```
vendor/hyva-themes/magento2-default-theme/web/tailwind/
├── tailwind.config.js       # Main config
├── tailwind-source.css      # Source styles
└── components/              # Custom components
```

---

#### 1.3a How Hyvä Layout XML Works
**Objective:** Deep dive into Hyvä's layout XML architecture and implementation patterns

**Topics:**
- Hyvä-specific layout handle structure
- The `default_hyva.xml` layout handle and its purpose
- Understanding `<update handle=""/>` directive
- Custom layout handles in Hyvä
- Layout XML hierarchy and inheritance
- ViewModels in layout arguments
- Container vs Block in Hyvä context
- Alpine.js integration points in layout XML

---

##### Understanding Hyvä Layout Architecture

**1. Layout Handle Hierarchy:**

Hyvä uses a layered approach to layout XML:

```
Standard Magento Layouts (vendor/magento/)
         ↓
Hyvä Base Layout (default_hyva.xml)
         ↓
Hyvä Theme Layouts (default.xml, specific pages)
         ↓
Custom Theme Layouts (your overrides)
```

**2. Key Hyvä Layout Handles:**

- **`default_hyva.xml`** - Core Hyvä configuration (Alpine.js, plugins, scripts)
  - Location: `vendor/hyva-themes/magento2-theme-module/src/view/frontend/layout/`
  - Purpose: Loads Alpine.js, plugins, Hyvä-specific JavaScript
  - Applied to: Every page in Hyvä theme

- **`default.xml`** - Theme-specific layout (header, footer, navigation)
  - Location: `vendor/hyva-themes/magento2-default-theme/Magento_Theme/layout/`
  - Purpose: Defines page structure, header, footer, menus
  - Applied to: Every page, builds on `default_hyva.xml`

- **Page-specific handles** - Individual page layouts (product, category, etc.)
  - Examples: `catalog_product_view.xml`, `catalog_category_view.xml`, `checkout_cart_index.xml`
  - Purpose: Customize specific page types
  - Applied to: Only their respective pages

**3. The `<update handle=""/>` Directive:**

This is how Hyvä includes other layout files:

```xml
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <update handle="default_hyva"/>  <!-- Include Hyvä core -->
    <body>
        <!-- Page content -->
    </body>
</page>
```

**What it does:**
- Merges another layout XML file into current layout
- Loads in specified order (top to bottom)
- Commonly used handles in Hyvä:
  - `default_hyva` - Loads Alpine.js and core functionality
  - `catalog_product_opengraph` - SEO metadata for products
  - `hyva_product_slider` - Product slider configuration
  - `catalog_list_item` - Product list item template

**Example from `catalog_product_view.xml`:**
```xml
<page layout="1column">
    <update handle="catalog_product_opengraph"/>  <!-- SEO metadata -->
    <update handle="hyva_product_slider" />       <!-- Slider config -->
    <body>
        <!-- Product page blocks -->
    </body>
</page>
```

---

##### Anatomy of default_hyva.xml

**File:** `vendor/hyva-themes/magento2-theme-module/src/view/frontend/layout/default_hyva.xml`

This file loads essential Hyvä components:

**1. Head Section (JavaScript variables):**
```xml
<referenceContainer name="head.additional">
    <!-- Hyvä JavaScript configuration -->
    <block name="head.js" template="Hyva_Theme::page/js/variables.phtml"/>

    <!-- Core Hyvä scripts -->
    <block name="head.hyva-scripts" template="Hyva_Theme::page/js/hyva.phtml"/>

    <!-- Preloading optimization -->
    <block name="speculationrules" template="Hyva_Theme::page/js/speculationrules.phtml"/>
</referenceContainer>
```

**2. Alpine.js Loading (before body end):**
```xml
<referenceContainer name="before.body.end">
    <!-- Alpine.js core -->
    <block name="script-alpine-js" template="Hyva_Theme::page/js/alpinejs.phtml">

        <!-- Alpine.js plugins -->
        <block name="alpine-plugin-snap-slider" template="...snap-slider.phtml"/>
        <block name="alpine-plugin-htmldialog" template="...htmldialog.phtml"/>
        <block name="alpine-plugin-intersect" template="...intersect.phtml"/>
        <block name="alpine-plugin-ignore" template="...ignore.phtml"/>
        <block name="alpine-plugin-defer" template="...defer.phtml"/>
    </block>

    <!-- Hyvä utilities -->
    <block name="script-cookies" template="Hyva_Theme::page/js/cookies.phtml"/>
    <block name="script-private-content" template="...private-content.phtml"/>
</referenceContainer>
```

**Why this matters:**
- Alpine.js loads before `</body>` (non-blocking, optimal performance)
- Plugins are loaded before Alpine initializes
- Cookie handling and private content are Hyvä-specific utilities

---

##### ViewModels in Layout XML

Hyvä heavily uses ViewModels to separate business logic from templates.

**Syntax:**
```xml
<block name="my.block" template="Vendor_Module::template.phtml">
    <arguments>
        <argument name="view_model" xsi:type="object">
            Hyva\Theme\ViewModel\StoreConfig
        </argument>
    </arguments>
</block>
```

**In template (template.phtml):**
```php
<?php
/** @var \Hyva\Theme\ViewModel\StoreConfig $viewModel */
$viewModel = $block->getViewModel();
$storeName = $viewModel->getStoreConfig('general/store_information/name');
?>
```

**Common Hyvä ViewModels:**

| ViewModel | Purpose | Usage |
|-----------|---------|-------|
| `Hyva\Theme\ViewModel\StoreConfig` | Access store configuration | Get config values |
| `Hyva\Theme\ViewModel\CurrentProduct` | Current product data | Product pages |
| `Hyva\Theme\ViewModel\Modal` | Modal/dialog functionality | Popups, dialogs |
| `Hyva\Theme\ViewModel\HeroiconsSolid` | Heroicons (solid) | Icon rendering |
| `Hyva\Theme\ViewModel\HeroiconsOutline` | Heroicons (outline) | Icon rendering |
| `Hyva\Theme\ViewModel\SvgIcons` | Generic SVG icons | Custom icons |
| `Hyva\Theme\ViewModel\Navigation` | Menu navigation | Header menus |
| `Hyva\Theme\ViewModel\Cart` | Cart data and actions | Cart/mini-cart |
| `Hyva\Theme\ViewModel\Customer` | Customer session data | Customer info |
| `Hyva\Theme\ViewModel\ProductPrice` | Price rendering | Product prices |
| `Hyva\Theme\ViewModel\ProductList` | Product listing logic | Category pages |

**Example - Logo with ViewModel:**
```xml
<block class="Magento\Theme\Block\Html\Header\Logo" name="logo">
    <arguments>
        <argument name="logoPathResolver" xsi:type="object">
            Hyva\Theme\ViewModel\Logo\LogoPathResolver
        </argument>
        <argument name="logo_width" xsi:type="number">189</argument>
        <argument name="logo_height" xsi:type="number">64</argument>
    </arguments>
</block>
```

---

##### Custom Layout Handles in Hyvä

Hyvä creates custom handles for reusability:

**1. `hyva_product_slider` Handle:**

**File:** `Magento_Catalog/layout/hyva_product_slider.xml`
```xml
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <update handle="catalog_list_item"/>  <!-- Includes product item template -->
</page>
```

This handle can be included anywhere product sliders are needed:
- Related products
- Upsell products
- Cross-sell products
- Recently viewed

**Usage in `catalog_product_view.xml`:**
```xml
<block name="upsell" template="Magento_Catalog::product/slider/product-slider.phtml">
    <arguments>
        <argument name="type" xsi:type="string">upsell</argument>
        <argument name="title" xsi:type="string">We found other products you might like!</argument>
    </arguments>
</block>
```

**2. `catalog_list_item` Handle:**

Defines structure for a single product item in listings.
Used across:
- Category pages
- Search results
- Sliders
- Widgets

---

##### Layout XML Patterns in Hyvä

**1. Passing Arguments to Templates:**

```xml
<block name="header-content" template="Magento_Theme::html/header.phtml">
    <arguments>
        <argument name="show_compare" xsi:type="boolean">true</argument>
        <argument name="show_wishlist" xsi:type="boolean">true</argument>
        <argument name="custom_class" xsi:type="string">bg-blue-500</argument>
        <argument name="items" xsi:type="array">
            <item name="item1" xsi:type="string">value1</item>
            <item name="item2" xsi:type="string">value2</item>
        </argument>
    </arguments>
</block>
```

**In template:**
```php
<?php
$showCompare = $block->getData('show_compare'); // true
$customClass = $block->getData('custom_class'); // "bg-blue-500"
$items = $block->getData('items'); // array
?>
```

**2. Conditional Rendering with `ifconfig`:**

```xml
<block name="cart-drawer"
       template="Magento_Theme::html/cart/cart-drawer.phtml"
       ifconfig="checkout/sidebar/display">
    <!-- Only rendered if config is enabled -->
</block>
```

**3. Container vs Block:**

```xml
<!-- Container: No template, just structure -->
<container name="product.info.additional.actions">
    <block name="product.info.addtowishlist" template="..."/>
    <block name="product.info.addtocompare" template="..."/>
</container>

<!-- Block: Has template and logic -->
<block name="product.info" template="Magento_Catalog::product/view.phtml">
    <!-- Block content -->
</block>
```

**4. Moving Elements:**

```xml
<!-- Move breadcrumbs to different location -->
<move element="alert.urls" destination="product.info" as="alert.urls"/>
```

**5. Referencing and Modifying Existing Blocks:**

```xml
<!-- Add CSS classes to existing page title -->
<referenceBlock name="page.main.title">
    <arguments>
        <argument name="css_classes" xsi:type="string">text-3xl font-semibold mb-8</argument>
    </arguments>
</referenceBlock>

<!-- Add items to existing container -->
<referenceContainer name="footer">
    <block name="my-custom-footer-block" template="..."/>
</referenceContainer>
```

---

##### How to Debug Hyvä Layout XML

**1. Enable Layout Debugging:**
```bash
php bin/magento dev:template-hints:enable
php bin/magento dev:template-hints:status
```

**2. Check Which Layouts Are Loaded:**

Add to any template:
```php
<?php
// Show all loaded layout handles
$layout = $block->getLayout();
$handles = $layout->getUpdate()->getHandles();
echo '<pre>'; print_r($handles); echo '</pre>';
?>
```

**3. View Merged Layout XML:**

```bash
# Generate merged layout for specific page
php bin/magento dev:xml:convert \
  app/design/frontend/Vendor/Theme/Magento_Catalog/layout/catalog_product_view.xml \
  -o merged-layout.xml
```

**4. Common Containers in Hyvä:**

| Container Name | Location | Purpose |
|----------------|----------|---------|
| `header.container` | Top of page | Header elements |
| `after.body.start` | After `<body>` | Form keys, notices |
| `top.container` | Below header | Breadcrumbs |
| `main.content` | Main area | Page content |
| `content` | Inside main | Core content |
| `columns.top` | Above content | Page title |
| `footer` | Bottom | Footer blocks |
| `before.body.end` | Before `</body>` | Scripts, Alpine.js |
| `head.additional` | In `<head>` | Custom head content |

---

##### Practical Examples

**Example 1: Add Custom Block to Product Page**

Create: `app/design/frontend/YourVendor/your-theme/Magento_Catalog/layout/catalog_product_view.xml`

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceContainer name="product.info.main">
            <block name="product.custom.info"
                   template="Magento_Catalog::product/view/custom-info.phtml"
                   after="product.info.price">
                <arguments>
                    <argument name="view_model" xsi:type="object">
                        Hyva\Theme\ViewModel\StoreConfig
                    </argument>
                </arguments>
            </block>
        </referenceContainer>
    </body>
</page>
```

**Example 2: Override Header with Custom Arguments**

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceBlock name="header-content">
            <arguments>
                <argument name="show_compare" xsi:type="boolean">false</argument>
                <argument name="custom_header_class" xsi:type="string">bg-gradient-to-r from-purple-500</argument>
            </arguments>
        </referenceBlock>
    </body>
</page>
```

**Example 3: Add Alpine.js Plugin via Layout**

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceBlock name="script-alpine-js">
            <block name="alpine-plugin-custom"
                   template="Vendor_Module::page/js/custom-alpine-plugin.phtml"
                   before="alpine-plugin-intersect"/>
        </referenceBlock>
    </body>
</page>
```

---

##### Key Takeaways

1. **`default_hyva.xml`** is the foundation - it loads Alpine.js and core functionality
2. **`<update handle=""/>`** merges layout files for reusability
3. **ViewModels** separate business logic from templates (best practice in Hyvä)
4. **Arguments** pass data from layout XML to templates
5. **Containers** provide structure; **Blocks** render templates
6. **`referenceBlock`** and **`referenceContainer`** modify existing elements
7. Hyvä keeps JavaScript loading non-blocking by placing scripts before `</body>`

---

**Hands-on Exercise:**

1. Examine `vendor/hyva-themes/magento2-default-theme/Magento_Theme/layout/default.xml`
2. Trace how `default_hyva` handle is included
3. Find where Alpine.js plugins are loaded in `default_hyva.xml`
4. Create a custom layout override in your theme
5. Add a ViewModel to a custom block
6. Use template hints to see which blocks render on a page

---

#### 1.4 Alpine.js Fundamentals
**Objective:** Learn Alpine.js reactive programming

**Topics:**
- Data binding with `x-data`
- Directives: `x-show`, `x-if`, `x-for`, `x-bind`, `x-on`
- Event handling
- Component state management
- Magic properties: `$el`, `$refs`, `$watch`, `$dispatch`
- Alpine.js stores (shared state)
- Component lifecycle

**Hands-on:**
- Create simple interactive components
- Build dropdown menu
- Create cart mini component
- Handle form validation

**Example Patterns:**
```html
<!-- Simple toggle -->
<div x-data="{ open: false }">
    <button @click="open = !open">Toggle</button>
    <div x-show="open">Content</div>
</div>

<!-- Iterating -->
<ul x-data="{ items: ['a', 'b', 'c'] }">
    <template x-for="item in items">
        <li x-text="item"></li>
    </template>
</ul>
```

---

#### 1.5 Core Hyvä Concepts (Essential Knowledge)
**Objective:** Master Hyvä's core architecture, ViewModels, icons, and built-in functionality

This section covers essential Hyvä concepts that you'll use throughout development.

---

##### 1.5.1 Hyvä Directory Structure

**Understanding the Hyvä Package Structure:**

Hyvä consists of multiple packages with specific purposes:

**1. magento2-theme-module** (Core Functionality)
```
vendor/hyva-themes/magento2-theme-module/src/
├── Block/                          # Block classes
│   ├── ViewModelCacheTagsBlock.php
│   └── ...
├── Console/                        # CLI commands
├── Framework/                      # Framework extensions
├── Model/                          # Business logic
├── Observer/                       # Event observers
├── Plugin/                         # Plugins/Interceptors
├── Service/                        # Service classes
├── ViewModel/                      # 53 Core ViewModels ⭐
│   ├── BlockCache.php
│   ├── Customer.php
│   ├── CurrentProduct.php
│   ├── HeroiconsOutline.php
│   ├── HeroiconsSolid.php
│   ├── LucideIcons.php
│   ├── Modal.php
│   ├── Navigation.php
│   └── ... (47 more)
├── etc/                            # Configuration
│   ├── module.xml
│   ├── di.xml
│   └── ...
└── view/frontend/
    ├── layout/
    │   └── default_hyva.xml        # Core Hyvä layout ⭐
    └── templates/
        └── Hyva_Theme/
            └── page/
                └── js/             # Alpine.js plugins ⭐
```

**2. magento2-default-theme** (Default Theme)
```
vendor/hyva-themes/magento2-default-theme/
├── theme.xml                       # Theme metadata
├── registration.php                # Theme registration
├── composer.json                   # Dependencies
├── etc/
│   └── view.xml                    # Image sizes, breakpoints
├── web/
│   ├── css/
│   ├── js/
│   ├── svg/                        # SVG icons
│   └── tailwind/                   # Tailwind configuration ⭐
│       ├── tailwind.config.js
│       ├── tailwind-source.css
│       ├── package.json
│       ├── base/                   # Base Tailwind styles
│       ├── components/             # Component styles
│       ├── theme/                  # Theme customizations
│       └── utilities/              # Utility classes
├── Hyva_Theme/                     # Hyvä Theme module overrides
│   ├── templates/
│   │   ├── elements/
│   │   └── ui/
│   └── web/
│       └── svg/                    # Heroicons SVGs
├── Magento_Catalog/                # Catalog module overrides
│   ├── layout/
│   │   ├── catalog_product_view.xml
│   │   ├── catalog_category_view.xml
│   │   └── hyva_product_slider.xml  # Custom handle
│   └── templates/
│       ├── product/
│       ├── category/
│       └── navigation/
├── Magento_Checkout/               # Checkout overrides
├── Magento_Customer/               # Customer overrides
├── Magento_Theme/                  # Theme overrides
│   ├── layout/
│   │   └── default.xml             # Main layout ⭐
│   └── templates/
│       └── html/
│           ├── header.phtml
│           ├── footer.phtml
│           └── ...
└── ... (50+ module overrides)
```

**3. Your Custom Theme Structure**
```
app/design/frontend/YourVendor/your-theme/
├── registration.php
├── theme.xml
├── composer.json
├── etc/
│   └── view.xml
├── web/
│   └── tailwind/
│       └── tailwind.config.js      # Extends Hyvä config
├── Magento_Catalog/                # Override Hyvä templates
│   ├── layout/
│   │   └── catalog_product_view.xml
│   └── templates/
│       └── product/
│           └── list/
│               └── item.phtml      # Your customization
└── ... (module overrides as needed)
```

**Key Insights:**
- `magento2-theme-module` = Functionality (ViewModels, Alpine.js, utilities)
- `magento2-default-theme` = Templates & Layout (actual theme files)
- Your custom theme = Inheritance & overrides (only what you change)

---

##### 1.5.2 Hyvä Template & Layout Naming Convention

**Hyvä-Specific Naming Pattern:**

Hyvä uses a specific convention to separate Hyvä-specific files from standard Magento files:

**Layout XML Naming:**
```
Standard Magento:     catalog_product_view.xml
Hyvä-Specific:        hyva_product_slider.xml       ✅
                      hyva_catalog_product_view.xml ✅
```

**Template Directory Naming:**
```
app/design/frontend/Vendor/Theme/
└── Magento_Catalog/
    └── templates/
        ├── product/
        │   └── view.phtml          # Standard location
        └── hyva/                    # Hyvä-specific directory ✅
            └── product/
                └── view.phtml       # Hyvä version
```

**Why This Matters:**
- Allows both Luma and Hyvä templates to coexist
- Fallback mechanism can route to correct template
- Clear separation of concerns
- Easy to identify Hyvä-specific customizations

**Example: Product List Item Template**

**Standard Location:**
```
Magento_Catalog/templates/product/list/item.phtml
```

**Hyvä-Specific Location (Optional):**
```
Magento_Catalog/templates/hyva/product/list/item.phtml
```

**Template Fallback Order:**
1. Your custom theme: `app/design/frontend/Vendor/Theme/Magento_Catalog/templates/product/list/item.phtml`
2. Hyvä default theme: `vendor/hyva-themes/magento2-default-theme/Magento_Catalog/templates/product/list/item.phtml`
3. Magento default: `vendor/magento/module-catalog/view/frontend/templates/product/list/item.phtml`

---

##### 1.5.3 Hyvä Core ViewModels (53 ViewModels)

**What are ViewModels in Hyvä?**

ViewModels separate business logic from presentation layer. Instead of putting logic in templates, you call ViewModel methods.

**Benefits:**
- ✅ Reusable business logic
- ✅ Testable code
- ✅ Clean templates
- ✅ Type hinting and IDE support
- ✅ Dependency injection

**Location:**
```
vendor/hyva-themes/magento2-theme-module/src/ViewModel/
```

**Complete List of Core Hyvä ViewModels:**

| ViewModel | Purpose | Common Use Cases |
|-----------|---------|------------------|
| **Product & Catalog** |||
| `CurrentProduct` | Get current product data | Product pages |
| `CurrentCategory` | Get current category | Category pages |
| `ProductPrice` | Price formatting | Product prices |
| `ProductList` | Product listing logic | Category pages, search |
| `ProductListItem` | Single product item | List items |
| `ProductPage` | Product page utilities | Product view |
| `ProductCompare` | Product comparison | Compare functionality |
| `Slider` | Product sliders | Related, upsell products |
| `SwatchRenderer` | Configurable swatches | Product options |
| `CustomOption` | Custom product options | Configurable products |
| **Cart & Checkout** |||
| `Cart` | Cart data and operations | Cart, mini-cart |
| `CartItem` | Single cart item | Cart items |
| **Customer** |||
| `Customer` | Customer session data | Customer info |
| `CustomerRegistration` | Registration logic | Sign-up forms |
| `CustomerSectionData` | Private content | User-specific data |
| **Navigation & UI** |||
| `Navigation` | Menu navigation | Header menus |
| `NavigationAsJson` | JSON navigation data | Mobile menus |
| `Breadcrumbs` | Breadcrumb trail | Navigation |
| `Modal` | Modal/dialog functionality | Popups, dialogs ⭐ |
| `Notification` | Notification messages | Alerts, toasts |
| **Icons** |||
| `HeroiconsOutline` | Heroicons outline style | Icons (outline) ⭐ |
| `HeroiconsSolid` | Heroicons solid style | Icons (solid) ⭐ |
| `LucideIcons` | Lucide icon library | Alternative icons ⭐ |
| `SvgIcons` | Generic SVG icons | Custom SVGs |
| **Configuration** |||
| `StoreConfig` | Store configuration | Config values |
| `StoreInfo` | Store information | Store details |
| `Currency` | Currency formatting | Prices, totals |
| `Locale` | Locale/translation | i18n |
| **Images & Media** |||
| `Image` | Image helper | Image URLs |
| `Media` | Media gallery | Product galleries |
| `ProductImage` | Product images | Product photos |
| **Forms** |||
| `FormKey` | Form security | Forms |
| `ReCaptcha` | ReCaptcha integration | Form protection |
| **Utilities** |||
| `BlockCache` | Block caching | Cache management |
| `Cookie` | Cookie management | Cookies |
| `Date` | Date formatting | Dates, times |
| `Directory` | Directory info | Countries, regions |
| `DeployMode` | Magento mode | Dev/prod mode |
| `GoogleMapsApi` | Google Maps | Store locator |
| `HyvaCsp` | CSP policies | Security |
| `HyvaMetadata` | Hyvä info | Version, config |
| `MagentoMetadata` | Magento info | Version info |
| `PageBuilder` | Page Builder | CMS pages |
| `PageConfig` | Page configuration | Meta, title |
| `Footer` | Footer data | Footer |
| `EmailToFriend` | Email product | Share functionality |
| **WordPress specific data, code** |||
| `Escaper/HtmlPurifier` | HTML sanitization | XSS protection |
| `Escaper/HtmlTagFilter` | HTML filtering | Content filtering |

**How to Use ViewModels:**

**Step 1: Add ViewModel to Layout XML**
```xml
<block name="my.block" template="Vendor_Module::template.phtml">
    <arguments>
        <argument name="view_model" xsi:type="object">
            Hyva\Theme\ViewModel\CurrentProduct
        </argument>
    </arguments>
</block>
```

**Step 2: Use in Template**
```php
<?php
/** @var \Hyva\Theme\ViewModel\CurrentProduct $productViewModel */
$productViewModel = $block->getViewModel();
$product = $productViewModel->get();
?>

<h1><?= $escaper->escapeHtml($product->getName()) ?></h1>
<p><?= $escaper->escapeHtml($product->getSku()) ?></p>
```

**Multiple ViewModels in One Block:**
```xml
<block name="my.block" template="Vendor_Module::template.phtml">
    <arguments>
        <argument name="product_view_model" xsi:type="object">
            Hyva\Theme\ViewModel\CurrentProduct
        </argument>
        <argument name="icons_view_model" xsi:type="object">
            Hyva\Theme\ViewModel\HeroiconsOutline
        </argument>
    </arguments>
</block>
```

```php
<?php
$productViewModel = $block->getData('product_view_model');
$iconsViewModel = $block->getData('icons_view_model');
?>
```

---

##### 1.5.4 Icon System (Heroicons & Lucide Icons)

**Hyvä includes two icon libraries:**

**1. Heroicons** (by Tailwind Labs)
- Over 200 icons
- Two styles: Outline and Solid
- Optimized SVGs
- ViewModels: `HeroiconsOutline`, `HeroiconsSolid`

**2. Lucide Icons**
- Over 1000 icons
- Modern, consistent design
- Single stroke style
- ViewModel: `LucideIcons`

---

**Using Heroicons:**

**Add ViewModel to Layout:**
```xml
<block name="my.block" template="Vendor_Module::template.phtml">
    <arguments>
        <argument name="heroicons" xsi:type="object">
            Hyva\Theme\ViewModel\HeroiconsOutline
        </argument>
        <argument name="heroiconsSolid" xsi:type="object">
            Hyva\Theme\ViewModel\HeroiconsSolid
        </argument>
    </arguments>
</block>
```

**In Template:**
```php
<?php
/** @var \Hyva\Theme\ViewModel\HeroiconsOutline $heroicons */
/** @var \Hyva\Theme\ViewModel\HeroiconsSolid $heroiconsSolid */
$heroicons = $block->getData('heroicons');
$heroiconsSolid = $block->getData('heroiconsSolid');
?>

<!-- Outline icon (default) -->
<?= $heroicons->renderHtml('shopping-cart', 'w-6 h-6 text-gray-600') ?>

<!-- Solid icon -->
<?= $heroiconsSolid->renderHtml('heart', 'w-6 h-6 text-red-500') ?>

<!-- With ARIA label -->
<?= $heroicons->renderHtml(
    'search',
    'w-5 h-5',
    null,
    ['aria-label' => __('Search')]
) ?>
```

**Available Heroicons (Popular):**
```
shopping-cart, heart, star, user, search, menu, x,
chevron-down, chevron-up, chevron-left, chevron-right,
check, plus, minus, trash, eye, eye-off, lock, unlock,
mail, phone, map-pin, home, cog, bell, arrow-right, arrow-left
```

---

**Using Lucide Icons:**

**Add ViewModel:**
```xml
<argument name="lucide" xsi:type="object">
    Hyva\Theme\ViewModel\LucideIcons
</argument>
```

**In Template:**
```php
<?php
/** @var \Hyva\Theme\ViewModel\LucideIcons $lucide */
$lucide = $block->getData('lucide');
?>

<?= $lucide->renderHtml('shopping-bag', 'w-6 h-6') ?>
<?= $lucide->renderHtml('heart', 'w-5 h-5 text-red-500') ?>
<?= $lucide->renderHtml('star', 'w-4 h-4 text-yellow-400') ?>
```

---

**Custom SVG Icons:**

**Add your own SVG:**
```php
<?php
/** @var \Hyva\Theme\ViewModel\SvgIcons $svgIcons */
$svgIcons = $block->getData('svg_icons');
?>

<!-- From file -->
<?= $svgIcons->renderHtml('Vendor_Module::icons/custom-icon.svg', 'w-6 h-6') ?>

<!-- Inline SVG -->
<svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M..."/>
</svg>
```

---

**Icon Helper Component (Reusable):**

Create a custom icon helper:

**Template:** `Magento_Theme/templates/elements/icon.phtml`
```php
<?php
/**
 * Icon Component
 * @var string $icon Icon name
 * @var string $type 'outline'|'solid'|'lucide'
 * @var string $class CSS classes
 */
$icon = $block->getData('icon');
$type = $block->getData('type') ?? 'outline';
$class = $block->getData('class') ?? 'w-6 h-6';

$heroicons = $block->getData('heroicons');
$heroiconsSolid = $block->getData('heroiconsSolid');
$lucide = $block->getData('lucide');

switch ($type) {
    case 'solid':
        echo $heroiconsSolid->renderHtml($icon, $class);
        break;
    case 'lucide':
        echo $lucide->renderHtml($icon, $class);
        break;
    default:
        echo $heroicons->renderHtml($icon, $class);
}
?>
```

**Usage:**
```php
<?= $block->getLayout()
    ->createBlock(\Magento\Framework\View\Element\Template::class)
    ->setTemplate('Magento_Theme::elements/icon.phtml')
    ->setData('icon', 'shopping-cart')
    ->setData('type', 'outline')
    ->setData('class', 'w-5 h-5 text-primary-500')
    ->toHtml()
?>
```

---

##### 1.5.5 Alpine.js Plugins in Hyvä

**Built-in Alpine Plugins:**

Hyvä loads these plugins automatically via `default_hyva.xml`:

**1. Snap Slider Plugin**
```javascript
// For carousels and sliders
Alpine.plugin(snapSlider)
```

**Usage:**
```html
<div x-data="hyva.initSlider({
    loop: true,
    autoplay: true,
    delay: 3000
})">
    <!-- Slider content -->
</div>
```

**2. HTMLDialog Plugin**
```javascript
// For native <dialog> element support
Alpine.plugin(htmlDialog)
```

**Usage:**
```html
<dialog x-ref="myDialog" x-data="{}" @click.outside="$el.close()">
    <p>Dialog content</p>
    <button @click="$refs.myDialog.close()">Close</button>
</dialog>

<button @click="$refs.myDialog.showModal()">Open Dialog</button>
```

**3. Intersect Plugin**
```javascript
// Trigger when element enters viewport
Alpine.plugin(intersect)
```

**Usage:**
```html
<div x-data="{ shown: false }"
     x-intersect="shown = true"
     x-show="shown"
     x-transition>
    Loads when scrolled into view
</div>
```

**4. Ignore Plugin**
```javascript
// Prevent Alpine from processing certain elements
Alpine.plugin(ignore)
```

**Usage:**
```html
<div x-ignore>
    <!-- Alpine.js will not process this -->
    <div x-data="...">This won't work</div>
</div>
```

**5. Defer Plugin**
```javascript
// Defer component initialization
Alpine.plugin(defer)
```

**Usage:**
```html
<!-- Defers loading until intersect -->
<div x-data="expensiveComponent()" x-defer="intersect">
    <!-- Component loads when visible -->
</div>

<!-- Defers loading by time -->
<div x-data="component()" x-defer="2000">
    <!-- Loads after 2 seconds -->
</div>
```

---

**Plugin Loading Order:**

From `default_hyva.xml`:
```xml
<block name="script-alpine-js" template="Hyva_Theme::page/js/alpinejs.phtml">
    <!-- Plugins load BEFORE Alpine.start() -->
    <block name="alpine-plugin-snap-slider" template="...snap-slider.phtml"/>
    <block name="alpine-plugin-htmldialog" template="...htmldialog.phtml"/>
    <block name="alpine-plugin-intersect" template="...intersect.phtml"/>
    <block name="alpine-plugin-ignore" template="...ignore.phtml"/>
    <block name="alpine-plugin-defer" template="...defer.phtml"/>
</block>
```

**Order matters:**
1. Plugins registered
2. Alpine.js starts
3. Components initialize

---

**Creating Custom Alpine Plugin:**

**File:** `app/design/frontend/Vendor/Theme/Magento_Theme/templates/page/js/custom-plugin.phtml`
```javascript
<script>
(function() {
    'use strict';

    function customPlugin(Alpine) {
        // Add magic property
        Alpine.magic('customHelper', () => {
            return function(value) {
                return 'Custom: ' + value;
            }
        });

        // Add directive
        Alpine.directive('custom-directive', (el, { expression }, { evaluate }) => {
            const value = evaluate(expression);
            el.textContent = 'Directive value: ' + value;
        });
    }

    // Register plugin
    document.addEventListener('alpine:init', () => {
        Alpine.plugin(customPlugin);
    });
})();
</script>
```

**Load in Layout:**
```xml
<referenceBlock name="script-alpine-js">
    <block name="alpine-plugin-custom"
           template="Magento_Theme::page/js/custom-plugin.phtml"
           before="alpine-plugin-intersect"/>
</referenceBlock>
```

---

##### 1.5.6 Modal System

**Hyvä Modal ViewModel:**

The `Modal` ViewModel provides utilities for creating accessible modals.

**Add to Layout:**
```xml
<block name="my.block" template="Vendor_Module::modal-example.phtml">
    <arguments>
        <argument name="modal" xsi:type="object">
            Hyva\Theme\ViewModel\Modal
        </argument>
    </arguments>
</block>
```

---

**Basic Modal Example:**

```phtml
<?php
/** @var \Hyva\Theme\ViewModel\Modal $modal */
$modal = $block->getData('modal');
?>

<div x-data="{ open: false }">
    <!-- Trigger Button -->
    <button @click="open = true"
            class="btn-primary">
        Open Modal
    </button>

    <!-- Modal -->
    <template x-if="open">
        <div class="fixed inset-0 z-modal flex items-center justify-center"
             @keydown.escape.window="open = false">

            <!-- Backdrop -->
            <div class="absolute inset-0 bg-black bg-opacity-50"
                 @click="open = false"></div>

            <!-- Modal Content -->
            <div class="relative bg-white rounded-lg shadow-2xl max-w-md w-full mx-4 p-6"
                 @click.stop
                 x-transition>

                <!-- Close Button -->
                <button @click="open = false"
                        class="absolute top-4 right-4 text-gray-400 hover:text-gray-600"
                        aria-label="<?= $escaper->escapeHtmlAttr(__('Close')) ?>">
                    <?= $heroicons->renderHtml('x', 'w-6 h-6') ?>
                </button>

                <!-- Modal Header -->
                <h2 class="text-2xl font-bold mb-4">
                    Modal Title
                </h2>

                <!-- Modal Body -->
                <p class="text-gray-600 mb-6">
                    Modal content goes here.
                </p>

                <!-- Modal Footer -->
                <div class="flex justify-end gap-4">
                    <button @click="open = false" class="btn-outline">
                        Cancel
                    </button>
                    <button @click="open = false" class="btn-primary">
                        Confirm
                    </button>
                </div>
            </div>
        </div>
    </template>
</div>
```

---

**Using Native `<dialog>` Element:**

```phtml
<div x-data="{}">
    <!-- Trigger -->
    <button @click="$refs.myDialog.showModal()"
            class="btn-primary">
        Open Dialog
    </button>

    <!-- Dialog Element -->
    <dialog x-ref="myDialog"
            class="max-w-md w-full p-6 rounded-lg shadow-2xl backdrop:bg-black backdrop:bg-opacity-50"
            @click.outside="$el.close()">

        <!-- Close Button -->
        <button @click="$refs.myDialog.close()"
                class="absolute top-4 right-4">
            <?= $heroicons->renderHtml('x', 'w-6 h-6') ?>
        </button>

        <!-- Content -->
        <h2 class="text-2xl font-bold mb-4">Dialog Title</h2>
        <p class="mb-6">Dialog content here.</p>

        <!-- Actions -->
        <div class="flex justify-end gap-4">
            <button @click="$refs.myDialog.close()" class="btn-outline">
                Close
            </button>
        </div>
    </dialog>
</div>
```

---

**Modal with Form Submission:**

```phtml
<div x-data="{
    open: false,
    loading: false,
    async submitForm() {
        this.loading = true;
        try {
            const response = await fetch('/api/endpoint', {
                method: 'POST',
                body: new FormData(this.$refs.form)
            });
            if (response.ok) {
                this.open = false;
                alert('Success!');
            }
        } finally {
            this.loading = false;
        }
    }
}">
    <button @click="open = true">Open Form Modal</button>

    <template x-if="open">
        <div class="fixed inset-0 z-modal flex items-center justify-center">
            <div class="absolute inset-0 bg-black bg-opacity-50" @click="open = false"></div>

            <div class="relative bg-white rounded-lg max-w-md w-full p-6" @click.stop>
                <form x-ref="form" @submit.prevent="submitForm()">
                    <h2 class="text-2xl font-bold mb-4">Contact Form</h2>

                    <input type="text" name="name" placeholder="Name" class="form-input mb-4"/>
                    <input type="email" name="email" placeholder="Email" class="form-input mb-4"/>
                    <textarea name="message" placeholder="Message" class="form-input mb-4"></textarea>

                    <button type="submit"
                            :disabled="loading"
                            class="btn-primary w-full"
                            x-text="loading ? 'Sending...' : 'Send'">
                    </button>
                </form>
            </div>
        </div>
    </template>
</div>
```

---

**Key Takeaways:**

1. **Hyvä Directory Structure**: Understanding where files live helps you navigate and override correctly
2. **Naming Conventions**: `hyva_` prefix for layouts, optional `/hyva/` subdirectory for templates
3. **ViewModels (53)**: Core business logic - use them everywhere for clean, testable code
4. **Icons**: Heroicons (200+) and Lucide (1000+) built-in, easy to use with ViewModels
5. **Alpine Plugins**: 5 built-in plugins for common functionality (sliders, intersect, defer, etc.)
6. **Modal System**: Use Modal ViewModel + Alpine.js for accessible, reusable modals

---

**Hands-on Exercise:**

1. Explore all 53 ViewModels in `/vendor/hyva-themes/magento2-theme-module/src/ViewModel/`
2. Find and examine `default_hyva.xml` to see Alpine plugin loading
3. Browse Heroicons: https://heroicons.com
4. Browse Lucide Icons: https://lucide.dev
5. Create a simple modal using the examples above
6. Use at least 3 different ViewModels in a custom template

---

### **Phase 2: Template Development** (Building Pages)

#### 2.1 Template Structure and Layout XML
**Objective:** Understand Hyvä's template organization

**Topics:**
- Layout XML in Hyvä context
- Template file locations
- Block classes and ViewModels
- Template inheritance
- Fallback mechanism (theme-fallback module)

**Hands-on:**
- Trace homepage layout XML
- Find product page templates
- Override a template in custom theme
- Create custom layout handle

**Key Directories:**
```
Magento_Catalog/
├── layout/
│   ├── catalog_product_view.xml
│   ├── catalog_category_view.xml
│   └── default.xml
└── templates/
    ├── product/
    │   ├── view.phtml
    │   ├── list.phtml
    │   └── view/
    └── category/
```

---

#### 2.2 Working with ViewModels
**Objective:** Use Hyvä's ViewModel pattern for business logic

**Topics:**
- What are ViewModels?
- Hyva\Theme\ViewModel namespace
- Common ViewModels (StoreConfig, CurrentProduct, Modal)
- Creating custom ViewModels
- Dependency injection in ViewModels

**Hands-on:**
- Use existing ViewModels in templates
- Create custom ViewModel
- Pass data from block to ViewModel
- Use ViewModel for API calls

**Example:**
```php
// In layout XML
<argument name="view_model" xsi:type="object">Hyva\Theme\ViewModel\StoreConfig</argument>

// In template
<?php $storeConfig = $block->getViewModel(); ?>
<?= $storeConfig->getStoreConfig('general/store_information/name') ?>
```

---

#### 2.3 Product Page Customization
**Objective:** Master product detail page templates

**Topics:**
- Product view template structure
- Product gallery (Alpine.js + JavaScript)
- Add to cart functionality
- Product options (configurable, bundle, grouped)
- Custom options
- Tier pricing display
- Product reviews

**Hands-on:**
- Customize product image gallery
- Add custom product attribute display
- Modify add-to-cart button
- Create custom product tab

**Templates:**
```
Magento_Catalog/templates/product/view/
├── gallery.phtml           # Image gallery
├── options.phtml           # Custom options
├── form.phtml             # Add to cart form
├── details.phtml          # Product details tabs
└── attributes.phtml       # Additional attributes
```

---

#### 2.4 Category and Listing Pages
**Objective:** Build and customize product listing pages

**Topics:**
- Category page layout
- Product list item template
- Layered navigation (filters)
- Toolbar (sort, pagination, view mode)
- Grid vs list view
- Infinite scroll/Load more

**Hands-on:**
- Customize product list item
- Modify filter UI
- Change pagination style
- Add quick view functionality

**Templates:**
```
Magento_Catalog/templates/product/
├── list.phtml              # Main list template
├── list/
│   ├── item.phtml         # Single product item
│   └── toolbar.phtml      # Sorting/pagination
└── category/
    └── view.phtml         # Category description
```

---

#### 2.5 Checkout Customization
**Objective:** Understand Hyvä checkout flow

**Topics:**
- Checkout page structure
- Shipping step customization
- Payment methods
- Order summary
- React checkout vs Alpine checkout
- Success page

**Hands-on:**
- Add custom checkout field
- Modify shipping methods display
- Customize order summary
- Add custom validation

**Templates:**
```
Magento_Checkout/templates/
├── cart.phtml              # Shopping cart
├── onepage.phtml          # Checkout container
└── cart/
    ├── item/
    └── totals.phtml
```

---

#### 2.6 Customer Account Pages
**Objective:** Customize customer dashboard and account pages

**Topics:**
- Account dashboard
- Order history
- Address book
- Wishlist
- Product reviews
- Newsletter subscription

**Hands-on:**
- Customize dashboard layout
- Add custom account section
- Modify order detail page
- Style wishlist

**Templates:**
```
Magento_Customer/templates/
├── account/
│   ├── dashboard.phtml
│   ├── navigation.phtml
│   └── orders.phtml
└── form/
    ├── login.phtml
    └── register.phtml
```

---

### **Phase 3: Advanced Components** (Interactive Features)

#### 3.1 Modal and Dialog Components
**Objective:** Implement modals and popups

**Topics:**
- Hyvä Modal ViewModel
- Alpine.js modal patterns
- Dialog API integration
- Accessibility considerations

**Hands-on:**
- Create product quick view modal
- Build newsletter signup popup
- Implement confirmation dialogs

---

#### 3.2 Form Handling and Validation
**Objective:** Build forms with validation

**Topics:**
- Form validation with Alpine.js
- Magento form key integration
- AJAX form submission
- Client-side validation
- Server-side validation feedback

**Hands-on:**
- Create contact form
- Add custom validation rules
- Handle form errors gracefully
- Show success messages

---

#### 3.3 Cart and Mini-Cart
**Objective:** Master cart functionality

**Topics:**
- Mini-cart Alpine.js component
- Add to cart AJAX
- Update quantity
- Remove items
- Cross-sell products
- Cart validation

**Hands-on:**
- Customize mini-cart UI
- Add product recommendations
- Implement cart notifications
- Create cart drawer

---

#### 3.4 Search and Autocomplete
**Objective:** Enhance search functionality

**Topics:**
- Search form template
- Autocomplete component
- Search results page
- Advanced search
- Search suggestions

**Hands-on:**
- Customize search dropdown
- Add product images to suggestions
- Implement category suggestions
- Style results page

---

#### 3.5 Navigation Menus
**Objective:** Build complex navigation

**Topics:**
- Header navigation
- Mobile menu
- Mega menu
- Category navigation
- Breadcrumbs

**Hands-on:**
- Create mega menu
- Build mobile-first navigation
- Add category images to menu
- Implement sticky header

---

### **Phase 4: Performance Optimization** (Speed & Efficiency)

#### 4.1 Image Optimization
**Objective:** Optimize image loading

**Topics:**
- Lazy loading images
- Responsive images (srcset)
- WebP format
- Image CDN integration
- Placeholder images

**Hands-on:**
- Implement lazy loading
- Use picture element
- Configure image sizes
- Test performance impact

---

#### 4.2 JavaScript Optimization
**Objective:** Minimize JS footprint

**Topics:**
- Alpine.js bundle size
- Defer/async loading
- Remove unused JS
- Code splitting
- Third-party script optimization

**Hands-on:**
- Audit JS dependencies
- Implement lazy component loading
- Optimize third-party scripts

---

#### 4.3 CSS Optimization
**Objective:** Optimize Tailwind CSS output

**Topics:**
- PurgeCSS configuration
- Critical CSS
- Tailwind JIT compiler
- Remove unused utilities
- CSS bundle size

**Hands-on:**
- Configure purge paths
- Analyze CSS bundle
- Implement critical CSS
- Test production build

---

#### 4.4 Caching Strategies
**Objective:** Leverage Magento and browser caching

**Topics:**
- Full Page Cache (FPC)
- Block cache
- Browser caching headers
- Cache warming
- ESI holes for dynamic content

**Hands-on:**
- Configure cache policies
- Identify cacheable blocks
- Test cache effectiveness
- Monitor cache hit rates

---

### **Phase 5: Creating Custom Theme** (Your Own Theme)

---

## Building a Custom Hyvä Theme - Complete Guide with Best Practices

This comprehensive guide will walk you through creating a production-ready custom Hyvä theme from scratch, following industry best practices.

---

### **Overview: Why Create a Custom Theme?**

**Benefits:**
- Brand-specific design and styling
- Custom functionality without modifying core Hyvä files
- Easy updates (Hyvä updates won't overwrite your customizations)
- Maintainable codebase
- Performance optimizations specific to your needs

**Approach:**
- Inherit from Hyvä default theme (child theme pattern)
- Override only what you need
- Follow Magento and Hyvä conventions
- Use Tailwind CSS for styling
- Leverage Alpine.js for interactivity

---

### **5.1 Theme Setup and Registration**

#### Step 1: Create Theme Directory Structure

**Location:** `app/design/frontend/{Vendor}/{theme-name}/`

**Recommended Structure:**
```
app/design/frontend/MyCompany/hyva-custom/
├── composer.json              # Theme dependencies
├── registration.php           # Register theme with Magento
├── theme.xml                  # Theme metadata and parent
├── etc/
│   └── view.xml              # Theme configuration (images, breakpoints)
├── i18n/
│   └── en_US.csv             # Translations
├── media/
│   └── preview.jpg           # Theme preview image (200x200px)
├── web/
│   ├── css/
│   │   └── source/          # Custom CSS/SCSS
│   ├── images/              # Theme images
│   ├── js/                  # Custom JavaScript
│   ├── fonts/               # Custom fonts
│   └── tailwind/
│       ├── tailwind.config.js     # Tailwind configuration
│       ├── tailwind.css          # Compiled CSS (generated)
│       └── components/           # Custom Tailwind components
│           └── custom-components.css
├── Magento_Theme/           # Module overrides
│   ├── layout/
│   │   └── default.xml
│   ├── templates/
│   │   └── html/
│   │       ├── header.phtml
│   │       └── footer.phtml
│   └── web/
│       └── css/
│           └── source/
├── Magento_Catalog/
│   ├── layout/
│   │   ├── catalog_product_view.xml
│   │   └── catalog_category_view.xml
│   └── templates/
│       └── product/
├── Magento_Checkout/
├── Magento_Customer/
└── README.md                # Documentation
```

---

#### Step 2: Create registration.php

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

**Best Practices:**
- Use clear, descriptive vendor and theme names
- Follow naming convention: `{Vendor}/{theme-name}` (lowercase with hyphens)
- Add proper PHPDoc comments
- Keep it simple - only registration code

---

#### Step 3: Create theme.xml

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
        <preview_image>media/preview.jpg</preview_image>
    </media>
</theme>
```

**Key Elements:**
- `<title>` - Display name in admin
- `<parent>` - Inherit from Hyvä default theme
- `<preview_image>` - Theme thumbnail (optional but recommended)

**Best Practices:**
- Always inherit from `Hyva/default` (not `Magento/blank` or `Magento/luma`)
- Use descriptive, professional theme title
- Include preview image (200x200px, JPG/PNG)

---

#### Step 4: Create composer.json

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

**Best Practices:**
- Specify Hyvä version dependencies
- Use semantic versioning
- Include proper metadata (authors, description)
- Set appropriate PHP and Magento version requirements

---

#### Step 5: Create etc/view.xml

**File:** `app/design/frontend/MyCompany/hyva-custom/etc/view.xml`

```xml
<?xml version="1.0"?>
<!--
/**
 * Theme View Configuration
 * Configure image sizes, gallery settings, and responsive breakpoints
 */
-->
<view xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/view.xsd">
    <media>
        <images module="Magento_Catalog">
            <!-- Product Listing Images -->
            <image id="category_page_grid" type="small_image">
                <width>240</width>
                <height>300</height>
            </image>
            <image id="category_page_list" type="small_image">
                <width>240</width>
                <height>300</height>
            </image>

            <!-- Product Detail Images -->
            <image id="product_page_image_large" type="image">
                <width>700</width>
                <height>875</height>
            </image>
            <image id="product_page_image_medium" type="image">
                <width>560</width>
                <height>700</height>
            </image>
            <image id="product_page_image_small" type="thumbnail">
                <width>88</width>
                <height>110</height>
            </image>

            <!-- Related/Upsell Products -->
            <image id="related_products_list" type="small_image">
                <width>240</width>
                <height>300</height>
            </image>
            <image id="upsell_products_list" type="small_image">
                <width>240</width>
                <height>300</height>
            </image>

            <!-- Cart Images -->
            <image id="cart_page_product_thumbnail" type="small_image">
                <width>165</width>
                <height>165</height>
            </image>
            <image id="mini_cart_product_thumbnail" type="small_image">
                <width>78</width>
                <height>78</height>
            </image>
        </images>
    </media>

    <!-- Tailwind Breakpoints (should match tailwind.config.js) -->
    <vars module="Magento_Catalog">
        <var name="breakpoints">
            <var name="mobile">640px</var>
            <var name="tablet">768px</var>
            <var name="desktop">1024px</var>
            <var name="wide">1280px</var>
        </var>
    </vars>
</view>
```

**Best Practices:**
- Define consistent image sizes across the theme
- Match breakpoints with Tailwind configuration
- Use descriptive image IDs
- Consider mobile-first approach (smaller images first)
- Optimize for performance (don't use oversized images)

---

#### Step 6: Register and Activate Theme

**Commands:**
```bash
# 1. Clear cache
php bin/magento cache:flush

# 2. Upgrade database (registers theme)
php bin/magento setup:upgrade

# 3. Verify theme is registered
php bin/magento theme:list

# Expected output should include:
# frontend/MyCompany/hyva-custom | MyCompany Hyvä Custom Theme

# 4. Deploy static content
php bin/magento setup:static-content:deploy -f

# 5. Set as active theme (via Admin or CLI)
# Admin: Content > Design > Configuration > Edit Store View > Applied Theme
```

**Verify Installation:**
```bash
# Check theme files are in place
ls -la app/design/frontend/MyCompany/hyva-custom/

# Check theme is registered
php bin/magento theme:list | grep MyCompany

# Test frontend (should see Hyvä default styling initially)
```

---

### **5.2 Customizing Tailwind Configuration**

#### Understanding Tailwind in Hyvä

Hyvä uses Tailwind CSS v3 with JIT (Just-In-Time) compiler for optimal performance.

**Location:** `app/design/frontend/MyCompany/hyva-custom/web/tailwind/tailwind.config.js`

---

#### Complete Tailwind Configuration

**File:** `web/tailwind/tailwind.config.js`

```javascript
/**
 * Tailwind CSS Configuration
 *
 * This file extends Hyvä's default Tailwind configuration
 * with custom brand colors, fonts, spacing, and components.
 *
 * @see https://tailwindcss.com/docs/configuration
 */

const { spacing } = require('tailwindcss/defaultTheme')
const colors = require('tailwindcss/colors')
const hyvaModules = require('@hyva-themes/hyva-modules')

module.exports = {
    presets: [
        hyvaModules.getThemeConfig()
    ],
    content: [
        // Hyvä default theme templates
        '../../../../../../../vendor/hyva-themes/magento2-default-theme/**/*.phtml',
        '../../../../../../../vendor/hyva-themes/magento2-theme-module/src/**/*.phtml',

        // Custom theme templates
        '../../**/*.phtml',
        '../../*/layout/*.xml',
        '../../*/page_layout/*.xml',

        // Custom JavaScript with Alpine directives
        '../../*/web/js/**/*.js',
    ],
    theme: {
        extend: {
            // ==========================================
            // CUSTOM COLORS
            // ==========================================
            colors: {
                // Brand Primary Colors
                primary: {
                    50: '#f0f9ff',
                    100: '#e0f2fe',
                    200: '#bae6fd',
                    300: '#7dd3fc',
                    400: '#38bdf8',
                    500: '#0ea5e9',  // Main brand color
                    600: '#0284c7',
                    700: '#0369a1',
                    800: '#075985',
                    900: '#0c4a6e',
                    DEFAULT: '#0ea5e9',
                },

                // Brand Secondary Colors
                secondary: {
                    50: '#fdf4ff',
                    100: '#fae8ff',
                    200: '#f5d0fe',
                    300: '#f0abfc',
                    400: '#e879f9',
                    500: '#d946ef',  // Main secondary color
                    600: '#c026d3',
                    700: '#a21caf',
                    800: '#86198f',
                    900: '#701a75',
                    DEFAULT: '#d946ef',
                },

                // Accent Colors
                accent: {
                    orange: '#fb923c',
                    green: '#22c55e',
                    red: '#ef4444',
                    yellow: '#fbbf24',
                },

                // Neutral/Gray Scale (custom)
                gray: colors.slate,

                // Keep Hyvä's container colors or customize
                'container-lighter': colors.gray[100],
                'container-darker': colors.gray[200],
            },

            // ==========================================
            // TYPOGRAPHY
            // ==========================================
            fontFamily: {
                sans: [
                    'Inter',
                    'ui-sans-serif',
                    'system-ui',
                    '-apple-system',
                    'sans-serif'
                ],
                serif: [
                    'Merriweather',
                    'Georgia',
                    'serif'
                ],
                mono: [
                    'Fira Code',
                    'ui-monospace',
                    'monospace'
                ],
                display: [
                    'Poppins',
                    'sans-serif'
                ],
            },

            fontSize: {
                '2xs': ['0.625rem', { lineHeight: '0.875rem' }],
                'xs': ['0.75rem', { lineHeight: '1rem' }],
                'sm': ['0.875rem', { lineHeight: '1.25rem' }],
                'base': ['1rem', { lineHeight: '1.5rem' }],
                'lg': ['1.125rem', { lineHeight: '1.75rem' }],
                'xl': ['1.25rem', { lineHeight: '1.75rem' }],
                '2xl': ['1.5rem', { lineHeight: '2rem' }],
                '3xl': ['1.875rem', { lineHeight: '2.25rem' }],
                '4xl': ['2.25rem', { lineHeight: '2.5rem' }],
                '5xl': ['3rem', { lineHeight: '1' }],
                '6xl': ['3.75rem', { lineHeight: '1' }],
                '7xl': ['4.5rem', { lineHeight: '1' }],
                '8xl': ['6rem', { lineHeight: '1' }],
                '9xl': ['8rem', { lineHeight: '1' }],
            },

            // ==========================================
            // SPACING & SIZING
            // ==========================================
            spacing: {
                ...spacing,
                '18': '4.5rem',
                '88': '22rem',
                '100': '25rem',
                '112': '28rem',
                '128': '32rem',
            },

            maxWidth: {
                '8xl': '88rem',
                '9xl': '96rem',
            },

            // ==========================================
            // BREAKPOINTS (Match etc/view.xml)
            // ==========================================
            screens: {
                'xs': '480px',
                'sm': '640px',   // mobile
                'md': '768px',   // tablet
                'lg': '1024px',  // desktop
                'xl': '1280px',  // wide
                '2xl': '1536px',
            },

            // ==========================================
            // SHADOWS & EFFECTS
            // ==========================================
            boxShadow: {
                'sm': '0 1px 2px 0 rgba(0, 0, 0, 0.05)',
                'DEFAULT': '0 1px 3px 0 rgba(0, 0, 0, 0.1)',
                'md': '0 4px 6px -1px rgba(0, 0, 0, 0.1)',
                'lg': '0 10px 15px -3px rgba(0, 0, 0, 0.1)',
                'xl': '0 20px 25px -5px rgba(0, 0, 0, 0.1)',
                '2xl': '0 25px 50px -12px rgba(0, 0, 0, 0.25)',
                'inner': 'inset 0 2px 4px 0 rgba(0, 0, 0, 0.06)',
                'card': '0 4px 20px rgba(0, 0, 0, 0.08)',
                'hover-card': '0 8px 30px rgba(0, 0, 0, 0.12)',
            },

            // ==========================================
            // BORDER RADIUS
            // ==========================================
            borderRadius: {
                'none': '0',
                'sm': '0.125rem',
                'DEFAULT': '0.375rem',
                'md': '0.5rem',
                'lg': '0.75rem',
                'xl': '1rem',
                '2xl': '1.5rem',
                '3xl': '2rem',
                'full': '9999px',
            },

            // ==========================================
            // TRANSITIONS & ANIMATIONS
            // ==========================================
            transitionDuration: {
                '0': '0ms',
                '75': '75ms',
                '100': '100ms',
                '150': '150ms',
                '200': '200ms',
                '300': '300ms',
                '500': '500ms',
                '700': '700ms',
                '1000': '1000ms',
            },

            keyframes: {
                'fade-in': {
                    '0%': { opacity: '0' },
                    '100%': { opacity: '1' },
                },
                'fade-out': {
                    '0%': { opacity: '1' },
                    '100%': { opacity: '0' },
                },
                'slide-in-right': {
                    '0%': { transform: 'translateX(100%)' },
                    '100%': { transform: 'translateX(0)' },
                },
                'slide-out-right': {
                    '0%': { transform: 'translateX(0)' },
                    '100%': { transform: 'translateX(100%)' },
                },
                'bounce-in': {
                    '0%': { transform: 'scale(0.8)', opacity: '0' },
                    '50%': { transform: 'scale(1.05)' },
                    '100%': { transform: 'scale(1)', opacity: '1' },
                },
            },

            animation: {
                'fade-in': 'fade-in 0.3s ease-in-out',
                'fade-out': 'fade-out 0.3s ease-in-out',
                'slide-in-right': 'slide-in-right 0.3s ease-out',
                'slide-out-right': 'slide-out-right 0.3s ease-in',
                'bounce-in': 'bounce-in 0.4s ease-out',
            },

            // ==========================================
            // Z-INDEX LAYERS
            // ==========================================
            zIndex: {
                '0': '0',
                '10': '10',
                '20': '20',
                '30': '30',
                '40': '40',
                '50': '50',
                'dropdown': '1000',
                'sticky': '1020',
                'fixed': '1030',
                'modal-backdrop': '1040',
                'modal': '1050',
                'popover': '1060',
                'tooltip': '1070',
            },
        },
    },

    // ==========================================
    // PLUGINS
    // ==========================================
    plugins: [
        require('@tailwindcss/forms')({
            strategy: 'class', // Use form-* classes instead of global styling
        }),
        require('@tailwindcss/typography'),
        require('@tailwindcss/aspect-ratio'),

        // Custom plugin for Magento-specific utilities
        function({ addComponents, theme }) {
            addComponents({
                '.btn-primary': {
                    backgroundColor: theme('colors.primary.500'),
                    color: theme('colors.white'),
                    padding: `${theme('spacing.3')} ${theme('spacing.6')}`,
                    borderRadius: theme('borderRadius.md'),
                    fontWeight: theme('fontWeight.semibold'),
                    transition: 'all 0.2s ease',
                    '&:hover': {
                        backgroundColor: theme('colors.primary.600'),
                        transform: 'translateY(-1px)',
                        boxShadow: theme('boxShadow.md'),
                    },
                    '&:active': {
                        transform: 'translateY(0)',
                    },
                    '&:disabled': {
                        backgroundColor: theme('colors.gray.400'),
                        cursor: 'not-allowed',
                        transform: 'none',
                    },
                },
                '.btn-secondary': {
                    backgroundColor: theme('colors.secondary.500'),
                    color: theme('colors.white'),
                    padding: `${theme('spacing.3')} ${theme('spacing.6')}`,
                    borderRadius: theme('borderRadius.md'),
                    fontWeight: theme('fontWeight.semibold'),
                    transition: 'all 0.2s ease',
                    '&:hover': {
                        backgroundColor: theme('colors.secondary.600'),
                        transform: 'translateY(-1px)',
                        boxShadow: theme('boxShadow.md'),
                    },
                },
                '.btn-outline': {
                    backgroundColor: 'transparent',
                    color: theme('colors.primary.500'),
                    border: `2px solid ${theme('colors.primary.500')}`,
                    padding: `${theme('spacing.3')} ${theme('spacing.6')}`,
                    borderRadius: theme('borderRadius.md'),
                    fontWeight: theme('fontWeight.semibold'),
                    transition: 'all 0.2s ease',
                    '&:hover': {
                        backgroundColor: theme('colors.primary.500'),
                        color: theme('colors.white'),
                    },
                },
                '.card': {
                    backgroundColor: theme('colors.white'),
                    borderRadius: theme('borderRadius.lg'),
                    padding: theme('spacing.6'),
                    boxShadow: theme('boxShadow.card'),
                    transition: 'box-shadow 0.3s ease',
                    '&:hover': {
                        boxShadow: theme('boxShadow.hover-card'),
                    },
                },
                '.product-card': {
                    backgroundColor: theme('colors.white'),
                    borderRadius: theme('borderRadius.lg'),
                    overflow: 'hidden',
                    transition: 'all 0.3s ease',
                    '&:hover': {
                        transform: 'translateY(-4px)',
                        boxShadow: theme('boxShadow.xl'),
                    },
                },
            })
        },
    ],

    // ==========================================
    // CORE PLUGINS (Disable if not needed)
    // ==========================================
    corePlugins: {
        // container: false, // Uncomment to disable if using custom container
    },
}
```

---

#### Best Practices for Tailwind Configuration

**1. Color System:**
- Use semantic naming (`primary`, `secondary`, `accent`)
- Provide full color scale (50-900) for flexibility
- Set `DEFAULT` value for each color group
- Match brand guidelines

**2. Typography:**
- Define custom font families with fallbacks
- Use system fonts as fallbacks for performance
- Configure font sizes with line heights
- Consider web font loading strategy

**3. Spacing:**
- Extend default spacing, don't replace
- Add custom values only when needed
- Maintain consistency (use multiples of 4px/0.25rem)

**4. Breakpoints:**
- Match `etc/view.xml` breakpoints
- Use mobile-first approach
- Name breakpoints semantically

**5. Components:**
- Create reusable component classes (`.btn-primary`, `.card`)
- Use `@apply` sparingly (prefer utility classes)
- Document custom components

**6. Performance:**
- Use `content` array to purge unused styles
- Include all template locations
- Test production build size

---

### **5.3 Best Practices for Custom Hyvä Theme Development**

This section covers production-ready best practices for building maintainable, performant Hyvä themes.

---

#### 1. File Organization Best Practices

**Directory Structure Philosophy:**
- **Modular**: Organize by Magento module (`Magento_Catalog/`, `Magento_Checkout/`)
- **Predictable**: Follow Magento's naming conventions
- **Documented**: Add README files for complex customizations
- **Versioned**: Use Git with proper .gitignore

**Recommended .gitignore:**
```gitignore
# Generated files
web/tailwind/tailwind.css
web/tailwind/tailwind.css.map
web/css/*.css
web/css/*.css.map

# Node modules (if using npm for Tailwind)
node_modules/

# IDE files
.idea/
.vscode/
*.swp
*.swo

# OS files
.DS_Store
Thumbs.db

# Logs
*.log
```

---

#### 2. Template Override Best Practices

**When to Override Templates:**
- ✅ DO: Override when changing HTML structure or layout
- ✅ DO: Override for brand-specific functionality
- ❌ DON'T: Override just for styling (use Tailwind instead)
- ❌ DON'T: Override if layout XML changes are sufficient

**How to Override Templates:**

**Step 1: Identify the template to override**
```bash
# Enable template hints
php bin/magento dev:template-hints:enable

# Visit the page and note the template path
# Example: vendor/hyva-themes/magento2-default-theme/Magento_Catalog/templates/product/list/item.phtml
```

**Step 2: Copy to your theme**
```bash
# Create directory structure
mkdir -p app/design/frontend/MyCompany/hyva-custom/Magento_Catalog/templates/product/list/

# Copy template
cp vendor/hyva-themes/magento2-default-theme/Magento_Catalog/templates/product/list/item.phtml \
   app/design/frontend/MyCompany/hyva-custom/Magento_Catalog/templates/product/list/
```

**Step 3: Customize and document**
```phtml
<?php
/**
 * Custom Product List Item Template
 *
 * Customizations:
 * - Added product badges
 * - Modified image gallery structure
 * - Custom quick view button
 *
 * Parent template: Hyva/default::Magento_Catalog/templates/product/list/item.phtml
 *
 * @var $block \Magento\Catalog\Block\Product\ListProduct
 * @var \Hyva\Theme\ViewModel\HeroiconsOutline $heroicons
 */

// Your custom code here
?>
```

**Best Practices for Templates:**
- Add PHPDoc comments explaining changes
- Reference parent template in comments
- Keep modifications minimal
- Use ViewModels for business logic
- Follow PSR-12 coding standards

---

#### 3. Layout XML Best Practices

**Naming Conventions:**
```xml
<!-- Use descriptive, namespaced names -->
<block name="mycompany.product.custom.badge" ... />
<container name="mycompany.custom.sidebar" ... />

<!-- Avoid generic names that might conflict -->
<!-- BAD: name="custom-block" -->
<!-- GOOD: name="mycompany.product.custom.block" -->
```

**Organization:**
```xml
<?xml version="1.0"?>
<!--
/**
 * Product View Page Layout Customizations
 *
 * Changes:
 * - Added custom product badge container
 * - Moved reviews to sidebar
 * - Added trust badges below add-to-cart
 *
 * @category  Design
 * @package   MyCompany_HyvaCustom
 */
-->
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <!-- Group related changes with comments -->

        <!-- Custom Badge Section -->
        <referenceContainer name="product.info.main">
            <container name="mycompany.product.badges" before="-">
                <block name="mycompany.product.badge.new"
                       template="Magento_Catalog::product/badge/new.phtml"/>
            </container>
        </referenceContainer>

        <!-- Trust Badges -->
        <referenceBlock name="product.info.addtocart">
            <block name="mycompany.trust.badges"
                   template="Magento_Theme::trust-badges.phtml"
                   after="-"/>
        </referenceBlock>
    </body>
</page>
```

**Performance Tips:**
- Use `ttl` attribute for cacheable blocks
- Use `ifconfig` to conditionally load blocks
- Minimize nested blocks
- Use `move` instead of duplicating blocks

---

#### 4. Styling Best Practices

**Tailwind Class Organization:**
```phtml
<!-- Group classes logically -->
<div class="
    <!-- Layout -->
    flex flex-col gap-4

    <!-- Sizing -->
    w-full max-w-md

    <!-- Spacing -->
    p-6 mb-8

    <!-- Typography -->
    text-base font-semibold text-gray-900

    <!-- Background & Border -->
    bg-white rounded-lg border border-gray-200

    <!-- Effects -->
    shadow-md hover:shadow-lg

    <!-- Transitions -->
    transition-all duration-300

    <!-- Responsive -->
    md:flex-row md:p-8
">
    Content
</div>
```

**Component Extraction:**

When a set of classes is reused, extract to component:

**Bad (repetitive):**
```phtml
<button class="bg-primary-500 text-white px-6 py-3 rounded-md font-semibold hover:bg-primary-600">
<button class="bg-primary-500 text-white px-6 py-3 rounded-md font-semibold hover:bg-primary-600">
<button class="bg-primary-500 text-white px-6 py-3 rounded-md font-semibold hover:bg-primary-600">
```

**Good (extracted component):**
```phtml
<button class="btn-primary">Button 1</button>
<button class="btn-primary">Button 2</button>
<button class="btn-primary">Button 3</button>
```

Define `.btn-primary` in `tailwind.config.js` plugins section (shown earlier).

---

#### 5. Alpine.js Best Practices

**Component Organization:**
```phtml
<!-- Extract complex Alpine components to separate files -->
<div x-data="productGallery()" x-init="init()">
    <!-- Template -->
</div>

<script>
function productGallery() {
    return {
        currentImage: 0,
        images: [],

        init() {
            this.images = <?= $block->getGalleryImagesJson() ?>;
        },

        nextImage() {
            this.currentImage = (this.currentImage + 1) % this.images.length;
        },

        prevImage() {
            this.currentImage = this.currentImage === 0
                ? this.images.length - 1
                : this.currentImage - 1;
        }
    }
}
</script>
```

**Best Practices:**
- Use Alpine stores for shared state
- Extract complex logic to JavaScript files
- Use `x-cloak` to prevent flash of unstyled content
- Leverage Alpine plugins (intersect, focus, etc.)
- Use `$dispatch` for component communication

---

#### 6. Performance Optimization

**Image Optimization:**
```php
<?php
// Use proper image sizing from view.xml
$imageUrl = $block->getImage($product, 'category_page_grid')->getImageUrl();

// Add lazy loading
?>
<img src="<?= $escaper->escapeUrl($imageUrl) ?>"
     alt="<?= $escaper->escapeHtmlAttr($product->getName()) ?>"
     loading="lazy"
     class="w-full h-auto"
/>
```

**CSS Optimization:**
```javascript
// tailwind.config.js - Purge unused CSS
module.exports = {
    content: [
        '../../**/*.phtml',
        '../../*/layout/*.xml',
        // Include all locations where Tailwind classes are used
    ],
    // ...
}
```

**JavaScript Optimization:**
```phtml
<!-- Defer non-critical scripts -->
<script defer src="<?= $escaper->escapeUrl($block->getViewFileUrl('js/custom.js')) ?>"></script>

<!-- Use Alpine defer plugin for below-fold components -->
<div x-data="expensiveComponent()" x-defer="intersect">
    <!-- Component loads when scrolled into view -->
</div>
```

---

#### 7. Accessibility Best Practices

**Semantic HTML:**
```phtml
<!-- Use proper heading hierarchy -->
<h1 class="text-4xl font-bold"><?= $escaper->escapeHtml($product->getName()) ?></h1>
<h2 class="text-2xl font-semibold mt-6">Product Details</h2>

<!-- Use semantic elements -->
<nav aria-label="Product categories">
    <ul>...</ul>
</nav>

<article class="product-description">
    <?= $block->getProductDescription() ?>
</article>
```

**ARIA Labels:**
```phtml
<!-- Add ARIA labels for screen readers -->
<button
    type="button"
    aria-label="Add <?= $escaper->escapeHtmlAttr($product->getName()) ?> to cart"
    class="btn-primary"
>
    <span aria-hidden="true">Add to Cart</span>
</button>

<!-- Form labels -->
<label for="product-qty" class="block mb-2">Quantity:</label>
<input
    type="number"
    id="product-qty"
    name="qty"
    aria-describedby="qty-hint"
    class="form-input"
/>
<span id="qty-hint" class="text-sm text-gray-600">
    Enter quantity (1-999)
</span>
```

**Keyboard Navigation:**
```phtml
<!-- Ensure all interactive elements are keyboard accessible -->
<div
    x-data="{ open: false }"
    @keydown.escape="open = false"
    @click.away="open = false"
>
    <button
        @click="open = !open"
        @keydown.enter="open = !open"
        @keydown.space.prevent="open = !open"
        aria-expanded="false"
        :aria-expanded="open"
        class="focus:outline-none focus:ring-2 focus:ring-primary-500"
    >
        Menu
    </button>
</div>
```

---

#### 8. Internationalization (i18n)

**Translation Files:**

**File:** `app/design/frontend/MyCompany/hyva-custom/i18n/en_US.csv`
```csv
"Add to Cart","Add to Cart"
"Product Details","Product Details"
"Free Shipping on orders over $50","Free Shipping on orders over $50"
"Customer Reviews","Customer Reviews"
```

**In Templates:**
```phtml
<!-- Always use translation helpers -->
<?= $escaper->escapeHtml(__('Add to Cart')) ?>

<!-- With variables -->
<?= $escaper->escapeHtml(__('Only %1 left in stock', $product->getStockQty())) ?>

<!-- With HTML (use escapeHtml separately) -->
<p><?= /* @noEscape */ __('Free shipping on orders over <strong>$50</strong>') ?></p>
```

---

#### 9. Version Control Best Practices

**Commit Messages:**
```bash
# Use conventional commit format
git commit -m "feat(product-page): add trust badges below add-to-cart button"
git commit -m "fix(header): resolve mobile menu z-index issue"
git commit -m "style(cart): update button styling to match brand guidelines"
git commit -m "refactor(templates): extract product card to separate component"
git commit -m "docs(readme): add installation instructions"
```

**Branch Strategy:**
```bash
# Use feature branches
git checkout -b feature/custom-product-badges
git checkout -b fix/mobile-menu-overlay
git checkout -b refactor/product-list-template

# Main branches
main         # Production-ready code
develop      # Integration branch
staging      # Staging environment
```

---

#### 10. Documentation

**README.md Template:**
```markdown
# MyCompany Hyvä Custom Theme

Custom Hyvä theme for MyCompany e-commerce store.

## Requirements

- Magento 2.4.6+
- PHP 8.1+
- Hyvä Theme 1.3+
- Node.js 18+ (for Tailwind compilation)

## Installation

1. Copy theme to `app/design/frontend/MyCompany/hyva-custom/`
2. Run `php bin/magento setup:upgrade`
3. Run `php bin/magento setup:static-content:deploy`
4. Activate theme in Admin: Content > Design > Configuration

## Development

### Compile Tailwind CSS
```bash
cd app/design/frontend/MyCompany/hyva-custom/web/tailwind
npm install
npm run build
```

### Watch mode for development
```bash
npm run watch
```

## Customizations

### Product Page
- Custom trust badges below add-to-cart button
- Modified image gallery with zoom
- Custom product tabs

### Category Page
- Custom product card design
- Enhanced filtering UI
- Infinite scroll

### Checkout
- Custom delivery date picker
- Gift message field
- Trust badges

## Browser Support

- Chrome (latest 2 versions)
- Firefox (latest 2 versions)
- Safari (latest 2 versions)
- Edge (latest 2 versions)
- Mobile browsers (iOS Safari, Chrome Mobile)

## License

Proprietary - MyCompany

## Support

Contact: dev-team@mycompany.com
```

---

#### 11. Testing Checklist

**Before Deployment:**

**Functionality:**
- [ ] All pages load without errors
- [ ] Add to cart works for all product types
- [ ] Checkout flow completes successfully
- [ ] Customer account pages work
- [ ] Search functionality works
- [ ] Filters and sorting work
- [ ] Forms validate properly
- [ ] Payment methods display correctly

**Performance:**
- [ ] Lighthouse score > 90
- [ ] LCP < 2.5s
- [ ] FID < 100ms
- [ ] CLS < 0.1
- [ ] All images optimized
- [ ] CSS minified in production
- [ ] JavaScript deferred/async where possible

**Responsiveness:**
- [ ] Mobile (320px - 639px)
- [ ] Tablet (640px - 1023px)
- [ ] Desktop (1024px+)
- [ ] Touch targets min 44x44px
- [ ] Text readable without zooming

**Accessibility:**
- [ ] Keyboard navigation works
- [ ] Screen reader compatible
- [ ] Color contrast meets WCAG AA
- [ ] All images have alt text
- [ ] Forms have proper labels
- [ ] Focus indicators visible

**Cross-browser:**
- [ ] Chrome
- [ ] Firefox
- [ ] Safari
- [ ] Edge
- [ ] Mobile Safari
- [ ] Chrome Mobile

**SEO:**
- [ ] Meta titles unique and descriptive
- [ ] Meta descriptions present
- [ ] Canonical URLs set
- [ ] Schema.org markup present
- [ ] XML sitemap generated
- [ ] Robots.txt configured

---

#### 12. Maintenance Best Practices

**Regular Tasks:**
```bash
# Update Hyvä packages
composer update hyva-themes/magento2-default-theme
composer update hyva-themes/magento2-theme-module

# Clear cache
php bin/magento cache:flush

# Recompile
php bin/magento setup:di:compile
php bin/magento setup:static-content:deploy -f

# Check for issues
php bin/magento setup:db:status
```

**Monitor:**
- Error logs: `var/log/system.log`, `var/log/exception.log`
- Performance metrics: Core Web Vitals
- User feedback: Contact forms, support tickets
- Analytics: Bounce rate, conversion rate, page load times

---

### **5.4 Building Common Components**

Now let's build some common components you'll need in your custom theme.

---

#### Custom Product Badge Component

**Layout XML:** `Magento_Catalog/layout/catalog_product_view.xml`
```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceContainer name="product.info.media">
            <block name="product.badge"
                   template="Magento_Catalog::product/badge.phtml"
                   before="-"/>
        </referenceContainer>
    </body>
</page>
```

**Template:** `Magento_Catalog/templates/product/badge.phtml`
```phtml
<?php
/**
 * Product Badge Component
 * Displays "New", "Sale", or custom badges
 */

/** @var $block \Magento\Catalog\Block\Product\View */
/** @var $escaper \Magento\Framework\Escaper */
/** @var $product \Magento\Catalog\Model\Product */

$product = $block->getProduct();
$badges = [];

// Check if product is new
if ($product->getNewsFromDate() && $product->getNewsToDate()) {
    $now = new \DateTime();
    $newsFrom = new \DateTime($product->getNewsFromDate());
    $newsTo = new \DateTime($product->getNewsToDate());

    if ($now >= $newsFrom && $now <= $newsTo) {
        $badges[] = [
            'label' => __('New'),
            'class' => 'bg-green-500 text-white'
        ];
    }
}

// Check if product is on sale
if ($product->getFinalPrice() < $product->getPrice()) {
    $discount = round((($product->getPrice() - $product->getFinalPrice()) / $product->getPrice()) * 100);
    $badges[] = [
        'label' => __('-%1%', $discount),
        'class' => 'bg-red-500 text-white'
    ];
}

// Custom badge attribute
if ($product->getCustomAttribute('custom_badge')) {
    $badges[] = [
        'label' => $product->getCustomAttribute('custom_badge')->getValue(),
        'class' => 'bg-primary-500 text-white'
    ];
}

?>

<?php if (!empty($badges)): ?>
<div class="absolute top-4 left-4 z-10 flex flex-col gap-2">
    <?php foreach ($badges as $badge): ?>
        <span class="<?= $escaper->escapeHtmlAttr($badge['class']) ?>
                     px-3 py-1 text-sm font-semibold rounded-md shadow-md">
            <?= $escaper->escapeHtml($badge['label']) ?>
        </span>
    <?php endforeach; ?>
</div>
<?php endif; ?>
```

---

#### Trust Badges Component

**Template:** `Magento_Theme/templates/trust-badges.phtml`
```phtml
<?php
/**
 * Trust Badges Component
 * Display trust/security badges
 */
?>
<div class="flex flex-wrap items-center justify-center gap-6 py-6 border-t border-gray-200">
    <!-- Secure Payment -->
    <div class="flex items-center gap-2 text-gray-600">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"/>
        </svg>
        <span class="text-sm font-medium"><?= $escaper->escapeHtml(__('Secure Payment')) ?></span>
    </div>

    <!-- Free Shipping -->
    <div class="flex items-center gap-2 text-gray-600">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z"/>
        </svg>
        <span class="text-sm font-medium"><?= $escaper->escapeHtml(__('Free Shipping $50+')) ?></span>
    </div>

    <!-- Money Back Guarantee -->
    <div class="flex items-center gap-2 text-gray-600">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"/>
        </svg>
        <span class="text-sm font-medium"><?= $escaper->escapeHtml(__('30-Day Returns')) ?></span>
    </div>
</div>
```

---

#### Newsletter Popup Component

**Alpine.js Component:**
```phtml
<div x-data="newsletterPopup()"
     x-init="init()"
     x-show="show"
     x-cloak
     class="fixed inset-0 z-modal flex items-center justify-center"
     style="display: none;">

    <!-- Backdrop -->
    <div class="absolute inset-0 bg-black bg-opacity-50"
         @click="close()"></div>

    <!-- Modal -->
    <div class="relative bg-white rounded-lg shadow-2xl max-w-md w-full mx-4 p-8"
         @click.stop
         x-transition>

        <!-- Close button -->
        <button @click="close()"
                class="absolute top-4 right-4 text-gray-400 hover:text-gray-600">
            <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
            </svg>
        </button>

        <!-- Content -->
        <h2 class="text-2xl font-bold mb-4">Get 10% Off Your First Order</h2>
        <p class="text-gray-600 mb-6">Subscribe to our newsletter for exclusive deals and updates.</p>

        <!-- Form -->
        <form @submit.prevent="subscribe()">
            <input type="email"
                   x-model="email"
                   placeholder="Enter your email"
                   required
                   class="w-full px-4 py-3 border border-gray-300 rounded-md mb-4 focus:ring-2 focus:ring-primary-500 focus:border-transparent"/>

            <button type="submit"
                    :disabled="loading"
                    class="btn-primary w-full"
                    x-text="loading ? 'Subscribing...' : 'Subscribe'">
            </button>
        </form>
    </div>
</div>

<script>
function newsletterPopup() {
    return {
        show: false,
        email: '',
        loading: false,

        init() {
            // Show popup after 5 seconds if not previously dismissed
            const dismissed = localStorage.getItem('newsletter_popup_dismissed');
            if (!dismissed) {
                setTimeout(() => {
                    this.show = true;
                }, 5000);
            }
        },

        close() {
            this.show = false;
            localStorage.setItem('newsletter_popup_dismissed', 'true');
        },

        async subscribe() {
            this.loading = true;

            try {
                const response = await fetch('/newsletter/subscriber/new/', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/x-www-form-urlencoded',
                    },
                    body: `email=${encodeURIComponent(this.email)}`
                });

                if (response.ok) {
                    alert('Thanks for subscribing!');
                    this.close();
                } else {
                    alert('Subscription failed. Please try again.');
                }
            } catch (error) {
                alert('An error occurred. Please try again.');
            } finally {
                this.loading = false;
            }
        }
    }
}
</script>
```

---

### Summary: Custom Theme Development Workflow

**1. Setup** (Covered in 5.1)
- Create theme structure
- Register theme
- Configure Tailwind
- Set as active theme

**2. Customize** (Covered in 5.2-5.3)
- Override templates as needed
- Customize layouts
- Configure Tailwind colors/typography
- Build reusable components

**3. Test** (Covered in 5.3)
- Functionality testing
- Performance testing
- Accessibility testing
- Cross-browser testing

**4. Deploy** (Covered in Phase 8)
- Production mode
- Static content deployment
- Cache configuration
- Monitoring

**5. Maintain** (Covered in 5.3)
- Regular updates
- Performance monitoring
- Bug fixes
- Feature additions

---

#### 5.4 Custom Components and Widgets
**Objective:** Build reusable components

**Topics:**
- CMS widgets for Hyvä
- Custom Alpine components
- Shared state management
- Component library
- Documentation

**Hands-on:**
- Create custom slider widget
- Build testimonial component
- Product showcase widget
- FAQ accordion

---

#### 5.4 Icon System
**Objective:** Implement icon library

**Topics:**
- SVG icons in Hyvä
- Heroicons integration
- Lucide icons
- Custom icon sets
- Icon component

**Hands-on:**
- Use built-in icons
- Add custom SVG icons
- Create icon helper
- Optimize SVG output

---

### **Phase 6: Integration & Extensions** (Third-party Modules)

#### 6.1 Module Compatibility
**Objective:** Make modules work with Hyvä

**Topics:**
- Compat module system
- Creating compatibility modules
- Template overrides
- GraphQL integration
- Fallback mechanism

**Hands-on:**
- Identify incompatible modules
- Create compat module
- Override problematic templates
- Test functionality

---

#### 6.2 GraphQL Integration
**Objective:** Use GraphQL for frontend data

**Topics:**
- GraphQL queries in Hyvä
- Apollo client (if used)
- Custom GraphQL resolvers
- Authentication with tokens
- Caching GraphQL responses

**Hands-on:**
- Query product data
- Customer authentication
- Cart operations via GraphQL
- Custom endpoint integration

---

#### 6.3 Payment Gateway Integration
**Objective:** Integrate payment methods

**Topics:**
- Payment method rendering
- Mollie integration (installed)
- Custom payment templates
- Payment validation
- Checkout agreements

**Hands-on:**
- Configure Mollie
- Customize payment UI
- Test checkout flow
- Handle payment errors

---

#### 6.4 Third-party Services
**Objective:** Integrate external services

**Topics:**
- Analytics (GA4, GTM)
- Live chat widgets
- Product recommendations
- Marketing pixels
- Newsletter services

**Hands-on:**
- Install Google Tag Manager
- Add tracking events
- Integrate chat widget
- Configure GTM module

---

### **Phase 7: Testing & Quality** (Production Ready)

#### 7.1 Cross-browser Testing
**Objective:** Ensure compatibility

**Topics:**
- Browser support matrix
- Polyfills for older browsers
- Feature detection
- Progressive enhancement
- Graceful degradation

**Hands-on:**
- Test in Chrome, Firefox, Safari, Edge
- Test mobile browsers
- Verify Alpine.js compatibility
- Test accessibility features

---

#### 7.2 Accessibility (a11y)
**Objective:** Make theme accessible

**Topics:**
- WCAG 2.1 guidelines
- Keyboard navigation
- Screen reader support
- ARIA attributes
- Focus management
- Color contrast

**Hands-on:**
- Audit with Lighthouse
- Test keyboard navigation
- Use screen reader
- Fix accessibility issues
- Document accessibility features

---

#### 7.3 Performance Testing
**Objective:** Validate performance gains

**Topics:**
- Lighthouse scores
- Core Web Vitals (LCP, FID, CLS)
- PageSpeed Insights
- WebPageTest
- Real User Monitoring

**Hands-on:**
- Run Lighthouse audit
- Analyze Core Web Vitals
- Compare with Luma theme
- Identify bottlenecks
- Optimize critical path

---

#### 7.4 Mobile Responsiveness
**Objective:** Perfect mobile experience

**Topics:**
- Mobile-first approach
- Touch interactions
- Viewport configuration
- Mobile menu patterns
- Performance on mobile

**Hands-on:**
- Test all breakpoints
- Test on real devices
- Optimize touch targets
- Test mobile checkout flow
- Verify mobile performance

---

### **Phase 8: Deployment & Maintenance** (Going Live)

#### 8.1 Production Deployment
**Objective:** Deploy to production

**Topics:**
- Production mode setup
- Static content deployment
- CSS/JS minification
- CDN configuration
- Cache configuration

**Commands:**
```bash
php bin/magento deploy:mode:set production
php bin/magento setup:static-content:deploy -f
php bin/magento cache:flush
```

**Checklist:**
- Verify all pages load correctly
- Test critical user flows
- Check analytics tracking
- Verify payment processing
- Monitor error logs

---

#### 8.2 Monitoring and Debugging
**Objective:** Monitor production issues

**Topics:**
- Error logging
- Performance monitoring
- User behavior tracking
- A/B testing
- Error reporting tools

**Hands-on:**
- Set up error monitoring
- Configure logging
- Create performance dashboard
- Monitor Core Web Vitals

---

#### 8.3 Version Upgrades
**Objective:** Keep Hyvä updated

**Topics:**
- Semantic versioning
- Changelog review
- Testing upgrades
- Backward compatibility
- Migration guides

**Hands-on:**
- Review Hyvä changelog
- Test in staging
- Update dependencies
- Run regression tests

---

#### 8.4 Documentation
**Objective:** Document your customizations

**Topics:**
- Code comments
- README files
- Component documentation
- Setup instructions
- Troubleshooting guide

**Hands-on:**
- Document custom components
- Create developer guide
- Write user documentation
- Maintain changelog

---

## Recommended Learning Resources

### Official Documentation
- **Hyvä Docs**: https://docs.hyva.io
- **Hyvä GitLab**: https://gitlab.hyva.io/hyva-themes/magento2-default-theme
- **Hyvä Slack Community**: https://hyva-io.slack.com

### Alpine.js Resources
- **Alpine.js Docs**: https://alpinejs.dev
- **Alpine.js Examples**: https://alpinejs.dev/examples
- **Alpine Toolbox**: https://www.alpinetoolbox.com

### Tailwind CSS Resources
- **Tailwind Docs**: https://tailwindcss.com/docs
- **Tailwind UI**: https://tailwindui.com
- **Tailwind Components**: https://tailwindcomponents.com

### Magento Resources
- **Magento DevDocs**: https://devdocs.magento.com
- **Magento Stack Exchange**: https://magento.stackexchange.com

---

## Practice Projects

### Beginner Projects
1. **Simple Header Customization** - Change logo, colors, navigation
2. **Footer Widget** - Create custom footer with newsletter signup
3. **Product Badge** - Add "New" or "Sale" badges to products
4. **Custom CMS Block** - Create reusable content blocks

### Intermediate Projects
1. **Category Landing Page** - Build custom category page with hero and grid
2. **Product Quick View** - Modal popup with product details
3. **Wishlist Enhancement** - Add social sharing to wishlist
4. **Custom Form** - Contact form with validation

### Advanced Projects
1. **Full Custom Theme** - Build complete brand-specific theme
2. **Product Configurator** - Complex product configuration UI
3. **Store Locator** - Map-based store finder
4. **Custom Checkout Steps** - Add delivery date picker to checkout

---

## Troubleshooting Common Issues

### 1. Theme Not Appearing
```bash
php bin/magento cache:flush
php bin/magento setup:static-content:deploy -f
```

### 2. JavaScript Errors
- Check Alpine.js is loaded
- Verify no conflicting jQuery
- Check browser console for errors

### 3. Styling Issues
- Verify Tailwind config
- Check CSS is compiled
- Clear pub/static folder
- Rebuild static content

### 4. Module Conflicts
- Check compatibility modules
- Review module dependencies
- Test with modules disabled
- Create compat module

---

## Next Steps

Start with **Phase 1.1 - Understanding Hyvä Architecture** and tell me when you're ready to dive into that topic. I'll provide detailed guidance for each section as you progress.

For each phase, I can provide:
- Detailed code examples
- Hands-on exercises
- Real-world scenarios
- Testing strategies
- Best practices

**Let me know which section you'd like to start with!**
