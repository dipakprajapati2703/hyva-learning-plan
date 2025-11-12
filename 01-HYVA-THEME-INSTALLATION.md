# Hyvä Themes - Complete Installation & Setup Guide

**Step-by-step guide to install and configure Hyvä Themes on Magento 2**

---

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installation Method](#installation-method)
3. [Composer Installation](#composer-installation)
4. [Post-Installation Configuration](#post-installation-configuration)
5. [Theme Activation](#theme-activation)
6. [Hyvä Theme Fallback Configuration](#hyvä-theme-fallback-configuration)
7. [Tailwind CSS Setup](#tailwind-css-setup)
8. [Verification & Testing](#verification--testing)
9. [Troubleshooting](#troubleshooting)
10. [Next Steps](#next-steps)

---

## Prerequisites

### System Requirements

**Magento Version:**
- ✅ Magento 2.4.4-p9 or higher
- ✅ Magento 2.4.5-p8 or higher
- ✅ Magento 2.4.6-p7 or higher (Your setup: 2.4.6-p8 ✅)
- ✅ Magento 2.4.7-p1 or higher
- ✅ Open Source or Commerce edition

**Server Requirements:**
- ✅ PHP 7.4, 8.1, 8.2, 8.3, or 8.4 (Your setup: PHP 8.2 ✅)
- ✅ MySQL 8.0+
- ✅ Apache 2.4+ or Nginx 1.x
- ✅ Composer 2.x
- ✅ Node.js 20+ (for development instances with Tailwind compilation)
- ✅ npm 9+

**Development Environment:**
- ✅ WSL2 (if on Windows) - Your current setup
- ✅ Ubuntu 22.04 LTS (or similar)
- ✅ Git for version control

---

### Pre-Installation Checklist

Before installing Hyvä, verify your Magento installation:

```bash
# 1. Check Magento version
php bin/magento --version
# Expected: Magento CLI 2.4.6-p8 or higher

# 2. Check PHP version
php -v
# Expected: PHP 8.1.x or 8.2.x

# 3. Check Node.js version
node --version
# Expected: v18.x.x or higher

# 4. Check npm version
npm --version
# Expected: 9.x.x or higher

# 5. Check Composer version
composer --version
# Expected: Composer version 2.x.x

# 6. Verify Magento is working
# Visit: http://your-magento-url/
# Should see default Magento store
```

---

### Hyvä License

**Important:** Hyvä Themes is a **commercial product** that requires a license.

**Getting a License:**
1. Visit: https://www.hyva.io/
2. Purchase a license (pricing varies by project type)
3. Receive license keys via email
4. Configure Composer authentication

**Free Trial:**
- Hyvä offers a free demo/evaluation
- Contact Hyvä support for trial access

---

## Installation Method

Hyvä Themes uses **Composer** for installation. This is the only officially supported method.

**Why Composer?**
- ✅ Easy updates and dependency management
- ✅ Standard Magento workflow
- ✅ Automatic dependency resolution
- ✅ Version control friendly
- ✅ Official support from Hyvä team

---

## Composer Installation

### Step 1: Register and Get Hyvä License Key

**Before Installation:**
1. Visit https://www.hyva.io/
2. Register for an account
3. Purchase a Hyvä license (or request trial)
4. Navigate to your account dashboard
5. Generate a **packagist authentication key**
6. Copy your project token and key

---

### Step 2: Configure Composer Authentication

Hyvä packages are hosted on a private packagist repository. You need to configure authentication.

**Add Hyvä repository and authentication:**

```bash
# Navigate to Magento root
cd /var/www/html/m246p8

# Step 1: Configure authentication (replace [KEY] with your packagist key)
composer config --auth http-basic.hyva-themes.repo.packagist.com token [YOUR-PACKAGIST-KEY]

# Step 2: Add Hyvä private repository (replace [PROJECT] with your project ID)
composer config repositories.private-packagist composer https://hyva-themes.repo.packagist.com/[YOUR-PROJECT-ID]/
```

**Example with actual values:**
```bash
# Example (use YOUR actual keys)
composer config repositories.private-packagist composer https://hyva-themes.repo.packagist.com/m248-local-m5zmk****/
composer config --auth http-basic.hyva-themes.repo.packagist.com token 8bdb847611f1cc5c88486d556e1f7d7dd6bcdd497a72109*******
```

---

**What Gets Added to composer.json:**

After running the above commands, your `composer.json` will automatically include:

```json
{
    "name": "magento/project-community-edition",
    "description": "eCommerce Platform for Growth (Community Edition)",
    "type": "project",
    "repositories": {
        "private-packagist": {
            "type": "composer",
            "url": "https://hyva-themes.repo.packagist.com/m248-local-m5zmk5v0x/"
        },
        "0": {
            "type": "composer",
            "url": "https://repo.magento.com/"
        }
    },
    "require": {
        "magento/product-community-edition": "2.4.6-p8",
        "hyva-themes/magento2-default-theme": "^1.3"
    }
}
```

**Key Points:**
- `repositories.private-packagist` - Your Hyvä private repository
- `repositories.0` - Magento's official repository (already exists)
- Both repositories are needed for dependencies

---

**What Gets Added to auth.json:**

Your authentication credentials are stored separately in `auth.json`:

```json
{
    "http-basic": {
        "repo.magento.com": {
            "username": "YOUR-MAGENTO-PUBLIC-KEY",
            "password": "YOUR-MAGENTO-PRIVATE-KEY"
        },
        "hyva-themes.repo.packagist.com": {
            "username": "token",
            "password": "8bdb847611f1cc5c88486d556e1f7d7dd6bcdd497a72109787********"
        }
    }
}
```

**Security Notes:**
- `composer.json` - Safe to commit to Git (contains URLs only)
- `auth.json` - **MUST be kept private** (contains authentication tokens)
- Add `auth.json` to `.gitignore`

---

**Alternative: Manual Configuration**

If you prefer to edit files manually instead of using Composer commands:

**1. Edit composer.json:**

```bash
# Add Hyvä repository to composer.json
nano composer.json
```

Add the `private-packagist` repository to the `repositories` section:

```json
{
    "repositories": {
        "private-packagist": {
            "type": "composer",
            "url": "https://hyva-themes.repo.packagist.com/YOUR-PROJECT-ID/"
        },
        "0": {
            "type": "composer",
            "url": "https://repo.magento.com/"
        }
    }
}
```

**2. Edit auth.json:**

```bash
# Create or edit auth.json
nano auth.json
```

Add your Hyvä authentication:

```json
{
    "http-basic": {
        "repo.magento.com": {
            "username": "YOUR-MAGENTO-PUBLIC-KEY",
            "password": "YOUR-MAGENTO-PRIVATE-KEY"
        },
        "hyva-themes.repo.packagist.com": {
            "username": "token",
            "password": "YOUR-HYVA-PACKAGIST-TOKEN"
        }
    }
}
```

**Example with your actual values:**

```json
{
    "http-basic": {
        "repo.magento.com": {
            "username": "your-magento-public-key",
            "password": "your-magento-private-key"
        },
        "hyva-themes.repo.packagist.com": {
            "username": "token",
            "password": "8bdb847611f1cc5c88486d556e1f7d7dd6bcdd497a72109787********"
        }
    }
}
```

---

**Verify Configuration:**

After adding credentials, verify they're configured correctly:

```bash
# Check repositories are configured
composer config repositories

# Expected output:
# repositories.private-packagist.type composer
# repositories.private-packagist.url https://hyva-themes.repo.packagist.com/YOUR-PROJECT/

# Check authentication is set
composer config http-basic.hyva-themes.repo.packagist.com

# Expected output:
# [username] => token
# [password] => YOUR-TOKEN (hidden)
```

---

**Security Best Practices:**

```bash
# 1. Add auth.json to .gitignore
echo "auth.json" >> .gitignore

# 2. Verify it's not tracked by Git
git status

# 3. Set proper file permissions (optional but recommended)
chmod 600 auth.json

# 4. Never commit sensitive credentials to version control
```

---

**Common Authentication Errors:**

| Error | Cause | Solution |
|-------|-------|----------|
| "Could not find package hyva-themes/..." | Repository not configured | Run `composer config repositories.private-packagist` |
| "401 Unauthorized" | Invalid token | Regenerate token at hyva.io |
| "403 Forbidden" | Expired license | Contact Hyvä support |
| "The requested URL returned error: 404" | Wrong project ID | Check project ID in Hyvä account |

---

### Step 3: Install Hyvä Default Theme

**Install the base Hyvä theme package:**

```bash
# Install Hyvä Default Theme (includes theme-module as dependency)
composer require hyva-themes/magento2-default-theme
```

**Note:** The `hyva-themes/magento2-default-theme` package automatically pulls in:
- `hyva-themes/magento2-theme-module` (core functionality)
- `hyva-themes/magento2-theme-fallback` (fallback mechanism)
- Other required dependencies

**Optional Additional Packages:**
```bash
# Reset theme (removes default Magento layouts)
composer require hyva-themes/magento2-reset-theme

# Email module (Hyvä-compatible email templates)
composer require hyva-themes/magento2-email-module

# Specific compatibility modules
composer require hyva-themes/magento2-checkout
```

**Expected Output:**
```
./composer.json has been updated
Loading composer repositories with package information
Updating dependencies
Lock file operations: X installs, 0 updates, 0 removals
...
Package operations: X installs, 0 updates, 0 removals
  - Installing hyva-themes/magento2-theme-module (1.x.x)
  - Installing hyva-themes/magento2-default-theme (1.x.x)
...
```

**Troubleshooting:**
If you get authentication errors, verify your credentials:
```bash
composer config http-basic.hyva.repo.packagist.com
```

---

### Step 4: Run Magento Setup Upgrade

After installing packages, run Magento setup:

```bash
# Run setup upgrade (enables modules and applies DB changes)
php bin/magento setup:upgrade

# Expected output:
# Module 'Hyva_Theme':
# Module 'Hyva_ThemeFallback':
# ...
# Schema creation/updates:
# ...
# Module version setup:
```

**What this does:**
- Automatically enables all Hyvä modules
- Applies database schema changes
- Updates module versions

**Note:** In Magento 2.3+, `module:enable` is called automatically by `setup:upgrade`

**Expected Duration:**
- `setup:upgrade`: 1-2 minutes
- `setup:di:compile`: 3-5 minutes
- `setup:static-content:deploy`: 5-10 minutes

---

### Step 4: Verify Installation

Check that Hyvä modules are installed and enabled:

```bash
# List all Hyva modules
php bin/magento module:status | grep Hyva

# Expected output:
# Hyva_Theme                           enabled
```

**Check theme is registered:**

```bash
# List all themes
php bin/magento theme:list

# Expected output should include:
# frontend | Hyva/default | Hyva Default Theme | Hyva/default
```


## Post-Installation Configuration

### Step 1: Enable Required GraphQL Modules

**Important:** Hyvä uses GraphQL for many frontend operations. Enable all required GraphQL modules:

```bash
# Enable all GraphQL modules
php bin/magento module:enable \
  Magento_BundleGraphQl \
  Magento_CatalogGraphQl \
  Magento_CatalogInventoryGraphQl \
  Magento_CatalogUrlRewriteGraphQl \
  Magento_ConfigurableProductGraphQl \
  Magento_CustomerGraphQl \
  Magento_DirectoryGraphQl \
  Magento_DownloadableGraphQl \
  Magento_EavGraphQl \
  Magento_GiftCardGraphQl \
  Magento_GroupedProductGraphQl \
  Magento_QuoteGraphQl \
  Magento_SalesGraphQl \
  Magento_StoreGraphQl \
  Magento_SwatchesGraphQl \
  Magento_TaxGraphQl \
  Magento_ThemeGraphQl \
  Magento_UrlRewriteGraphQl \
  Magento_VaultGraphQl \
  Magento_WeeeGraphQl \
  Magento_WishlistGraphQl

# Run setup upgrade
php bin/magento setup:upgrade
```

**Why this matters:**
- Hyvä uses GraphQL for product data, cart operations, customer info
- Missing GraphQL modules = broken functionality
- These are core Magento modules, safe to enable

---

### Step 2: Disable Magento's Built-in Minification

**Critical:** Hyvä has its own optimization. Disable Magento's minification to avoid conflicts.

```bash
# Disable HTML minification
php bin/magento config:set dev/template/minify_html 0

# Disable CSS minification and merging
php bin/magento config:set dev/css/minify_files 0
php bin/magento config:set dev/css/merge_css_files 0

# Disable JavaScript minification, merging, and bundling
php bin/magento config:set dev/js/minify_files 0
php bin/magento config:set dev/js/enable_js_bundling 0
php bin/magento config:set dev/js/merge_files 0

# Clear config cache
php bin/magento cache:clean config
```

**Explanation:**
- Hyvä uses Tailwind CSS (JIT) - doesn't benefit from CSS merging
- Alpine.js is already optimized - doesn't need bundling
- Magento's minification can break Hyvä's JavaScript

---

### Step 3: Disable Magento's Default Captcha

Hyvä uses different captcha solutions (ReCaptcha v3, hCaptcha). Disable the default:

```bash
# Disable built-in CAPTCHA
php bin/magento config:set customer/captcha/enable 0

# Clear config cache
php bin/magento cache:clean config
```

**Note:** Configure Google ReCaptcha or hCaptcha separately if needed.

---

### Step 4: Configure Hyvä Settings

**Via Admin Panel:**

1. Navigate to: **Stores > Configuration**
2. Expand: **Hyvä Themes** (left sidebar)
3. Configure sections:
   - **General Settings**
   - **Performance**
   - **Developer Options**

**Key Configurations:**

```
Hyvä Themes > General
├── Enable Hyvä: Yes
├── Use Tailwind JIT: Yes (recommended)
└── Lazy Load Images: Yes

Hyvä Themes > Performance
├── Minify CSS: Yes (production)
├── Minify JavaScript: Yes (production)
└── Merge CSS Files: Yes (production)

Hyvä Themes > Developer
├── Enable Template Hints: No (Yes for development)
├── Debug Mode: No (Yes for development)
└── Source Maps: No (Yes for development)
```

---

### Step 2: Configure Image Sizes

**File:** `app/design/frontend/Hyva/default/etc/view.xml` (or your custom theme)

```xml
<view xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:noNamespaceSchemaLocation="urn:magento:framework:Config/etc/view.xsd">
    <media>
        <images module="Magento_Catalog">
            <image id="category_page_grid" type="small_image">
                <width>240</width>
                <height>300</height>
            </image>
            <image id="product_page_image_large" type="image">
                <width>700</width>
                <height>875</height>
            </image>
        </images>
    </media>
</view>
```

---

### Step 3: Configure Store Design

**Set Default Design:**

Admin: **Content > Design > Configuration**

1. Select your store view
2. Click **Edit**
3. **Applied Theme** dropdown: Select `Hyva Default Theme`
4. Click **Save Configuration**
5. Clear cache: `php bin/magento cache:flush`

---

## Theme Activation

### Method 1: Via Admin Panel (Recommended)

**Important:** Theme must be set at the **Website level**, not just Store View level.

**Steps:**
1. Login to Magento Admin
2. Navigate: **Content > Design > Configuration**
3. Find your **Website** row in the grid (not Store View)
4. Click **Edit** in the Action column
5. In **Applied Theme** section:
   - **Applied Theme dropdown**: Select `Hyva/default`
6. Click **Save Configuration**
7. **Also set for Store Views** (optional but recommended):
   - Repeat steps 3-6 for each Store View
8. Clear cache:
   ```bash
   php bin/magento cache:flush
   ```

**Why Website Level?**
- Store View settings can override Website settings
- Setting at Website level ensures consistent theme across all stores
- Avoids theme conflicts

---

### Method 2: Via Command Line

```bash
# Set theme for store view ID 1
php bin/magento config:set design/theme/theme_id 4 --scope=stores --scope-code=default

# Clear cache
php bin/magento cache:flush

# Verify
php bin/magento config:show design/theme/theme_id
```

**Note:** Theme ID `4` is typical for Hyvä. Find your theme ID:

```bash
# Get theme ID
php bin/magento theme:list
```

---

### Method 3: Temporarily (for testing)

**URL Parameters:**
```
http://your-magento-url/?theme=Hyva/default
```

**Note:** This only works if URL parameter override is enabled in Magento.

---

## Tailwind CSS Setup

Hyvä uses Tailwind CSS for styling. You need to compile Tailwind to generate the CSS.

### Step 1: Navigate to Tailwind Directory

```bash
cd /var/www/html/m246p8/vendor/hyva-themes/magento2-default-theme/web/tailwind/
```

**Directory Contents:**
```
web/tailwind/
├── tailwind.config.js       # Tailwind configuration
├── tailwind-source.css      # Source CSS
├── tailwind.css             # Compiled CSS (generated)
└── package.json             # NPM dependencies
```

---

### Step 2: Install NPM Dependencies

```bash
# Install packages
npm install

# Expected output:
# added XX packages, and audited XX packages
# ...
```

**Packages Installed:**
- `tailwindcss` - Tailwind CSS core
- `@hyva-themes/hyva-modules` - Hyvä-specific Tailwind extensions
- `@tailwindcss/forms` - Form styling
- `@tailwindcss/typography` - Typography plugin

---

### Step 3: Compile Tailwind CSS

**For Development (with watch mode):**

```bash
# Watch mode - recompiles on file changes
npm run watch

# Expected output:
# Rebuilding...
# Done in XXms
```

Keep this terminal window open. Tailwind will watch for changes and recompile automatically.

**For Production (minified):**

```bash
# Build minified CSS for production
npm run build

# Expected output:
# Rebuilding...
# Done in XXms
```

---

### Step 4: Deploy Static Content

After compiling Tailwind, deploy to Magento:

```bash
# Navigate back to Magento root
cd /var/www/html/m246p8

# Deploy static files
php bin/magento setup:static-content:deploy -f en_US

# Clear cache
php bin/magento cache:flush
```

**Static Files Location:**
```
pub/static/frontend/Hyva/default/en_US/css/tailwind.css
```

---

## Verification & Testing

### Step 1: Check Frontend

**Open your store in browser:**
```
http://your-magento-url/
```

**What to Look For:**
- ✅ Clean, modern design
- ✅ Fast page load (check Network tab)
- ✅ Tailwind classes in HTML (Inspect Element)
- ✅ Alpine.js loaded (Console: check for `Alpine` object)
- ✅ No JavaScript errors in console

---

### Step 2: Verify Page Elements

**Homepage:**
- Header with logo and navigation
- Search bar
- Product listings (if configured)
- Footer

**Product Page:**
- Product images (gallery)
- Product details
- Add to cart button
- Tabs (description, reviews, etc.)

**Category Page:**
- Product grid
- Filters (layered navigation)
- Sorting options
- Pagination

---

### Step 3: Check Developer Tools

**Browser Console:**

```javascript
// Check Alpine.js is loaded
console.log(Alpine);
// Expected: Object with Alpine.js methods

// Check Tailwind classes
document.querySelector('body').className;
// Expected: String with Tailwind utility classes
```

**Network Tab:**
- `tailwind.css` loads successfully (200 status)
- `alpine.min.js` loads successfully
- No 404 errors

---

### Step 4: Enable Template Hints (Development Only)

**Via CLI:**

```bash
# Enable template hints
php bin/magento dev:template-hints:enable

# Check status
php bin/magento dev:template-hints:status

# Disable when done
php bin/magento dev:template-hints:disable
```

**Via Admin:**

**Stores > Configuration > Advanced > Developer > Debug**
- Template Path Hints for Storefront: **Yes**
- Add Block Class Type to Hints: **Yes**

**Note:** Only enable in development mode.

---

## Troubleshooting

### Issue 1: Theme Not Appearing

**Symptoms:**
- Store still shows Luma/Blank theme
- Hyvä styles not applied

**Solutions:**

```bash
# 1. Verify theme is set
php bin/magento config:show design/theme/theme_id

# 2. Clear cache
php bin/magento cache:flush

# 3. Remove generated files
rm -rf generated/* pub/static/* var/view_preprocessed/*

# 4. Redeploy
php bin/magento setup:static-content:deploy -f

# 5. Verify file permissions
sudo chown -R dipak:www-data pub/static generated var
sudo chmod -R 775 pub/static generated var
```

---

### Issue 2: Tailwind CSS Not Loading

**Symptoms:**
- Unstyled page
- Missing `tailwind.css` in Network tab

**Solutions:**

```bash
# 1. Verify Tailwind CSS is compiled
ls -la vendor/hyva-themes/magento2-default-theme/web/tailwind/tailwind.css

# 2. If missing, compile it
cd vendor/hyva-themes/magento2-default-theme/web/tailwind/
npm install
npm run build

# 3. Deploy static content
cd /var/www/html/m246p8
php bin/magento setup:static-content:deploy -f

# 4. Check deployed file exists
ls -la pub/static/frontend/Hyva/default/en_US/css/tailwind.css

# 5. Clear browser cache (Ctrl + Shift + R)
```

---

### Issue 3: Alpine.js Not Working

**Symptoms:**
- Interactive elements don't work
- `x-data`, `x-show` directives not functioning

**Solutions:**

```bash
# 1. Check browser console for errors
# Open DevTools > Console

# 2. Verify Alpine.js is loaded
# In console: console.log(Alpine)

# 3. Check if JavaScript is minified incorrectly
php bin/magento config:set dev/js/merge_files 0
php bin/magento config:set dev/js/minify_files 0
php bin/magento cache:flush

# 4. Redeploy
php bin/magento setup:static-content:deploy -f
```

---

### Issue 4: Slow Performance in Development

**Symptoms:**
- Long page load times
- Slow admin panel

**Solutions:**

```bash
# 1. Enable developer mode
php bin/magento deploy:mode:set developer

# 2. Disable unnecessary modules
php bin/magento module:disable Magento_TwoFactorAuth
php bin/magento module:disable Magento_AdminAdobeImsTwoFactorAuth

# 3. Use Tailwind watch mode (faster than rebuild)
cd vendor/hyva-themes/magento2-default-theme/web/tailwind/
npm run watch

# 4. Disable full-page cache in development
php bin/magento cache:disable full_page block_html
```

---

### Issue 5: Composer Authentication Errors

**Symptoms:**
```
Could not find package hyva-themes/magento2-theme-module
```

**Solutions:**

```bash
# 1. Verify credentials
composer config http-basic.hyva.repo.packagist.com

# 2. Re-add credentials
composer config http-basic.hyva.repo.packagist.com YOUR-USERNAME YOUR-PASSWORD

# 3. Clear Composer cache
composer clear-cache

# 4. Try installing again
composer require hyva-themes/magento2-theme-module
```

---

### Issue 6: Module Not Enabling

**Symptoms:**
```
Module 'Hyva_Theme' is not an enabled module
```

**Solutions:**

```bash
# 1. Manually enable module
php bin/magento module:enable Hyva_Theme

# 2. Check module exists
ls -la vendor/hyva-themes/magento2-theme-module/

# 3. Verify registration
grep -r "Hyva_Theme" vendor/hyva-themes/magento2-theme-module/registration.php

# 4. Run setup
php bin/magento setup:upgrade
php bin/magento cache:flush
```

---

## Hyvä Theme Fallback Configuration

### What is Theme Fallback?

The Hyvä Theme Fallback module allows you to use the traditional Luma theme for specific pages that are not yet fully compatible with Hyvä, such as:
- **Checkout pages** (if using standard Magento checkout)
- **Payment gateway pages** (PayPal, Stripe, etc.)
- **Third-party extension pages** (incompatible modules)

This ensures a seamless user experience while you gradually migrate all functionality to Hyvä.

### Complete Fallback Guide

**For detailed fallback configuration, troubleshooting, and best practices, see:**

**[HYVA-THEME-FALLBACK.md](./HYVA-THEME-FALLBACK.md)** - Complete Luma Theme Fallback Guide

The dedicated fallback guide covers:
- ✅ Installation and configuration steps
- ✅ URL pattern syntax and examples
- ✅ Common use cases (checkout, payment gateways)
- ✅ Testing and verification
- ✅ Advanced configuration options
- ✅ Troubleshooting guide
- ✅ Performance considerations
- ✅ Migration strategy
- ✅ Best practices

---

### Step 1: Install Fallback Module

**Note:** The fallback module is often installed automatically as a dependency of `magento2-default-theme`. Check if it's already installed:

```bash
# Check if fallback module exists
php bin/magento module:status | grep Fallback

# Expected output:
# Hyva_ThemeFallback                    enabled
```

**If not installed, install it manually:**

```bash
# Navigate to Magento root
cd /var/www/html/m246p8

# Install fallback module
composer require hyva-themes/magento2-theme-fallback

# Run setup upgrade
php bin/magento setup:upgrade

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
```

---

### Step 2: Verify Module is Enabled

```bash
# Check module status
php bin/magento module:status Hyva_ThemeFallback

# Expected output:
# Module is enabled
```

**If disabled, enable it:**

```bash
php bin/magento module:enable Hyva_ThemeFallback
php bin/magento setup:upgrade
```

---

### Step 3: Configure Fallback in Admin Panel

**Navigate to Admin Configuration:**

1. Login to **Magento Admin Panel**
2. Go to: **Stores → Configuration**
3. In left sidebar, expand: **HYVA THEMES**
4. Click: **Fallback theme**

**Or use direct URL:**
```
http://your-magento-url/admin/admin/system_config/edit/section/hyva_theme_fallback/
```

For your setup:
```
http://m246p8.local/admin/admin/system_config/edit/section/hyva_theme_fallback/
```

---

### Step 4: Configure Fallback Settings

**Section: General Settings**

| Setting | Value | Description |
|---------|-------|-------------|
| **Enable** | Yes | Activate fallback functionality |
| **Theme full path** | `frontend/Magento/luma` | Fallback to Luma theme |
| **The list of URL's parts** | See below | URL patterns to fallback |

**URL Patterns to Add:**

```
checkout/index
checkout/cart
checkout/onepage
paypal/express/review
paypal/express/saveShippingMethod
braintree/paypal/review
authorizenet/directpost/payment
```

**How to Add Multiple URLs:**
- Enter one URL pattern per line in the textarea
- No commas or semicolons needed
- Use forward slashes for path separators

---

### Step 5: Common Fallback URL Patterns

**Standard Checkout Pages:**
```
checkout/index
checkout/cart
checkout/onepage
checkout/sidebar
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
```

---

### Step 6: Complete Configuration Example

**Admin Panel Configuration:**

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

---

### Step 7: Save Configuration

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

### Step 8: Test Fallback

**Test Checkout Page:**

1. **Add product to cart**
2. **Navigate to checkout:**
   ```
   http://m246p8.local/checkout/
   ```
3. **Verify theme switched to Luma:**
   - Look for Luma design elements
   - Check browser DevTools > Elements
   - Should see Luma CSS classes (not Tailwind utilities)
   - Should see traditional Magento layout

**Test Regular Pages:**

1. **Visit homepage:**
   ```
   http://m246p8.local/
   ```
2. **Verify still using Hyvä:**
   - Modern design with Tailwind classes
   - Alpine.js functionality
   - Hyvä navigation

---

### Step 9: Verify Fallback is Working

**Check in Browser:**

```bash
# 1. Open checkout page
# http://m246p8.local/checkout/

# 2. Open Browser DevTools (F12)
# 3. Check Elements tab
# 4. Look for <body> tag attributes

# Hyvä theme (non-fallback pages):
<body class="bg-white text-gray-900 ...">
  <!-- Tailwind utility classes -->

# Luma theme (fallback pages):
<body class="checkout-index-index page-layout-1column">
  <!-- Traditional Magento classes -->
```

---

### Step 10: CLI Configuration (Alternative Method)

**Configure fallback via command line:**

```bash
# Enable fallback
php bin/magento config:set hyva_theme_fallback/general/enable 1

# Set fallback theme
php bin/magento config:set hyva_theme_fallback/general/theme_path "frontend/Magento/luma"

# Set fallback URL patterns
php bin/magento config:set hyva_theme_fallback/general/url_patterns "checkout/index
paypal/express/review
paypal/express/saveShippingMethod"

# Clear config cache
php bin/magento cache:clean config

# Verify configuration
php bin/magento config:show hyva_theme_fallback/general/enable
php bin/magento config:show hyva_theme_fallback/general/theme_path
```

---

### Advanced Fallback Configuration

#### Fallback by Controller

**Use full controller paths for precise control:**

```
# Full controller path format:
module/controller/action

# Examples:
Magento_Checkout/cart/index
Magento_Checkout/onepage/success
Magento_Customer/account/login
```

---

#### Fallback by URL Pattern

**Use wildcard patterns:**

```
# All checkout pages
checkout/*

# All PayPal pages
paypal/*

# Specific payment method
braintree/paypal/*

# All customer account pages
customer/account/*
```

---

#### Fallback for Specific Store Views

**Via Admin:**

1. Navigate to: **Stores → Configuration**
2. **Store View dropdown**: Select specific store
3. Configure fallback URLs per store view
4. Save configuration

**Via CLI:**

```bash
# Set fallback for specific store view
php bin/magento config:set hyva_theme_fallback/general/enable 1 \
  --scope=stores --scope-code=default

php bin/magento config:set hyva_theme_fallback/general/theme_path "frontend/Magento/luma" \
  --scope=stores --scope-code=default
```

---

### Troubleshooting Fallback

#### Issue 1: Fallback Not Working

**Symptoms:**
- Checkout still shows Hyvä theme
- URLs not falling back to Luma

**Solutions:**

```bash
# 1. Verify module is enabled
php bin/magento module:status Hyva_ThemeFallback

# 2. Check configuration
php bin/magento config:show hyva_theme_fallback/general/enable
php bin/magento config:show hyva_theme_fallback/general/theme_path

# 3. Clear all caches
php bin/magento cache:flush

# 4. Regenerate code
php bin/magento setup:di:compile

# 5. Clear generated files
rm -rf generated/* var/view_preprocessed/*

# 6. Deploy static content
php bin/magento setup:static-content:deploy -f
```

---

#### Issue 2: Luma Theme Not Found

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

# 2. If missing, reinstall Magento sample data
php bin/magento sampledata:deploy
php bin/magento setup:upgrade

# 3. Use alternative fallback theme
php bin/magento config:set hyva_theme_fallback/general/theme_path "frontend/Magento/blank"
```

---

#### Issue 3: Styles Broken on Fallback Pages

**Symptoms:**
- Fallback pages display but styling is broken
- Missing CSS or JavaScript

**Solutions:**

```bash
# 1. Deploy static content for Luma theme
php bin/magento setup:static-content:deploy -f --theme=Magento/luma

# 2. Clear browser cache (Ctrl + Shift + R)

# 3. Verify static files exist
ls -la pub/static/frontend/Magento/luma/en_US/

# 4. Check file permissions
sudo chmod -R 755 pub/static/frontend/Magento/luma/
```

---

#### Issue 4: Both Themes Loading Simultaneously

**Symptoms:**
- Checkout page shows mix of Hyvä and Luma elements
- Conflicting CSS classes

**Solutions:**

```bash
# 1. Clear all caches
php bin/magento cache:flush

# 2. Remove view_preprocessed files
rm -rf var/view_preprocessed/*

# 3. Remove page_cache
rm -rf var/page_cache/*

# 4. Disable full-page cache temporarily
php bin/magento cache:disable full_page

# 5. Test fallback

# 6. Re-enable cache when working
php bin/magento cache:enable full_page
```

---

### When to Use Fallback

**Use Fallback For:**
- ✅ Standard Magento checkout (until Hyvä Checkout is purchased)
- ✅ Payment gateway pages (PayPal, Braintree, etc.)
- ✅ Third-party extensions not yet compatible with Hyvä
- ✅ Complex forms with heavy JavaScript dependencies
- ✅ Admin-generated pages (CMS pages with Luma widgets)

**Don't Use Fallback For:**
- ❌ Product pages (fully compatible with Hyvä)
- ❌ Category pages (fully compatible with Hyvä)
- ❌ CMS pages (unless using Luma-specific widgets)
- ❌ Customer account pages (Hyvä provides templates)
- ❌ Search pages (Hyvä provides templates)

---

### Migration Strategy

**Phase 1: Use Fallback (Initial Setup)**
```
checkout/*           → Luma
paypal/*            → Luma
braintree/*         → Luma
```

**Phase 2: Migrate Gradually**
```
# Remove checkout fallback after purchasing Hyvä Checkout
checkout/*           → Hyvä Checkout

# Keep payment gateways in fallback
paypal/*            → Luma
braintree/*         → Luma
```

**Phase 3: Full Hyvä (Goal)**
```
# All pages use Hyvä
(no fallback URLs configured)
```

---

### Performance Considerations

**Fallback Impact:**
- ✅ Minimal performance impact
- ✅ Only affects specified URL patterns
- ✅ No overhead on regular Hyvä pages
- ⚠️ May cause flash of unstyled content when switching themes

**Optimization Tips:**

```bash
# 1. Enable full-page cache for fallback pages
php bin/magento config:set system/full_page_cache/caching_application 2

# 2. Warm up cache
php bin/magento cache:flush
curl -I http://m246p8.local/checkout/

# 3. Use CDN for static files
# Configure CDN in: Stores > Configuration > Web > Base URLs (Secure)
```

---

### Fallback Module Files

**Key Files:**

```
vendor/hyva-themes/magento2-theme-fallback/
├── etc/
│   ├── module.xml                    # Module declaration
│   ├── adminhtml/system.xml          # Admin configuration
│   └── config.xml                    # Default configuration
├── Plugin/
│   └── ThemePlugin.php               # Theme switching logic
├── Model/
│   ├── Config.php                    # Configuration helper
│   └── UrlMatcher.php                # URL pattern matching
└── view/
    └── adminhtml/
        └── templates/
            └── system/config/
                └── url_patterns.phtml  # Admin form field
```

---

### Debug Fallback

**Enable debug logging:**

```bash
# 1. Enable developer mode
php bin/magento deploy:mode:set developer

# 2. Check logs
tail -f var/log/system.log | grep -i "fallback"

# 3. Test URL
curl -I http://m246p8.local/checkout/

# 4. Check which theme is loaded
grep -i "theme" var/log/system.log
```

---

## Next Steps

### After Successful Installation

**1. Explore Hyvä Features**
- Browse product pages
- Test add-to-cart functionality
- Check checkout process (with fallback if configured)
- Test customer account pages

**2. Start Learning**
- Read: [HYVA-LEARNING-PLAN.md](./HYVA-LEARNING-PLAN.md)
- Study: [HYVA-TAILWIND-ALPINE-SETUP.md](./HYVA-TAILWIND-ALPINE-SETUP.md)
- Practice: Create simple template overrides

**3. Create Custom Theme**
- Follow Phase 5 in learning plan
- Build on top of Hyvä default
- Customize colors, fonts, spacing

**4. Join Community**
- Hyvä Slack: https://hyva-io.slack.com
- Ask questions
- Share your progress

---

## Installation Checklist

Use this checklist to track your installation progress:

### Pre-Installation
- [ ] Magento 2.4.6+ installed
- [ ] PHP 8.1/8.2 verified
- [ ] Node.js 18+ installed
- [ ] Composer 2.x configured
- [ ] Hyvä license obtained

### Installation
- [ ] Composer repositories configured
- [ ] Authentication added
- [ ] `hyva-themes/magento2-theme-module` installed
- [ ] `hyva-themes/magento2-default-theme` installed
- [ ] `hyva-themes/magento2-theme-fallback` installed
- [ ] `php bin/magento setup:upgrade` completed
- [ ] `php bin/magento setup:di:compile` completed
- [ ] `php bin/magento setup:static-content:deploy` completed
- [ ] Cache flushed

### Configuration
- [ ] Theme activated in admin (Website level)
- [ ] GraphQL modules enabled
- [ ] Magento minification disabled
- [ ] Hyvä settings configured
- [ ] Image sizes configured
- [ ] Store design set

### Fallback Configuration (Optional)
- [ ] Fallback module installed
- [ ] Fallback enabled in admin
- [ ] Fallback theme path set (`frontend/Magento/luma`)
- [ ] URL patterns configured (checkout, payment gateways)
- [ ] Luma static content deployed
- [ ] Fallback tested on checkout page

### Tailwind CSS
- [ ] NPM dependencies installed
- [ ] Tailwind CSS compiled
- [ ] Static content deployed
- [ ] CSS loading in browser

### Verification
- [ ] Frontend displays Hyvä theme
- [ ] No JavaScript errors
- [ ] Alpine.js working
- [ ] All pages loading correctly
- [ ] Fallback working (if configured)
- [ ] Performance is good

---

## Quick Reference

### Essential Commands

```bash
# Install Hyvä
composer require hyva-themes/magento2-theme-module
composer require hyva-themes/magento2-default-theme

# Setup
php bin/magento module:enable Hyva_Theme
php bin/magento setup:upgrade
php bin/magento setup:di:compile
php bin/magento setup:static-content:deploy -f
php bin/magento cache:flush

# Compile Tailwind
cd vendor/hyva-themes/magento2-default-theme/web/tailwind/
npm install
npm run build  # or npm run watch

# Verify
php bin/magento module:status | grep Hyva
php bin/magento theme:list | grep Hyva
```

---

### File Locations

| Purpose | Path |
|---------|------|
| **Theme Module** | `vendor/hyva-themes/magento2-theme-module/` |
| **Default Theme** | `vendor/hyva-themes/magento2-default-theme/` |
| **Tailwind Config** | `vendor/hyva-themes/magento2-default-theme/web/tailwind/tailwind.config.js` |
| **Compiled CSS** | `pub/static/frontend/Hyva/default/en_US/css/tailwind.css` |
| **Custom Theme** | `app/design/frontend/YourVendor/your-theme/` |

---

### Support Resources

| Resource | URL |
|----------|-----|
| **Official Docs** | https://docs.hyva.io |
| **GitLab** | https://gitlab.hyva.io |
| **Slack Community** | https://hyva-io.slack.com |
| **Support Email** | support@hyva.io |

---

## Conclusion

You should now have a fully functional Hyvä Themes installation on your Magento 2 store.

**What's Next?**
1. Start with [README.md](./README.md) for learning roadmap
2. Follow [HYVA-LEARNING-PLAN.md](./HYVA-LEARNING-PLAN.md) Phase 1
3. Build your first custom theme
4. Join the Hyvä community

**Need Help?**
- Check the troubleshooting section above
- Join Hyvä Slack for community support
- Review official documentation at https://docs.hyva.io

---

**Happy Building! 🚀**

*Last Updated: 2025-11-12*
*Version: 1.0*
