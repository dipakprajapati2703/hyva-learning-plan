# Hyvä ViewModels - Complete Reference

**Complete Catalog of All 71 Hyvä ViewModels with Usage Examples**

---

## Table of Contents

1. [Overview](#overview)
2. [What Are ViewModels?](#what-are-viewmodels)
3. [Product & Catalog ViewModels](#product--catalog-viewmodels)
4. [Cart & Checkout ViewModels](#cart--checkout-viewmodels)
5. [Customer ViewModels](#customer-viewmodels)
6. [Navigation & UI ViewModels](#navigation--ui-viewmodels)
7. [Icon ViewModels](#icon-viewmodels)
8. [Media & Images ViewModels](#media--images-viewmodels)
9. [Configuration ViewModels](#configuration-viewmodels)
10. [Forms & Validation ViewModels](#forms--validation-viewmodels)
11. [Utilities ViewModels](#utilities-viewmodels)
12. [Sales ViewModels](#sales-viewmodels)
13. [Usage Patterns](#usage-patterns)
14. [Best Practices](#best-practices)

---

## Overview

Hyvä Themes provides **71 ViewModels** to separate business logic from templates. All ViewModels are located in:

```
vendor/hyva-themes/magento2-theme-module/src/ViewModel/
```

### Why ViewModels?

**ViewModels provide:**
- ✅ **Business logic separation** from templates
- ✅ **Reusable code** across multiple templates
- ✅ **Testable logic** via unit tests
- ✅ **Type safety** with PHP type declarations
- ✅ **Cache tag management** via IdentityInterface
- ✅ **Clean templates** without complex PHP logic

---

## What Are ViewModels?

### ViewModel Structure

All Hyvä ViewModels implement `Magento\Framework\View\Element\Block\ArgumentInterface`:

```php
<?php
declare(strict_types=1);

namespace Hyva\Theme\ViewModel;

use Magento\Framework\View\Element\Block\ArgumentInterface;

class ExampleViewModel implements ArgumentInterface
{
    public function getData(): string
    {
        return 'Example data';
    }
}
```

### How to Use ViewModels

#### 1. Register in Layout XML

```xml
<block name="my.block" template="Vendor_Module::template.phtml">
    <arguments>
        <argument name="productViewModel" xsi:type="object">
            Hyva\Theme\ViewModel\CurrentProduct
        </argument>
    </arguments>
</block>
```

#### 2. Access in Template

```php
<?php
/** @var \Hyva\Theme\ViewModel\CurrentProduct $productViewModel */
$productViewModel = $block->getData('productViewModel');
$product = $productViewModel->get();
?>

<h1><?= $escaper->escapeHtml($product->getName()) ?></h1>
```

---

## Product & Catalog ViewModels

### 1. CurrentProduct

**File:** `ViewModel/CurrentProduct.php`

**Purpose:** Access and manage the current product on product detail pages

**Key Methods:**

```php
// Get current product
public function get(): ?ProductInterface

// Set current product
public function set(ProductInterface $product): void

// Check if product exists
public function exists(): bool

// Get cache identities
public function getIdentities(): array
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\CurrentProduct $productViewModel */
$productViewModel = $block->getData('productViewModel');

if ($productViewModel->exists()) {
    $product = $productViewModel->get();
    ?>
    <div class="product-info">
        <h1><?= $escaper->escapeHtml($product->getName()) ?></h1>
        <p>SKU: <?= $escaper->escapeHtml($product->getSku()) ?></p>
        <p>Price: <?= $product->getFinalPrice() ?></p>
    </div>
    <?php
}
?>
```

### 2. CurrentCategory

**File:** `ViewModel/CurrentCategory.php`

**Purpose:** Access the current category on category pages

**Key Methods:**

```php
// Get current category
public function get(): ?CategoryInterface

// Check if category exists
public function exists(): bool
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\CurrentCategory $categoryViewModel */
$categoryViewModel = $block->getData('categoryViewModel');

if ($categoryViewModel->exists()) {
    $category = $categoryViewModel->get();
    ?>
    <h1><?= $escaper->escapeHtml($category->getName()) ?></h1>
    <div class="category-description">
        <?= $category->getDescription() ?>
    </div>
    <?php
}
?>
```

### 3. ProductList

**File:** `ViewModel/ProductList.php`

**Purpose:** Manage product collections for listing pages

**Key Methods:**

```php
// Get product collection
public function getLoadedProductCollection(): Collection

// Get products array
public function getProducts(): array

// Get product count
public function getProductCount(): int

// Add filter
public function addFieldToFilter(string $field, $condition): self
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\ProductList $productList */
$productList = $block->getData('productList');
$products = $productList->getProducts();
?>

<div class="product-grid grid grid-cols-2 md:grid-cols-4 gap-4">
    <?php foreach ($products as $product): ?>
        <div class="product-item">
            <h3><?= $escaper->escapeHtml($product->getName()) ?></h3>
            <p class="price"><?= $product->getFinalPrice() ?></p>
        </div>
    <?php endforeach; ?>
</div>

<p>Total: <?= $productList->getProductCount() ?> products</p>
```

### 4. ProductListItem

**File:** `ViewModel/ProductListItem.php`

**Purpose:** Product list item data and rendering

**Key Methods:**

```php
// Get product URL
public function getProductUrl(ProductInterface $product): string

// Get image data
public function getImageData(ProductInterface $product, string $imageId): array

// Get add to cart URL
public function getAddToCartUrl(ProductInterface $product): string

// Get product rating summary
public function getRatingSummary(ProductInterface $product): float
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\ProductListItem $itemViewModel */
$itemViewModel = $block->getData('itemViewModel');
$product = $block->getData('product');
?>

<div class="product-card">
    <a href="<?= $escaper->escapeUrl($itemViewModel->getProductUrl($product)) ?>">
        <img src="<?= $itemViewModel->getImageData($product, 'product_list_image')['url'] ?>"
             alt="<?= $escaper->escapeHtmlAttr($product->getName()) ?>">
    </a>
    <h3><?= $escaper->escapeHtml($product->getName()) ?></h3>
    <div class="rating">
        <?= $itemViewModel->getRatingSummary($product) ?>%
    </div>
    <button data-post='{"action":"<?= $itemViewModel->getAddToCartUrl($product) ?>"}'>
        Add to Cart
    </button>
</div>
```

### 5. ProductPage

**File:** `ViewModel/ProductPage.php`

**Purpose:** Product detail page specific functionality

**Key Methods:**

```php
// Get product
public function getProduct(): ProductInterface

// Get product image
public function getProductImage(string $imageId): string

// Check if reviews enabled
public function canReview(): bool

// Get breadcrumbs
public function getBreadcrumbs(): array
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\ProductPage $productPage */
$productPage = $block->getData('productPage');
$product = $productPage->getProduct();
?>

<div class="product-detail-page">
    <div class="breadcrumbs">
        <?php foreach ($productPage->getBreadcrumbs() as $crumb): ?>
            <a href="<?= $crumb['url'] ?>"><?= $crumb['label'] ?></a>
        <?php endforeach; ?>
    </div>

    <img src="<?= $productPage->getProductImage('product_page_main') ?>"
         alt="<?= $escaper->escapeHtmlAttr($product->getName()) ?>">

    <?php if ($productPage->canReview()): ?>
        <div class="reviews-section">
            <!-- Reviews content -->
        </div>
    <?php endif; ?>
</div>
```

### 6. ProductPrice

**File:** `ViewModel/ProductPrice.php`

**Purpose:** Price formatting and currency handling

**Key Methods:**

```php
// Format price
public function format(float $price): string

// Get currency symbol
public function currency(): string

// Get formatted price with currency
public function formatPrice(float $price, bool $includeContainer = true): string
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\ProductPrice $priceViewModel */
$priceViewModel = $block->getData('priceViewModel');
$product = $block->getData('product');

$regularPrice = $product->getPrice();
$finalPrice = $product->getFinalPrice();
?>

<div class="product-price">
    <?php if ($regularPrice > $finalPrice): ?>
        <span class="old-price line-through">
            <?= $priceViewModel->format($regularPrice) ?>
        </span>
        <span class="special-price text-red-600 font-bold">
            <?= $priceViewModel->format($finalPrice) ?>
        </span>
    <?php else: ?>
        <span class="regular-price">
            <?= $priceViewModel->format($finalPrice) ?>
        </span>
    <?php endif; ?>
</div>
```

### 7. ProductStockItem

**File:** `ViewModel/ProductStockItem.php`

**Purpose:** Stock information and availability

**Key Methods:**

```php
// Get stock item
public function getStockItem(ProductInterface $product): StockItemInterface

// Get quantity
public function getQty(ProductInterface $product): float

// Check if in stock
public function isInStock(ProductInterface $product): bool

// Get stock status message
public function getStockStatus(ProductInterface $product): string
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\ProductStockItem $stockViewModel */
$stockViewModel = $block->getData('stockViewModel');
$product = $block->getData('product');
?>

<div class="stock-info">
    <?php if ($stockViewModel->isInStock($product)): ?>
        <span class="text-green-600">
            In Stock (<?= $stockViewModel->getQty($product) ?> available)
        </span>
    <?php else: ?>
        <span class="text-red-600">Out of Stock</span>
    <?php endif; ?>
    <p><?= $stockViewModel->getStockStatus($product) ?></p>
</div>
```

### 8. ProductAttributes

**File:** `ViewModel/ProductAttributes.php`

**Purpose:** Product attribute management

**Key Methods:**

```php
// Get attribute from group
public function getAttributeFromGroup(string $groupCode, string $attributeCode): ?Attribute

// Get all groups
public function getAllGroups(): array

// Get attributes by group
public function getAttributesByGroup(string $groupCode): array
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\ProductAttributes $attributesViewModel */
$attributesViewModel = $block->getData('attributesViewModel');
?>

<div class="product-attributes">
    <?php foreach ($attributesViewModel->getAllGroups() as $group): ?>
        <h3><?= $group['label'] ?></h3>
        <dl class="attribute-list">
            <?php foreach ($attributesViewModel->getAttributesByGroup($group['code']) as $attribute): ?>
                <dt><?= $attribute->getLabel() ?></dt>
                <dd><?= $attribute->getValue() ?></dd>
            <?php endforeach; ?>
        </dl>
    <?php endforeach; ?>
</div>
```

### 9. ProductCompare

**File:** `ViewModel/ProductCompare.php`

**Purpose:** Product comparison functionality

**Key Methods:**

```php
// Get compare list
public function getCompareList(): array

// Get compare URL
public function getCompareUrl(): string

// Get item count
public function getItemCount(): int
```

### 10. ProductAlert

**File:** `ViewModel/ProductAlert.php`

**Purpose:** Stock and price alerts

**Key Methods:**

```php
// Check if enabled
public function isEnabled(): bool

// Get alert URL
public function getAlertUrl(string $type): string
```

### 11. RecentlyViewedProducts

**File:** `ViewModel/RecentlyViewedProducts.php`

**Purpose:** Recently viewed products

**Key Methods:**

```php
// Get recently viewed products
public function getRecentlyViewedProducts(int $limit = 5): array

// Get lifetime in seconds
public function getLifetime(): int
```

### 12. CustomOption

**File:** `ViewModel/CustomOption.php`

**Purpose:** Product custom options

**Key Methods:**

```php
// Get options
public function getOptions(ProductInterface $product): array

// Get option by ID
public function getOption(int $optionId): ?Option

// Get option price
public function getPrice(Option $option): float
```

### 13. Slider

**File:** `ViewModel/Slider.php`

**Purpose:** Product slider functionality

**Key Methods:**

```php
// Get products
public function getProducts(): array

// Get item limit
public function getItemLimit(): int

// Get slider config
public function getSliderConfig(): array
```

---

## Cart & Checkout ViewModels

### 14. Cart

**File:** `ViewModel/Cart.php`

**Purpose:** Shopping cart functionality

**Key Methods:**

```php
// Get quote/cart
public function getQuote(): Quote

// Get item count
public function getItemCount(): int

// Get cart items
public function getItems(): array

// Get subtotal
public function getSubtotal(): float

// Get grand total
public function getGrandTotal(): float
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\Cart $cartViewModel */
$cartViewModel = $block->getData('cartViewModel');
$quote = $cartViewModel->getQuote();
?>

<div class="mini-cart">
    <p class="item-count">
        <?= $cartViewModel->getItemCount() ?> items
    </p>
    <div class="cart-items">
        <?php foreach ($cartViewModel->getItems() as $item): ?>
            <div class="cart-item">
                <span><?= $item->getName() ?></span>
                <span><?= $item->getQty() ?></span>
            </div>
        <?php endforeach; ?>
    </div>
    <div class="cart-totals">
        <p>Subtotal: <?= $cartViewModel->getSubtotal() ?></p>
        <p>Total: <?= $cartViewModel->getGrandTotal() ?></p>
    </div>
</div>
```

### 15. CartItem

**File:** `ViewModel/Cart/CartItem.php`

**Purpose:** Individual cart item management

**Key Methods:**

```php
// Get item
public function getItem(): QuoteItem

// Get product
public function getProduct(): ProductInterface

// Get item quantity
public function getQty(): float

// Get row total
public function getRowTotal(): float
```

---

## Customer ViewModels

### 16. Customer

**File:** `ViewModel/Customer.php`

**Purpose:** Customer session and data

**Key Methods:**

```php
// Get customer
public function getCustomer(): ?CustomerInterface

// Check if logged in
public function isLoggedIn(): bool

// Get customer ID
public function getId(): ?int

// Get customer name
public function getName(): string
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\Customer $customerViewModel */
$customerViewModel = $block->getData('customerViewModel');
?>

<div class="customer-info">
    <?php if ($customerViewModel->isLoggedIn()): ?>
        <p>Welcome, <?= $escaper->escapeHtml($customerViewModel->getName()) ?>!</p>
    <?php else: ?>
        <a href="/customer/account/login">Login</a>
    <?php endif; ?>
</div>
```

### 17. CustomerRegistration

**File:** `ViewModel/CustomerRegistration.php`

**Purpose:** Customer registration forms

**Key Methods:**

```php
// Get form data
public function getFormData(): array

// Get attribute metadata
public function getAttributeMetadata(string $attributeCode): array
```

### 18. CustomerSectionData

**File:** `ViewModel/CustomerSectionData.php`

**Purpose:** Customer section data (cart, wishlist, etc.)

**Key Methods:**

```php
// Get section data
public function getSection(string $sectionName): array

// Get all section data
public function getSectionData(): array
```

---

## Navigation & UI ViewModels

### 19. Navigation

**File:** `ViewModel/Navigation.php`

**Purpose:** Menu navigation rendering

**Key Methods:**

```php
// Get navigation data
public function getNavigation(): array

// Render navigation HTML
public function renderNavigation(): string

// Get menu items
public function getMenuItems(int $parentId = 0): array
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\Navigation $navViewModel */
$navViewModel = $block->getData('navViewModel');
?>

<nav class="main-navigation">
    <?= $navViewModel->renderNavigation() ?>
</nav>

<!-- OR manually render -->
<ul class="menu">
    <?php foreach ($navViewModel->getMenuItems() as $item): ?>
        <li>
            <a href="<?= $item['url'] ?>"><?= $item['name'] ?></a>
            <?php if (!empty($item['children'])): ?>
                <ul class="submenu">
                    <!-- Nested items -->
                </ul>
            <?php endif; ?>
        </li>
    <?php endforeach; ?>
</ul>
```

### 20. NavigationAsJson

**File:** `ViewModel/NavigationAsJson.php`

**Purpose:** Navigation data as JSON for JavaScript

**Key Methods:**

```php
// Get navigation as JSON string
public function getNavigationJson(): string
```

### 21. Modal

**File:** `ViewModel/Modal.php`

**Purpose:** Modal dialog functionality

**Key Methods:**

```php
// Render modal
public function render(string $modalId, string $content): string

// Get modal config
public function getModalConfig(string $modalId): array
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\Modal $modalViewModel */
$modalViewModel = $block->getData('modalViewModel');
?>

<button @click="$dispatch('open-modal', {id: 'product-details'})">
    View Details
</button>

<?= $modalViewModel->render('product-details', $block->getChildHtml('modal.content')) ?>
```

### 22. Footer

**File:** `ViewModel/Footer.php`

**Purpose:** Footer content management

**Key Methods:**

```php
// Get footer blocks
public function getBlocks(): array

// Get CMS block
public function getCmsBlock(string $identifier): string
```

### 23. PageConfig

**File:** `ViewModel/PageConfig.php`

**Purpose:** Page configuration and metadata

**Key Methods:**

```php
// Get page title
public function getTitle(): string

// Get meta description
public function getDescription(): string

// Get meta keywords
public function getKeywords(): string
```

### 24. BlockCache

**File:** `ViewModel/BlockCache.php`

**Purpose:** Block caching utilities

**Key Methods:**

```php
// Get cache key
public function getCacheKey(): string

// Get cache lifetime
public function getCacheLifetime(): int

// Get cache tags
public function getCacheTags(): array
```

---

## Icon ViewModels

### 25. Heroicons

**File:** `ViewModel/Heroicons.php`

**Purpose:** Base class for Heroicons rendering

**Key Methods:**

```php
// Render icon HTML
public function renderHtml(string $icon, string $classNames = '', array $attributes = []): string

// Get icon list
public function getIconList(): array

// Check if icon exists
public function hasIcon(string $icon): bool
```

### 26. HeroiconsOutline

**File:** `ViewModel/HeroiconsOutline.php`

**Purpose:** Render outline Heroicons

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\HeroiconsOutline $heroicons */
$heroicons = $block->getData('heroicons');
?>

<!-- Shopping cart icon -->
<?= $heroicons->renderHtml('shopping-cart', 'w-6 h-6 text-gray-600') ?>

<!-- Heart icon -->
<?= $heroicons->renderHtml('heart', 'w-5 h-5 text-red-500') ?>

<!-- User icon with custom attributes -->
<?= $heroicons->renderHtml('user', 'w-8 h-8', ['aria-label' => 'User profile']) ?>
```

### 27. HeroiconsSolid

**File:** `ViewModel/HeroiconsSolid.php`

**Purpose:** Render solid Heroicons

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\HeroiconsSolid $heroiconsSolid */
$heroiconsSolid = $block->getData('heroiconsSolid');
?>

<!-- Solid heart icon -->
<?= $heroiconsSolid->renderHtml('heart', 'w-6 h-6 text-red-600') ?>

<!-- Solid star icon -->
<?= $heroiconsSolid->renderHtml('star', 'w-5 h-5 text-yellow-400') ?>
```

### 28. LucideIcons

**File:** `ViewModel/LucideIcons.php`

**Purpose:** Render Lucide Icons (1000+ icons)

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\LucideIcons $lucide */
$lucide = $block->getData('lucide');
?>

<!-- Shopping bag icon -->
<?= $lucide->renderHtml('shopping-bag', 'w-6 h-6') ?>

<!-- Package icon -->
<?= $lucide->renderHtml('package', 'w-5 h-5 text-blue-600') ?>

<!-- Truck icon -->
<?= $lucide->renderHtml('truck', 'w-6 h-6 text-green-500') ?>
```

### 29. SvgIcons

**File:** `ViewModel/SvgIcons.php`

**Purpose:** Custom SVG icon management

**Key Methods:**

```php
// Render custom icon
public function renderHtml(string $iconId, string $classNames = ''): string

// Add custom icon
public function addIcon(string $iconId, string $svgContent): void

// Get all icons
public function getIcons(): array
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\SvgIcons $svgIcons */
$svgIcons = $block->getData('svgIcons');

// Add custom icon
$customSvg = '<svg viewBox="0 0 24 24"><path d="..."/></svg>';
$svgIcons->addIcon('my-custom-icon', $customSvg);
?>

<!-- Render custom icon -->
<?= $svgIcons->renderHtml('my-custom-icon', 'w-6 h-6 text-blue-500') ?>
```

---

## Media & Images ViewModels

### 30. Image

**File:** `ViewModel/Image.php`

**Purpose:** Image helper utilities

**Key Methods:**

```php
// Resize image
public function resize(string $imagePath, int $width, int $height): string

// Get image URL
public function getUrl(string $imagePath): string

// Get image data
public function getImageData(string $imagePath): array
```

### 31. Media

**File:** `ViewModel/Media.php`

**Purpose:** Media gallery management

**Key Methods:**

```php
// Get gallery images
public function getGalleryImages(ProductInterface $product): array

// Get media URL
public function getMediaUrl(string $file): string
```

---

## Configuration ViewModels

### 32. StoreConfig

**File:** `ViewModel/StoreConfig.php`

**Purpose:** Store configuration access

**Key Methods:**

```php
// Get config value
public function getStoreConfig(string $path): ?string

// Check if flag is set
public function isSetFlag(string $path): bool
```

**Usage Example:**

```php
<?php
/** @var \Hyva\Theme\ViewModel\StoreConfig $storeConfig */
$storeConfig = $block->getData('storeConfig');

$storeName = $storeConfig->getStoreConfig('general/store_information/name');
$isReviewsEnabled = $storeConfig->isSetFlag('catalog/review/active');
?>

<p>Store: <?= $escaper->escapeHtml($storeName) ?></p>

<?php if ($isReviewsEnabled): ?>
    <div class="reviews"><!-- Reviews --></div>
<?php endif; ?>
```

### 33. Currency

**File:** `ViewModel/Currency.php`

**Purpose:** Currency formatting and handling

**Key Methods:**

```php
// Format amount
public function format(float $amount): string

// Get current currency
public function getCurrentCurrency(): string

// Get currency symbol
public function getSymbol(): string
```

### 34. Locale

**File:** `ViewModel/Locale.php`

**Purpose:** Locale information

**Key Methods:**

```php
// Get locale code
public function getLocale(): string

// Get locale data
public function getLocaleData(): array
```

### 35. Store

**File:** `ViewModel/Store.php`

**Purpose:** Store information

**Key Methods:**

```php
// Get store
public function getStore(): StoreInterface

// Get store ID
public function getStoreId(): int

// Get store code
public function getStoreCode(): string
```

### 36. StoreSwitcher

**File:** `ViewModel/StoreSwitcher.php`

**Purpose:** Store/language switcher

**Key Methods:**

```php
// Get stores
public function getStores(): array

// Get current store
public function getCurrentStore(): StoreInterface

// Get switch URL
public function getSwitchUrl(int $storeId): string
```

---

## Forms & Validation ViewModels

### 37. ReCaptcha

**File:** `ViewModel/ReCaptcha.php`

**Purpose:** Google ReCaptcha integration

**Key Methods:**

```php
// Check if enabled
public function isEnabled(): bool

// Get site key
public function getSiteKey(): string

// Get ReCaptcha HTML
public function getRecaptchaHtml(): string
```

### 38. EmailToFriend

**File:** `ViewModel/EmailToFriend.php`

**Purpose:** Email to friend functionality

**Key Methods:**

```php
// Check if can email
public function canEmailToFriend(): bool

// Get max recipients
public function getMaxRecipients(): int
```

### 39. SendFriend

**File:** `ViewModel/SendFriend.php`

**Purpose:** Send to friend form

**Key Methods:**

```php
// Get send URL
public function getSendUrl(): string

// Check if can send
public function canSend(): bool
```

---

## Utilities ViewModels

### 40. Cookie

**File:** `ViewModel/Cookie.php`

**Purpose:** Cookie management

**Key Methods:**

```php
// Get cookie value
public function getCookie(string $name): ?string

// Check if cookie exists
public function hasCookie(string $name): bool
```

### 41. Date

**File:** `ViewModel/Date.php`

**Purpose:** Date formatting

**Key Methods:**

```php
// Format date
public function format(string $date, string $format = 'Y-m-d'): string

// Get timezone
public function getTimezone(): string
```

### 42. Directory

**File:** `ViewModel/Directory.php`

**Purpose:** Directory information (countries, regions)

**Key Methods:**

```php
// Get countries
public function getCountries(): array

// Get regions by country
public function getRegions(string $countryId): array
```

### 43. DeployMode

**File:** `ViewModel/DeployMode.php`

**Purpose:** Deployment mode detection

**Key Methods:**

```php
// Check if developer mode
public function isDeveloperMode(): bool

// Check if production mode
public function isProductionMode(): bool

// Get mode
public function getMode(): string
```

### 44-71. Additional Utility ViewModels

- **Escaper** - HTML/URL escaping utilities
- **GoogleMapsApi** - Google Maps integration
- **HyvaCsp** - Content Security Policy management
- **HyvaMetadata** - Hyvä version and metadata
- **MagentoMetadata** - Magento version information
- **PageBuilder** - PageBuilder support detection
- **SpeculationRules** - Prefetch/prerender rules
- **SwatchRenderer** - Product swatch rendering
- **ThemeLibrariesConfig** - Theme library configuration
- And more...

---

## Sales ViewModels

Located in `ViewModel/Sales/`:

### Sales/Order

**Purpose:** Order information and management

### Sales/Invoice

**Purpose:** Invoice data

### Sales/Shipment

**Purpose:** Shipment and tracking information

---

## Usage Patterns

### Pattern 1: Single ViewModel

```xml
<!-- Layout XML -->
<block name="product.info" template="Magento_Catalog::product/view.phtml">
    <arguments>
        <argument name="productViewModel" xsi:type="object">
            Hyva\Theme\ViewModel\CurrentProduct
        </argument>
    </arguments>
</block>
```

```php
<?php
// Template
/** @var \Hyva\Theme\ViewModel\CurrentProduct $productViewModel */
$productViewModel = $block->getViewModel();
$product = $productViewModel->get();
?>
```

### Pattern 2: Multiple ViewModels

```xml
<!-- Layout XML -->
<block name="product.card" template="Magento_Catalog::product/list/item.phtml">
    <arguments>
        <argument name="productViewModel" xsi:type="object">Hyva\Theme\ViewModel\CurrentProduct</argument>
        <argument name="priceViewModel" xsi:type="object">Hyva\Theme\ViewModel\ProductPrice</argument>
        <argument name="stockViewModel" xsi:type="object">Hyva\Theme\ViewModel\ProductStockItem</argument>
        <argument name="heroicons" xsi:type="object">Hyva\Theme\ViewModel\HeroiconsOutline</argument>
    </arguments>
</block>
```

```php
<?php
// Template
$productViewModel = $block->getData('productViewModel');
$priceViewModel = $block->getData('priceViewModel');
$stockViewModel = $block->getData('stockViewModel');
$heroicons = $block->getData('heroicons');

$product = $productViewModel->get();
?>

<div class="product-card">
    <h3><?= $escaper->escapeHtml($product->getName()) ?></h3>
    <p class="price"><?= $priceViewModel->format($product->getFinalPrice()) ?></p>

    <?php if ($stockViewModel->isInStock($product)): ?>
        <span class="stock-status text-green-600">
            <?= $heroicons->renderHtml('check-circle', 'w-5 h-5') ?>
            In Stock
        </span>
    <?php endif; ?>
</div>
```

### Pattern 3: ViewModel with Cache Tags

```php
<?php
namespace Vendor\Module\ViewModel;

use Magento\Framework\DataObject\IdentityInterface;
use Magento\Framework\View\Element\Block\ArgumentInterface;

class CustomViewModel implements ArgumentInterface, IdentityInterface
{
    private $cacheIdentities = [];

    public function getData(): array
    {
        return ['data' => 'value'];
    }

    public function getIdentities(): array
    {
        return $this->cacheIdentities;
    }

    public function addCacheTag(string $tag): void
    {
        $this->cacheIdentities[] = $tag;
    }
}
```

---

## Best Practices

### 1. Always Use ViewModels for Business Logic

**❌ Don't:**
```php
<?php
// Directly in template
$objectManager = \Magento\Framework\App\ObjectManager::getInstance();
$product = $objectManager->get('Magento\Catalog\Model\Product')->load(123);
?>
```

**✅ Do:**
```php
<?php
/** @var \Hyva\Theme\ViewModel\CurrentProduct $productViewModel */
$productViewModel = $block->getViewModel();
$product = $productViewModel->get();
?>
```

### 2. Type Hint ViewModels

```php
<?php
declare(strict_types=1);

/** @var \Magento\Framework\View\Element\Template $block */
/** @var \Magento\Framework\Escaper $escaper */
/** @var \Hyva\Theme\ViewModel\CurrentProduct $productViewModel */

$productViewModel = $block->getData('productViewModel');
?>
```

### 3. Check for Null Values

```php
<?php
$productViewModel = $block->getData('productViewModel');

if ($productViewModel && $productViewModel->exists()) {
    $product = $productViewModel->get();
    // Use product
}
?>
```

### 4. Use Specific ViewModels

Don't fetch all data in one ViewModel. Use specific ViewModels for specific purposes:

- **CurrentProduct** - for product data
- **ProductPrice** - for price formatting
- **ProductStockItem** - for stock information
- **HeroiconsOutline** - for icons

### 5. Implement Cache Tags

```php
<?php
namespace Vendor\Module\ViewModel;

use Magento\Framework\DataObject\IdentityInterface;

class MyViewModel implements ArgumentInterface, IdentityInterface
{
    public function getIdentities(): array
    {
        return ['my_cache_tag_' . $this->getId()];
    }
}
```

### 6. Keep ViewModels Stateless

ViewModels should not maintain state between calls:

**❌ Don't:**
```php
class MyViewModel {
    private $cachedData;

    public function getData() {
        if (!$this->cachedData) {
            $this->cachedData = $this->loadData();
        }
        return $this->cachedData;
    }
}
```

**✅ Do:**
```php
class MyViewModel {
    public function getData() {
        return $this->loadData();
    }
}
```

### 7. Use Dependency Injection

```php
<?php
namespace Vendor\Module\ViewModel;

class MyViewModel implements ArgumentInterface
{
    private ProductRepositoryInterface $productRepository;

    public function __construct(
        ProductRepositoryInterface $productRepository
    ) {
        $this->productRepository = $productRepository;
    }

    public function getProduct(int $id): ProductInterface
    {
        return $this->productRepository->getById($id);
    }
}
```

---

## Quick Reference

### Most Used ViewModels

| ViewModel | Purpose | Common Use |
|-----------|---------|------------|
| **CurrentProduct** | Current product | Product pages |
| **ProductList** | Product collection | Category pages |
| **ProductPrice** | Price formatting | Everywhere prices shown |
| **Cart** | Cart data | Mini cart, cart page |
| **Customer** | Customer session | Header, account |
| **Navigation** | Menu | Header navigation |
| **HeroiconsOutline** | Icons | UI elements |
| **StoreConfig** | Configuration | Conditional features |
| **Modal** | Modals | Dialogs, popups |

### Registration Example

```xml
<block name="example" template="Vendor_Module::example.phtml">
    <arguments>
        <argument name="productViewModel" xsi:type="object">Hyva\Theme\ViewModel\CurrentProduct</argument>
        <argument name="priceViewModel" xsi:type="object">Hyva\Theme\ViewModel\ProductPrice</argument>
    </arguments>
</block>
```

---

**Last Updated:** November 2025
**Total ViewModels:** 71
**Document Version:** 1.0.0
