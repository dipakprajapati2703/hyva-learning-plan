# Hyvä Luma Theme Fallback - Complete Guide

**Your guide to configuring theme fallback for checkout, payment gateways, and third-party extensions**

---

## Table of Contents

1. [What is Theme Fallback?](#what-is-theme-fallback)
2. [Why Use Fallback?](#why-use-fallback)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [URL Pattern Syntax](#url-pattern-syntax)
6. [Common Use Cases](#common-use-cases)
7. [Testing Fallback](#testing-fallback)
8. [Advanced Configuration](#advanced-configuration)
9. [Troubleshooting](#troubleshooting)
10. [Performance Considerations](#performance-considerations)
11. [Migration Strategy](#migration-strategy)
12. [Best Practices](#best-practices)

---

## What is Theme Fallback?

The **Hyvä Theme Fallback** module allows you to use the traditional Luma theme (or any other theme) for specific pages that are not yet fully compatible with Hyvä.

### How It Works

When a visitor accesses a URL configured for fallback:
- ❌ **Hyvä theme is NOT active**
- ❌ **Tailwind CSS is NOT loaded**
- ❌ **Alpine.js is NOT loaded**
- ✅ **Luma theme is active**
- ✅ **RequireJS is loaded**
- ✅ **All Luma dependencies are available**

This creates a seamless experience where users don't notice the theme switch.

---

## Why Use Fallback?

### Primary Use Cases

**1. Checkout Process**
- Standard Magento checkout is not Hyvä-compatible by default
- Fallback to Luma checkout until Hyvä Checkout is purchased
- Ensures checkout functionality works immediately

**2. Payment Gateway Pages**
- PayPal Express Checkout
- Braintree payment pages
- Authorize.net redirect pages
- Third-party payment methods

**3. Third-Party Extensions**
- Extensions that rely on Luma/KnockoutJS
- Modules not yet Hyvä-compatible
- Custom functionality built for Luma

**4. Temporary Solution**
- Gradual migration from Luma to Hyvä
- Test Hyvä on storefront while keeping checkout stable
- Reduce risk during theme transition

---

### Important Considerations

**Styling Must Be Rebuilt:**
- You cannot use Tailwind CSS classes on fallback pages
- You cannot use Alpine.js on fallback pages
- Must use standard Magento/Luma styling approaches
- RequireJS must be used for JavaScript functionality

---

## Installation

### Step 1: Check if Already Installed

The fallback module is often installed automatically as a dependency of `magento2-default-theme`.

```bash
# Navigate to Magento root
cd /var/www/html/m246p8

# Check if fallback module exists
php bin/magento module:status | grep Fallback

# Expected output if installed:
# Hyva_ThemeFallback                    enabled
```

---

### Step 2: Install Fallback Module (If Needed)

If the module is not installed, install it via Composer:

```bash
# Install fallback module
composer require hyva-themes/magento2-theme-fallback

# Run setup upgrade
bin/magento setup:upgrade

# Clear cache
php bin/magento cache:flush
```

**Expected Output:**
```
./composer.json has been updated
Loading composer repositories with package information
Updating dependencies
Lock file operations: 1 install, 0 updates, 0 removals
  - Installing hyva-themes/magento2-theme-fallback (1.x.x)
Writing lock file
Installing dependencies from lock file
Package operations: 1 install, 0 updates, 0 removals
  - Installing hyva-themes/magento2-theme-fallback (1.x.x): Extracting archive
```

---

### Step 3: Verify Installation

```bash
# Check module status
php bin/magento module:status Hyva_ThemeFallback

# Expected output:
# Module is enabled

# List all Hyva modules
php bin/magento module:status | grep Hyva

# Expected output:
# Hyva_Theme                           enabled
# Hyva_ThemeFallback                   enabled
```

**If module is disabled:**

```bash
# Enable module
php bin/magento module:enable Hyva_ThemeFallback

# Run setup upgrade
php bin/magento setup:upgrade

# Clear cache
php bin/magento cache:flush
```

---

## Configuration

### Method 1: Via Admin Panel (Recommended)

**Navigate to Configuration:**

1. Login to **Magento Admin Panel**
2. Go to: **Stores → Configuration**
3. In left sidebar, expand: **HYVA THEMES**
4. Click: **Fallback theme**

**Direct URL:**
```
http://your-magento-url/admin/admin/system_config/edit/section/hyva_theme_fallback/
```

**For your setup:**
```
http://m246p8.local/admin/admin/system_config/edit/section/hyva_theme_fallback/
```

---

### Configuration Settings

**Section: General Settings**

| Setting | Config Path | Value | Description |
|---------|-------------|-------|-------------|
| **Enable** | `hyva_theme_fallback/general/enable` | Yes | Activate fallback functionality |
| **Theme full path** | `hyva_theme_fallback/general/theme_full_path` | `frontend/Magento/luma` | Fallback theme to use |
| **The list of URL's parts** | `hyva_theme_fallback/general/list_part_of_url` | See below | URL patterns to fallback |

---

### Admin Panel Configuration Example

```
┌─────────────────────────────────────────────────────┐
│ HYVA THEMES → Fallback theme                       │
├─────────────────────────────────────────────────────┤
│                                                     │
│ General Settings                                    │
│ ├─ Enable: [Yes ▼]                                 │
│ ├─ Theme full path: [frontend/Magento/luma]        │
│ └─ The list of URL's parts:                        │
│    ┌───────────────────────────────────────────┐   │
│    │ checkout/index                            │   │
│    │ checkout/cart                             │   │
│    │ paypal/express/review                     │   │
│    │ paypal/express/saveShippingMethod         │   │
│    └───────────────────────────────────────────┘   │
│                                                     │
│ [Save Config]                                       │
└─────────────────────────────────────────────────────┘
```

**How to Add Multiple URLs:**
- Enter one URL pattern per line in the textarea
- No commas or semicolons needed
- Use forward slashes for path separators
- Press Enter after each pattern

---

### Method 2: Via Command Line

```bash
# Enable fallback
php bin/magento config:set hyva_theme_fallback/general/enable 1

# Set fallback theme path
php bin/magento config:set hyva_theme_fallback/general/theme_full_path "frontend/Magento/luma"

# Set URL patterns (use newlines in quotes)
php bin/magento config:set hyva_theme_fallback/general/list_part_of_url "checkout/index
paypal/express/review
paypal/express/saveShippingMethod"

# Clear config cache
php bin/magento cache:clean config

# Clear full page cache
php bin/magento cache:clean full_page
```

**Verify Configuration:**

```bash
# Check if enabled
php bin/magento config:show hyva_theme_fallback/general/enable

# Check theme path
php bin/magento config:show hyva_theme_fallback/general/theme_full_path

# Check URL patterns
php bin/magento config:show hyva_theme_fallback/general/list_part_of_url
```

---

### Save Configuration

**After configuring in Admin:**

1. Click **Save Config** button (top right)
2. Wait for success message: "You saved the configuration."
3. Clear cache:

```bash
php bin/magento cache:clean config full_page layout
```

**Or clear all caches:**

```bash
php bin/magento cache:flush
```

---

## URL Pattern Syntax

### Pattern Matching Logic

The fallback module uses two matching strategies:

#### 1. Route/Controller/Action Matching

**Format:** `module/controller/action`

**Examples:**

| Pattern | Matches | Explanation |
|---------|---------|-------------|
| `customer/account` | `customer/account/*` | All customer account pages |
| `customer/account/login` | `customer/account/login` only | Only login page |
| `checkout` | `checkout/*` | All checkout pages |
| `checkout/cart` | `checkout/cart/*` | Cart and related pages |

**How it works:**
- Configured URL `customer/account` applies to **ALL** `customer/account/*` requests
- Configured URL `customer/account/login` applies **ONLY** to the login page
- More specific patterns override general patterns

---

#### 2. Path Segment Matching

**Format:** Any URL path segment

**Examples:**

| Pattern | Matches | Explanation |
|---------|---------|-------------|
| `demo-product.html` | `*/demo-product.html` | Any page containing this string |
| `special-page` | `*/special-page/*` | Any URL with this segment |
| `.html` | `*.html` | All URLs ending with .html |

**How it works:**
- Configured value `demo-product.html` applies to **ALL** pages containing that string in the path
- Pattern can match anywhere in the URL path
- Case-sensitive matching

---

### URL Pattern Examples

**Standard Checkout Pages:**
```
checkout/index
checkout/cart
checkout/onepage
checkout/sidebar
checkout/onepage/success
```

**PayPal Integration:**
```
paypal/express/review
paypal/express/saveShippingMethod
paypal/payflowexpress/review
paypal/transparent/redirect
```

**Payment Gateway Pages:**
```
braintree/paypal/review
authorizenet/directpost/payment
stripe/payment/redirect
```

**Third-Party Extensions:**
```
amasty/checkout/index
aheadworks/onestepcheckout/index
mageworx/checkout/index
```

**Customer Account (if needed):**
```
customer/account/login
customer/account/create
customer/account/forgotpassword
customer/account/edit
```

---

### Wildcard Patterns

**Note:** The fallback module does NOT support explicit wildcards (`*` character), but partial matches work as described above.

**To match all URLs in a module:**
```
# Instead of: checkout/*
# Use: checkout

# This will match:
# - checkout/index
# - checkout/cart
# - checkout/onepage
# - checkout/sidebar
# etc.
```

---

## Common Use Cases

### Use Case 1: Standard Magento Checkout

**Scenario:** Using standard Magento checkout until Hyvä Checkout is purchased.

**Configuration:**
```
checkout/index
checkout/cart
checkout/onepage
checkout/sidebar
checkout/onepage/success
checkout/onepage/failure
```

**What this does:**
- All checkout pages use Luma theme
- Product pages, categories, etc. use Hyvä theme
- Seamless transition between themes

---

### Use Case 2: PayPal Express Checkout

**Scenario:** PayPal integration requires Luma-specific JavaScript.

**Configuration:**
```
paypal/express/review
paypal/express/saveShippingMethod
paypal/payflowexpress/review
paypal/transparent/redirect
```

---

### Use Case 3: Third-Party Checkout Extension

**Scenario:** Using Amasty One Step Checkout or similar.

**Configuration:**
```
amasty/checkout/index
onestepcheckout/index
```

---

### Use Case 4: Customer Login Pages

**Scenario:** Custom login page with Luma-specific functionality.

**Configuration:**
```
customer/account/login
customer/account/create
customer/account/forgotpassword
```

---

### Use Case 5: Specific Product URLs

**Scenario:** Certain products require Luma theme (legacy functionality).

**Configuration:**
```
legacy-product.html
special-product-123.html
```

---

## Testing Fallback

### Step 1: Configure Test Environment

```bash
# Ensure fallback is enabled
php bin/magento config:show hyva_theme_fallback/general/enable
# Expected: 1

# Ensure theme path is set
php bin/magento config:show hyva_theme_fallback/general/theme_full_path
# Expected: frontend/Magento/luma

# Clear cache
php bin/magento cache:flush
```

---

### Step 2: Test Checkout Page (Fallback)

**Navigate to checkout:**
```
http://m246p8.local/checkout/
```

**What to verify:**

1. **Visual Check:**
   - Luma design elements visible
   - Traditional Magento layout
   - No Hyvä modern design

2. **Browser DevTools (F12):**
   - Open **Elements** tab
   - Check `<body>` tag:
     ```html
     <!-- Luma (fallback) -->
     <body class="checkout-index-index page-layout-1column">

     <!-- NOT Hyvä -->
     <body class="bg-white text-gray-900 ...">
     ```

3. **Console Check:**
   ```javascript
   // Check if Alpine.js is NOT loaded
   console.log(typeof Alpine);
   // Expected: "undefined"

   // Check if RequireJS IS loaded
   console.log(typeof require);
   // Expected: "function"
   ```

4. **Network Tab:**
   - Check loaded CSS files
   - Should see Luma CSS, NOT Tailwind CSS
   - No `alpine.min.js` loaded

---

### Step 3: Test Regular Pages (Hyvä)

**Navigate to homepage:**
```
http://m246p8.local/
```

**What to verify:**

1. **Visual Check:**
   - Modern Hyvä design
   - Tailwind utility classes
   - Alpine.js functionality

2. **Browser DevTools:**
   ```html
   <!-- Hyvä theme -->
   <body class="bg-white text-gray-900 antialiased ...">
   ```

3. **Console Check:**
   ```javascript
   // Alpine.js should be loaded
   console.log(Alpine);
   // Expected: Object with Alpine.js methods
   ```

---

### Step 4: Test Theme Switching

**Test navigation between Hyvä and Fallback:**

1. **Start on homepage (Hyvä)**
   ```
   http://m246p8.local/
   ```

2. **Add product to cart**

3. **Navigate to checkout (Fallback)**
   ```
   http://m246p8.local/checkout/
   ```

4. **Verify theme switched:**
   - Page should display Luma design
   - No flash of unstyled content (FOUC)
   - Smooth transition

5. **Return to homepage (Hyvä)**
   - Click logo or navigate back
   - Verify Hyvä theme is active again

---

## Advanced Configuration

### Per Store View Configuration

**Configure fallback for specific store views:**

**Via Admin:**

1. Navigate to: **Stores → Configuration**
2. **Store View dropdown** (top left): Select specific store
3. Configure fallback URLs per store view
4. **Uncheck "Use Website"** checkbox to override
5. Save configuration

**Via CLI:**

```bash
# Set fallback for specific store view
php bin/magento config:set hyva_theme_fallback/general/enable 1 \
  --scope=stores --scope-code=default

# Set theme path for specific store
php bin/magento config:set hyva_theme_fallback/general/theme_full_path "frontend/Magento/luma" \
  --scope=stores --scope-code=default

# Set URL patterns for specific store
php bin/magento config:set hyva_theme_fallback/general/list_part_of_url "checkout/index" \
  --scope=stores --scope-code=default
```

---

### Per Website Configuration

```bash
# Set fallback for specific website
php bin/magento config:set hyva_theme_fallback/general/enable 1 \
  --scope=websites --scope-code=base

php bin/magento config:set hyva_theme_fallback/general/theme_full_path "frontend/Magento/luma" \
  --scope=websites --scope-code=base
```

---

### Using Different Fallback Theme

**Instead of Luma, use Magento Blank or custom theme:**

```bash
# Fallback to Blank theme
php bin/magento config:set hyva_theme_fallback/general/theme_full_path "frontend/Magento/blank"

# Fallback to custom theme
php bin/magento config:set hyva_theme_fallback/general/theme_full_path "frontend/YourVendor/custom-theme"

# Verify theme exists
php bin/magento theme:list | grep "YourVendor/custom-theme"
```

---

### Conditional Fallback by Customer Group

**Note:** The fallback module does not natively support customer group conditions. This requires custom development.

**Custom Implementation:**
```php
// app/code/Vendor/CustomFallback/Plugin/FallbackPlugin.php

namespace Vendor\CustomFallback\Plugin;

class FallbackPlugin
{
    public function afterIsActive(
        \Hyva\ThemeFallback\Model\Config $subject,
        $result
    ) {
        // Custom logic to check customer group
        // Return true/false based on conditions
    }
}
```

---

## Troubleshooting

### Issue 1: Fallback Not Working

**Symptoms:**
- Checkout still shows Hyvä theme
- URLs not falling back to Luma
- Alpine.js still loaded on fallback pages

**Solutions:**

```bash
# 1. Verify module is enabled
php bin/magento module:status Hyva_ThemeFallback
# Expected: Module is enabled

# 2. Check configuration
php bin/magento config:show hyva_theme_fallback/general/enable
# Expected: 1

php bin/magento config:show hyva_theme_fallback/general/theme_full_path
# Expected: frontend/Magento/luma

php bin/magento config:show hyva_theme_fallback/general/list_part_of_url
# Expected: Your URL patterns

# 3. Clear all caches
php bin/magento cache:flush

# 4. Regenerate compiled code
php bin/magento setup:di:compile

# 5. Clear generated files
rm -rf generated/* var/view_preprocessed/* var/page_cache/*

# 6. Deploy static content for both themes
php bin/magento setup:static-content:deploy -f --theme=Hyva/default
php bin/magento setup:static-content:deploy -f --theme=Magento/luma

# 7. Test again
curl -I http://m246p8.local/checkout/
```

---

### Issue 2: Luma Theme Not Found

**Symptoms:**
```
Error: Could not find theme with path: frontend/Magento/luma
```

**Solutions:**

```bash
# 1. Verify Luma theme exists
php bin/magento theme:list

# Expected output should include:
# frontend | Magento/luma | Magento Luma | Magento/luma

# 2. If missing, check if Luma is installed
ls -la vendor/magento/theme-frontend-luma/

# 3. If missing, reinstall Magento sample data
php bin/magento sampledata:deploy
php bin/magento setup:upgrade

# 4. Use alternative fallback theme
php bin/magento config:set hyva_theme_fallback/general/theme_full_path "frontend/Magento/blank"
php bin/magento cache:flush
```

---

### Issue 3: Styles Broken on Fallback Pages

**Symptoms:**
- Fallback pages display but styling is broken
- Missing CSS or JavaScript
- 404 errors in Network tab

**Solutions:**

```bash
# 1. Deploy static content for Luma theme
php bin/magento setup:static-content:deploy -f --theme=Magento/luma

# 2. Verify static files exist
ls -la pub/static/frontend/Magento/luma/en_US/

# Expected: Multiple directories (css/, js/, images/, etc.)

# 3. Check file permissions
sudo chown -R dipak:www-data pub/static/frontend/Magento/luma/
sudo chmod -R 755 pub/static/frontend/Magento/luma/

# 4. Clear browser cache (Ctrl + Shift + R)

# 5. Test with cache disabled
php bin/magento cache:disable full_page block_html
# Test fallback page
# Re-enable: php bin/magento cache:enable full_page block_html
```

---

### Issue 4: Both Themes Loading Simultaneously

**Symptoms:**
- Checkout page shows mix of Hyvä and Luma elements
- Conflicting CSS classes
- Layout is broken

**Solutions:**

```bash
# 1. Clear all caches
php bin/magento cache:flush

# 2. Remove view_preprocessed files
rm -rf var/view_preprocessed/*

# 3. Remove page_cache
rm -rf var/page_cache/*

# 4. Remove generated files
rm -rf generated/*

# 5. Disable full-page cache temporarily
php bin/magento cache:disable full_page

# 6. Test fallback (without cache)
curl -I http://m246p8.local/checkout/

# 7. If working, re-enable cache
php bin/magento cache:enable full_page
php bin/magento cache:flush
```

---

### Issue 5: URL Pattern Not Matching

**Symptoms:**
- Specific URL should fallback but doesn't
- Other URLs in same module work

**Solutions:**

```bash
# 1. Check exact URL pattern
php bin/magento config:show hyva_theme_fallback/general/list_part_of_url

# 2. Use more specific pattern
# Instead of: customer/account
# Try: customer/account/login

# 3. Use less specific pattern
# Instead of: checkout/onepage/index
# Try: checkout/onepage

# 4. Test URL directly
curl -I http://m246p8.local/checkout/onepage/

# 5. Clear cache after changing patterns
php bin/magento cache:clean config full_page
```

---

### Issue 6: Fallback Works but JavaScript Broken

**Symptoms:**
- Luma theme loads correctly
- But buttons, forms, AJAX don't work

**Solutions:**

```bash
# 1. Check browser console for errors
# Open DevTools (F12) > Console

# 2. Verify RequireJS is loaded
# In console: console.log(typeof require)
# Expected: "function"

# 3. Check if jQuery is loaded
# In console: console.log(typeof jQuery)
# Expected: "function"

# 4. Redeploy Luma static content
php bin/magento setup:static-content:deploy -f --theme=Magento/luma

# 5. Check requirejs-config.js exists
ls -la pub/static/frontend/Magento/luma/en_US/requirejs-config.js

# 6. Disable JavaScript minification
php bin/magento config:set dev/js/minify_files 0
php bin/magento config:set dev/js/merge_files 0
php bin/magento cache:flush
```

---

## Performance Considerations

### Fallback Impact

**Positive:**
- ✅ Minimal performance impact on regular pages
- ✅ Only affects specified URL patterns
- ✅ No overhead on Hyvä pages
- ✅ Allows gradual migration

**Negative:**
- ⚠️ May cause flash of unstyled content (FOUC) when switching themes
- ⚠️ Requires both themes' static content to be deployed
- ⚠️ Increases total static content size
- ⚠️ May confuse page caching

---

### Optimization Tips

**1. Enable Full-Page Cache for Fallback Pages:**

```bash
# Enable Varnish or built-in FPC
php bin/magento config:set system/full_page_cache/caching_application 2

# Warm up cache
php bin/magento cache:flush
curl -I http://m246p8.local/checkout/
```

**2. Use CDN for Static Files:**

Configure CDN in Admin:
- **Stores → Configuration → Web → Base URLs (Secure)**
- Set CDN URL for static files

**3. Minimize Fallback Pages:**

Only use fallback for absolutely necessary pages:
```
# Instead of:
checkout/*
customer/account/*
paypal/*

# Use only:
checkout/onepage
paypal/express/review
```

**4. Deploy Static Content in Production Mode:**

```bash
# Production deployment
php bin/magento deploy:mode:set production

# Deploy both themes
php bin/magento setup:static-content:deploy --theme=Hyva/default --theme=Magento/luma

# Enable all caches
php bin/magento cache:enable
```

---

### Monitoring Fallback Performance

**1. Check Page Load Times:**

```bash
# Test Hyvä page
curl -w "@curl-format.txt" -o /dev/null -s http://m246p8.local/

# Test fallback page
curl -w "@curl-format.txt" -o /dev/null -s http://m246p8.local/checkout/
```

**curl-format.txt:**
```
time_namelookup:  %{time_namelookup}\n
time_connect:  %{time_connect}\n
time_total:  %{time_total}\n
```

**2. Use Browser Performance Tools:**

- Chrome DevTools > Performance tab
- Lighthouse audit
- Compare Hyvä vs. Fallback page scores

---

## Migration Strategy

### Phase 1: Initial Setup (Use Fallback)

**Goal:** Get Hyvä running on frontend, keep checkout stable.

**Configuration:**
```
checkout/index
checkout/cart
checkout/onepage
checkout/sidebar
paypal/express/review
braintree/paypal/review
```

**Timeline:** Weeks 1-4

---

### Phase 2: Test & Optimize (Reduce Fallback)

**Goal:** Test Hyvä functionality, reduce fallback pages.

**Actions:**
- Purchase Hyvä Checkout module
- Test payment integrations
- Verify third-party extensions

**Configuration:**
```
# Remove checkout fallback
# checkout/index  ← REMOVED

# Keep only payment gateways
paypal/express/review
braintree/paypal/review
```

**Timeline:** Weeks 5-8

---

### Phase 3: Full Migration (Remove Fallback)

**Goal:** 100% Hyvä theme, no fallback.

**Actions:**
- Implement Hyvä-compatible payment modules
- Replace remaining incompatible extensions
- Remove all fallback URLs

**Configuration:**
```
# No fallback URLs configured
(empty)
```

**Timeline:** Weeks 9-12

---

## Best Practices

### 1. Minimize Fallback URLs

**Bad:**
```
checkout/*
customer/*
paypal/*
catalog/*
```

**Good:**
```
checkout/onepage
paypal/express/review
```

**Why:** Each fallback URL adds complexity and reduces the benefits of Hyvä.

---

### 2. Use Specific Patterns

**Bad:**
```
customer
```
(Falls back ALL customer pages)

**Good:**
```
customer/account/login
customer/account/create
```
(Falls back only specific pages)

---

### 3. Test Fallback Thoroughly

**Testing Checklist:**
- [ ] Test theme switching (Hyvä → Fallback → Hyvä)
- [ ] Test fallback page functionality (buttons, forms, AJAX)
- [ ] Test payment gateways on fallback pages
- [ ] Test with cache enabled
- [ ] Test on different browsers
- [ ] Test mobile responsiveness

---

### 4. Document Fallback Configuration

**Create documentation for your team:**

```markdown
# Fallback Configuration

## Active Fallback URLs
- checkout/onepage - Standard Magento checkout
- paypal/express/review - PayPal Express review page

## Reason for Fallback
- Hyvä Checkout not yet purchased
- PayPal integration requires Luma JavaScript

## Migration Plan
- Q2 2025: Purchase Hyvä Checkout
- Q3 2025: Implement PayPal compatibility
- Q4 2025: Remove all fallback URLs
```

---

### 5. Monitor for Issues

**Set up monitoring:**

```bash
# Log fallback activations
tail -f var/log/system.log | grep -i "fallback"

# Monitor error logs
tail -f var/log/exception.log

# Check for 404s on fallback pages
grep "404" /var/log/apache2/access.log | grep "checkout"
```

---

### 6. Keep Both Themes Updated

**When updating Magento:**

```bash
# Update both Hyvä and Luma static content
php bin/magento setup:static-content:deploy -f \
  --theme=Hyva/default \
  --theme=Magento/luma
```

---

### 7. Use Fallback Temporarily

**Fallback is NOT a long-term solution:**
- ✅ Use for initial Hyvä setup
- ✅ Use during gradual migration
- ❌ Don't rely on fallback indefinitely
- ❌ Don't use fallback for core features

**Goal:** Remove fallback within 3-6 months of Hyvä installation.

---

## Fallback Module Files

### Directory Structure

```
vendor/hyva-themes/magento2-theme-fallback/
├── etc/
│   ├── module.xml                    # Module declaration
│   ├── adminhtml/
│   │   └── system.xml                # Admin configuration
│   └── config.xml                    # Default configuration
├── Model/
│   ├── Config.php                    # Configuration helper
│   └── UrlMatcher.php                # URL pattern matching logic
├── Plugin/
│   └── ThemePlugin.php               # Theme switching plugin
└── view/
    └── adminhtml/
        └── templates/
            └── system/
                └── config/
                    └── url_patterns.phtml  # Admin form field
```

---

### Key Files Explained

**1. Model/Config.php**
- Reads fallback configuration from database
- Provides methods: `isEnabled()`, `getThemePath()`, `getUrlPatterns()`

**2. Model/UrlMatcher.php**
- Matches current URL against configured patterns
- Implements route/controller/action matching
- Implements path segment matching

**3. Plugin/ThemePlugin.php**
- Intercepts theme resolution
- Switches theme based on URL matching
- Executed on every request

---

## Debug Fallback

### Enable Debug Logging

```bash
# 1. Enable developer mode
php bin/magento deploy:mode:set developer

# 2. Enable debug logging
php bin/magento config:set dev/debug/debug_logging 1

# 3. Watch logs
tail -f var/log/system.log | grep -i "fallback"

# 4. Test URL
curl -I http://m246p8.local/checkout/

# 5. Check logs for fallback activation
grep -i "theme" var/log/system.log
```

---

### Add Custom Logging

**Create custom logger:**

```php
// app/code/Vendor/Module/Plugin/FallbackLogger.php

namespace Vendor\Module\Plugin;

use Psr\Log\LoggerInterface;

class FallbackLogger
{
    public function __construct(
        private readonly LoggerInterface $logger
    ) {}

    public function afterIsActive(
        \Hyva\ThemeFallback\Model\Config $subject,
        $result
    ) {
        $this->logger->info('Fallback active: ' . ($result ? 'YES' : 'NO'));
        return $result;
    }
}
```

---

## Conclusion

The Hyvä Luma Theme Fallback module is a powerful tool for gradual migration from Luma to Hyvä. It allows you to:

- ✅ Use Hyvä on frontend immediately
- ✅ Keep checkout and payment gateways stable
- ✅ Migrate at your own pace
- ✅ Reduce risk during theme transition

**Remember:**
- Fallback is a temporary solution
- Plan for full Hyvä migration
- Test thoroughly
- Document your configuration

---

## Related Documentation

- **[HYVA-THEME-SETUP.md](./HYVA-THEME-SETUP.md)** - Installation guide
- **[HYVA-LEARNING-PLAN.md](./HYVA-LEARNING-PLAN.md)** - Complete learning path
- **[HYVA-TAILWIND-ALPINE-SETUP.md](./HYVA-TAILWIND-ALPINE-SETUP.md)** - Technical setup details
- **[README.md](./README.md)** - Main documentation hub

---

## Support Resources

| Resource | URL |
|----------|-----|
| **Official Docs** | https://docs.hyva.io/hyva-themes/luma-theme-fallback/ |
| **Hyvä Slack** | https://hyva-io.slack.com |
| **GitLab Issues** | https://gitlab.hyva.io |
| **Support Email** | support@hyva.io |

---

**Happy Building! 🚀**

*Last Updated: 2025-11-12*
*Version: 1.0*
*Reference: https://docs.hyva.io/hyva-themes/luma-theme-fallback/index.html*
