# Building Hyvä-Compatible Magento 2 Extensions

**Complete guide for creating custom extensions that work seamlessly with Hyvä Themes**

---

## Table of Contents

1. [Extension Development Fundamentals](#extension-development-fundamentals)
2. [Hyvä-Compatible Extension Structure](#hyva-compatible-extension-structure)
3. [Understanding Hyvä Layout Naming Conventions](#understanding-hyvä-layout-naming-conventions)
4. [Step-by-Step: Creating a Hyvä-Compatible Extension](#step-by-step-creating-a-hyvä-compatible-extension)
5. [ViewModels for Business Logic](#viewmodels-for-business-logic)
6. [GraphQL Integration](#graphql-integration)
7. [Creating Compatibility Modules](#creating-compatibility-modules)
8. [Testing & Best Practices](#testing--best-practices)

---

## Extension Development Fundamentals

### What Makes an Extension Hyvä-Compatible?

**Key Requirements:**
1. ✅ No RequireJS dependencies
2. ✅ No KnockoutJS/UI Components
3. ✅ No jQuery (or minimal, isolated usage)
4. ✅ Uses Alpine.js for interactivity
5. ✅ Uses Tailwind CSS for styling
6. ✅ Provides ViewModels for business logic
7. ✅ Optional: GraphQL support

---

## Hyvä-Compatible Extension Structure

### Complete Directory Structure

```
app/code/Vendor/ModuleName/
├── registration.php                          # Module registration
├── composer.json                             # Composer configuration
├── etc/
│   ├── module.xml                            # Module declaration
│   ├── di.xml                                # Dependency injection
│   ├── acl.xml                               # Admin ACL permissions
│   ├── adminhtml/
│   │   ├── system.xml                        # Admin configuration
│   │   └── menu.xml                          # Admin menu
│   └── frontend/
│       ├── di.xml                            # Frontend DI
│       ├── routes.xml                        # Frontend routes
│       └── events.xml                        # Event observers
│
├── Model/                                    # Business logic
│   ├── Config.php                            # Configuration reader
│   ├── ResourceModel/
│   │   ├── Item.php                          # Resource model
│   │   └── Item/
│   │       └── Collection.php                # Collection
│   └── Item.php                              # Model
│
├── Block/                                    # Block classes
│   ├── Widget.php                            # Widget block
│   └── Product/
│       └── Badge.php                         # Product badge block
│
├── ViewModel/                                # ViewModels (Hyvä pattern)
│   ├── Badge.php                             # Badge ViewModel
│   ├── Config.php                            # Config ViewModel
│   └── Product/
│       └── CustomData.php                    # Product data ViewModel
│
├── Controller/
│   ├── Index/
│   │   └── Index.php                         # Frontend controller
│   └── Adminhtml/
│       └── Item/
│           ├── Index.php                     # Admin grid
│           ├── Edit.php                      # Admin edit
│           ├── Save.php                      # Admin save
│           └── Delete.php                    # Admin delete
│
├── Helper/
│   └── Data.php                              # Helper class
│
├── Observer/
│   └── ProductSaveAfter.php                  # Event observer
│
├── Plugin/
│   └── ProductPlugin.php                     # Plugin (interceptor)
│
├── Api/                                      # API interfaces
│   ├── Data/
│   │   └── ItemInterface.php                 # Data interface
│   └── ItemRepositoryInterface.php           # Repository interface
│
├── Ui/                                       # Admin UI components
│   └── Component/
│       └── Listing/
│           └── Column/
│               └── Actions.php
│
├── view/
│   ├── adminhtml/                            # Admin area
│   │   ├── layout/
│   │   │   ├── vendor_modulename_item_index.xml
│   │   │   └── vendor_modulename_item_edit.xml
│   │   ├── ui_component/
│   │   │   ├── vendor_modulename_item_listing.xml
│   │   │   └── vendor_modulename_item_form.xml
│   │   ├── templates/
│   │   └── web/
│   │       ├── css/
│   │       └── js/
│   │
│   └── frontend/                             # Frontend (Hyvä-compatible)
│       ├── layout/
│       │   ├── default.xml                   # Global layout (standard Magento)
│       │   ├── catalog_product_view.xml      # Product page (standard Magento)
│       │   ├── hyva_custom_feature.xml       # Hyvä-specific custom layout
│       │   └── vendor_modulename_index_index.xml
│       │
│       ├── templates/                        # Hyvä-compatible templates
│       │   ├── product/
│       │   │   └── badge.phtml               # Alpine.js + Tailwind
│       │   ├── widget/
│       │   │   └── slider.phtml              # Custom widget
│       │   └── page/
│       │       └── custom.phtml
│       │
│       └── web/
│           ├── css/
│           │   └── source/
│           │       └── _module.less          # Fallback styling
│           └── js/
│               └── custom.js                 # Vanilla JS / Alpine components
│
├── i18n/
│   └── en_US.csv                             # Translations
│
├── Setup/
│   ├── InstallSchema.php                     # Install DB schema
│   ├── UpgradeSchema.php                     # Upgrade DB schema
│   ├── InstallData.php                       # Install data
│   ├── UpgradeData.php                       # Upgrade data
│   └── Patch/
│       └── Data/
│           └── AddCustomAttribute.php        # Data patch
│
└── Test/
    ├── Unit/                                 # Unit tests
    │   ├── Model/
    │   │   └── ItemTest.php
    │   └── ViewModel/
    │       └── BadgeTest.php
    └── Integration/                          # Integration tests
        └── Model/
            └── ItemRepositoryTest.php
```

---

## Understanding Hyvä Layout Naming Conventions

### Standard vs Hyvä-Specific Layouts

**Important:** There are TWO types of layout files in Hyvä:

#### 1. Standard Magento Layout Files (Most Common)

These are **regular Magento layout files** that work in BOTH Luma and Hyvä themes:

```
view/frontend/layout/
├── default.xml                        # Standard Magento
├── catalog_product_view.xml           # Standard Magento
├── catalog_category_view.xml          # Standard Magento
├── checkout_cart_index.xml            # Standard Magento
└── vendor_modulename_index_index.xml  # Standard Magento
```

**When to use:** 99% of the time for your custom extensions

**Example:** `catalog_product_view.xml` (NO hyva_ prefix)

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceContainer name="product.info.media">
            <block name="vendor.product.badge"
                   template="Vendor_ProductBadge::product/badge.phtml">
                <arguments>
                    <argument name="view_model" xsi:type="object">
                        Vendor\ProductBadge\ViewModel\Badge
                    </argument>
                </arguments>
            </block>
        </referenceContainer>
    </body>
</page>
```

#### 2. Hyvä-Specific Layout Files (Special Cases Only)

These layout files are **ONLY loaded in Hyvä themes** and are prefixed with `hyva_`:

```
Hyvä Theme Module has these built-in:
├── hyva_modal.xml                     # Modal component definitions
├── hyva_form_validation.xml           # Form validation setup
├── hyva_form_validation_date.xml      # Date validation
├── hyva_form_validation_files.xml     # File upload validation
└── hyva_product_slider.xml            # Product slider component
```

**When to use:** ONLY when you need to:
- Add Hyvä-specific components that shouldn't load in Luma
- Create reusable Hyvä layout handles
- Add modal definitions
- Register Alpine.js plugins globally

**Example:** `hyva_custom_component.xml`

```xml
<?xml version="1.0"?>
<!-- This ONLY loads in Hyvä theme, NOT in Luma -->
<layout xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <container name="hyva.custom.component">
        <block name="hyva.custom.alpine.component"
               template="Vendor_Module::hyva/component.phtml"/>
    </container>
</layout>
```

---

### Key Naming Rules

| Scenario | Layout File Name | Applies To |
|----------|-----------------|------------|
| Override product page | `catalog_product_view.xml` | Both Luma & Hyvä |
| Override category page | `catalog_category_view.xml` | Both Luma & Hyvä |
| Override cart page | `checkout_cart_index.xml` | Both Luma & Hyvä |
| Custom controller page | `vendor_module_controller_action.xml` | Both Luma & Hyvä |
| Hyvä-only component | `hyva_custom_feature.xml` | Hyvä ONLY |
| Hyvä-only modal | `hyva_custom_modal.xml` | Hyvä ONLY |

---

### Common Mistake to Avoid

❌ **WRONG:** Using `hyva_` prefix for standard pages
```
view/frontend/layout/
└── hyva_catalog_product_view.xml  # ❌ WRONG! This won't work!
```

✅ **CORRECT:** Use standard Magento names
```
view/frontend/layout/
└── catalog_product_view.xml       # ✅ CORRECT! Works in Hyvä
```

---

### When You See `hyva_` Prefix

You'll see `hyva_` prefix in these situations:

1. **Hyvä Core Module** (`vendor/hyva-themes/magento2-theme-module/`):
   - `hyva_modal.xml` - Modal definitions
   - `hyva_form_validation.xml` - Form validation
   - `hyva_product_slider.xml` - Product slider

2. **Hyvä Default Theme** (`vendor/hyva-themes/magento2-default-theme/`):
   - `hyva_checkout_cart_item_renderers.xml` - Custom cart renderers
   - `hyva_checkout_cart_index.xml` - Checkout customizations

3. **Your Custom Hyvä-Only Features**:
   - `hyva_custom_widget.xml` - If you need Hyvä-specific widget
   - `hyva_alpine_plugin.xml` - If registering global Alpine plugin

---

### Summary

**For Your Extensions (99% of cases):**
```xml
<!-- Use standard Magento layout names -->
view/frontend/layout/
├── default.xml
├── catalog_product_view.xml           ✅ Use this!
├── catalog_category_view.xml          ✅ Use this!
└── vendor_module_index_index.xml      ✅ Use this!

<!-- NOT these -->
├── hyva_catalog_product_view.xml      ❌ Don't use!
└── hyva_default.xml                   ❌ Don't use!
```

**Only use `hyva_` prefix when:**
- Creating reusable Hyvä components
- Adding Hyvä-specific features that shouldn't load in Luma
- Following official Hyvä patterns for modals/plugins

---

### Real-World Examples

#### Example 1: Product Badge Extension ✅ CORRECT

**Goal:** Add custom badges to product pages

**Layout:** `view/frontend/layout/catalog_product_view.xml`

```xml
<?xml version="1.0"?>
<!-- Standard Magento layout - works in both Luma and Hyvä -->
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceContainer name="product.info.media">
            <block name="vendor.product.badge"
                   template="Vendor_ProductBadge::product/badge.phtml"/>
        </referenceContainer>
    </body>
</page>
```

**Why this works:** Hyvä theme automatically loads all standard Magento layouts. Your template uses Alpine.js + Tailwind, making it Hyvä-compatible.

---

#### Example 2: Hyvä-Only Modal Component ✅ CORRECT (Special Case)

**Goal:** Create reusable modal component ONLY for Hyvä theme

**Layout:** `view/frontend/layout/hyva_custom_modal.xml`

```xml
<?xml version="1.0"?>
<!-- Hyvä-specific layout - only loads in Hyvä theme -->
<layout xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <container name="hyva.custom.modal">
        <block name="hyva.modal.quick.view"
               template="Vendor_Module::modal/quick-view.phtml"/>
    </container>
</layout>
```

**Usage in other layouts:**
```xml
<referenceContainer name="before.body.end">
    <updateHandle>hyva_custom_modal</updateHandle>
</referenceContainer>
```

**Why use `hyva_` here:** This modal uses Hyvä-specific Alpine.js patterns and shouldn't load in Luma theme at all.

---

#### Example 3: Category Page Customization ✅ CORRECT

**Goal:** Add custom filters to category pages

**Layout:** `view/frontend/layout/catalog_category_view.xml` (NOT `hyva_catalog_category_view.xml`)

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceContainer name="sidebar.main">
            <block name="vendor.category.filters"
                   template="Vendor_Module::category/filters.phtml">
                <arguments>
                    <argument name="view_model" xsi:type="object">
                        Vendor\Module\ViewModel\CategoryFilters
                    </argument>
                </arguments>
            </block>
        </referenceContainer>
    </body>
</page>
```

---

### Quick Decision Tree

```
Do I need to add functionality to a standard Magento page?
    ├─ YES → Use standard Magento layout name
    │         Example: catalog_product_view.xml ✅
    │
    └─ NO → Am I creating a Hyvä-specific reusable component?
            ├─ YES → Use hyva_ prefix
            │         Example: hyva_custom_modal.xml ✅
            │
            └─ NO → Use standard Magento layout name
                      Example: vendor_module_index_index.xml ✅
```

---

## Step-by-Step: Creating a Hyvä-Compatible Extension

### Example: Product Badge Extension

We'll create a complete extension that displays custom badges on product pages.

---

### Step 1: Create Module Registration

**File:** `app/code/Vendor/ProductBadge/registration.php`

```php
<?php
/**
 * Module Registration
 *
 * @category  Vendor
 * @package   Vendor_ProductBadge
 * @author    Your Name <your.email@company.com>
 * @copyright Copyright (c) 2025 Vendor
 */

declare(strict_types=1);

use Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(
    ComponentRegistrar::MODULE,
    'Vendor_ProductBadge',
    __DIR__
);
```

---

### Step 2: Create composer.json

**File:** `app/code/Vendor/ProductBadge/composer.json`

```json
{
    "name": "vendor/module-product-badge",
    "description": "Custom product badges for Hyvä themes",
    "type": "magento2-module",
    "version": "1.0.0",
    "license": "proprietary",
    "authors": [
        {
            "name": "Your Name",
            "email": "your.email@company.com"
        }
    ],
    "require": {
        "php": "^8.1|^8.2",
        "magento/framework": "^103.0",
        "magento/module-catalog": "^104.0"
    },
    "suggest": {
        "hyva-themes/magento2-theme-module": "For Hyvä theme compatibility"
    },
    "autoload": {
        "files": [
            "registration.php"
        ],
        "psr-4": {
            "Vendor\\ProductBadge\\": ""
        }
    }
}
```

---

### Step 3: Create module.xml

**File:** `app/code/Vendor/ProductBadge/etc/module.xml`

```xml
<?xml version="1.0"?>
<!--
/**
 * Module Declaration
 *
 * @category  Vendor
 * @package   Vendor_ProductBadge
 */
-->
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="Vendor_ProductBadge" setup_version="1.0.0">
        <sequence>
            <module name="Magento_Catalog"/>
            <module name="Hyva_Theme"/> <!-- Optional: only if strictly required -->
        </sequence>
    </module>
</config>
```

**Note:** Only add `Hyva_Theme` in sequence if your module absolutely requires it. Most Hyvä-compatible modules don't need this dependency.

---

### Step 4: Create Admin Configuration

**File:** `app/code/Vendor/ProductBadge/etc/adminhtml/system.xml`

```xml
<?xml version="1.0"?>
<!--
/**
 * System Configuration
 */
-->
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:module:Magento_Config:etc/system_file.xsd">
    <system>
        <tab id="vendor_extensions" translate="label" sortOrder="100">
            <label>Vendor Extensions</label>
        </tab>

        <section id="vendor_productbadge" translate="label" sortOrder="10" showInDefault="1" showInWebsite="1" showInStore="1">
            <label>Product Badges</label>
            <tab>vendor_extensions</tab>
            <resource>Vendor_ProductBadge::config</resource>

            <group id="general" translate="label" sortOrder="10" showInDefault="1" showInWebsite="1" showInStore="1">
                <label>General Settings</label>

                <field id="enabled" translate="label comment" type="select" sortOrder="10" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Enable Product Badges</label>
                    <source_model>Magento\Config\Model\Config\Source\Yesno</source_model>
                    <comment>Enable/disable custom product badges</comment>
                </field>

                <field id="position" translate="label" type="select" sortOrder="20" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Badge Position</label>
                    <source_model>Vendor\ProductBadge\Model\Config\Source\Position</source_model>
                    <depends>
                        <field id="enabled">1</field>
                    </depends>
                </field>

                <field id="custom_css" translate="label comment" type="textarea" sortOrder="30" showInDefault="1" showInWebsite="1" showInStore="1">
                    <label>Custom CSS</label>
                    <comment>Add custom CSS for badges</comment>
                    <depends>
                        <field id="enabled">1</field>
                    </depends>
                </field>
            </group>
        </section>
    </system>
</config>
```

---

### Step 5: Create Config Source Model

**File:** `app/code/Vendor/ProductBadge/Model/Config/Source/Position.php`

```php
<?php
/**
 * Badge Position Source Model
 *
 * @category  Vendor
 * @package   Vendor_ProductBadge
 */

declare(strict_types=1);

namespace Vendor\ProductBadge\Model\Config\Source;

use Magento\Framework\Data\OptionSourceInterface;

class Position implements OptionSourceInterface
{
    public const POSITION_TOP_LEFT = 'top-left';
    public const POSITION_TOP_RIGHT = 'top-right';
    public const POSITION_BOTTOM_LEFT = 'bottom-left';
    public const POSITION_BOTTOM_RIGHT = 'bottom-right';

    /**
     * @inheritDoc
     */
    public function toOptionArray(): array
    {
        return [
            ['value' => self::POSITION_TOP_LEFT, 'label' => __('Top Left')],
            ['value' => self::POSITION_TOP_RIGHT, 'label' => __('Top Right')],
            ['value' => self::POSITION_BOTTOM_LEFT, 'label' => __('Bottom Left')],
            ['value' => self::POSITION_BOTTOM_RIGHT, 'label' => __('Bottom Right')],
        ];
    }
}
```

---

### Step 6: Create ViewModel (Hyvä Pattern)

**File:** `app/code/Vendor/ProductBadge/ViewModel/Badge.php`

```php
<?php
/**
 * Badge ViewModel
 * Contains all business logic for product badges
 * Compatible with Hyvä themes
 *
 * @category  Vendor
 * @package   Vendor_ProductBadge
 */

declare(strict_types=1);

namespace Vendor\ProductBadge\ViewModel;

use Magento\Catalog\Api\Data\ProductInterface;
use Magento\Catalog\Model\Product;
use Magento\Framework\App\Config\ScopeConfigInterface;
use Magento\Framework\Stdlib\DateTime\DateTime;
use Magento\Framework\View\Element\Block\ArgumentInterface;
use Magento\Store\Model\ScopeInterface;

class Badge implements ArgumentInterface
{
    private const XML_PATH_ENABLED = 'vendor_productbadge/general/enabled';
    private const XML_PATH_POSITION = 'vendor_productbadge/general/position';

    /**
     * @param ScopeConfigInterface $scopeConfig
     * @param DateTime $dateTime
     */
    public function __construct(
        private readonly ScopeConfigInterface $scopeConfig,
        private readonly DateTime $dateTime
    ) {
    }

    /**
     * Check if badges are enabled
     */
    public function isEnabled(): bool
    {
        return $this->scopeConfig->isSetFlag(
            self::XML_PATH_ENABLED,
            ScopeInterface::SCOPE_STORE
        );
    }

    /**
     * Get badge position
     */
    public function getPosition(): string
    {
        return (string) $this->scopeConfig->getValue(
            self::XML_PATH_POSITION,
            ScopeInterface::SCOPE_STORE
        );
    }

    /**
     * Get position CSS classes
     */
    public function getPositionClasses(string $position = null): string
    {
        $position = $position ?: $this->getPosition();

        return match ($position) {
            'top-left' => 'top-4 left-4',
            'top-right' => 'top-4 right-4',
            'bottom-left' => 'bottom-4 left-4',
            'bottom-right' => 'bottom-4 right-4',
            default => 'top-4 left-4',
        };
    }

    /**
     * Get all badges for a product
     *
     * @param ProductInterface|Product $product
     * @return array
     */
    public function getBadges(ProductInterface $product): array
    {
        if (!$this->isEnabled()) {
            return [];
        }

        $badges = [];

        // Check if product is new
        if ($this->isNewProduct($product)) {
            $badges[] = [
                'label' => __('New'),
                'type' => 'new',
                'classes' => 'bg-green-500 text-white',
            ];
        }

        // Check if product is on sale
        if ($this->isOnSale($product)) {
            $discount = $this->getDiscountPercentage($product);
            $badges[] = [
                'label' => __('-%1%', round($discount)),
                'type' => 'sale',
                'classes' => 'bg-red-500 text-white',
            ];
        }

        // Custom badge attribute
        $customBadge = $product->getData('custom_badge');
        if ($customBadge) {
            $badges[] = [
                'label' => $customBadge,
                'type' => 'custom',
                'classes' => 'bg-primary-500 text-white',
            ];
        }

        // Low stock badge
        if ($this->isLowStock($product)) {
            $badges[] = [
                'label' => __('Low Stock'),
                'type' => 'low_stock',
                'classes' => 'bg-orange-500 text-white',
            ];
        }

        return $badges;
    }

    /**
     * Check if product is new
     */
    private function isNewProduct(ProductInterface $product): bool
    {
        $newsFromDate = $product->getNewsFromDate();
        $newsToDate = $product->getNewsToDate();

        if (!$newsFromDate && !$newsToDate) {
            return false;
        }

        $now = $this->dateTime->gmtTimestamp();
        $fromTimestamp = $newsFromDate ? strtotime($newsFromDate) : 0;
        $toTimestamp = $newsToDate ? strtotime($newsToDate) : PHP_INT_MAX;

        return $now >= $fromTimestamp && $now <= $toTimestamp;
    }

    /**
     * Check if product is on sale
     */
    private function isOnSale(ProductInterface $product): bool
    {
        $finalPrice = (float) $product->getFinalPrice();
        $regularPrice = (float) $product->getPrice();

        return $finalPrice < $regularPrice && $finalPrice > 0;
    }

    /**
     * Get discount percentage
     */
    private function getDiscountPercentage(ProductInterface $product): float
    {
        $finalPrice = (float) $product->getFinalPrice();
        $regularPrice = (float) $product->getPrice();

        if ($regularPrice <= 0) {
            return 0;
        }

        return (($regularPrice - $finalPrice) / $regularPrice) * 100;
    }

    /**
     * Check if product has low stock
     */
    private function isLowStock(ProductInterface $product): bool
    {
        $stockItem = $product->getExtensionAttributes()?->getStockItem();

        if (!$stockItem) {
            return false;
        }

        $qty = (float) $stockItem->getQty();
        $minQty = (float) $stockItem->getMinQty();
        $lowStockThreshold = 10; // Configure this

        return $qty > $minQty && $qty <= $lowStockThreshold;
    }

    /**
     * Get badge HTML classes
     */
    public function getBadgeClasses(array $badge): string
    {
        $baseClasses = 'px-3 py-1 text-sm font-semibold rounded-md shadow-md';
        $typeClasses = $badge['classes'] ?? '';

        return "{$baseClasses} {$typeClasses}";
    }

    /**
     * Format badge label
     */
    public function formatLabel(string $label): string
    {
        return strtoupper($label);
    }
}
```

**Key Features:**
- ✅ Implements `ArgumentInterface` (required for ViewModels)
- ✅ All business logic encapsulated
- ✅ No direct template logic
- ✅ Type-safe with readonly properties (PHP 8.1+)
- ✅ Uses dependency injection
- ✅ Returns structured data for templates

---

### Step 7: Create Frontend Layout XML

**File:** `app/code/Vendor/ProductBadge/view/frontend/layout/catalog_product_view.xml`

```xml
<?xml version="1.0"?>
<!--
/**
 * Product Page Layout
 * Adds badge block to product media container
 */
-->
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceContainer name="product.info.media">
            <block name="vendor.product.badge"
                   template="Vendor_ProductBadge::product/badge.phtml"
                   before="-">
                <arguments>
                    <argument name="view_model" xsi:type="object">
                        Vendor\ProductBadge\ViewModel\Badge
                    </argument>
                </arguments>
            </block>
        </referenceContainer>
    </body>
</page>
```

---

### Step 8: Create Hyvä-Compatible Template

**File:** `app/code/Vendor/ProductBadge/view/frontend/templates/product/badge.phtml`

```phtml
<?php
/**
 * Product Badge Template
 * Hyvä-compatible: Uses Alpine.js and Tailwind CSS
 *
 * @var $block \Magento\Framework\View\Element\Template
 * @var $escaper \Magento\Framework\Escaper
 * @var \Vendor\ProductBadge\ViewModel\Badge $badgeViewModel
 */

use Vendor\ProductBadge\ViewModel\Badge;

/** @var Badge $badgeViewModel */
$badgeViewModel = $block->getViewModel();

// Get current product
$product = $block->getData('product');
if (!$product) {
    // Try to get from registry (fallback)
    $product = $block->getData('registry')->registry('current_product');
}

if (!$product || !$badgeViewModel->isEnabled()) {
    return;
}

$badges = $badgeViewModel->getBadges($product);

if (empty($badges)) {
    return;
}

$positionClasses = $badgeViewModel->getPositionClasses();
?>

<!-- Badge Container with Alpine.js -->
<div class="absolute z-10 flex flex-col gap-2 <?= $escaper->escapeHtmlAttr($positionClasses) ?>"
     x-data="productBadges()"
     x-init="init()"
     x-cloak>

    <?php foreach ($badges as $index => $badge): ?>
        <!-- Single Badge -->
        <div class="<?= $escaper->escapeHtmlAttr($badgeViewModel->getBadgeClasses($badge)) ?>"
             x-show="badges[<?= (int) $index ?>].visible"
             x-transition:enter="transition ease-out duration-300"
             x-transition:enter-start="opacity-0 transform scale-90"
             x-transition:enter-end="opacity-100 transform scale-100"
             x-transition:leave="transition ease-in duration-200"
             x-transition:leave-start="opacity-100"
             x-transition:leave-end="opacity-0"
             data-badge-type="<?= $escaper->escapeHtmlAttr($badge['type']) ?>"
             role="status"
             aria-label="<?= $escaper->escapeHtmlAttr(__('Product badge: %1', $badge['label'])) ?>">

            <?= $escaper->escapeHtml($badge['label']) ?>
        </div>
    <?php endforeach; ?>
</div>

<script>
/**
 * Product Badges Alpine.js Component
 */
function productBadges() {
    return {
        badges: <?= /* @noEscape */ json_encode(array_map(function($badge) {
            return ['visible' => true, 'type' => $badge['type']];
        }, $badges)) ?>,

        init() {
            // Animate badges in sequence
            this.badges.forEach((badge, index) => {
                setTimeout(() => {
                    badge.visible = true;
                }, index * 100);
            });

            // Optional: Track badge impression
            this.trackBadgeImpression();
        },

        trackBadgeImpression() {
            // Example: Send analytics event
            if (window.gtag) {
                window.gtag('event', 'badge_view', {
                    'product_id': <?= (int) $product->getId() ?>,
                    'badge_types': this.badges.map(b => b.type).join(',')
                });
            }
        }
    }
}
</script>

<style>
/* Optional: Custom styles for specific badges */
[data-badge-type="new"] {
    animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}

@keyframes pulse {
    0%, 100% {
        opacity: 1;
    }
    50% {
        opacity: .8;
    }
}
</style>
```

**Hyvä Best Practices:**
- ✅ Uses ViewModel for all logic
- ✅ Alpine.js for interactivity
- �wind CSS for styling
- ✅ Accessibility (ARIA labels, roles)
- ✅ Animations with Alpine.js transitions
- ✅ Inline Alpine component (scoped)
- ✅ Progressive enhancement

---

### Step 9: Add Dependency Injection Configuration

**File:** `app/code/Vendor/ProductBadge/etc/frontend/di.xml`

```xml
<?xml version="1.0"?>
<!--
/**
 * Dependency Injection Configuration
 */
-->
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">

    <!-- Add ViewModel to list of allowed ViewModels -->
    <type name="Hyva\Theme\ViewModel\ViewModelRegistry">
        <arguments>
            <argument name="viewModels" xsi:type="array">
                <item name="vendor_productbadge_badge" xsi:type="string">
                    Vendor\ProductBadge\ViewModel\Badge
                </item>
            </argument>
        </arguments>
    </type>

    <!-- Plugin example: Add custom data to product -->
    <type name="Magento\Catalog\Api\ProductRepositoryInterface">
        <plugin name="vendor_productbadge_add_badge_data"
                type="Vendor\ProductBadge\Plugin\ProductRepositoryPlugin"
                sortOrder="10"/>
    </type>
</config>
```

---

### Step 10: Create ACL Configuration

**File:** `app/code/Vendor/ProductBadge/etc/acl.xml`

```xml
<?xml version="1.0"?>
<!--
/**
 * Access Control List
 */
-->
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Acl/etc/acl.xsd">
    <acl>
        <resources>
            <resource id="Magento_Backend::admin">
                <resource id="Magento_Backend::stores">
                    <resource id="Magento_Backend::stores_settings">
                        <resource id="Magento_Config::config">
                            <resource id="Vendor_ProductBadge::config"
                                      title="Product Badges Configuration"
                                      sortOrder="100"/>
                        </resource>
                    </resource>
                </resource>
            </resource>
        </resources>
    </acl>
</config>
```

---

### Step 11: Install Module

```bash
# 1. Enable module
php bin/magento module:enable Vendor_ProductBadge

# 2. Run setup
php bin/magento setup:upgrade

# 3. Compile DI
php bin/magento setup:di:compile

# 4. Deploy static content
php bin/magento setup:static-content:deploy -f

# 5. Clear cache
php bin/magento cache:flush

# 6. Verify module is enabled
php bin/magento module:status Vendor_ProductBadge
```

---

### Step 12: Configure in Admin

1. Navigate to: **Stores > Configuration > Vendor Extensions > Product Badges**
2. Set "Enable Product Badges" to **Yes**
3. Choose "Badge Position"
4. Save configuration
5. Clear cache

---

## ViewModels for Business Logic

### Why ViewModels in Hyvä?

**Traditional Magento 2:**
```phtml
<?php
// Business logic mixed with presentation
$product = $block->getProduct();
$finalPrice = $product->getFinalPrice();
$regularPrice = $product->getPrice();

if ($finalPrice < $regularPrice) {
    $discount = (($regularPrice - $finalPrice) / $regularPrice) * 100;
    echo "Save " . round($discount) . "%";
}
?>
```

**Hyvä Pattern with ViewModel:**
```phtml
<?php
$badgeViewModel = $block->getViewModel();
$badges = $badgeViewModel->getBadges($product);

foreach ($badges as $badge) {
    echo $badge['label'];
}
?>
```

**Benefits:**
- ✅ Testable (unit tests for ViewModels)
- ✅ Reusable across templates
- ✅ Clean separation of concerns
- ✅ Type-safe
- ✅ IDE autocomplete support

---

### ViewModel Best Practices

1. **Implement ArgumentInterface**
```php
use Magento\Framework\View\Element\Block\ArgumentInterface;

class MyViewModel implements ArgumentInterface
{
    // ...
}
```

2. **Use Dependency Injection (Constructor)**
```php
public function __construct(
    private readonly ScopeConfigInterface $scopeConfig,
    private readonly ProductRepository $productRepository
) {
}
```

3. **Return Structured Data**
```php
public function getProductData(Product $product): array
{
    return [
        'id' => $product->getId(),
        'name' => $product->getName(),
        'badges' => $this->getBadges($product),
        'meta' => [
            'is_new' => $this->isNew($product),
            'is_sale' => $this->isOnSale($product),
        ],
    ];
}
```

4. **Keep Methods Public and Well-Named**
```php
// Good
public function getFormattedPrice(Product $product): string

// Bad
private function doStuff($p): mixed
```

5. **Add Type Hints**
```php
public function getBadges(ProductInterface $product): array
{
    // Return type is clear
}
```

---

## GraphQL Integration

### Adding GraphQL Support to Your Extension

**File:** `app/code/Vendor/ProductBadge/etc/schema.graphqls`

```graphql
type Query {
    productBadges(
        sku: String!
    ): [ProductBadge] @resolver(class: "Vendor\\ProductBadge\\Model\\Resolver\\ProductBadges") @doc(description: "Get badges for a product")
}

type ProductBadge {
    label: String @doc(description: "Badge label")
    type: String @doc(description: "Badge type (new, sale, custom)")
    classes: String @doc(description: "CSS classes")
}

interface ProductInterface {
    badges: [ProductBadge] @resolver(class: "Vendor\\ProductBadge\\Model\\Resolver\\ProductBadgesField") @doc(description: "Product badges")
}
```

**Resolver:** `app/code/Vendor/ProductBadge/Model/Resolver/ProductBadgesField.php`

```php
<?php

declare(strict_types=1);

namespace Vendor\ProductBadge\Model\Resolver;

use Magento\Catalog\Model\Product;
use Magento\Framework\GraphQl\Config\Element\Field;
use Magento\Framework\GraphQl\Query\ResolverInterface;
use Magento\Framework\GraphQl\Schema\Type\ResolveInfo;
use Vendor\ProductBadge\ViewModel\Badge;

class ProductBadgesField implements ResolverInterface
{
    public function __construct(
        private readonly Badge $badgeViewModel
    ) {
    }

    public function resolve(
        Field $field,
        $context,
        ResolveInfo $info,
        array $value = null,
        array $args = null
    ) {
        if (!isset($value['model'])) {
            return [];
        }

        /** @var Product $product */
        $product = $value['model'];

        return $this->badgeViewModel->getBadges($product);
    }
}
```

**GraphQL Query:**
```graphql
query {
    products(filter: {sku: {eq: "24-MB01"}}) {
        items {
            name
            sku
            badges {
                label
                type
                classes
            }
        }
    }
}
```

---

## Creating Compatibility Modules

### What is a Compatibility Module?

When a third-party extension doesn't work with Hyvä out of the box, you create a "compat" module to bridge the gap.

---

### Structure of Compatibility Module

```
app/code/Vendor/HyvaModuleNameCompat/
├── registration.php
├── composer.json
├── etc/
│   └── module.xml
└── view/frontend/
    ├── layout/
    │   └── [original_module_layouts].xml      # Override layouts
    └── templates/
        └── [original_module_templates].phtml   # Hyvä-compatible versions
```

**Example:** `app/code/Vendor/HyvaSlickSliderCompat/`

---

### Step-by-Step: Slick Slider Compat Module

**Problem:** Third-party module uses jQuery + Slick Slider (not Hyvä-compatible)

**Solution:** Create compat module with Alpine.js slider

**File:** `app/code/Vendor/HyvaSlickSliderCompat/registration.php`

```php
<?php

declare(strict_types=1);

use Magento\Framework\Component\ComponentRegistrar;

ComponentRegistrar::register(
    ComponentRegistrar::MODULE,
    'Vendor_HyvaSlickSliderCompat',
    __DIR__
);
```

**File:** `app/code/Vendor/HyvaSlickSliderCompat/etc/module.xml`

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:noNamespaceSchemaLocation="urn:magento:framework:Module/etc/module.xsd">
    <module name="Vendor_HyvaSlickSliderCompat">
        <sequence>
            <module name="ThirdParty_SlickSlider"/>
            <module name="Hyva_Theme"/>
        </sequence>
    </module>
</config>
```

**File:** `app/code/Vendor/HyvaSlickSliderCompat/view/frontend/layout/default.xml`

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <!-- Remove jQuery Slick Slider -->
        <referenceBlock name="thirdparty.slick.slider" remove="true"/>

        <!-- Add Hyvä-compatible slider -->
        <referenceContainer name="content">
            <block name="hyva.slider"
                   template="Vendor_HyvaSlickSliderCompat::slider.phtml"
                   after="thirdparty.slick.slider"/>
        </referenceContainer>
    </body>
</page>
```

**File:** `app/code/Vendor/HyvaSlickSliderCompat/view/frontend/templates/slider.phtml`

```phtml
<?php
/**
 * Hyvä-Compatible Slider
 * Replaces jQuery Slick Slider with Alpine.js
 */
?>
<div x-data="hyvaSlider()"
     x-init="init()"
     class="relative overflow-hidden">

    <!-- Slides Container -->
    <div class="flex transition-transform duration-500 ease-in-out"
         :style="`transform: translateX(-${currentSlide * 100}%)`">

        <?php foreach ($block->getSlides() as $slide): ?>
            <div class="w-full flex-shrink-0">
                <img src="<?= $escaper->escapeUrl($slide->getImage()) ?>"
                     alt="<?= $escaper->escapeHtmlAttr($slide->getTitle()) ?>"
                     class="w-full h-auto"/>
            </div>
        <?php endforeach; ?>
    </div>

    <!-- Navigation -->
    <button @click="prev()"
            class="absolute left-4 top-1/2 transform -translate-y-1/2 bg-white rounded-full p-2 shadow-lg">
        ← Prev
    </button>

    <button @click="next()"
            class="absolute right-4 top-1/2 transform -translate-y-1/2 bg-white rounded-full p-2 shadow-lg">
        Next →
    </button>

    <!-- Dots -->
    <div class="absolute bottom-4 left-1/2 transform -translate-x-1/2 flex gap-2">
        <template x-for="(slide, index) in slides" :key="index">
            <button @click="goToSlide(index)"
                    class="w-3 h-3 rounded-full transition-all"
                    :class="currentSlide === index ? 'bg-primary-500 w-6' : 'bg-gray-300'">
            </button>
        </template>
    </div>
</div>

<script>
function hyvaSlider() {
    return {
        currentSlide: 0,
        slides: <?= /* @noEscape */ json_encode($block->getSlides()) ?>,
        autoplayInterval: null,

        init() {
            this.startAutoplay();
        },

        next() {
            this.currentSlide = (this.currentSlide + 1) % this.slides.length;
        },

        prev() {
            this.currentSlide = this.currentSlide === 0
                ? this.slides.length - 1
                : this.currentSlide - 1;
        },

        goToSlide(index) {
            this.currentSlide = index;
        },

        startAutoplay() {
            this.autoplayInterval = setInterval(() => {
                this.next();
            }, 5000);
        }
    }
}
</script>
```

---

## Testing & Best Practices

### Unit Testing ViewModels

**File:** `app/code/Vendor/ProductBadge/Test/Unit/ViewModel/BadgeTest.php`

```php
<?php

declare(strict_types=1);

namespace Vendor\ProductBadge\Test\Unit\ViewModel;

use Magento\Catalog\Model\Product;
use Magento\Framework\App\Config\ScopeConfigInterface;
use Magento\Framework\Stdlib\DateTime\DateTime;
use PHPUnit\Framework\TestCase;
use Vendor\ProductBadge\ViewModel\Badge;

class BadgeTest extends TestCase
{
    private Badge $viewModel;
    private ScopeConfigInterface $scopeConfig;
    private DateTime $dateTime;

    protected function setUp(): void
    {
        $this->scopeConfig = $this->createMock(ScopeConfigInterface::class);
        $this->dateTime = $this->createMock(DateTime::class);

        $this->viewModel = new Badge(
            $this->scopeConfig,
            $this->dateTime
        );
    }

    public function testIsEnabledReturnsTrueWhenConfigured(): void
    {
        $this->scopeConfig->expects($this->once())
            ->method('isSetFlag')
            ->willReturn(true);

        $this->assertTrue($this->viewModel->isEnabled());
    }

    public function testGetBadgesReturnsEmptyArrayWhenDisabled(): void
    {
        $this->scopeConfig->expects($this->once())
            ->method('isSetFlag')
            ->willReturn(false);

        $product = $this->createMock(Product::class);

        $this->assertEmpty($this->viewModel->getBadges($product));
    }

    // More tests...
}
```

**Run Tests:**
```bash
vendor/bin/phpunit -c dev/tests/unit/phpunit.xml.dist app/code/Vendor/ProductBadge/Test/Unit/
```

---

### Best Practices Checklist

**Code Quality:**
- [ ] Strict types declared (`declare(strict_types=1);`)
- [ ] Readonly properties where possible
- [ ] Type hints on all methods
- [ ] PHPDoc comments
- [ ] PSR-12 coding standards

**Hyvä Compatibility:**
- [ ] No RequireJS dependencies
- [ ] No KnockoutJS/UI Components
- [ ] Uses Alpine.js for interactivity
- [ ] Uses Tailwind CSS for styling
- [ ] ViewModels for business logic
- [ ] Works without jQuery

**Performance:**
- [ ] Minimal JavaScript
- [ ] Lazy loading where appropriate
- [ ] Optimized images
- [ ] Cached data where possible
- [ ] Efficient database queries

**Accessibility:**
- [ ] ARIA labels
- [ ] Keyboard navigation
- [ ] Semantic HTML
- [ ] Focus management
- [ ] Screen reader support

**Testing:**
- [ ] Unit tests for ViewModels
- [ ] Integration tests for critical paths
- [ ] Manual testing on real Hyvä store
- [ ] Cross-browser testing
- [ ] Performance testing

---

## Summary

**Key Takeaways:**

1. **Structure:** Follow Magento 2 conventions, add Hyvä-specific patterns
2. **ViewModels:** Use for ALL business logic (testable, reusable)
3. **Templates:** Alpine.js + Tailwind CSS, no jQuery
4. **Compatibility:** Create compat modules for third-party extensions
5. **Testing:** Unit test ViewModels, integration test critical flows

**Complete Extension Checklist:**
- ✅ registration.php
- ✅ composer.json
- ✅ etc/module.xml
- ✅ etc/adminhtml/system.xml (config)
- ✅ etc/acl.xml (permissions)
- ✅ etc/frontend/di.xml (DI)
- ✅ ViewModel/ (business logic)
- ✅ view/frontend/layout/*.xml
- ✅ view/frontend/templates/*.phtml (Alpine.js + Tailwind)
- ✅ Test/Unit/ (tests)
- ✅ i18n/en_US.csv (translations)

**Next Steps:**
1. Build your first Hyvä-compatible extension
2. Create compat modules for existing extensions
3. Contribute to Hyvä ecosystem
4. Share your learnings with the community

---

**Resources:**
- Hyvä Docs: https://docs.hyva.io
- Hyvä GitLab: https://gitlab.hyva.io
- Magento DevDocs: https://devdocs.magento.com
- Alpine.js: https://alpinejs.dev
- Tailwind CSS: https://tailwindcss.com
