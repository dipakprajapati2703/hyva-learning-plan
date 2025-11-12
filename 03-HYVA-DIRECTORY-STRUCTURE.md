# Hyvä Themes - Directory Structure & File Organization

**Complete guide to Hyvä's template, layout, and file organization patterns**

---

## Table of Contents

1. [Overview](#overview)
2. [Hyvä vs Standard Magento Structure](#hyvä-vs-standard-magento-structure)
3. [Layout XML Files](#layout-xml-files)
4. [Template Directory Structure](#template-directory-structure)
5. [ViewModels Directory](#viewmodels-directory)
6. [Complete Directory Examples](#complete-directory-examples)
7. [File Naming Conventions](#file-naming-conventions)
8. [Template Resolution Order](#template-resolution-order)
9. [Best Practices](#best-practices)
10. [Real-World Examples](#real-world-examples)
11. [Troubleshooting](#troubleshooting)

---

## Overview

### What Makes Hyvä Different?

Hyvä introduces a **parallel structure** for templates and layouts that allows Hyvä-specific files to coexist with standard Magento files. This enables:

- ✅ **Gradual migration** from Luma to Hyvä
- ✅ **Theme fallback** functionality
- ✅ **Clean separation** of Hyvä and Luma code
- ✅ **No conflicts** with standard Magento themes

### Key Concepts

**1. `hyva_` Layout XML Prefix**
- Hyvä-specific layouts use `hyva_` prefix
- Example: `hyva_catalog_product_view.xml`

**2. `hyva/` Template Directory**
- Hyvä templates can be placed in `hyva/` subdirectory
- Optional but recommended for organization

**3. Template Resolution Priority**
- Hyvä templates override standard templates
- Fallback to standard if Hyvä version doesn't exist

---

## Hyvä vs Standard Magento Structure

### Standard Magento Theme Structure

```
app/design/frontend/Vendor/theme-name/
├── Magento_Catalog/
│   ├── layout/
│   │   ├── catalog_product_view.xml
│   │   └── catalog_category_view.xml
│   └── templates/
│       └── product/
│           └── view/
│               ├── gallery.phtml
│               └── details.phtml
├── Magento_Checkout/
│   ├── layout/
│   │   └── checkout_index_index.xml
│   └── templates/
│       └── cart/
│           └── item/
│               └── default.phtml
└── web/
    ├── css/
    └── js/
```

---

### Hyvä Theme Structure

```
app/design/frontend/Vendor/hyva-theme/
├── Magento_Catalog/
│   ├── layout/
│   │   ├── catalog_product_view.xml              # Standard (optional)
│   │   ├── hyva_catalog_product_view.xml         # Hyvä-specific ✅
│   │   ├── catalog_category_view.xml             # Standard (optional)
│   │   └── hyva_catalog_category_view.xml        # Hyvä-specific ✅
│   └── templates/
│       ├── product/
│       │   └── view/
│       │       └── gallery.phtml                  # Standard fallback
│       └── hyva/                                  # Hyvä directory ✅
│           └── product/
│               └── view/
│                   └── gallery.phtml              # Hyvä version
├── Magento_Checkout/
│   ├── layout/
│   │   └── hyva_checkout_cart_index.xml          # Hyvä-specific
│   └── templates/
│       └── hyva/
│           └── cart/
│               └── item/
│                   └── default.phtml
├── Hyva_Theme/                                    # Hyvä module templates
│   └── templates/
│       ├── page/
│       │   └── js/
│       │       └── alpinejs.phtml
│       └── elements/
│           └── messages.phtml
└── web/
    └── tailwind/
        ├── tailwind.config.js
        ├── tailwind-source.css
        └── components/
```

---

## Layout XML Files

### Standard Layout XML

**File:** `Magento_Catalog/layout/catalog_product_view.xml`

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceContainer name="content">
            <block class="Magento\Catalog\Block\Product\View"
                   name="product.info"
                   template="Magento_Catalog::product/view.phtml">
            </block>
        </referenceContainer>
    </body>
</page>
```

**Used by:** Luma, Blank, and fallback pages

---

### Hyvä Layout XML

**File:** `Magento_Catalog/layout/hyva_catalog_product_view.xml`

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceContainer name="content">
            <block class="Magento\Catalog\Block\Product\View"
                   name="product.info"
                   template="Magento_Catalog::hyva/product/view.phtml">
                <arguments>
                    <argument name="view_model" xsi:type="object">
                        Hyva\Theme\ViewModel\ProductPage
                    </argument>
                </arguments>
            </block>
        </referenceContainer>
    </body>
</page>
```

**Key Differences:**
- ✅ File name starts with `hyva_`
- ✅ Template path points to `hyva/` subdirectory
- ✅ Often includes ViewModels
- ✅ Uses Alpine.js and Tailwind CSS

---

### Layout XML Naming Convention

| Page Type | Standard Layout | Hyvä Layout |
|-----------|----------------|-------------|
| **Product View** | `catalog_product_view.xml` | `hyva_catalog_product_view.xml` |
| **Category View** | `catalog_category_view.xml` | `hyva_catalog_category_view.xml` |
| **Cart Page** | `checkout_cart_index.xml` | `hyva_checkout_cart_index.xml` |
| **Customer Account** | `customer_account_index.xml` | `hyva_customer_account_index.xml` |
| **CMS Home** | `cms_index_index.xml` | `hyva_cms_index_index.xml` |
| **Search Results** | `catalogsearch_result_index.xml` | `hyva_catalogsearch_result_index.xml` |

**Pattern:** `hyva_` + `{module}_{controller}_{action}.xml`

---

## Template Directory Structure

### Option 1: Separate `hyva/` Directory (Recommended)

```
Magento_Catalog/
└── templates/
    ├── product/
    │   ├── list.phtml                    # Standard Magento
    │   └── view/
    │       └── gallery.phtml             # Standard Magento
    └── hyva/                             # Hyvä templates ✅
        └── product/
            ├── list.phtml                # Hyvä version
            └── view/
                └── gallery.phtml         # Hyvä version
```

**Advantages:**
- ✅ Clear separation between Hyvä and standard templates
- ✅ Easy to identify Hyvä-specific files
- ✅ No naming conflicts
- ✅ Better organization for large themes

**Template Path in Layout XML:**
```xml
<block template="Magento_Catalog::hyva/product/list.phtml"/>
```

---

### Option 2: Same Directory with Different Names

```
Magento_Catalog/
└── templates/
    └── product/
        ├── list.phtml                    # Standard Magento
        ├── list-hyva.phtml               # Hyvä version
        ├── view/
        │   ├── gallery.phtml             # Standard Magento
        │   └── gallery-hyva.phtml        # Hyvä version
```

**Advantages:**
- ✅ All related templates in same directory
- ✅ Easy to compare standard vs Hyvä

**Disadvantages:**
- ❌ Less organized for large projects
- ❌ Naming can become inconsistent

**Template Path in Layout XML:**
```xml
<block template="Magento_Catalog::product/list-hyva.phtml"/>
```

---

### Option 3: Complete Separation (Advanced)

```
app/design/frontend/Vendor/hyva-theme/
├── Magento_Catalog/
│   ├── layout/
│   │   └── hyva_catalog_product_view.xml
│   └── templates/
│       └── hyva/                         # Only Hyvä templates
│           └── product/
│               └── view.phtml

vendor/hyva-themes/magento2-default-theme/
└── Magento_Catalog/
    └── templates/
        └── product/                      # Hyvä base templates
            └── view.phtml
```

**Advantages:**
- ✅ Your theme only contains overrides
- ✅ Inherits from Hyvä default theme
- ✅ Minimal duplication

---

## ViewModels Directory

### ViewModels in Hyvä Themes

```
app/design/frontend/Vendor/hyva-theme/
└── Hyva_Theme/
    └── templates/
        └── elements/
            └── messages.phtml
```

**vs**

```
app/code/Vendor/HyvaExtension/
├── etc/
│   └── frontend/
│       └── di.xml
├── ViewModel/
│   ├── ProductBadge.php              # Custom ViewModel
│   └── CustomData.php                # Custom ViewModel
└── view/
    └── frontend/
        ├── layout/
        │   └── hyva_catalog_product_view.xml
        └── templates/
            └── hyva/
                └── product/
                    └── badge.phtml
```

**Key Points:**
- ViewModels go in **modules**, not themes
- ViewModels implement `ArgumentInterface`
- Injected via layout XML `<argument>` tag

---

## Complete Directory Examples

### Example 1: Custom Hyvä Theme (Child of Hyva/default)

```
app/design/frontend/Acme/storefront/
├── theme.xml                                  # Theme registration
├── registration.php                           # Theme registration
├── composer.json                              # Dependencies
├── etc/
│   └── view.xml                               # Image sizes, etc.
├── Magento_Catalog/
│   ├── layout/
│   │   ├── hyva_catalog_product_view.xml     # Product page layout
│   │   └── hyva_catalog_category_view.xml    # Category page layout
│   └── templates/
│       └── hyva/
│           ├── product/
│           │   ├── view.phtml                # Product view template
│           │   ├── view/
│           │   │   ├── gallery.phtml         # Image gallery
│           │   │   └── details.phtml         # Product details
│           │   └── list.phtml                # Product list item
│           └── category/
│               └── products.phtml            # Category products grid
├── Magento_Checkout/
│   ├── layout/
│   │   └── hyva_checkout_cart_index.xml
│   └── templates/
│       └── hyva/
│           └── cart/
│               └── item/
│                   └── default.phtml
├── Magento_Customer/
│   ├── layout/
│   │   ├── hyva_customer_account_index.xml
│   │   └── hyva_customer_account_login.xml
│   └── templates/
│       └── hyva/
│           └── account/
│               └── dashboard.phtml
├── Magento_Cms/
│   ├── layout/
│   │   └── hyva_cms_index_index.xml          # Homepage layout
│   └── templates/
│       └── hyva/
│           └── page/
│               └── homepage.phtml
├── Hyva_Theme/
│   └── templates/
│       ├── page/
│       │   ├── header.phtml                  # Custom header
│       │   └── footer.phtml                  # Custom footer
│       └── elements/
│           └── messages.phtml                # Custom messages
└── web/
    ├── tailwind/
    │   ├── tailwind.config.js                # Custom Tailwind config
    │   ├── tailwind-source.css               # Custom CSS
    │   └── components/
    │       ├── buttons.css                   # Button styles
    │       └── forms.css                     # Form styles
    ├── images/
    │   └── logo.svg
    └── fonts/
        └── custom-font.woff2
```

---

### Example 2: Hyvä-Compatible Custom Module

```
app/code/Acme/ProductBadge/
├── registration.php
├── composer.json
├── etc/
│   ├── module.xml
│   └── frontend/
│       └── di.xml                            # ViewModel injection
├── Model/
│   └── Badge.php                             # Business logic
├── ViewModel/
│   └── ProductBadge.php                      # ViewModel for templates
└── view/
    └── frontend/
        ├── layout/
        │   ├── catalog_product_view.xml      # Standard (for fallback)
        │   └── hyva_catalog_product_view.xml # Hyvä-specific ✅
        └── templates/
            ├── product/
            │   └── badge.phtml               # Standard template
            └── hyva/                         # Hyvä templates ✅
                └── product/
                    └── badge.phtml           # Hyvä template with Alpine.js
```

**`hyva_catalog_product_view.xml`:**
```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceContainer name="product.info.main">
            <block name="product.badge"
                   template="Acme_ProductBadge::hyva/product/badge.phtml"
                   before="-">
                <arguments>
                    <argument name="view_model" xsi:type="object">
                        Acme\ProductBadge\ViewModel\ProductBadge
                    </argument>
                </arguments>
            </block>
        </referenceContainer>
    </body>
</page>
```

**`hyva/product/badge.phtml`:**
```php
<?php
/** @var \Acme\ProductBadge\ViewModel\ProductBadge $viewModel */
$viewModel = $block->getViewModel();
$product = $viewModel->getProduct();
$badges = $viewModel->getBadges($product);
?>

<?php if ($badges): ?>
    <div class="product-badges flex gap-2 mb-4" x-data="productBadges()">
        <?php foreach ($badges as $badge): ?>
            <span class="<?= $escaper->escapeHtmlAttr($viewModel->getBadgeClasses($badge)) ?>">
                <?= $escaper->escapeHtml($badge['label']) ?>
            </span>
        <?php endforeach; ?>
    </div>

    <script>
        function productBadges() {
            return {
                init() {
                    console.log('Product badges initialized');
                }
            }
        }
    </script>
<?php endif; ?>
```

---

## File Naming Conventions

### Layout XML Files

**Pattern:** `hyva_{module}_{controller}_{action}.xml`

| Context | File Name |
|---------|-----------|
| Product Page | `hyva_catalog_product_view.xml` |
| Category Page | `hyva_catalog_category_view.xml` |
| Cart | `hyva_checkout_cart_index.xml` |
| Search Results | `hyva_catalogsearch_result_index.xml` |
| Customer Dashboard | `hyva_customer_account_index.xml` |
| Default Layout | `hyva_default.xml` |

---

### Template Files

**Option 1: Separate Directory (Recommended)**
```
templates/hyva/product/view.phtml
templates/hyva/category/products.phtml
templates/hyva/cart/item/default.phtml
```

**Option 2: Suffix Naming**
```
templates/product/view-hyva.phtml
templates/category/products-hyva.phtml
templates/cart/item/default-hyva.phtml
```

---

### ViewModel Files

**Pattern:** `{Vendor}\{Module}\ViewModel\{Name}`

```php
namespace Acme\ProductBadge\ViewModel;

use Magento\Framework\View\Element\Block\ArgumentInterface;

class ProductBadge implements ArgumentInterface
{
    // ViewModel implementation
}
```

**File Location:**
```
app/code/Acme/ProductBadge/ViewModel/ProductBadge.php
```

---

## Template Resolution Order

### How Hyvä Resolves Templates

**Priority Order (Highest to Lowest):**

1. **Custom Theme Hyvä Template**
   ```
   app/design/frontend/Acme/storefront/
   └── Magento_Catalog/templates/hyva/product/view.phtml
   ```

2. **Custom Theme Standard Template**
   ```
   app/design/frontend/Acme/storefront/
   └── Magento_Catalog/templates/product/view.phtml
   ```

3. **Parent Theme Hyvä Template**
   ```
   vendor/hyva-themes/magento2-default-theme/
   └── Magento_Catalog/templates/product/view.phtml
   ```

4. **Module Hyvä Template**
   ```
   app/code/Acme/Module/view/frontend/templates/hyva/product/view.phtml
   ```

5. **Module Standard Template**
   ```
   vendor/magento/module-catalog/view/frontend/templates/product/view.phtml
   ```

---

### Layout XML Resolution

**For Hyvä Pages:**

1. `hyva_{handle}.xml` in custom theme
2. `hyva_{handle}.xml` in parent theme (Hyva/default)
3. `{handle}.xml` as fallback

**For Fallback Pages (Luma):**

1. `{handle}.xml` in Luma theme
2. `{handle}.xml` in module

---

### Example Resolution Flow

**URL:** `http://store.com/product-name.html`

**Layout Handle:** `catalog_product_view`

**Hyvä Resolution:**
```
1. Check: app/design/frontend/Acme/storefront/Magento_Catalog/layout/hyva_catalog_product_view.xml ✅
2. Check: vendor/hyva-themes/magento2-default-theme/Magento_Catalog/layout/catalog_product_view.xml
3. Fallback: vendor/magento/module-catalog/view/frontend/layout/catalog_product_view.xml
```

**Template Resolution (from layout XML):**

If layout specifies: `template="Magento_Catalog::hyva/product/view.phtml"`

```
1. Check: app/design/frontend/Acme/storefront/Magento_Catalog/templates/hyva/product/view.phtml ✅
2. Check: vendor/hyva-themes/magento2-default-theme/Magento_Catalog/templates/product/view.phtml
3. Fallback: vendor/magento/module-catalog/view/frontend/templates/product/view.phtml
```

---

## Best Practices

### 1. Always Use `hyva_` Prefix for Layout XML

**Good:**
```
Magento_Catalog/layout/hyva_catalog_product_view.xml
```

**Bad:**
```
Magento_Catalog/layout/catalog_product_view.xml  # Will affect Luma!
```

---

### 2. Use `hyva/` Subdirectory for Templates

**Good:**
```
Magento_Catalog/templates/hyva/product/view.phtml
```

**Reasoning:**
- Clear separation from standard templates
- Easy to identify Hyvä-specific code
- Better organization

---

### 3. Always Include ViewModels in Hyvä Layouts

**Good:**
```xml
<block template="Vendor_Module::hyva/template.phtml">
    <arguments>
        <argument name="view_model" xsi:type="object">
            Vendor\Module\ViewModel\Data
        </argument>
    </arguments>
</block>
```

**Why:**
- Separates business logic from templates
- Makes templates testable
- Follows Hyvä best practices

---

### 4. Keep Hyvä and Standard Templates Separate

**Good:**
```
templates/
├── product/
│   └── view.phtml                  # Standard
└── hyva/
    └── product/
        └── view.phtml              # Hyvä
```

**Bad:**
```
templates/
└── product/
    ├── view.phtml                  # Standard
    └── view-hyva.phtml             # Hyvä (confusing naming)
```

---

### 5. Document Template Overrides

**Add comments to custom templates:**

```php
<?php
/**
 * Custom Hyvä Template: Product View
 *
 * Overrides: Hyva_Theme::product/view.phtml
 * Purpose: Add custom product badges and custom gallery
 * Dependencies: Acme_ProductBadge module
 *
 * @var \Magento\Catalog\Block\Product\View $block
 * @var \Hyva\Theme\ViewModel\ProductPage $viewModel
 */
$viewModel = $block->getViewModel();
$product = $viewModel->getProduct();
?>
```

---

### 6. Use Consistent Directory Structure

**Follow this pattern across all modules:**

```
Vendor_Module/
├── layout/
│   ├── default.xml                      # Standard
│   └── hyva_default.xml                 # Hyvä
└── templates/
    ├── standard/
    │   └── template.phtml
    └── hyva/
        └── template.phtml
```

---

### 7. Version Control Best Practices

**`.gitignore` for Hyvä themes:**

```gitignore
# Compiled Tailwind CSS
web/tailwind/tailwind.css

# Node modules
web/tailwind/node_modules/

# npm lock files
web/tailwind/package-lock.json

# Generated static content
pub/static/frontend/Vendor/theme-name/
```

---

## Real-World Examples

### Example 1: Custom Product Badge

**Directory Structure:**
```
app/code/Acme/ProductBadge/
├── ViewModel/
│   └── Badge.php
└── view/frontend/
    ├── layout/
    │   └── hyva_catalog_product_view.xml
    └── templates/
        └── hyva/
            └── badge.phtml
```

**Layout XML:**
```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceContainer name="product.info.media">
            <block name="product.badge"
                   template="Acme_ProductBadge::hyva/badge.phtml"
                   before="-">
                <arguments>
                    <argument name="view_model" xsi:type="object">
                        Acme\ProductBadge\ViewModel\Badge
                    </argument>
                </arguments>
            </block>
        </referenceContainer>
    </body>
</page>
```

**Template (`hyva/badge.phtml`):**
```php
<?php
/** @var \Acme\ProductBadge\ViewModel\Badge $badgeViewModel */
$badgeViewModel = $block->getViewModel();
$badges = $badgeViewModel->getProductBadges();
?>

<div class="product-badges absolute top-4 left-4 z-10 flex flex-col gap-2">
    <?php foreach ($badges as $badge): ?>
        <span class="badge px-3 py-1 rounded-full text-sm font-semibold
                     <?= $escaper->escapeHtmlAttr($badge['css_class']) ?>">
            <?= $escaper->escapeHtml($badge['label']) ?>
        </span>
    <?php endforeach; ?>
</div>
```

---

### Example 2: Custom Category Filter

**Directory Structure:**
```
app/design/frontend/Acme/storefront/
└── Magento_Catalog/
    ├── layout/
    │   └── hyva_catalog_category_view.xml
    └── templates/
        └── hyva/
            └── category/
                └── filters.phtml
```

**Layout XML:**
```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceContainer name="sidebar.main">
            <block name="category.filters.custom"
                   template="Magento_Catalog::hyva/category/filters.phtml"
                   before="-">
                <arguments>
                    <argument name="view_model" xsi:type="object">
                        Hyva\Theme\ViewModel\ProductList
                    </argument>
                </arguments>
            </block>
        </referenceContainer>
    </body>
</page>
```

**Template (`hyva/category/filters.phtml`):**
```php
<?php
/** @var \Hyva\Theme\ViewModel\ProductList $viewModel */
$viewModel = $block->getViewModel();
$filters = $viewModel->getFilters();
?>

<div class="category-filters"
     x-data="categoryFilters()"
     x-init="init()">

    <div class="filters-header flex justify-between items-center mb-4">
        <h3 class="text-lg font-semibold"><?= $escaper->escapeHtml(__('Filter By')) ?></h3>
        <button @click="clearAll()"
                class="text-sm text-primary hover:underline">
            <?= $escaper->escapeHtml(__('Clear All')) ?>
        </button>
    </div>

    <?php foreach ($filters as $filter): ?>
        <div class="filter-group mb-6">
            <h4 class="font-medium mb-2"><?= $escaper->escapeHtml($filter['label']) ?></h4>

            <?php foreach ($filter['options'] as $option): ?>
                <label class="flex items-center mb-2 cursor-pointer">
                    <input type="checkbox"
                           value="<?= $escaper->escapeHtmlAttr($option['value']) ?>"
                           @change="applyFilter('<?= $escaper->escapeJs($filter['code']) ?>', $event)"
                           class="mr-2">
                    <span><?= $escaper->escapeHtml($option['label']) ?></span>
                    <span class="ml-auto text-gray-500 text-sm">
                        (<?= $escaper->escapeHtml($option['count']) ?>)
                    </span>
                </label>
            <?php endforeach; ?>
        </div>
    <?php endforeach; ?>
</div>

<script>
function categoryFilters() {
    return {
        selectedFilters: {},

        init() {
            // Initialize from URL parameters
            this.loadFromUrl();
        },

        applyFilter(attribute, event) {
            const value = event.target.value;
            const isChecked = event.target.checked;

            if (!this.selectedFilters[attribute]) {
                this.selectedFilters[attribute] = [];
            }

            if (isChecked) {
                this.selectedFilters[attribute].push(value);
            } else {
                this.selectedFilters[attribute] = this.selectedFilters[attribute]
                    .filter(v => v !== value);
            }

            this.updateUrl();
            this.reloadProducts();
        },

        clearAll() {
            this.selectedFilters = {};
            this.updateUrl();
            this.reloadProducts();
        },

        loadFromUrl() {
            // Parse URL parameters
            const params = new URLSearchParams(window.location.search);
            // Implementation...
        },

        updateUrl() {
            // Update URL with selected filters
            // Implementation...
        },

        reloadProducts() {
            // Fetch and display filtered products
            // Implementation...
        }
    }
}
</script>
```

---

### Example 3: Custom Checkout Step

**Directory Structure:**
```
app/code/Acme/CustomCheckout/
├── ViewModel/
│   └── CheckoutStep.php
└── view/frontend/
    ├── layout/
    │   └── hyva_checkout_index_index.xml
    └── templates/
        └── hyva/
            └── checkout/
                └── custom-step.phtml
```

---

## Troubleshooting

### Issue 1: Template Not Loading

**Symptoms:**
- Hyvä template not being used
- Standard template loads instead

**Solutions:**

```bash
# 1. Check template path in layout XML
# Verify: template="Vendor_Module::hyva/path/to/template.phtml"

# 2. Verify file exists
ls -la app/design/frontend/Vendor/theme/Module/templates/hyva/path/to/template.phtml

# 3. Clear cache
php bin/magento cache:flush

# 4. Remove generated files
rm -rf generated/* var/view_preprocessed/*

# 5. Deploy static content
php bin/magento setup:static-content:deploy -f

# 6. Enable template hints
php bin/magento dev:template-hints:enable
```

---

### Issue 2: Layout XML Not Applied

**Symptoms:**
- `hyva_` layout XML not loading
- Block not appearing on page

**Solutions:**

```bash
# 1. Check file naming
# Must be: hyva_{module}_{controller}_{action}.xml

# 2. Verify XML syntax
xmllint app/design/frontend/Vendor/theme/Module/layout/hyva_*.xml

# 3. Check layout handle
# Add to template for debugging:
<?php echo $block->getNameInLayout(); ?>

# 4. Clear layout cache
php bin/magento cache:clean layout

# 5. Check module is enabled
php bin/magento module:status | grep Module
```

---

### Issue 3: ViewModel Not Available

**Symptoms:**
- `$block->getViewModel()` returns null
- "Call to a member function on null" error

**Solutions:**

```bash
# 1. Verify ViewModel is injected in layout XML
<argument name="view_model" xsi:type="object">
    Vendor\Module\ViewModel\Name
</argument>

# 2. Check ViewModel implements ArgumentInterface
namespace Vendor\Module\ViewModel;
use Magento\Framework\View\Element\Block\ArgumentInterface;
class Name implements ArgumentInterface { }

# 3. Verify class exists
ls -la app/code/Vendor/Module/ViewModel/Name.php

# 4. Regenerate DI
php bin/magento setup:di:compile

# 5. Check for PHP errors
tail -f var/log/system.log
```

---

### Issue 4: Wrong Template Loads in Fallback

**Symptoms:**
- Luma pages load Hyvä templates
- Fallback pages look broken

**Solutions:**

```bash
# 1. Verify fallback configuration
php bin/magento config:show hyva_theme_fallback/general/enable
php bin/magento config:show hyva_theme_fallback/general/list_part_of_url

# 2. Check layout XML naming
# Hyvä: hyva_catalog_product_view.xml
# Standard: catalog_product_view.xml (no hyva_ prefix)

# 3. Separate templates properly
templates/
├── product/view.phtml           # Standard (for fallback)
└── hyva/product/view.phtml      # Hyvä only

# 4. Clear all caches
php bin/magento cache:flush

# 5. Deploy static content for both themes
php bin/magento setup:static-content:deploy -f --theme=Hyva/default --theme=Magento/luma
```

---

### Issue 5: Tailwind Classes Not Working

**Symptoms:**
- Tailwind classes have no effect
- Styles not applied

**Solutions:**

```bash
# 1. Compile Tailwind CSS
cd vendor/hyva-themes/magento2-default-theme/web/tailwind/
npm install
npm run build

# 2. Check tailwind.config.js includes your templates
module.exports = {
    content: [
        '../templates/**/*.phtml',
        '../../../../app/design/frontend/**/*.phtml',
        // ...
    ]
}

# 3. Deploy static content
cd /var/www/html/m246p8
php bin/magento setup:static-content:deploy -f

# 4. Clear browser cache (Ctrl + Shift + R)

# 5. Check CSS is loading
# Browser DevTools > Network > Check for tailwind.css (200 status)
```

---

## Quick Reference

### File Naming Cheatsheet

| File Type | Standard | Hyvä |
|-----------|----------|------|
| **Layout XML** | `{handle}.xml` | `hyva_{handle}.xml` |
| **Template** | `path/to/template.phtml` | `hyva/path/to/template.phtml` |
| **ViewModel** | N/A (not required) | Required for complex logic |

---

### Directory Structure Cheatsheet

```
app/design/frontend/Vendor/theme/
├── Module/
│   ├── layout/
│   │   ├── {handle}.xml                # Standard (optional)
│   │   └── hyva_{handle}.xml           # Hyvä (required) ✅
│   └── templates/
│       ├── path/
│       │   └── template.phtml          # Standard (optional)
│       └── hyva/                       # Hyvä directory ✅
│           └── path/
│               └── template.phtml      # Hyvä (required)
```

---

### Template Path Examples

```php
// Layout XML
template="Magento_Catalog::hyva/product/view.phtml"
         └─────┬─────┘  └──────┬──────────────┘
            Module          File Path

// Resolves to:
app/design/frontend/Vendor/theme/
└── Magento_Catalog/templates/hyva/product/view.phtml
```

---

## Related Documentation

- **[HYVA-LEARNING-PLAN.md](./HYVA-LEARNING-PLAN.md)** - Complete learning path
- **[HYVA-THEME-SETUP.md](./HYVA-THEME-SETUP.md)** - Installation guide
- **[HYVA-TAILWIND-ALPINE-SETUP.md](./HYVA-TAILWIND-ALPINE-SETUP.md)** - Technical setup
- **[HYVA-EXTENSIONS-GUIDE.md](./HYVA-EXTENSIONS-GUIDE.md)** - Extension development
- **[README.md](./README.md)** - Main documentation hub

---

## Support Resources

| Resource | URL |
|----------|-----|
| **Official Docs** | https://docs.hyva.io |
| **Hyvä GitLab** | https://gitlab.hyva.io |
| **Hyvä Slack** | https://hyva-io.slack.com |
| **Support Email** | support@hyva.io |

---

**Happy Building! 🚀**

*Last Updated: 2025-11-12*
*Version: 1.0*
