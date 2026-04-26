# Car Showcase App — Project Documentation

> **Project:** AutoVault — Car Showcase Salesforce App  
> **Framework:** Lightning Web Component  
> **Last Updated:** April 26, 2026  
> **Author:** Vikaskumar Pandey  

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Commit History](#commit-history)
3. [Commit 1 — Custom Page Template Design](#commit-1--custom-page-template-design)
   - [Bundle File Structure](#bundle-file-structure)
   - [.auradoc — Documentation File](#auradoc--documentation-file)
   - [.cmp — Main Component File](#cmp--main-component-file)
   - [.cmp-meta.xml — Metadata Config](#cmp-metaxml--metadata-config)
   - [.css — Scoped Styles](#css--scoped-styles)
   - [JavaScript Layer](#javascript-layer)
   - [Grid System Breakdown](#grid-system-breakdown)
   - [How It All Works Together](#how-it-all-works-together)
   - [Key Concepts & Exam Notes](#key-concepts--exam-notes)

---

## Project Overview

AutoVault is a custom Salesforce application built to showcase different cars. The app uses a **custom Lightning App Page Template** implemented via the **Aura Component Framework** to define a unique three-column layout for the App Home page.

| Property | Detail |
|---|---|
| App Name | AutoVault — Car Showcase |
| Object | Car__c (custom object) |
| Owner | Vikaskumar Pandey |
| API Version | 66.0 (Spring '25) |

---

### Car__c — Fields & Relationships
 
| Field Label | Field Name | Data Type | Notes |
|---|---|---|---|
| Car Name | `Name` | Text(80) | Standard name field · Indexed |
| Category | `Category__c` | Picklist | Custom · Car category (Sports, SUV, Sedan, etc.) |
| Control | `Control__c` | Picklist | Custom · Transmission type (Manual / Automatic) |
| Created By | `CreatedById` | Lookup(User) | System field — auto-populated |
| Description | `Description__c` | Long Text Area(32768) | Custom · Full car description |
| Fuel Type | `Fuel_Type__c` | Text(250) | Custom · e.g. Petrol, Diesel, Electric |
| Last Modified By | `LastModifiedById` | Lookup(User) | System field — auto-populated |
| Make | `Make__c` | Picklist | Custom · Car brand (Ferrari, Tesla, BMW, etc.) |
| MSRP | `MSRP__c` | Currency(6, 0) | Custom · Manufacturer's Suggested Retail Price |
| Number of Seats | `Number_of_Seats__c` | Number(1, 0) | Custom · Seating capacity |
| Owner | `Owner__c` | Lookup(User) | Custom owner lookup |
| Owner | `OwnerId` | Lookup(User, Group) | Standard ownership field |
| Picture URL | `Picture_URL__c` | URL(255) | Custom · Direct URL to car image |
 
---

## Commit History

| # | Commit Name | Description | Status |
|---|---|---|---|
| 1 | Custom Page Template Design | Three-column Aura page template — `pageTemplate_2_7_3` | ✅ Done |
| 2 | _(next commit)_ | — | 🔜 Pending |

---

## Commit 1 — Custom Page Template Design
**Component Name:** `pageTemplate_2_7_3`  
**Purpose:** Defines a custom three-column layout (Left 2 / Center 7 / Right 5) registered as a Lightning App Page Template in App Builder.

---

### Bundle File Structure

An Aura component bundle is a folder containing multiple files, each serving a specific role. Below is the complete file listing for `pageTemplate_2_7_3`:

| File | Role | Runtime Impact |
|---|---|---|
| `pageTemplate_2_7_3.auradoc` | Developer documentation — shown in Component Library | None |
| `pageTemplate_2_7_3.cmp` | Main markup — defines layout, attributes, grid | Core |
| `pageTemplate_2_7_3.cmp-meta.xml` | Deployment metadata — API version, description | Deploy only |
| `pageTemplate_2_7_3.css` | Scoped styles using `.THIS` prefix | Styling |
| `pageTemplate_2_7_3.design` | Exposes attributes to App Builder for admin config | App Builder UI |
| `pageTemplate_2_7_3.svg` | Custom icon shown in App Builder template picker | App Builder UI |
| `pageTemplate_2_7_3Controller.js` | Client-side event handlers triggered from markup | JS logic |
| `pageTemplate_2_7_3Helper.js` | Reusable JS logic called by Controller | JS logic |
| `pageTemplate_2_7_3Renderer.js` | Custom render lifecycle overrides | Rendering |

---

### .auradoc — Documentation File

```xml
<aura:documentation>
  <aura:description>Documentation</aura:description>
  <aura:example name="ExampleName" ref="exampleComponentName" label="Label">
    Example Description
  </aura:example>
</aura:documentation>
```

**What it does:**

- Provides **developer-facing documentation** visible in Salesforce's Component Library (`/componentReference`)
- `<aura:description>` — plain text description of the component's purpose
- `<aura:example>` — links to an example component that demonstrates usage in action
- Has **zero impact on runtime functionality** — purely informational/documentation

---

### .cmp — Main Component File

This is the most important file. It defines the component's interface, layout regions, and grid structure.

#### Component Declaration

```xml
<aura:component
  implements="lightning:appHomeTemplate"
  description="Three Columns Layout"
  access="global">
```

| Attribute | Value | Meaning |
|---|---|---|
| `implements` | `lightning:appHomeTemplate` | Registers this as an App Page Template in App Builder |
| `description` | `"Three Columns Layout"` | Human-readable label shown in the template picker |
| `access` | `"global"` | Makes component available across all namespaces and packages |

> **`implements` is the key.** Without this interface, the component will not appear as a template option in App Builder. For different page types, use the corresponding interface:

| Interface | Page Type |
|---|---|
| `lightning:appHomeTemplate` | App pages |
| `lightning:recordHomeTemplate` | Record detail pages |
| `lightning:homeTemplate` | Lightning Home pages |

---

#### Attributes — The Three Droppable Regions

```xml
<aura:attribute name="left"   type="Aura.Component[]" access="global" />
<aura:attribute name="center" type="Aura.Component[]" access="global" />
<aura:attribute name="right"  type="Aura.Component[]" access="global" />
```

| Concept | Explanation |
|---|---|
| `type="Aura.Component[]"` | An array of components — this is what creates a **droppable region** in App Builder |
| Each attribute | Becomes a **slot** where admins can drag and drop Lightning components at design time |
| `access="global"` | Makes the slot visible and configurable inside Lightning App Builder |
| Runtime rendering | Each slot is rendered via `{!v.attributeName}` expression in the markup |

---

#### Layout Markup

```xml
<div aura:id="container">
  <lightning:layout>

    <lightning:layoutItem aura:id="leftColumn" size="2">
      {!v.left}
    </lightning:layoutItem>

    <lightning:layoutItem aura:id="centerColumn" size="7" class="center">
      {!v.center}
    </lightning:layoutItem>

    <lightning:layoutItem aura:id="rightColumn" size="5">
      {!v.right}
    </lightning:layoutItem>

  </lightning:layout>
</div>
```

**Key elements explained:**

- `aura:id` — a unique identifier used to reference the element from JavaScript via `component.find("container")`
- `lightning:layout` — Salesforce's 12-column grid container (similar to Bootstrap's row)
- `lightning:layoutItem` — individual column within the grid, sized using the `size` attribute
- `{!v.left}`, `{!v.center}`, `{!v.right}` — Aura expression syntax; `v` is the **view** value provider that resolves attribute values at runtime

---

### .cmp-meta.xml — Metadata Config

```xml
<?xml version="1.0" encoding="UTF-8"?>
<AuraDefinitionBundle xmlns="http://soap.sforce.com/2006/04/metadata">
  <apiVersion>66.0</apiVersion>
  <description>A Lightning Component Bundle</description>
</AuraDefinitionBundle>
```

- `apiVersion: 66.0` corresponds to **Spring '25** release
- Tells the Salesforce metadata engine which API version to use when deploying or retrieving this bundle
- `description` appears in Setup when browsing Lightning components
- Required for deployment via SFDX, Workbench, or Change Sets

---

### .css — Scoped Styles

```css
.THIS .center {
  padding: 0 12px;
}
```

| Concept | Explanation |
|---|---|
| `.THIS` | Aura's auto-generated CSS scoping token — replaced at runtime with the component's unique CSS class |
| `.THIS .center` | Targets only `.center` child elements belonging to **this** component — not any other component on the page |
| `padding: 0 12px` | Adds 12px of horizontal breathing room to the center column |
| LWC equivalent | `:host` selector in LWC CSS — same isolation concept, different syntax |

> `.THIS` prevents style leakage between components on the same page. Never write global CSS selectors (e.g. `.center { ... }`) inside an Aura component's CSS file.

---

### JavaScript Layer

The Aura JS layer follows a strict three-file separation pattern:

#### Controller.js — Event Entry Point

```javascript
({
    handleClick : function(component, event, helper) {
        // Delegate complex logic to Helper
        helper.doSomething(component);
    }
})
```

- Handles **user-triggered events** (button clicks, input changes) referenced in markup via `action="{!c.functionName}"`
- Should stay lean — complex logic belongs in Helper
- Has access to `component`, `event`, and `helper` as parameters

#### Helper.js — Reusable Logic

```javascript
({
    doSomething : function(component) {
        var val = component.get("v.myAttr");
        component.set("v.myAttr", val + 1);
    }
})
```

- Contains **shared/reusable logic** called by Controller functions
- Called from Controller via `helper.functionName(component, event)`
- Keeps Controller clean and testable

#### Renderer.js — Render Lifecycle Overrides

```javascript
({
    render   : function(component, helper) { /* custom render */ },
    rerender : function(component, helper) { /* custom rerender */ },
    afterRender : function(component, helper) { /* DOM ready hook */ },
    unrender : function(component, helper) { /* cleanup */ }
})
```

- Overrides Aura's **default rendering lifecycle**
- `afterRender()` is the most commonly used hook — fires when the DOM is ready
- Used for direct DOM manipulation, canvas drawing, or third-party library initialization
- Rarely needed for standard page templates — typically left as default

---

### Grid System Breakdown

`lightning:layout` uses a **12-column grid**. All `size` values across `lightning:layoutItem` elements in one row should ideally sum to ≤ 12.

| Column | `aura:id` | `size` | Grid Width | Use |
|---|---|---|---|---|
| Left | `leftColumn` | 2 | ~16.6% | Sidebar / navigation |
| Center | `centerColumn` | 7 | ~58.3% | Main content area |
| Right | `rightColumn` | 5 | ~41.6% | Secondary / filter panel |

> ⚠️ **Bug Note:** In this commit, 2 + 7 + 5 = **14**, which exceeds 12. This will cause the right column to **wrap to the next row** unless `multipleRows="false"` or layout wrapping behavior is explicitly handled. Always verify column sizes sum to 12 to avoid unintended wrapping.

---

### How It All Works Together

```
Admin opens Lightning App Builder
        ↓
Selects "Three Columns Layout" template (shown via .svg icon)
        ↓
App Builder reads .design file → exposes Left / Center / Right regions
        ↓
.cmp renders lightning:layout with 3 layoutItems
{!v.left} | {!v.center} | {!v.right}
        ↓
.css scopes center column padding via .THIS
        ↓
Admin drags components into each region → saved to page definition
        ↓
Page activated → assigned to App / Profile
        ↓
Users see the three-column layout at runtime
```

---

### Key Concepts & Exam Notes

| Concept | Rule |
|---|---|
| Required interface | `lightning:appHomeTemplate` — mandatory for App page templates |
| Droppable region type | `Aura.Component[]` — the only type that creates App Builder slots |
| CSS scoping | `.THIS` prefix — never omit; prevents style leakage across components |
| Expression syntax | `{!v.attr}` for values, `{!c.func}` for controller, `{!helper.func}` from controller |
| Grid columns | `lightning:layout` = 12-column grid; `size` values must sum to ≤ 12 |
| LWC support | Custom page templates cannot be built with LWC — Aura only |
| `.design` vs `.cmp-meta.xml` | `.design` = App Builder UI config; `.cmp-meta.xml` = deployment metadata |
| `access="global"` | Required on both component and attributes for App Builder visibility |

---

*This document is updated with each commit. Add new commits below Commit 1 following the same structure.*
