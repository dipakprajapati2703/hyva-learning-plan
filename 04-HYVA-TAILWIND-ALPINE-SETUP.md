# Complete Tailwind CSS & Alpine.js Setup Guide for Hyvä Themes

**Comprehensive guide for understanding how Tailwind CSS and Alpine.js are configured in Hyvä v1.4.1**

---

## Table of Contents

1. [Tailwind CSS Setup](#tailwind-css-setup)
2. [Alpine.js Setup](#alpinejs-setup)
3. [File Structure Deep Dive](#file-structure-deep-dive)
4. [Compilation Process](#compilation-process)
5. [Customization Workflow](#customization-workflow)
6. [Troubleshooting](#troubleshooting)

---

## Tailwind CSS Setup

### Overview

Hyvä v1.4.1 uses **Tailwind CSS v4.1.15** (latest) with modern CSS-first configuration. Tailwind v4 introduces a new approach:
- **@import "tailwindcss"** directive instead of JS config
- **@source** directive to define content paths
- **@theme** directive for design tokens
- No separate `tailwind.config.js` needed (uses `hyva.config.json` for tokens)

---

### File Structure for Tailwind CSS

```
vendor/hyva-themes/magento2-default-theme/
└── web/
    ├── css/
    │   └── styles.css                 # Final compiled CSS (~96KB minified)
    └── tailwind/
        ├── tailwind-source.css        # Main entry point (Tailwind v4 syntax)
        ├── hyva.config.json           # Design tokens & configuration
        ├── package.json               # NPM dependencies (@tailwindcss/cli ^4.1.12)
        ├── base/
        │   ├── index.css              # Base layer entry
        │   ├── preflight.css          # Tailwind CSS reset
        │   └── print.css              # Print styles
        ├── components/
        │   └── index.css              # Component styles
        ├── theme/
        │   ├── index.css              # Theme layer entry
        │   ├── form.css               # Form styling
        │   ├── product-price.css      # Product prices
        │   ├── page-cms.css           # CMS pages
        │   └── ...more theme files
        ├── utilities/
        │   └── index.css              # Custom utilities
        └── generated/
            ├── hyva-source.css        # Auto-generated from Hyva modules
            └── hyva-tokens.css        # Auto-generated design tokens

Your custom theme (app/design/frontend/MyCompany/hyva-custom/):
└── web/
    └── tailwind/
        ├── tailwind-source.css        # Your custom source
        ├── hyva.config.json           # Your custom tokens
        ├── package.json
        └── (copy entire structure from default theme)
```

---

### Step 1: Understanding tailwind-source.css (Tailwind v4 Entry Point)

**Location:** `vendor/hyva-themes/magento2-default-theme/web/tailwind/tailwind-source.css`

**Purpose:** Main entry point for Tailwind v4 compilation. This file uses the new Tailwind v4 syntax.

**Content:**

```css
/**
 * Tailwind CSS v4 Source File
 * Hyvä Theme v1.4.1
 */

/* 1. Import Hyva modules CSS */
@import "@hyva-themes/hyva-modules/css";

/* 2. Import Tailwind CSS v4 */
@import "tailwindcss" source(none);

/* 3. Define content sources (files to scan for classes) */
@source "../../**/*.phtml";   /* All template files */
@source "../../**/*.xml";      /* All XML layout files */

/* 4. Import custom base styles */
@import "./base";              /* → ./base/index.css */

/* 5. Import components */
@import "./components";        /* → ./components/index.css */

/* 6. Import theme-specific styles */
@import "./theme";             /* → ./theme/index.css */

/* 7. Import utilities */
@import "./utilities";         /* → ./utilities/index.css */

/* 8. Import generated Hyva module styles */
@import "./generated/hyva-source.css";

/* 9. Import design tokens */
@import "./generated/hyva-tokens.css";

/* 10. Define custom design tokens */
@theme {
    --color-bg: var(--color-slate-50);
    --color-fg: var(--color-slate-950);
    --color-fg-secondary: var(--color-slate-600);
    --color-surface: var(--color-white);
}
```

**Key Concepts:**
- `@import "tailwindcss"` - Imports Tailwind v4 core
- `@source` - Defines which files to scan (replaces `content` in v3 config)
- `@theme` - Defines design tokens (CSS variables)

---

### Step 2: Understanding hyva.config.json (Design Tokens)

**Location:** `vendor/hyva-themes/magento2-default-theme/web/tailwind/hyva.config.json`

**Purpose:** Defines design tokens (colors, form styling, etc.) for Tailwind v4. Replaces parts of the old `tailwind.config.js`.

**Content:**

```json
{
  "tailwind": {
    "include": [],  /* Additional Tailwind plugins to include */
    "exclude": []   /* Tailwind features to exclude */
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

**Key Features:**
- **OKLCH colors** - Modern color space for better perceptual uniformity
- **Design tokens** - Centralized theme values
- **Form defaults** - Consistent form styling across the theme

---

### Step 3: Understanding package.json

**Location:** `vendor/hyva-themes/magento2-default-theme/web/tailwind/package.json`

**Purpose:** Defines NPM dependencies and build scripts for Tailwind v4.

**Content:**

```json
{
  "name": "@hyva-themes/magento2-default-theme",
  "version": "3.0.0",
  "type": "module",
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
    "prebuild": "npm run generate",
    "build": "npx tailwindcss -i tailwind-source.css -o ../css/styles.css --minify"
  },
  "engines": {
    "node": ">=20.0.0"
  }
}
```

**Scripts Explained:**
- `npm run generate` - Generates `hyva-source.css` and `hyva-tokens.css`
- `npm run watch` - Development mode (auto-rebuild on changes)
- `npm run build` - Production build (minified output)

**Important:**
- Requires Node.js >= 20.0.0
- Uses Tailwind CSS v4.1.15 (not v3)
- Outputs to `../css/styles.css` (not `tailwind.css`)

---

### Step 4: Complete Tailwind CSS Flow (Request to Browser)

**This section explains how Tailwind CSS works in Hyvä from a browser request to final CSS rendering.**

---

#### 🔄 Complete Sequence: How Tailwind CSS Works

```
┌──────────────────────────────────────────────────────────────────┐
│ USER BROWSER → SERVER REQUEST                                    │
└──────────────────────────────────────────────────────────────────┘

STEP 1: User visits page
        └─→ Browser requests: http://yoursite.com/

STEP 2: Magento processes layout XML
        └─→ File: vendor/hyva-themes/magento2-default-theme/
                  Magento_Theme/layout/default.xml
            <page>
                <update handle="default_hyva"/>  ← Loads Hyvä setup
            </page>

STEP 3: Load Hyvä default layout
        └─→ File: vendor/hyva-themes/magento2-theme-module/
                  src/view/frontend/layout/default_hyva.xml
            - Loads Alpine.js and JavaScript components

STEP 4: Load CSS in <head>
        └─→ File: vendor/hyva-themes/magento2-default-theme/
                  Magento_Theme/layout/default_head_blocks.xml
            <head>
                <css src="css/styles.css"/>  ← THIS LOADS TAILWIND!
            </head>

STEP 5: Magento resolves CSS path
        └─→ Theme fallback system searches:
            1. app/design/frontend/[YourVendor]/[YourTheme]/web/css/styles.css
            2. vendor/hyva-themes/magento2-default-theme/web/css/styles.css ✓
            ✓ Found!

STEP 6: Static content deployment (after bin/magento setup:static-content:deploy)
        └─→ Copies to: pub/static/frontend/Hyva/default/en_US/css/styles.css
            File size: ~96 KB (minified)

STEP 7: Browser loads CSS
        └─→ URL: https://yoursite.com/static/version123/frontend/Hyva/default/en_US/css/styles.css
            Browser parses and applies styles


┌──────────────────────────────────────────────────────────────────┐
│ BUILD PROCESS - How styles.css Was Created                      │
└──────────────────────────────────────────────────────────────────┘

STEP 8: Entry point - tailwind-source.css
        └─→ File: vendor/hyva-themes/magento2-default-theme/
                  web/tailwind/tailwind-source.css

            /* Import Hyva modules CSS */
            @import "@hyva-themes/hyva-modules/css";

            /* Import Tailwind CSS v4 */
            @import "tailwindcss" source(none);

            /* Define content sources */
            @source "../../**/*.phtml";   ← Scan all templates
            @source "../../**/*.xml";      ← Scan all layouts

            /* Import layers */
            @import "./base";              ← Base styles
            @import "./components";        ← Components
            @import "./theme";             ← Theme styles
            @import "./utilities";         ← Utilities
            @import "./generated/hyva-source.css";   ← Auto-generated
            @import "./generated/hyva-tokens.css";   ← Design tokens

STEP 9: Import base layer
        └─→ File: web/tailwind/base/index.css
            @import "./preflight.css";  ← Tailwind CSS reset
            @import "./print.css";      ← Print styles

            @layer base {
                :root { scrollbar-color: var(--color-primary); }
                body { background: var(--color-bg); }
            }

            [x-cloak] { display: none !important; }  ← Alpine.js

STEP 10: Import theme layer
         └─→ File: web/tailwind/theme/index.css
             @import "./form.css";              ← Form elements
             @import "./product-price.css";     ← Product prices
             @import "./page-cms.css";          ← CMS pages
             @import "./page-catalog.css";      ← Catalog pages
             (+ more theme files)

STEP 11: Read design tokens
         └─→ File: web/tailwind/hyva.config.json
             {
               "tokens": {
                 "values": {
                   "color": {
                     "primary": { "DEFAULT": "oklch(46% 0.2 265)" },
                     "secondary": { "DEFAULT": "oklch(53% 0.15 150)" }
                   }
                 }
               }
             }

STEP 12: NPM build scripts
         └─→ File: web/tailwind/package.json
             {
               "scripts": {
                 "generate": "npx hyva-sources && npx hyva-tokens",
                 "watch": "npx tailwindcss -i tailwind-source.css -o ../css/styles.css --watch",
                 "build": "npx tailwindcss -i tailwind-source.css -o ../css/styles.css --minify"
               }
             }

STEP 13: Build process runs
         └─→ Command: npm run build
             ├─→ npx hyva-sources
             │   └─→ Generates: generated/hyva-source.css
             │       (Hyva-compatible modules CSS)
             │
             ├─→ npx hyva-tokens
             │   └─→ Generates: generated/hyva-tokens.css
             │       :root {
             │         --color-primary: oklch(46% 0.2 265);
             │         --color-secondary: oklch(53% 0.15 150);
             │       }
             │
             └─→ npx tailwindcss -i tailwind-source.css -o ../css/styles.css --minify
                 Input:  tailwind-source.css
                 Output: ../css/styles.css (96KB minified)

STEP 14: Generated files created
         ├─→ generated/hyva-source.css   (Auto-generated module CSS)
         └─→ generated/hyva-tokens.css   (Auto-generated design tokens)

STEP 15: Final output - styles.css
         └─→ File: vendor/hyva-themes/magento2-default-theme/web/css/styles.css
             /*! tailwindcss v4.1.12 | MIT License */
             /* Single-line minified CSS containing:
                - Tailwind CSS reset (Preflight)
                - CSS custom properties (design tokens)
                - Base styles
                - Component classes
                - Utility classes
                - Theme customizations
             */

STEP 16: Tailwind scans for classes
         └─→ Scans paths defined by @source directives:
             ../../**/*.phtml  ← All template files
             ../../**/*.xml    ← All layout files

             Example files scanned:
             - Magento_Catalog/templates/product/list.phtml
             - Magento_Catalog/layout/catalog_product_view.xml
             - Magento_Theme/templates/html/header.phtml

             Finds classes like:
             <div class="flex items-center gap-4 px-6 py-4">
                          ↓
             Generates corresponding CSS utilities in styles.css

STEP 17: Browser loads and applies CSS
         └─→ Browser receives CSS file
             ├─→ Parses CSS rules
             ├─→ Builds CSSOM (CSS Object Model)
             ├─→ Combines with DOM
             └─→ Renders styled page
```

---

#### 📊 Visual Flow Diagram

```
                    ┌─────────────────┐
                    │  User Request   │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │ Magento Layout  │
                    │  Processing     │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │  default.xml    │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │ default_hyva.xml│
                    │  (Alpine.js)    │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │default_head_    │
                    │blocks.xml       │
                    │<css src="css/   │
                    │styles.css"/>    │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │ Resolve Path    │
                    │ vendor/hyva-    │
                    │ themes/.../web/ │
                    │ css/styles.css  │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │ Static Deploy   │
                    │ Copy to pub/    │
                    │ static/         │
                    └────────┬────────┘
                             ↓
                    ┌─────────────────┐
                    │ Browser Loads   │
                    │  styles.css     │
                    └─────────────────┘

╔═══════════════════════════════════════════════════════════╗
║  HOW styles.css WAS BUILT (Build Process)                ║
╚═══════════════════════════════════════════════════════════╝

        ┌──────────────────────────┐
        │ tailwind-source.css      │
        │  @import "tailwindcss"   │
        │  @source ".../*.phtml"   │
        └────────────┬─────────────┘
                     ↓
        ┌──────────────────────────┐
        │ Import Layers            │
        ├─→ base/index.css         │
        ├─→ components/index.css   │
        ├─→ theme/index.css        │
        ├─→ utilities/index.css    │
        └────────────┬─────────────┘
                     ↓
        ┌──────────────────────────┐
        │ Read hyva.config.json    │
        │  - Design tokens         │
        │  - Color palette         │
        │  - Form defaults         │
        └────────────┬─────────────┘
                     ↓
        ┌──────────────────────────┐
        │ npm run build            │
        ├─→ npx hyva-sources       │
        ├─→ npx hyva-tokens        │
        └─→ npx tailwindcss        │
             └────────┬─────────────┘
                      ↓
        ┌──────────────────────────┐
        │ Generate Files           │
        ├─→ hyva-source.css        │
        └─→ hyva-tokens.css        │
             └────────┬─────────────┘
                      ↓
        ┌──────────────────────────┐
        │ Compile & Minify         │
        │  - Scan templates        │
        │  - Extract classes       │
        │  - Generate CSS          │
        │  - Minify output         │
        └────────────┬─────────────┘
                     ↓
        ┌──────────────────────────┐
        │ Output: ../css/styles.css│
        │  Size: ~96KB             │
        └──────────────────────────┘
```

---

#### 🗂️ Complete File Tree

```
/var/www/html/m247/
│
├── vendor/hyva-themes/magento2-default-theme/
│   │
│   ├── Magento_Theme/
│   │   └── layout/
│   │       ├── default.xml                     [STEP 2] ← Main layout
│   │       └── default_head_blocks.xml         [STEP 4] ← Loads CSS
│   │
│   ├── web/
│   │   ├── css/
│   │   │   └── styles.css                      [STEP 15] ← Final CSS (96KB)
│   │   │
│   │   └── tailwind/
│   │       ├── tailwind-source.css             [STEP 8] ← Entry point
│   │       ├── hyva.config.json                [STEP 11] ← Design tokens
│   │       ├── package.json                    [STEP 12] ← Build scripts
│   │       │
│   │       ├── base/
│   │       │   ├── index.css                   [STEP 9] ← Base layer
│   │       │   ├── preflight.css               ← CSS reset
│   │       │   └── print.css                   ← Print styles
│   │       │
│   │       ├── theme/
│   │       │   ├── index.css                   [STEP 10] ← Theme layer
│   │       │   ├── form.css
│   │       │   ├── product-price.css
│   │       │   ├── page-cms.css
│   │       │   └── ...
│   │       │
│   │       ├── components/
│   │       │   └── index.css
│   │       │
│   │       ├── utilities/
│   │       │   └── index.css
│   │       │
│   │       └── generated/
│   │           ├── hyva-source.css             [STEP 14] ← Auto-generated
│   │           └── hyva-tokens.css             [STEP 14] ← Tokens
│   │
│   └── etc/
│       ├── view.xml
│       └── hyva-libraries.json                 ← Alpine v3 config
│
├── vendor/hyva-themes/magento2-theme-module/
│   └── src/view/frontend/layout/
│       └── default_hyva.xml                    [STEP 3] ← Hyvä layout
│
└── pub/static/frontend/Hyva/default/en_US/
    └── css/
        └── styles.css                          [STEP 6] ← Deployed CSS
```

---

#### ⚡ Quick Reference: Build Workflow

```bash
# DEVELOPMENT MODE
cd vendor/hyva-themes/magento2-default-theme/web/tailwind/
npm install                    # Install dependencies (Node >= 20)
npm run watch                  # Watch mode (auto-rebuild)
  ↓
  ├─→ Generates: generated/hyva-source.css
  ├─→ Generates: generated/hyva-tokens.css
  └─→ Compiles: tailwind-source.css → ../css/styles.css

# PRODUCTION MODE
npm run build                  # Production build (minified)
  ↓
  └─→ Output: ../css/styles.css (96KB)

# MAGENTO DEPLOYMENT
cd /var/www/html/m247
bin/magento setup:static-content:deploy -f
  ↓
  └─→ Copies: web/css/styles.css → pub/static/.../css/styles.css

bin/magento cache:clean
  ↓
  └─→ Browser loads fresh CSS
```

---

### Step 5: How Tailwind CSS is Loaded in Browser

**Layout XML:** `vendor/hyva-themes/magento2-default-theme/Magento_Theme/layout/default_head_blocks.xml`

```xml
<?xml version="1.0"?>
<page>
    <head>
        <meta name="viewport" content="width=device-width, initial-scale=1"/>
        <css src="css/styles.css"/>  <!-- ← Loads compiled Tailwind CSS -->
    </head>
</page>
```

**Main Layout:** `vendor/hyva-themes/magento2-default-theme/Magento_Theme/layout/default.xml`

```xml
<?xml version="1.0"?>
<page>
    <update handle="default_hyva"/>  <!-- Loads Hyvä-specific setup -->
    <body>
        <!-- Page structure blocks -->
    </body>
</page>
```

**Complete Flow:**

```
1. Magento processes layout XML
   ↓
2. default.xml includes default_hyva.xml
   ↓
3. default_head_blocks.xml adds <css src="css/styles.css"/>
   ↓
4. Magento resolves path:
   vendor/hyva-themes/magento2-default-theme/web/css/styles.css
   ↓
5. Static deployment copies to:
   pub/static/frontend/Hyva/default/en_US/css/styles.css
   ↓
6. Browser loads CSS
```

---

### Step 6: Building Tailwind CSS (Step-by-Step Commands)

#### For Development (Hyvä Default Theme):

```bash
# 1. Navigate to Tailwind directory
cd vendor/hyva-themes/magento2-default-theme/web/tailwind/

# 2. Install NPM dependencies (first time only - requires Node.js >= 20.0.0)
npm install

# 3. Watch mode (auto-rebuild on changes)
npm run watch
# This runs:
# - npx hyva-sources (generates generated/hyva-source.css)
# - npx hyva-tokens (generates generated/hyva-tokens.css)
# - npx tailwindcss -i tailwind-source.css -o ../css/styles.css --watch
```

#### For Production:

```bash
# Build minified CSS for production
npm run build
# This runs:
# - npx hyva-sources
# - npx hyva-tokens
# - npx tailwindcss -i tailwind-source.css -o ../css/styles.css --minify
```

#### For Custom Theme:

```bash
# 1. Copy Tailwind setup to your theme
cp -r vendor/hyva-themes/magento2-default-theme/web/tailwind \
      app/design/frontend/MyCompany/hyva-custom/web/

# 2. Navigate to your theme's Tailwind directory
cd app/design/frontend/MyCompany/hyva-custom/web/tailwind/

# 3. Install dependencies
npm install

# 4. Build
npm run build

# 5. Deploy to Magento static files
cd /var/www/html/m247
php bin/magento setup:static-content:deploy -f
php bin/magento cache:clean
```

---

### Step 7: Where Compiled CSS is Stored

**After Compilation:**
- `vendor/hyva-themes/magento2-default-theme/web/css/styles.css` - Final compiled CSS (~96KB minified)

**After Magento Static Content Deploy:**
- `pub/static/frontend/Hyva/default/en_US/css/styles.css`
- `pub/static/frontend/MyCompany/hyva-custom/en_US/css/styles.css`

**Browser loads from:**
```
https://yourstore.com/static/version123456789/frontend/Hyva/default/en_US/css/styles.css
```

**File Contents:**
```css
/*! tailwindcss v4.1.12 | MIT License | https://tailwindcss.com */
@layer properties{...}
/* Single-line minified CSS containing all utilities */
```

---

## Alpine.js Setup

### Overview

Hyvä uses **Alpine.js v3** as a lightweight JavaScript framework for reactive UI components. Alpine.js is loaded from CDN or bundled, with plugins for extended functionality.

---

### File Structure for Alpine.js

```
vendor/hyva-themes/magento2-theme-module/src/
└── view/frontend/
    ├── layout/
    │   └── default_hyva.xml                              # Alpine.js layout configuration
    └── templates/
        └── page/
            └── js/
                ├── alpinejs.phtml                        # Main Alpine.js loader
                ├── plugins/
                │   ├── snap-slider.phtml                 # Snap slider plugin
                │   ├── htmldialog.phtml                  # Dialog plugin
                │   ├── intersect.phtml                   # Intersect plugin
                │   ├── ignore.phtml                      # Ignore plugin
                │   └── defer.phtml                       # Defer plugin
                ├── alpine-defer-rules.phtml              # Defer configuration
                ├── cookies.phtml                         # Cookie handling
                ├── private-content.phtml                 # Private content
                └── variables.phtml                       # JavaScript variables
```

---

### Step 1: Understanding default_hyva.xml (Alpine.js Loading)

**Location:** `vendor/hyva-themes/magento2-theme-module/src/view/frontend/layout/default_hyva.xml`

```xml
<?xml version="1.0"?>
<page>
    <body>
        <!-- Alpine.js loaded before </body> for non-blocking load -->
        <referenceContainer name="before.body.end">

            <!-- Main Alpine.js block -->
            <block name="script-alpine-js" template="Hyva_Theme::page/js/alpinejs.phtml">

                <!-- Plugins (loaded BEFORE Alpine.js initializes) -->
                <block name="alpine-plugin-snap-slider"
                       template="Hyva_Theme::page/js/plugins/snap-slider.phtml"/>

                <block name="alpine-plugin-htmldialog"
                       template="Hyva_Theme::page/js/plugins/htmldialog.phtml"/>

                <block name="alpine-plugin-intersect"
                       template="Hyva_Theme::page/js/plugins/intersect.phtml"/>

                <block name="alpine-plugin-ignore"
                       template="Hyva_Theme::page/js/plugins/ignore.phtml"/>

                <block name="alpine-plugin-defer"
                       template="Hyva_Theme::page/js/plugins/defer.phtml">

                    <!-- Defer rules configuration -->
                    <block name="alpine-defer-rules"
                           template="Hyva_Theme::page/js/alpine-defer-rules.phtml"/>
                </block>
            </block>

            <!-- Utility scripts -->
            <block name="script-cookies" template="Hyva_Theme::page/js/cookies.phtml"/>
            <block name="script-private-content" template="Hyva_Theme::page/js/private-content.phtml"/>

        </referenceContainer>
    </body>
</page>
```

**Why before.body.end?**
- Non-blocking: HTML renders first, then JavaScript loads
- DOM is ready when scripts execute
- Optimal performance

---

### Step 2: Understanding alpinejs.phtml

**Location:** `vendor/hyva-themes/magento2-theme-module/src/view/frontend/templates/page/js/alpinejs.phtml`

**Purpose:** Main Alpine.js loader template

**Content (simplified):**

```php
<?php
/**
 * Alpine.js Main Loader
 * Loads Alpine.js core and initializes plugins
 */
?>
<script>
    /**
     * Alpine.js Plugin Registration
     * Plugins must be registered BEFORE Alpine.start()
     */
    window.Alpine = window.Alpine || {};

    // Plugin initialization placeholder
    window.Alpine.plugins = window.Alpine.plugins || [];

    <?= /* @noEscape */ $block->getChildHtml() ?>
    // Child blocks render plugin code here

</script>

<!-- Load Alpine.js Core -->
<script defer src="https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js"></script>

<!-- OR load from local file -->
<!-- <script defer src="<?= $escaper->escapeUrl($block->getViewFileUrl('Hyva_Theme::js/alpine.min.js')) ?>"></script> -->
```

**Load Order:**
1. Plugin definitions (child blocks)
2. Alpine.js core library
3. Alpine automatically starts

---

### Step 3: Understanding Alpine.js Plugins

#### Plugin: Intersect

**File:** `plugins/intersect.phtml`

**Purpose:** Trigger actions when elements enter viewport (lazy loading, animations)

**Usage:**
```html
<div x-data="{ show: false }"
     x-intersect="show = true">
    <div x-show="show" x-transition>
        Appears when scrolled into view
    </div>
</div>
```

#### Plugin: Defer

**File:** `plugins/defer.phtml`

**Purpose:** Delay Alpine.js component initialization until needed (performance optimization)

**Usage:**
```html
<!-- Load component when it enters viewport -->
<div x-data="expensiveComponent()" x-defer="intersect">
    <!-- Heavy component -->
</div>

<!-- Load component on user interaction -->
<div x-data="slideshow()" x-defer="click">
    <!-- Slideshow -->
</div>
```

#### Plugin: HTMLDialog

**File:** `plugins/htmldialog.phtml`

**Purpose:** Native HTML `<dialog>` element support

**Usage:**
```html
<dialog x-data="{ open: false }"
        x-dialog="open">
    <p>Dialog content</p>
    <button @click="open = false">Close</button>
</dialog>

<button @click="open = true">Open Dialog</button>
```

#### Plugin: Snap Slider

**File:** `plugins/snap-slider.phtml`

**Purpose:** Touch-friendly product sliders with snap scrolling

**Usage:**
```html
<div x-data="hyva.snapSlider()"
     x-snap-slider>
    <div x-snap-slider-item>Slide 1</div>
    <div x-snap-slider-item>Slide 2</div>
    <div x-snap-slider-item>Slide 3</div>
</div>
```

---

### Step 4: Alpine.js Loading Flow

```
┌─────────────────────────────────────────────────────────────┐
│ 1. HTML Renders                                             │
│    - Page DOM is built                                      │
│    - Alpine directives (x-data, x-show) in HTML             │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 2. Load Plugins (from child blocks)                         │
│    - window.Alpine.plugin() calls                           │
│    - Register BEFORE Alpine.start()                         │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 3. Load Alpine.js Core (alpine.min.js)                      │
│    - Download from CDN or local                             │
│    - Initialize Alpine                                      │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 4. Alpine.start() (automatic)                               │
│    - Scan DOM for x-data components                         │
│    - Initialize reactive data                               │
│    - Bind event listeners                                   │
└────────────────┬────────────────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────────────────┐
│ 5. Components Active                                        │
│    - Alpine directives work                                 │
│    - User interactions trigger updates                      │
└─────────────────────────────────────────────────────────────┘
```

---

### Step 5: JavaScript Variables (Magento Data)

**File:** `page/js/variables.phtml`

**Purpose:** Pass PHP/Magento data to JavaScript

**Example:**
```php
<script>
    window.hyva = window.hyva || {};

    hyva.config = {
        baseUrl: '<?= $escaper->escapeJs($block->getBaseUrl()) ?>',
        storeCode: '<?= $escaper->escapeJs($block->getStoreCode()) ?>',
        currency: '<?= $escaper->escapeJs($block->getCurrentCurrencyCode()) ?>',
        priceFormat: <?= /* @noEscape */ $block->getPriceFormatJson() ?>,
        customer: {
            loggedIn: <?= $block->isCustomerLoggedIn() ? 'true' : 'false' ?>
        }
    };
</script>
```

**Access in Alpine.js:**
```html
<div x-data="{ baseUrl: window.hyva.config.baseUrl }">
    <a :href="baseUrl + '/checkout'">Checkout</a>
</div>
```

---

### Step 6: Adding Custom Alpine.js Plugin

**Step-by-Step:**

#### 1. Create Plugin Template

**File:** `app/design/frontend/MyCompany/hyva-custom/Magento_Theme/templates/page/js/custom-alpine-plugin.phtml`

```php
<script>
document.addEventListener('alpine:init', () => {
    // Custom Alpine.js Magic Property
    Alpine.magic('customHelper', () => {
        return (value) => {
            return value.toUpperCase();
        };
    });

    // Custom Alpine.js Directive
    Alpine.directive('highlight', (el, { expression }, { evaluate }) => {
        el.classList.add('bg-yellow-200');
    });
});
</script>
```

#### 2. Add to Layout XML

**File:** `app/design/frontend/MyCompany/hyva-custom/Magento_Theme/layout/default.xml`

```xml
<?xml version="1.0"?>
<page>
    <body>
        <referenceBlock name="script-alpine-js">
            <block name="alpine-plugin-custom"
                   template="Magento_Theme::page/js/custom-alpine-plugin.phtml"
                   before="alpine-plugin-intersect"/>
        </referenceBlock>
    </body>
</page>
```

#### 3. Use Custom Plugin

```html
<!-- Use custom magic property -->
<div x-data="{ text: 'hello' }">
    <p x-text="$customHelper(text)"></p> <!-- Outputs: HELLO -->
</div>

<!-- Use custom directive -->
<div x-highlight>This will be highlighted</div>
```

---

## File Structure Deep Dive

### Complete File Structure Map

```
Hyvä Theme Root
├── vendor/hyva-themes/
│   ├── magento2-default-theme/             # Default Hyvä theme
│   │   ├── web/
│   │   │   └── tailwind/
│   │   │       ├── tailwind.config.js      # Tailwind config
│   │   │       ├── tailwind-source.css     # Source CSS
│   │   │       ├── tailwind.css            # Compiled CSS (generated)
│   │   │       ├── package.json            # NPM dependencies
│   │   │       └── components/
│   │   │           └── custom-components.css
│   │   ├── Magento_Theme/
│   │   │   └── templates/
│   │   │       └── html/
│   │   │           ├── header.phtml
│   │   │           └── footer.phtml
│   │   └── ...
│   │
│   └── magento2-theme-module/              # Hyvä core module
│       └── src/
│           ├── view/frontend/
│           │   ├── layout/
│           │   │   └── default_hyva.xml    # Alpine.js & core setup
│           │   ├── templates/
│           │   │   └── page/
│           │   │       └── js/
│           │   │           ├── alpinejs.phtml
│           │   │           ├── plugins/
│           │   │           │   ├── snap-slider.phtml
│           │   │           │   ├── htmldialog.phtml
│           │   │           │   ├── intersect.phtml
│           │   │           │   ├── ignore.phtml
│           │   │           │   └── defer.phtml
│           │   │           ├── variables.phtml
│           │   │           ├── cookies.phtml
│           │   │           └── private-content.phtml
│           │   └── web/
│           │       ├── js/
│           │       │   └── alpine-plugins/
│           │       └── css/
│           └── ViewModel/                   # Hyvä ViewModels
│               ├── StoreConfig.php
│               ├── CurrentProduct.php
│               ├── Modal.php
│               └── ...
│
├── app/design/frontend/MyCompany/hyva-custom/  # Your custom theme
│   ├── registration.php
│   ├── theme.xml
│   ├── composer.json
│   ├── etc/
│   │   └── view.xml
│   ├── web/
│   │   ├── tailwind/
│   │   │   ├── tailwind.config.js          # Your custom Tailwind config
│   │   │   ├── tailwind-source.css         # Your custom source CSS
│   │   │   ├── package.json
│   │   │   └── components/
│   │   │       └── custom-components.css
│   │   ├── css/
│   │   │   └── source/
│   │   ├── js/
│   │   │   └── custom.js
│   │   └── images/
│   ├── Magento_Theme/
│   │   ├── layout/
│   │   │   └── default.xml                 # Override/extend layouts
│   │   └── templates/
│   │       ├── html/
│   │       │   ├── header.phtml
│   │       │   └── footer.phtml
│   │       └── page/
│   │           └── js/
│   │               └── custom-alpine-plugin.phtml
│   ├── Magento_Catalog/
│   │   ├── layout/
│   │   │   ├── catalog_product_view.xml
│   │   │   └── catalog_category_view.xml
│   │   └── templates/
│   │       └── product/
│   └── ...
│
└── pub/static/                              # Generated static files (after deployment)
    └── frontend/
        ├── Hyva/default/en_US/
        │   ├── css/
        │   │   └── tailwind.css            # Deployed Tailwind CSS
        │   └── js/
        └── MyCompany/hyva-custom/en_US/
            ├── css/
            │   └── tailwind.css
            └── js/
```

---

## Compilation Process

### Complete Build & Deploy Workflow

```
┌──────────────────────────────────────────────────────────────────┐
│ DEVELOPMENT PHASE                                                │
└──────────────────────────────────────────────────────────────────┘

1. Edit Templates
   ├── Add/modify .phtml files
   ├── Use Tailwind classes in templates
   └── Create Alpine.js components

2. Customize Tailwind Config
   ├── Edit web/tailwind/tailwind.config.js
   ├── Modify colors, fonts, spacing
   └── Add custom components/utilities

3. Compile Tailwind CSS
   ├── cd web/tailwind/
   ├── npm run watch (auto-rebuild)
   └── Output: tailwind.css (in same directory)

4. Test Locally
   ├── Clear Magento cache: php bin/magento cache:flush
   ├── Deploy static: php bin/magento setup:static-content:deploy -f
   └── Test in browser

┌──────────────────────────────────────────────────────────────────┐
│ PRODUCTION DEPLOYMENT                                            │
└──────────────────────────────────────────────────────────────────┘

1. Build Production CSS
   ├── cd web/tailwind/
   ├── npm run build (minified)
   └── Commit tailwind.css to repo (optional)

2. Magento Production Setup
   ├── php bin/magento deploy:mode:set production
   ├── php bin/magento setup:di:compile
   ├── php bin/magento setup:static-content:deploy
   └── php bin/magento cache:flush

3. Verify Deployment
   ├── Check pub/static/ directory
   ├── Test frontend
   └── Run Lighthouse audit
```

---

## Customization Workflow

### Scenario: Adding Custom Colors

**1. Edit tailwind.config.js**

```javascript
// app/design/frontend/MyCompany/hyva-custom/web/tailwind/tailwind.config.js

module.exports = {
    theme: {
        extend: {
            colors: {
                brand: {
                    50: '#f0f9ff',
                    500: '#0ea5e9',  // Main brand color
                    900: '#0c4a6e',
                },
            },
        },
    },
}
```

**2. Rebuild CSS**

```bash
cd app/design/frontend/MyCompany/hyva-custom/web/tailwind/
npm run build
```

**3. Use in Templates**

```phtml
<button class="bg-brand-500 text-white px-6 py-3 rounded-md">
    Shop Now
</button>
```

**4. Deploy**

```bash
cd /var/www/html/m246p8
php bin/magento setup:static-content:deploy -f
php bin/magento cache:flush
```

---

### Scenario: Adding Alpine.js Store (Global State)

**1. Create Store Definition**

**File:** `Magento_Theme/templates/page/js/alpine-cart-store.phtml`

```php
<script>
document.addEventListener('alpine:init', () => {
    Alpine.store('cart', {
        items: [],

        addItem(product) {
            this.items.push(product);
        },

        removeItem(productId) {
            this.items = this.items.filter(item => item.id !== productId);
        },

        get itemCount() {
            return this.items.length;
        },

        get total() {
            return this.items.reduce((sum, item) => sum + item.price, 0);
        }
    });
});
</script>
```

**2. Add to Layout**

```xml
<referenceContainer name="before.body.end">
    <block name="alpine-cart-store"
           template="Magento_Theme::page/js/alpine-cart-store.phtml"
           after="script-alpine-js"/>
</referenceContainer>
```

**3. Use in Templates**

```html
<!-- Header cart count -->
<div x-data>
    <span x-text="$store.cart.itemCount"></span> items
</div>

<!-- Add to cart button -->
<button @click="$store.cart.addItem({ id: 1, name: 'Product', price: 29.99 })">
    Add to Cart
</button>
```

---

## Troubleshooting

### Tailwind CSS Not Working

**Issue:** Classes not applying

**Solutions:**
```bash
# 1. Rebuild Tailwind CSS
cd web/tailwind/
npm run build

# 2. Clear Magento cache
cd /var/www/html/m246p8
php bin/magento cache:flush

# 3. Redeploy static content
php bin/magento setup:static-content:deploy -f

# 4. Check content paths in tailwind.config.js
# Make sure your templates are included in the content array

# 5. Hard refresh browser (Ctrl+Shift+R)
```

---

### Alpine.js Not Initializing

**Issue:** `x-data` not working

**Solutions:**
```bash
# 1. Check browser console for errors
# Open DevTools > Console

# 2. Verify Alpine.js is loaded
# In console: console.log(Alpine)

# 3. Check plugin registration order
# Plugins must load BEFORE Alpine.js core

# 4. Verify defer attribute
# <script defer src="alpine.js"></script>

# 5. Check for JavaScript errors above Alpine.js script
```

---

### Custom Plugin Not Loading

**Issue:** Custom Alpine.js plugin not registered

**Checklist:**
1. ✅ Plugin template exists
2. ✅ Added to layout XML
3. ✅ Block name is unique
4. ✅ `before="alpine-plugin-intersect"` (loads before core)
5. ✅ Wrapped in `document.addEventListener('alpine:init', ...)`
6. ✅ Cache cleared
7. ✅ Static content deployed

---

### Performance Issues

**Issue:** Slow page load

**Optimizations:**
```javascript
// 1. Use x-defer for below-fold components
<div x-data="heavyComponent()" x-defer="intersect">

// 2. Minimize Tailwind output
// In tailwind.config.js, ensure content paths are specific

// 3. Use Alpine.js $nextTick for heavy operations
Alpine.effect(() => {
    Alpine.nextTick(() => {
        // Heavy operation
    });
});

// 4. Lazy load images
<img loading="lazy" src="..." />

// 5. Use production mode
php bin/magento deploy:mode:set production
```

---

## Quick Reference

### Essential Commands

```bash
# Tailwind CSS
cd web/tailwind/
npm install              # Install dependencies
npm run build            # Production build
npm run watch            # Development watch mode

# Magento
php bin/magento cache:flush                      # Clear cache
php bin/magento setup:static-content:deploy -f   # Deploy static files
php bin/magento dev:template-hints:enable        # Enable template hints

# Debugging
php bin/magento dev:template-hints:enable        # Show templates
tail -f var/log/system.log                       # Watch logs
```

### Essential Files

| Purpose | File Location |
|---------|---------------|
| Tailwind Config | `web/tailwind/tailwind.config.js` |
| Tailwind Source | `web/tailwind/tailwind-source.css` |
| Tailwind Output | `web/tailwind/tailwind.css` |
| Alpine.js Loader | `Hyva_Theme::page/js/alpinejs.phtml` |
| Alpine.js Plugins | `Hyva_Theme::page/js/plugins/*.phtml` |
| Layout Configuration | `layout/default_hyva.xml` |
| JS Variables | `Hyva_Theme::page/js/variables.phtml` |

---

## Summary

**Tailwind CSS Setup:**
1. Configured in `tailwind.config.js`
2. Source CSS in `tailwind-source.css`
3. Compiled with `npm run build`
4. Output to `tailwind.css`
5. Deployed to `pub/static/`
6. Loaded via layout XML

**Alpine.js Setup:**
1. Defined in `default_hyva.xml`
2. Plugins loaded via child blocks
3. Core loaded from CDN or local
4. Automatically initializes
5. Custom plugins via layout XML

**Key Principles:**
- Tailwind: Utility-first, JIT compilation, minimal output
- Alpine.js: Lightweight, reactive, declarative
- Hyvä: Performance-focused, developer-friendly

---

**Next Steps:**
- Practice building custom themes (see HYVA-LEARNING-PLAN.md Phase 5)
- Experiment with Tailwind customization
- Create custom Alpine.js components
- Build real-world projects

---

## 🆕 What's New in Hyvä v1.4.1

### Tailwind CSS v4 Changes

| Aspect | Tailwind v3 (Old) | Tailwind v4 (Hyvä v1.4.1) |
|--------|------------------|---------------------------|
| **Config File** | `tailwind.config.js` (JavaScript) | `hyva.config.json` (JSON) + `tailwind-source.css` |
| **Content Scanning** | `content: [...]` in config | `@source ".../*.phtml"` in CSS |
| **Import Syntax** | `@tailwind base/components/utilities` | `@import "tailwindcss"` |
| **Design Tokens** | `theme.extend` in config | `@theme { ... }` in CSS |
| **Output File** | `tailwind.css` | `../css/styles.css` |
| **Version** | ^3.3.0 | ^4.1.15 |
| **CLI** | `tailwindcss` | `@tailwindcss/cli` |
| **Node Required** | >= 14.0.0 | >= 20.0.0 |

### Key File Locations (Hyvä v1.4.1)

```
vendor/hyva-themes/magento2-default-theme/
├── web/
│   ├── css/
│   │   └── styles.css                    # ← Final output (not tailwind.css!)
│   └── tailwind/
│       ├── tailwind-source.css           # ← Entry point (not tailwind.config.js!)
│       ├── hyva.config.json              # ← Design tokens (JSON, not JS!)
│       ├── package.json                  # ← Build scripts
│       ├── base/, components/, theme/, utilities/
│       └── generated/
│           ├── hyva-source.css           # ← Auto-generated
│           └── hyva-tokens.css           # ← Auto-generated
└── Magento_Theme/layout/
    └── default_head_blocks.xml           # ← Loads css/styles.css
```

### Migration Notes (v3 → v4)

If you have a custom theme using Tailwind v3:

1. **Update package.json dependencies:**
   ```json
   "@tailwindcss/cli": "^4.1.12",
   "tailwindcss": "^4.1.15"
   ```

2. **Migrate tailwind.config.js → tailwind-source.css:**
   - Move `content` paths to `@source` directives
   - Move `theme.extend` to `@theme` blocks

3. **Update build scripts:**
   ```bash
   # Old
   npm run build  # → tailwind.css

   # New
   npm run build  # → ../css/styles.css
   ```

4. **Update layout XML:**
   ```xml
   <!-- Old -->
   <css src="css/tailwind.css"/>

   <!-- New -->
   <css src="css/styles.css"/>
   ```

---

**Documentation:**
- Tailwind CSS v4: https://tailwindcss.com/docs
- Alpine.js: https://alpinejs.dev
- Hyvä Docs: https://docs.hyva.io
- Hyvä v1.4.1 Changelog: https://gitlab.hyva.io/hyva-themes/magento2-default-theme/-/blob/1.4.1/CHANGELOG.md
