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

Hyvä v1.4.1 uses **Alpine.js v3** as a lightweight JavaScript framework for reactive UI components. Alpine.js is loaded via local bundled files (not CDN), with a plugin architecture that extends functionality.

**Key Features:**
- **Alpine.js v3.x** - Modern reactive framework
- **Deferred Loading** - x-defer attribute for performance
- **Plugin System** - Intersect, Defer, HTMLDialog, Snap Slider, Ignore
- **Event-Driven** - Uses `alpine:init` and `alpine:initialized` events
- **CSP Compatible** - Works with Content Security Policy

---

### File Structure for Alpine.js

```
vendor/hyva-themes/magento2-theme-module/src/
├── view/
│   ├── base/                                              # Base (frontend + adminhtml)
│   │   ├── templates/page/js/
│   │   │   ├── alpinejs.phtml                            # Main Alpine.js loader
│   │   │   ├── alpine-defer-rules.phtml                  # Defer configuration
│   │   │   └── plugins/
│   │   │       ├── v3/                                   # Alpine v3 plugins
│   │   │       │   ├── intersect.phtml                   # Intersection Observer
│   │   │       │   ├── defer.phtml                       # Deferred loading
│   │   │       │   ├── htmldialog.phtml                  # Dialog support
│   │   │       │   └── snap-slider.phtml                 # Carousel/slider
│   │   │       ├── v2/                                   # Alpine v2 plugins (legacy)
│   │   │       │   └── ...
│   │   │       ├── ignore.phtml                          # Plugin loader wrapper
│   │   │       ├── intersect.phtml                       # Plugin loader wrapper
│   │   │       ├── defer.phtml                           # Plugin loader wrapper
│   │   │       └── snap-slider.phtml                     # Plugin loader wrapper
│   │   └── web/js/
│   │       ├── alpine3.min.js                            # Alpine.js v3 core (minified)
│   │       ├── alpine3.js                                # Alpine.js v3 core
│   │       ├── alpine3-csp.min.js                        # CSP version
│   │       └── alpine.min.js                             # Alpine v2 (legacy)
│   │
│   └── frontend/                                         # Frontend-specific
│       ├── layout/
│       │   └── default_hyva.xml                          # Alpine.js layout config
│       └── templates/page/js/
│           ├── hyva.phtml                                # Hyva utility functions
│           ├── variables.phtml                           # Global JS variables
│           ├── cookies.phtml                             # Cookie management
│           ├── private-content.phtml                     # Customer sections
│           └── require-min-alpine-version.phtml          # Version check

vendor/hyva-themes/magento2-default-theme/
└── Magento_Theme/layout/
    └── default.xml                                       # Loads default_hyva handle
```

---

### Step 1: Understanding Layout XML Flow

#### Entry Point: default.xml

**Location:** `vendor/hyva-themes/magento2-default-theme/Magento_Theme/layout/default.xml:10`

```xml
<?xml version="1.0"?>
<page>
    <update handle="default_hyva"/>  <!-- ← Triggers Alpine.js loading -->
    <body>
        <!-- Page structure -->
    </body>
</page>
```

**Purpose:** Every page in Hyvä includes the `default_hyva` handle, which sets up Alpine.js infrastructure.

---

#### Core Layout: default_hyva.xml

**Location:** `vendor/hyva-themes/magento2-theme-module/src/view/frontend/layout/default_hyva.xml`

**Key Sections:**

```xml
<?xml version="1.0"?>
<page>
    <body>
        <!-- 1. HEAD SECTION: Global variables and Hyva utilities -->
        <referenceContainer name="head.additional">
            <block name="head.js" template="Hyva_Theme::page/js/variables.phtml"/>
            <block name="head.hyva-scripts" template="Hyva_Theme::page/js/hyva.phtml"/>
        </referenceContainer>

        <!-- 2. BEFORE BODY END: Alpine.js and plugins -->
        <referenceContainer name="before.body.end">

            <!-- Main Alpine.js block with child plugin blocks -->
            <block name="script-alpine-js" template="Hyva_Theme::page/js/alpinejs.phtml">

                <!-- Child blocks = Alpine.js plugins -->
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
                    <block name="alpine-defer-rules"
                           template="Hyva_Theme::page/js/alpine-defer-rules.phtml"/>
                </block>
            </block>

            <!-- Supporting scripts -->
            <block name="script-cookies" template="Hyva_Theme::page/js/cookies.phtml"/>
            <block name="script-private-content" template="Hyva_Theme::page/js/private-content.phtml"/>
            <block name="require-alpine-v3" template="Hyva_Theme::page/js/require-min-alpine-version.phtml"/>
        </referenceContainer>
    </body>
</page>
```

**Why before.body.end?**
- ✅ **Non-blocking:** HTML content renders first
- ✅ **DOM ready:** All elements exist when scripts execute
- ✅ **Performance:** Deferred loading with `defer` attribute
- ✅ **Plugin order:** Plugins register before Alpine.js initializes

---

### Step 2: Complete Alpine.js Loading Sequence

```
┌──────────────────────────────────────────────────────────────────┐
│ PHASE 1: HEAD SECTION (Loads First)                             │
└──────────────────────────────────────────────────────────────────┘

STEP 1: Global Variables Setup
        File: vendor/hyva-themes/magento2-theme-module/src/view/frontend/
              templates/page/js/variables.phtml

        <script>
            var BASE_URL = '/';
            var COOKIE_CONFIG = { ... };
            var CURRENT_STORE_CODE = 'default';
            window.hyva = window.hyva || {}
        </script>

STEP 2: Hyva Utility Functions
        File: vendor/hyva-themes/magento2-theme-module/src/view/frontend/
              templates/page/js/hyva.phtml

        Functions created:
        - hyva.getCookie() / hyva.setCookie()
        - hyva.getFormKey()
        - hyva.formatPrice()
        - hyva.str() / hyva.strf()
        - hyva.replaceDomElement()
        - hyva.trapFocus() / hyva.releaseFocus()
        - hyva.alpineInitialized(callback) ← Listens for 'alpine:initialized'

┌──────────────────────────────────────────────────────────────────┐
│ PHASE 2: BEFORE BODY END (Loads Last)                           │
└──────────────────────────────────────────────────────────────────┘

STEP 3: Plugin Registration Scripts (Execute First)

        3a. Snap Slider Plugin
            File: .../templates/page/js/plugins/snap-slider.phtml
            → Delegates to: .../plugins/v3/snap-slider.phtml

            Registers: Carousel/slider functionality
            Listens: alpine:init event

        3b. HTML Dialog Plugin
            File: .../templates/page/js/plugins/htmldialog.phtml
            → Delegates to: .../plugins/v3/htmldialog.phtml

            Registers: Native <dialog> support
            Listens: alpine:init event

        3c. Intersect Plugin
            File: .../templates/page/js/plugins/intersect.phtml
            → Delegates to: .../plugins/v3/intersect.phtml

            <script>
                document.addEventListener("alpine:init", () => {
                    window.Alpine.plugin(src_default);  // Registers x-intersect
                });
            </script>

            Provides: x-intersect directive

        3d. Ignore Plugin
            File: .../templates/page/js/plugins/ignore.phtml

            Provides: x-ignore directive support

        3e. Defer Plugin
            File: .../templates/page/js/plugins/defer.phtml
            → Delegates to: .../plugins/v3/defer.phtml

            <script>
                // Initializes deferred component loading
                window.addEventListener('alpine:init', initDeferredComponents);

                // Supports:
                // x-defer="interact"  - Load on user interaction
                // x-defer="intersect" - Load when visible
                // x-defer="idle"      - Load on browser idle (4000ms)
                // x-defer="eager"     - Load immediately
                // x-defer="event:foo" - Load on custom event
            </script>

            Child: alpine-defer-rules.phtml (custom defer rules)

STEP 4: Alpine.js Core Loading
        File: vendor/hyva-themes/magento2-theme-module/src/view/base/
              templates/page/js/alpinejs.phtml

        <?php
        $version = $viewModels->require(ThemeLibrariesConfig::class)
                              ->getVersionIdFor('alpine'); // Returns "3"
        ?>

        <?= $block->getChildHtml() ?>  <!-- Renders all plugin blocks -->

        <script type="module"
                src="Hyva_Theme::js/alpine3.min.js"
                defer
                crossorigin
        ></script>

        Actual file: vendor/hyva-themes/magento2-theme-module/src/view/base/
                     web/js/alpine3.min.js

        What happens:
        1. Alpine.js downloads (or loads from cache)
        2. Dispatches 'alpine:init' event → Plugins receive
        3. Plugins attach to Alpine via Alpine.plugin()
        4. Alpine scans DOM for [x-data] components
        5. Initializes all components
        6. Dispatches 'alpine:initialized' event

STEP 5: Deferred Component Handling

        When Alpine initializes:
        - Defer plugin scans for [x-defer] attributes
        - Marks components with x-ignore (pauses initialization)
        - Waits for trigger condition:

          x-defer="interact" → Wait for user interaction (touch/mouse/key)
          x-defer="intersect" → Wait for element to enter viewport
          x-defer="idle" → Wait for browser idle (requestIdleCallback)
          x-defer="eager" → Initialize immediately

        - When triggered, calls: Alpine.initTree(element)

STEP 6: Supporting Scripts

        6a. Cookie Management
            File: templates/page/js/cookies.phtml
            Purpose: Cookie consent handling

        6b. Private Content (Customer Sections)
            File: templates/page/js/private-content.phtml
            Purpose: Load customer-specific data via AJAX

        6c. Alpine Version Check
            File: templates/page/js/require-min-alpine-version.phtml
            Purpose: Validates Alpine v3 is loaded

┌──────────────────────────────────────────────────────────────────┐
│ PHASE 3: USER INTERACTION (Runtime)                             │
└──────────────────────────────────────────────────────────────────┘

STEP 7: Page Fully Interactive
        - Alpine components are reactive
        - User interactions trigger Alpine updates
        - Deferred components initialize on demand
        - Customer sections load (cart, wishlist, etc.)
```

---

### Step 3: Understanding Alpine.js Core Loader

**File:** `vendor/hyva-themes/magento2-theme-module/src/view/base/templates/page/js/alpinejs.phtml`

```php
<?php
use Hyva\Theme\Model\ViewModelRegistry;
use Hyva\Theme\ViewModel\ThemeLibrariesConfig;

/** @var ViewModelRegistry $viewModels */

// Determine Alpine version (2 or 3)
$version = $viewModels->require(ThemeLibrariesConfig::class)
                      ->getVersionIdFor('alpine') ?? 2;
?>

<?= $block->getChildHtml() ?>  <!-- Renders plugin blocks first -->

<script type="module"
        src="<?= $escaper->escapeUrl($block->getViewFileUrl("Hyva_Theme::js/alpine{$version}.min.js")) ?>"
        defer
        crossorigin
></script>
```

**Key Points:**
- **Version Detection:** Reads from `etc/hyva-libraries.json` to determine Alpine version
- **Module Type:** Uses `type="module"` for ES6 modules
- **Defer:** Doesn't block page rendering
- **Crossorigin:** Enables better error logging
- **Child Blocks:** Plugins render before the `<script>` tag (DOM order)

---

### Step 4: Understanding Alpine.js Plugins

#### Plugin Architecture

All plugins follow this wrapper pattern:

**Wrapper Template:** `plugins/intersect.phtml`

```php
<?php
use Hyva\Theme\ViewModel\ThemeLibrariesConfig;

$version = $viewModels->require(ThemeLibrariesConfig::class)
                      ->getVersionIdFor('alpine') ?: '2';

if (substr($version, -4) === '-csp') {
    $version = substr($version, 0, -4);
}
?>
<?= $block->fetchView($block->getTemplateFile(sprintf('Hyva_Theme::page/js/plugins/v%s/intersect.phtml', $version))) ?>
```

**Purpose:** Dynamically loads correct plugin version (v2 or v3).

---

#### Plugin: Intersect (Intersection Observer)

**File:** `vendor/hyva-themes/magento2-theme-module/src/view/base/templates/page/js/plugins/v3/intersect.phtml`

**Purpose:** Triggers actions when elements enter/leave viewport

**Implementation:**

```javascript
<script>
    document.addEventListener("alpine:init", () => {
        window.Alpine.plugin(function(Alpine) {
            Alpine.directive("intersect", Alpine.skipDuringClone(
                (el, { value, expression, modifiers }, { evaluateLater, cleanup }) => {
                    let evaluate = evaluateLater(expression);
                    let options = {
                        rootMargin: getRootMargin(modifiers),
                        threshold: getThreshold(modifiers)
                    };
                    let observer = new IntersectionObserver((entries) => {
                        entries.forEach((entry) => {
                            if (entry.isIntersecting === (value === "leave")) return;
                            evaluate();
                            modifiers.includes("once") && observer.disconnect();
                        });
                    }, options);
                    observer.observe(el);
                    cleanup(() => observer.disconnect());
                }
            ));
        });
    });
</script>
```

**Usage Examples:**

```html
<!-- Trigger when element enters viewport -->
<div x-data="{ show: false }"
     x-intersect="show = true">
    <div x-show="show" x-transition>
        Appears when scrolled into view
    </div>
</div>

<!-- With modifiers -->
<div x-intersect.once="loadImages()">          <!-- Fire once only -->
<div x-intersect.half="onHalfVisible()">       <!-- 50% visible threshold -->
<div x-intersect.full="onFullyVisible()">      <!-- 100% visible -->
<div x-intersect.margin.500px="trigger()">     <!-- 500px margin -->
```

---

#### Plugin: Defer (Deferred Component Loading)

**File:** `vendor/hyva-themes/magento2-theme-module/src/view/base/templates/page/js/plugins/v3/defer.phtml`

**Purpose:** Delays Alpine component initialization for performance optimization

**Implementation:**

```javascript
<script>
    (function () {
        const hasAlpine = new Promise(resolve => {
            window.addEventListener('alpine:initialized', resolve, {once: true});
        });

        const hasInteract = new Promise(resolve => {
            ['touchstart', 'mouseover', 'wheel', 'scroll', 'keydown']
                .forEach(type => window.addEventListener(type, resolve, {once: true}))
        });

        const onIntersect = (el) => {
            return new Promise(resolve => {
                const observer = new IntersectionObserver(entries => {
                    if (entries[0].isIntersecting) {
                        observer.disconnect();
                        resolve();
                    }
                });
                observer.observe(el);
            });
        }

        function runComponent(el) {
            hasAlpine.then(() => {
                el.removeAttribute('x-ignore');
                queueMicrotask(() => Alpine.initTree(el));
            });
        }

        function initDeferredComponents() {
            document.querySelectorAll('[x-data][x-defer]').forEach(el => {
                el.setAttribute('x-ignore', '');  // Pause initialization
                const deferUntil = el.getAttribute('x-defer').trim();

                switch (deferUntil) {
                    case 'interact':
                        hasInteract.then(() => runComponent(el));
                        break;
                    case 'intersect':
                        onIntersect(el).then(() => runComponent(el))
                        break;
                    case 'idle':
                        window.requestIdleCallback
                            ? requestIdleCallback(() => runComponent(el), {timeout: 4000})
                            : setTimeout(() => runComponent(el), 4000);
                        break;
                    case 'eager':
                        runComponent(el);
                        break;
                    default:
                        if (deferUntil.startsWith('event:')) {
                            window.addEventListener(
                                deferUntil.substring(6),
                                () => runComponent(el),
                                {once: true}
                            );
                        }
                }
            });
        }

        window.addEventListener('alpine:init', initDeferredComponents, {once: true});
    })()
</script>
```

**Usage Examples:**

```html
<!-- Defer until element enters viewport (lazy loading) -->
<div x-data="productSlider()" x-defer="intersect">
    <!-- Heavy image slider -->
</div>

<!-- Defer until user interaction (click, scroll, touch) -->
<div x-data="reviewsComponent()" x-defer="interact">
    <!-- Reviews tab -->
</div>

<!-- Defer until browser is idle -->
<div x-data="analyticsWidget()" x-defer="idle">
    <!-- Non-critical analytics -->
</div>

<!-- Defer until custom event -->
<div x-data="cart()" x-defer="event:cart-updated">
    <!-- Mini cart -->
</div>

<!-- Load immediately (same as no defer) -->
<div x-data="header()" x-defer="eager">
    <!-- Header component -->
</div>
```

**Configuration:**
- Idle timeout: `hyva_theme_general/alpine_defer/defer_idle_timeout` (default: 4000ms)

---

#### Plugin: HTMLDialog (Native Dialog Support)

**File:** `vendor/hyva-themes/magento2-theme-module/src/view/base/templates/page/js/plugins/v3/htmldialog.phtml`

**Purpose:** Provides Alpine.js integration with native HTML `<dialog>` element

**Usage:**

```html
<dialog x-data="{ open: false }"
        :open="open"
        @click.outside="open = false">
    <h2>Dialog Title</h2>
    <p>Dialog content</p>
    <button @click="open = false">Close</button>
</dialog>

<button @click="open = true">Open Dialog</button>
```

---

#### Plugin: Snap Slider (Carousel/Slider)

**File:** `vendor/hyva-themes/magento2-theme-module/src/view/base/templates/page/js/plugins/v3/snap-slider.phtml`

**Purpose:** Touch-friendly product carousels with CSS scroll-snap

**Usage:**

```html
<div x-data="hyva.snapSlider()"
     x-snap-slider
     class="flex overflow-x-auto snap-x">
    <div x-snap-slider-item class="snap-start">Slide 1</div>
    <div x-snap-slider-item class="snap-start">Slide 2</div>
    <div x-snap-slider-item class="snap-start">Slide 3</div>
</div>
```

---

### Step 5: Global Variables and Hyva Utilities

#### variables.phtml

**File:** `vendor/hyva-themes/magento2-theme-module/src/view/frontend/templates/page/js/variables.phtml`

**Purpose:** Exposes PHP/Magento data to JavaScript

```php
<script>
    var BASE_URL = '<?= /* @noEscape */ $block->getUrl('/') ?>';
    var THEME_PATH = '<?= /* @noEscape */ $block->getViewFileUrl('/') ?>';
    var COOKIE_CONFIG = {
        "path": "/",
        "domain": ".example.com",
        "secure": true,
        "lifetime": "3600",
        "cookie_restriction_enabled": false
    };
    var CURRENT_STORE_CODE = 'default';
    var CURRENT_WEBSITE_ID = '1';

    window.hyva = window.hyva || {}
    window.cookie_consent_groups = window.cookie_consent_groups || {}
</script>
```

---

#### hyva.phtml

**File:** `vendor/hyva-themes/magento2-theme-module/src/view/frontend/templates/page/js/hyva.phtml`

**Purpose:** Core Hyva utility functions

**Key Functions:**

```javascript
// Cookie management
hyva.getCookie(name)
hyva.setCookie(name, value, days)
hyva.setSessionCookie(name, value)

// Form handling
hyva.getFormKey()
hyva.postForm({ action: '/cart/add', data: { product: 123 } })

// Price formatting
hyva.formatPrice(29.99, showSign = false, options = {})

// String formatting (like PHP sprintf)
hyva.str('%1 items in %2', 3, 'cart')  // "3 items in cart"
hyva.strf('%0 items', 5)               // "5 items"

// DOM manipulation
hyva.replaceDomElement(selector, htmlContent)

// Focus management
hyva.trapFocus(element)
hyva.releaseFocus(element)

// Alpine.js helpers
hyva.alpineInitialized(callback)  // Runs when Alpine is ready
hyva.createBooleanObject('visible', false)  // Toggle helper

// Number parsing
hyva.safeParseNumber('123.45')  // For x-model.number alternative
```

**Alpine Integration:**

```javascript
// Alpine v3 integration
hyva.alpineInitialized = (fn) => {
    window.addEventListener('alpine:initialized', fn, {once: true})
}

// Empty component registration
window.addEventListener('alpine:init', () => {
    Alpine.data('{}', () => ({}))
}, {once: true});
```

**Usage in Templates:**

```html
<div x-data="{
    price: 29.99,
    formatted: hyva.formatPrice(29.99)
}">
    Price: <span x-text="formatted"></span>
</div>

<script>
    hyva.alpineInitialized(() => {
        console.log('Alpine is ready!');
    });
</script>
```

---

### Step 6: Event Timeline

```
Page Load Start
    ↓
┌─────────────────────────────────────────────────────────┐
│ HEAD SECTION                                            │
├─────────────────────────────────────────────────────────┤
│ • Global variables defined (BASE_URL, COOKIE_CONFIG)    │
│ • hyva.* utility functions created                      │
│ • hyva.alpineInitialized() registered                   │
└─────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────┐
│ BODY CONTENT RENDERS                                    │
└─────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────┐
│ BEFORE BODY END - Plugin Scripts Execute               │
├─────────────────────────────────────────────────────────┤
│ • Snap Slider: Registers 'alpine:init' listener         │
│ • HTML Dialog: Registers 'alpine:init' listener         │
│ • Intersect: Registers 'alpine:init' listener           │
│ • Defer: Registers 'alpine:init' listener               │
│ • Ignore: Registers 'alpine:init' listener              │
└─────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────┐
│ Alpine.js Core Loads (alpine3.min.js)                   │
└─────────────────────────────────────────────────────────┘
    ↓
EVENT: 'alpine:init' dispatched ✨
    ↓
┌─────────────────────────────────────────────────────────┐
│ Plugins Attach to Alpine                                │
├─────────────────────────────────────────────────────────┤
│ • Alpine.plugin(snapSlider)                             │
│ • Alpine.plugin(htmlDialog)                             │
│ • Alpine.directive('intersect', ...)                    │
│ • initDeferredComponents() runs                         │
│   - Scans for [x-defer] components                      │
│   - Marks them with x-ignore                            │
└─────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────┐
│ Alpine Scans DOM                                        │
├─────────────────────────────────────────────────────────┤
│ • Finds [x-data] components                             │
│ • Skips [x-ignore] (deferred) components                │
│ • Initializes reactive data                             │
│ • Binds event listeners                                 │
└─────────────────────────────────────────────────────────┘
    ↓
EVENT: 'alpine:initialized' dispatched ✨
    ↓
┌─────────────────────────────────────────────────────────┐
│ Callbacks Execute                                       │
├─────────────────────────────────────────────────────────┤
│ • hyva.alpineInitialized() callbacks fire               │
│ • Deferred components wait for triggers                 │
└─────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────┐
│ PAGE FULLY INTERACTIVE                                  │
└─────────────────────────────────────────────────────────┘
    ↓
┌─────────────────────────────────────────────────────────┐
│ USER INTERACTIONS / VIEWPORT CHANGES                    │
├─────────────────────────────────────────────────────────┤
│ User scrolls → Deferred component enters viewport       │
│              → Alpine.initTree(el) called               │
│              → Component becomes active                 │
│                                                          │
│ User clicks → x-defer="interact" components load        │
│             → Alpine.initTree(el) called                │
└─────────────────────────────────────────────────────────┘
```

---

### Step 7: Complete File Reference

| Purpose | File Location | Lines |
|---------|---------------|-------|
| **Entry Point** | `magento2-default-theme/Magento_Theme/layout/default.xml` | 10 |
| **Alpine Setup** | `magento2-theme-module/src/view/frontend/layout/default_hyva.xml` | 28-54 |
| **Alpine Loader** | `magento2-theme-module/src/view/base/templates/page/js/alpinejs.phtml` | 32-36 |
| **Alpine Core** | `magento2-theme-module/src/view/base/web/js/alpine3.min.js` | - |
| **Global Variables** | `magento2-theme-module/src/view/frontend/templates/page/js/variables.phtml` | 38-70 |
| **Hyva Utilities** | `magento2-theme-module/src/view/frontend/templates/page/js/hyva.phtml` | 484-494 |
| **Intersect Plugin** | `magento2-theme-module/src/view/base/templates/page/js/plugins/v3/intersect.phtml` | 14-73 |
| **Defer Plugin** | `magento2-theme-module/src/view/base/templates/page/js/plugins/v3/defer.phtml` | 59-87 |
| **HTMLDialog Plugin** | `magento2-theme-module/src/view/base/templates/page/js/plugins/v3/htmldialog.phtml` | - |
| **Snap Slider Plugin** | `magento2-theme-module/src/view/base/templates/page/js/plugins/v3/snap-slider.phtml` | - |

---

### Step 8: Adding Custom Alpine.js Plugin

**Step-by-Step:**

#### 1. Create Plugin Template

**File:** `app/design/frontend/MyCompany/hyva-custom/Magento_Theme/templates/page/js/custom-alpine-plugin.phtml`

```php
<?php
/**
 * Custom Alpine.js Plugin Example
 */
use Hyva\Theme\ViewModel\HyvaCsp;

/** @var HyvaCsp $hyvaCsp */
?>
<script>
document.addEventListener('alpine:init', () => {
    // Custom Alpine.js Magic Property
    Alpine.magic('uppercase', () => {
        return (value) => String(value).toUpperCase();
    });

    // Custom Alpine.js Directive
    Alpine.directive('highlight', (el, { expression }, { evaluate }) => {
        const color = evaluate(expression) || 'yellow';
        el.style.backgroundColor = color;
    });

    // Custom Alpine.js Data Component
    Alpine.data('customCounter', (start = 0) => ({
        count: start,
        increment() { this.count++ },
        decrement() { this.count-- }
    }));
});
</script>
<?php $hyvaCsp->registerInlineScript() ?>
```

#### 2. Add to Layout XML

**File:** `app/design/frontend/MyCompany/hyva-custom/Magento_Theme/layout/default.xml`

```xml
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <body>
        <referenceContainer name="before.body.end">
            <block name="alpine-plugin-custom"
                   template="Magento_Theme::page/js/custom-alpine-plugin.phtml"
                   before="script-alpine-js"/>
            <!-- before="script-alpine-js" ensures plugin loads BEFORE Alpine.js core -->
        </referenceContainer>
    </body>
</page>
```

#### 3. Use Custom Plugin

```html
<!-- Use custom magic property -->
<div x-data="{ text: 'hello world' }">
    <p x-text="$uppercase(text)"></p>  <!-- Outputs: HELLO WORLD -->
</div>

<!-- Use custom directive -->
<div x-highlight="'lightblue'">This will be highlighted</div>

<!-- Use custom data component -->
<div x-data="customCounter(10)">
    <button @click="decrement">-</button>
    <span x-text="count"></span>
    <button @click="increment">+</button>
</div>
```

#### 4. Clear Cache and Deploy

```bash
php bin/magento cache:clean layout
php bin/magento cache:clean full_page
php bin/magento setup:static-content:deploy -f
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
