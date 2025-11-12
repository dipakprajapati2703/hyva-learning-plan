# Hyvä Themes - Complete Learning Path

**Your Step-by-Step Guide to Mastering Hyvä Themes for Magento 2**

---

## 📚 Overview

This learning path contains **11 comprehensive documents** (17,925+ lines) covering everything from installation to advanced extension development. Follow the sequence below for structured learning.

---

## 🎯 Quick Start - Choose Your Path

### Path 1: Complete Beginner (Never Used Hyvä)
**Start here →** [Step 1: Installation](#step-1-installation) → Continue sequentially

### Path 2: Installed Hyvä, Want to Learn
**Start here →** [Step 2: Fallback Configuration](#step-2-fallback-configuration) → Continue from there

### Path 3: Building Custom Theme
**Start here →** [Step 9: Custom Theme](#step-9-custom-theme)

### Path 4: Building Extensions
**Start here →** [Step 10: Extensions](#step-10-extensions-development)

---

## 📖 Complete Learning Sequence

### **Step 1: Installation**
**Document:** [`01-HYVA-THEME-INSTALLATION.md`](./01-HYVA-THEME-INSTALLATION.md) | **1,721 lines**

**What you'll learn:**
- Prerequisites and system requirements
- Installing Hyvä Themes (Composer method)
- License key configuration
- Post-installation setup
- Activating the theme
- Troubleshooting installation issues

**Time:** 1-2 hours | **Difficulty:** ⭐ Easy

**Hands-on:**
```bash
# Install Hyvä packages
composer require hyva-themes/magento2-default-theme
composer require hyva-themes/magento2-theme-module

# Activate theme
php bin/magento setup:upgrade
php bin/magento cache:flush
```

**Next:** Configure fallback → [Step 2: Fallback Configuration](#step-2-fallback-configuration)

---

### **Step 2: Fallback Configuration**
**Document:** [`02-HYVA-THEME-FALLBACK.md`](./02-HYVA-THEME-FALLBACK.md) | **1,279 lines**

**What you'll learn:**
- When and why to use fallback
- Installing fallback module
- URL pattern configuration
- Checkout fallback setup
- Payment gateway fallback
- Testing and verification

**Time:** 2-3 hours | **Difficulty:** ⭐⭐ Medium

**Common Use Cases:**
```bash
# Install fallback module
composer require hyva-themes/magento2-theme-fallback

# Configure URL patterns
checkout/index
paypal/express/review
braintree/paypal/review

# Enable fallback
php bin/magento config:set hyva_theme_fallback/general/enable 1
```

**When to Use:**
- Checkout not compatible yet
- Payment gateways need Luma
- Third-party modules incompatible
- Temporary compatibility solution

**Next:** Understand file structure → [Step 3: Directory Structure](#step-3-directory-structure)

---

### **Step 3: Directory Structure**
**Document:** [`03-HYVA-DIRECTORY-STRUCTURE.md`](./03-HYVA-DIRECTORY-STRUCTURE.md) | **1,164 lines**

**What you'll learn:**
- Hyvä's three-tier architecture
- `hyva_` layout XML prefix convention
- `/hyva/` template directory pattern
- File naming conventions
- Template resolution order

**Time:** 1-2 hours | **Difficulty:** ⭐⭐ Medium

**Key Concepts:**
```
Three Tiers:
1. magento2-theme-module (Core functionality - 71 ViewModels)
2. magento2-default-theme (Default implementation - 307 templates)
3. Your Custom Theme (Inheritance & overrides)

Naming Patterns:
- hyva_catalog_product_view.xml (Hyvä-specific layout)
- templates/hyva/product/view.phtml (Hyvä-specific template)
```

**Hands-on:**
- Explore vendor/hyva-themes directory structure
- Find `hyva_` prefixed layouts
- Locate ViewModels in theme-module

**Next:** Configure Tailwind & Alpine.js → [Step 4: Tailwind & Alpine.js Setup](#step-4-tailwind--alpinejs-setup)

---

### **Step 4: Tailwind & Alpine.js Setup**
**Document:** [`04-HYVA-TAILWIND-ALPINE-SETUP.md`](./04-HYVA-TAILWIND-ALPINE-SETUP.md) | **1,082 lines**

**What you'll learn:**
- Tailwind CSS v4 configuration
- Compiling CSS with Node.js
- Alpine.js basics and setup
- Development vs production builds
- Hot reload configuration

**Time:** 2-3 hours | **Difficulty:** ⭐⭐ Medium

**Hands-on:**
```bash
# Navigate to Tailwind directory
cd vendor/hyva-themes/magento2-default-theme/web/tailwind

# Install dependencies
npm install

# Start watch mode
npm run watch
```

**Practice:**
- Modify colors in `hyva.config.json`
- Watch CSS auto-compile
- Test Tailwind utilities in templates

**Next:** Deep dive into complete learning plan → [Step 5: Complete Learning Plan](#step-5-complete-learning-plan)

---

### **Step 5: Complete Learning Plan** ⭐ **Master Reference Document**
**Document:** [`05-HYVA-LEARNING-PLAN.md`](./05-HYVA-LEARNING-PLAN.md) | **3,656 lines**

**What you'll learn:**
- 8 comprehensive phases (Foundation to Deployment)
- Advanced topics and scenarios
- Performance optimization
- Testing strategies
- Production deployment
- Maintenance and upgrades

**Time:** Ongoing reference | **Difficulty:** ⭐⭐⭐⭐⭐ Reference

**8 Phases:**
1. Foundation & Setup
2. Template Development
3. Advanced Components
4. Performance Optimization
5. Creating Custom Theme (with Best Practices)
6. Integration & Extensions
7. Testing & Quality
8. Deployment & Maintenance

**This is your master reference document** - return to it throughout your learning journey for:
- Deep-dive tutorials
- Advanced patterns
- Best practices
- Production workflows
- Troubleshooting guides

**Next:** Study the default theme → [Step 6: Default Theme Reference](#step-6-default-theme-reference)

---

### **Step 6: Default Theme Reference**
**Document:** [`06-HYVA-DEFAULT-THEME.md`](./06-HYVA-DEFAULT-THEME.md) | **1,637 lines**

**What you'll learn:**
- Complete magento2-default-theme structure
- 55+ module overrides
- 307 template files catalog
- 165 layout XML files
- Tailwind CSS v4 configuration
- PageBuilder integration
- ReCaptcha integration
- Customization patterns

**Time:** 3-4 hours | **Difficulty:** ⭐⭐⭐ Advanced

**Module Overrides:**
```
Most Important:
- Magento_Catalog (80+ templates) - Products, categories
- Magento_Checkout (30+ templates) - Cart, checkout
- Magento_Customer (40+ templates) - Account pages
- Magento_Theme (25+ templates) - Header, footer, navigation
- Magento_Wishlist (20+ templates) - Wishlist
```

**Hands-on:**
- Browse default theme templates
- Study header.phtml structure
- Examine product list/detail templates
- Understand layout hierarchy

**Next:** Understand the core module → [Step 7: Theme Module](#step-7-theme-module)

---

### **Step 7: Theme Module**
**Document:** [`07-HYVA-THEME-MODULE.md`](./07-HYVA-THEME-MODULE.md) | **1,461 lines**

**What you'll learn:**
- Core Hyvä module (Hyva_Theme) architecture
- 71 ViewModels in detail
- Plugin system (19 plugin directories)
- Alpine.js integration
- Icon libraries (Heroicons 200+, Lucide 1000+)
- GraphQL schema extensions
- Layout handles (default_hyva.xml explained)

**Time:** 3-4 hours | **Difficulty:** ⭐⭐⭐ Advanced

**Key Files:**
```
magento2-theme-module/
├── src/ViewModel/ (71 ViewModels)
├── src/Plugin/ (19 plugin directories)
├── src/view/frontend/layout/default_hyva.xml (Core layout)
└── src/view/base/web/js/alpine-v3.14.3.js (Alpine.js)
```

**Hands-on:**
- Explore all 71 ViewModels
- Examine default_hyva.xml
- Test Heroicons and Lucide icons
- Understand plugin system

**Next:** Learn ViewModels in detail → [Step 8: ViewModels](#step-8-viewmodels)

---

### **Step 8: ViewModels**
**Document:** [`08-HYVA-VIEWMODELS.md`](./08-HYVA-VIEWMODELS.md) | **1,523 lines**

**What you'll learn:**
- What are ViewModels and why use them
- All 71 Hyvä ViewModels catalog
- Product & Catalog ViewModels
- Cart, Customer, Navigation ViewModels
- Icon ViewModels (Heroicons, Lucide)
- Using ViewModels in templates

**Time:** 3-4 hours | **Difficulty:** ⭐⭐⭐ Medium

**Key ViewModels:**
```php
// Product
Hyva\Theme\ViewModel\CurrentProduct
Hyva\Theme\ViewModel\ProductList
Hyva\Theme\ViewModel\ProductPrice

// Icons
Hyva\Theme\ViewModel\HeroiconsOutline
Hyva\Theme\ViewModel\LucideIcons

// Navigation
Hyva\Theme\ViewModel\Navigation
Hyva\Theme\ViewModel\Modal
```

**Hands-on:**
```php
// In layout XML
<argument name="productViewModel" xsi:type="object">
    Hyva\Theme\ViewModel\CurrentProduct
</argument>

// In template
<?php $product = $block->getViewModel()->get(); ?>
<h1><?= $escaper->escapeHtml($product->getName()) ?></h1>
```

**Practice:**
- Use CurrentProduct ViewModel
- Render icons with HeroiconsOutline
- Format prices with ProductPrice ViewModel

**Next:** Build your own theme → [Step 9: Custom Theme](#step-9-custom-theme)

---

### **Step 9: Custom Theme** ⭐ **Most Important for Theme Development**
**Document:** [`09-HYVA-CUSTOM-THEME.md`](./09-HYVA-CUSTOM-THEME.md) | **1,168 lines**

**What you'll learn:**
- Creating child theme (step-by-step)
- Theme registration and setup
- Tailwind parent theme path configuration
- Customizing colors and branding
- Template overrides
- Layout XML customization
- Development workflow
- Best practices

**Time:** 4-6 hours | **Difficulty:** ⭐⭐⭐⭐ Most Important

**Complete Setup Process:**

```bash
# 1. Create theme directory
mkdir -p app/design/frontend/MyCompany/hyva-custom

# 2. Create required files
# - registration.php
# - theme.xml
# - composer.json
# - etc/view.xml

# 3. Copy web directory
cp -r vendor/hyva-themes/magento2-default-theme/web/ \
      app/design/frontend/MyCompany/hyva-custom/web/

# 4. Configure parent theme path (hyva.config.json)
{
    "tailwind": {
        "include": [
            { "src": "vendor/hyva-themes/magento2-default-theme" }
        ]
    }
}

# 5. Customize colors
{
    "tokens": {
        "values": {
            "color": {
                "primary": {
                    "DEFAULT": "#3b82f6"
                }
            }
        }
    }
}

# 6. Register and activate
php bin/magento setup:upgrade
php bin/magento cache:flush

# 7. Compile Tailwind
cd web/tailwind
npm install
npm run watch
```

**Hands-on Project:**
- Create complete custom theme
- Customize header and footer
- Override product list item
- Style with custom colors
- Add custom components

**Next:** Build extensions → [Step 10: Extensions Development](#step-10-extensions-development)

---

### **Step 10: Extensions Development** ⭐ **For Extension Developers**
**Document:** [`10-HYVA-EXTENSIONS-GUIDE.md`](./10-HYVA-EXTENSIONS-GUIDE.md) | **1,345 lines**

**What you'll learn:**
- Building Hyvä-compatible extensions
- Custom ViewModels
- GraphQL integration
- Layout XML for Hyvä
- Compatibility modules
- Testing extensions

**Time:** 4-6 hours | **Difficulty:** ⭐⭐⭐⭐ Advanced

**Extension Structure:**
```
Vendor_Module/
├── ViewModel/
│   └── CustomViewModel.php
├── view/frontend/
│   ├── layout/
│   │   └── hyva_custom_page.xml
│   └── templates/
│       └── hyva/
│           └── custom.phtml
└── etc/
    ├── module.xml
    └── di.xml
```

**Hands-on:**
- Create custom ViewModel
- Build Hyvä-compatible module
- Integrate with Alpine.js
- Add GraphQL support

**Next:** Master Alpine.js plugins → [Step 11: Alpine.js Plugins](#step-11-alpinejs-plugins)

---

### **Step 11: Alpine.js Plugins**
**Document:** [`11-HYVA-ALPINE-PLUGINS.md`](./11-HYVA-ALPINE-PLUGINS.md) | **1,238 lines**

**What you'll learn:**
- Alpine.js v3 fundamentals
- 5 Built-in Hyvä plugins:
  1. **defer** - Lazy load components
  2. **htmldialog** - Native dialogs
  3. **intersect** - Scroll triggers
  4. **ignore** - Prevent Alpine processing
  5. **snap-slider** - Carousels
- Creating custom plugins
- Common patterns (tabs, dropdowns, modals)

**Time:** 4-5 hours | **Difficulty:** ⭐⭐⭐ Medium

**Hands-on:**

```html
<!-- Defer plugin - lazy load -->
<div x-data="productSlider()" x-defer="intersect">
    Loads when scrolled into view
</div>

<!-- HTMLDialog plugin - native modals -->
<dialog x-ref="myDialog">
    <button @click="$refs.myDialog.close()">Close</button>
</dialog>

<!-- Intersect plugin - scroll animations -->
<div x-data="{ visible: false }"
     x-intersect.once="visible = true"
     x-show="visible"
     x-transition>
    Animates on scroll
</div>

<!-- Snap Slider - carousels -->
<div x-data="initSnapSlider()">
    <button @click="previous()">←</button>
    <div class="slider-track">
        <div class="slide">Slide 1</div>
    </div>
    <button @click="next()">→</button>
</div>
```

**Practice:**
- Create a product slider with snap-slider
- Build a quick-view modal with htmldialog
- Add lazy-loaded sections with defer
- Animate elements with intersect

**Congratulations!** 🎉 You've completed the entire Hyvä Themes learning path!

---

## 📊 Learning Path Summary

### Beginner Track (Weeks 1-4)

**Week 1: Installation & Setup**
- [ ] Step 1: Installation (01-HYVA-THEME-INSTALLATION.md)
- [ ] Step 2: Fallback Configuration (02-HYVA-THEME-FALLBACK.md)
- [ ] Step 3: Directory Structure (03-HYVA-DIRECTORY-STRUCTURE.md)

**Week 2: Build Tools & Reference**
- [ ] Step 4: Tailwind & Alpine.js Setup (04-HYVA-TAILWIND-ALPINE-SETUP.md)
- [ ] Step 5: Complete Learning Plan (05-HYVA-LEARNING-PLAN.md) - Start reading
- [ ] Practice: Build simple components

**Week 3: Understanding Architecture**
- [ ] Step 6: Default Theme Reference (06-HYVA-DEFAULT-THEME.md)
- [ ] Step 7: Theme Module (07-HYVA-THEME-MODULE.md)
- [ ] Step 8: ViewModels (08-HYVA-VIEWMODELS.md)
- [ ] Practice: Explore default theme templates

**Week 4: Build Your Theme**
- [ ] Step 9: Custom Theme (09-HYVA-CUSTOM-THEME.md)
- [ ] Project: Complete custom theme

### Intermediate Track (Weeks 5-8)

**Week 5-6: Advanced Theme Development**
- [ ] Advanced customizations
- [ ] Complex layouts
- [ ] Custom components
- [ ] Performance optimization

**Week 7-8: Extension Development**
- [ ] Step 10: Extensions (10-HYVA-EXTENSIONS-GUIDE.md)
- [ ] Step 11: Alpine.js Plugins (11-HYVA-ALPINE-PLUGINS.md)
- [ ] Build custom extension
- [ ] GraphQL integration
- [ ] Testing and deployment

### Advanced Track (Ongoing)

**Reference:**
- [ ] Step 5: Complete Learning Plan (05-HYVA-LEARNING-PLAN.md) - Deep dive
- [ ] Advanced patterns
- [ ] Production optimization
- [ ] Community contributions

---

## 📚 Document Quick Reference

| Step | Document | Lines | Time | Difficulty |
|------|----------|-------|------|------------|
| **1** | 01-HYVA-THEME-INSTALLATION.md | 1,721 | 1-2h | ⭐ Easy |
| **2** | 02-HYVA-THEME-FALLBACK.md | 1,279 | 2-3h | ⭐⭐ Medium |
| **3** | 03-HYVA-DIRECTORY-STRUCTURE.md | 1,164 | 1-2h | ⭐⭐ Medium |
| **4** | 04-HYVA-TAILWIND-ALPINE-SETUP.md | 1,082 | 2-3h | ⭐⭐ Medium |
| **5** | 05-HYVA-LEARNING-PLAN.md | 3,656 | Ongoing | ⭐⭐⭐⭐⭐ Reference |
| **6** | 06-HYVA-DEFAULT-THEME.md | 1,637 | 3-4h | ⭐⭐⭐ Advanced |
| **7** | 07-HYVA-THEME-MODULE.md | 1,461 | 3-4h | ⭐⭐⭐ Advanced |
| **8** | 08-HYVA-VIEWMODELS.md | 1,523 | 3-4h | ⭐⭐⭐ Medium |
| **9** | 09-HYVA-CUSTOM-THEME.md | 1,168 | 4-6h | ⭐⭐⭐⭐ Important |
| **10** | 10-HYVA-EXTENSIONS-GUIDE.md | 1,345 | 4-6h | ⭐⭐⭐⭐ Advanced |
| **11** | 11-HYVA-ALPINE-PLUGINS.md | 1,238 | 4-5h | ⭐⭐⭐ Medium |
| | **Total** | **17,925** | **~50h** | |

---

## 🎯 Quick Navigation by Topic

### Installation & Setup
- [Installation Guide](./01-HYVA-THEME-INSTALLATION.md) - Get Hyvä installed
- [Fallback Configuration](./02-HYVA-THEME-FALLBACK.md) - Handle incompatibilities
- [Tailwind & Alpine Setup](./04-HYVA-TAILWIND-ALPINE-SETUP.md) - Configure build tools

### Core Concepts
- [Directory Structure](./03-HYVA-DIRECTORY-STRUCTURE.md) - File organization
- [ViewModels](./08-HYVA-VIEWMODELS.md) - Business logic (71 ViewModels)
- [Alpine.js Plugins](./11-HYVA-ALPINE-PLUGINS.md) - Interactive components

### Architecture
- [Theme Module](./07-HYVA-THEME-MODULE.md) - Core Hyva_Theme module
- [Default Theme](./06-HYVA-DEFAULT-THEME.md) - Reference implementation

### Development
- [Custom Theme](./09-HYVA-CUSTOM-THEME.md) - Build your theme ⭐
- [Extensions](./10-HYVA-EXTENSIONS-GUIDE.md) - Create extensions ⭐

### Master Reference
- [Complete Learning Plan](./05-HYVA-LEARNING-PLAN.md) - Deep-dive guide ⭐⭐⭐⭐⭐

---

## 💡 Learning Tips

### 1. Follow the Sequence
Start from Step 1 and progress sequentially. Each step builds on previous knowledge.

### 2. Hands-On Practice
**Code along** with every example. Don't just read - implement!

### 3. Build Projects
- **Week 1-2:** Simple components
- **Week 3:** Custom header/footer
- **Week 4:** Complete theme
- **Week 5+:** Extensions

### 4. Use the Checklist
```markdown
### My Progress
- [x] Step 1: Installation ✓
- [x] Step 2: Fallback Configuration ✓
- [ ] Step 3: Directory Structure (In Progress)
- [ ] Step 4: Tailwind & Alpine Setup
- [ ] Step 5: Complete Learning Plan
```

### 5. Reference Often
Keep these bookmarked:
- **Step 5: Complete Learning Plan** - Master reference for everything
- **Step 8: ViewModels** - You'll use this constantly
- **Step 9: Custom Theme** - During theme development
- **Step 11: Alpine Plugins** - For interactive features

### 6. Join Community
- [Hyvä Slack](https://hyva-io.slack.com) - Ask questions
- [Hyvä GitLab](https://gitlab.hyva.io) - Source code
- [GitHub Themes](https://github.com/hyva-themes) - Examples

---

## 🛠️ Development Environment Setup

### Required Tools
```bash
# PHP
php --version  # 8.1 or 8.2

# Composer
composer --version  # 2.x

# Node.js
node --version  # 18+
npm --version

# Magento
php bin/magento --version  # 2.4.6+

# Hyvä (after installation)
php bin/magento module:status Hyva_Theme
```

### Directory Structure
```
/var/www/html/m246p8/  (Your Magento root)
├── app/design/frontend/
│   └── YourCompany/your-theme/  (Your custom theme)
├── vendor/hyva-themes/
│   ├── magento2-theme-module/  (Core module)
│   └── magento2-default-theme/  (Default theme)
└── hyva-learning-plan/  (This documentation)
    ├── README.md (This file)
    └── 01-11-*.md (Learning documents)
```

---

## 📖 How to Use This Documentation

### Scenario 1: Complete Beginner
```
1. Read this README completely
2. Start with Step 1: Installation
3. Follow each step sequentially
4. Complete hands-on exercises
5. Build practice projects
```

### Scenario 2: Already Installed Hyvä
```
1. Skip to Step 2: Fallback Configuration
2. Learn Directory Structure (Step 3)
3. Configure Tailwind & Alpine (Step 4)
4. Study Complete Learning Plan (Step 5)
5. Build Custom Theme (Step 9)
```

### Scenario 3: Theme Developer
```
Focus on these:
- Step 3: Directory Structure
- Step 5: Complete Learning Plan (Master Reference)
- Step 6: Default Theme Reference
- Step 8: ViewModels
- Step 9: Custom Theme (Most important)
- Step 11: Alpine.js Plugins
```

### Scenario 4: Extension Developer
```
Focus on these:
- Step 5: Complete Learning Plan (Master Reference)
- Step 7: Theme Module
- Step 8: ViewModels
- Step 10: Extensions Development
- Step 11: Alpine.js Plugins
```

---

## 🎓 Certification Checklist

Track your mastery of Hyvä Themes:

### Junior Level
- [ ] Installed and configured Hyvä
- [ ] Configured fallback for incompatible pages
- [ ] Understand Tailwind compilation
- [ ] Can use ViewModels in templates
- [ ] Built simple Alpine.js components
- [ ] Created basic child theme
- [ ] Override templates successfully

### Mid Level
- [ ] Built complete custom theme
- [ ] Customized Tailwind configuration
- [ ] Created custom ViewModels
- [ ] Mastered Alpine.js plugins
- [ ] Implemented complex layouts
- [ ] Configured fallback for checkout
- [ ] Built custom Alpine.js plugin

### Senior Level
- [ ] Built Hyvä-compatible extensions
- [ ] Created compatibility modules
- [ ] Integrated GraphQL queries
- [ ] Optimized performance
- [ ] Contributed to community
- [ ] Mentored other developers
- [ ] Completed all 11 learning steps

---

## 🚀 Getting Started Right Now

### 1. Read This README (15 minutes)
You're already doing this! ✓

### 2. Start Step 1: Installation (1-2 hours)
```bash
# Open the installation guide
cat 01-HYVA-THEME-INSTALLATION.md

# Or in browser if you have markdown viewer
# Navigate to 01-HYVA-THEME-INSTALLATION.md
```

### 3. Follow Sequentially
Continue through each step in order

### 4. Build Projects
Apply what you learn immediately

---

## 📊 Document Statistics

| Category | Documents | Total Lines |
|----------|-----------|-------------|
| **Setup & Installation** | 2 | 3,000 |
| **Core Concepts** | 2 | 2,246 |
| **Build Tools** | 1 | 1,082 |
| **Master Reference** | 1 | 3,656 |
| **Architecture** | 3 | 4,621 |
| **Development** | 2 | 2,513 |
| **Interactive Features** | 1 | 1,238 |
| **TOTAL** | **12** | **17,925** |

---

## 🎯 Success Metrics

### After 1 Week
- ✓ Hyvä installed and configured
- ✓ Fallback configured for incompatible pages
- ✓ Understand directory structure
- ✓ Tailwind and Alpine basics working

### After 1 Month
- ✓ Built custom theme
- ✓ Customized colors and branding
- ✓ Created custom components
- ✓ Comfortable with ViewModels and Alpine.js

### After 3 Months
- ✓ Production-ready custom theme
- ✓ Built custom extensions
- ✓ Optimized performance
- ✓ Contributing to community

---

## 📧 Support & Community

**Need Help?**
- 💬 [Hyvä Slack Community](https://hyva-io.slack.com)
- 📖 [Official Documentation](https://docs.hyva.io)
- 🐛 [Report Issues on GitLab](https://gitlab.hyva.io)
- 💻 [GitHub Themes Examples](https://github.com/hyva-themes)
- ❓ [Magento Stack Exchange](https://magento.stackexchange.com)

**Official Resources:**
- [Hyvä Homepage](https://hyva.io)
- [Hyvä Blog](https://hyva.io/blog)
- [Hyvä Demo Store](https://demo.hyva.io)

---

## 🏆 Your Learning Journey Starts Here

```
Step 1: Installation
    ↓
Step 2: Fallback Configuration
    ↓
Step 3: Directory Structure
    ↓
Step 4: Tailwind & Alpine.js Setup
    ↓
Step 5: Complete Learning Plan ⭐ (Master Reference)
    ↓
Step 6: Default Theme Reference
    ↓
Step 7: Theme Module
    ↓
Step 8: ViewModels
    ↓
Step 9: Custom Theme ⭐ (Build Your Theme!)
    ↓
Step 10: Extensions Development
    ↓
Step 11: Alpine.js Plugins
```

---

## ✅ Start Learning Now

**Ready to begin?**

👉 **Open:** [`01-HYVA-THEME-INSTALLATION.md`](./01-HYVA-THEME-INSTALLATION.md)

---

**Last Updated:** November 2025
**Total Documentation:** 17,925 lines across 11 documents
**Maintained By:** Your Development Team
**License:** Educational Use

---

**Happy Learning! 🚀**

*"The best way to learn is by building. Start with Step 1 and code along with every example. You've got this!"*
