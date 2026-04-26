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
4. [Commit 2 — Component Creation & Placement](#commit-2--component-creation--placement)
   - [Components Overview](#components-overview)
   - [carFilter](#carfilter)
   - [carTileList](#cartilelist)
   - [carCard](#carcard)
   - [Component Placement in Page Template](#component-placement-in-page-template)
   - [LWC Bundle File Structure](#lwc-bundle-file-structure)
   - [Key Concepts & Exam Notes — LWC](#key-concepts--exam-notes--lwc)

---

## Project Overview

AutoVault is a custom Salesforce application built to showcase different cars. The app uses a **custom Lightning App Page Template** implemented via the **Aura Component Framework** to define a unique three-column layout for the App Home page.

| Property | Detail |
|---|---|
| App Name | AutoVault — Car Showcase |
| Object | `Car__c` (custom object) |
| Total Fields | 13 (including system fields) |
| Owner | Vikaskumar Pandey |
| API Version | 66.0 (Spring '25) |
| Template Type | Lightning App Home Page (`lightning:appHomeTemplate`) |

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
| 2 | Component Creation & Placement | Three LWC components — `carFilter`, `carTileList`, `carCard` | ✅ Done |
| 3 | _(next commit)_ | — | 🔜 Pending |
| 4 | _(next commit)_ | — | 🔜 Pending |
| 5 | _(next commit)_ | — | 🔜 Pending |
| 6 | _(next commit)_ | — | 🔜 Pending |
| 7 | _(next commit)_ | — | 🔜 Pending |
| 8 | _(next commit)_ | — | 🔜 Pending |
| 9 | _(next commit)_ | — | 🔜 Pending |

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

## Commit 2 — Component Creation & Placement

**Purpose:** Create three LWC components that power the AutoVault car showcase UI and place them into the three regions defined by the Aura page template from Commit 1.

---

### Components Overview

| Component | Placed In | Role | Communicates With |
|---|---|---|---|
| `carFilter` | Left column (size 2) | Sidebar filter panel — filters cars by Make, Category, etc. | Fires event → `carTileList` |
| `carTileList` | Center column (size 7) | Displays filtered list of car tiles | Receives filter → renders `carCard` |
| `carCard` | Center column (size 3) | Individual car card — shows image, name, MSRP, Make | Child of `carTileList` |

**Data flow:**

```
carFilter  ──(filter event)──▶  carTileList  ──(car record)──▶  carCard
  Left                            Center                         Center (child)
```

---

### carFilter

**Component Name:** `carFilter`  
**Placed In:** Left column of the page template  
**Purpose:** Renders a filter panel that lets users narrow down the car list by fields like Make, Category, or Max Price. Fires a custom event carrying the selected filter criteria up to `carTileList`.

#### File Structure

| File | Purpose |
|---|---|
| `carFilter.html` | Filter form markup — picklists, sliders, or input fields |
| `carFilter.js` | Handles filter change events, fires custom event to parent |
| `carFilter.js-meta.xml` | Metadata — exposes component to App Builder |
| `carFilter.css` | Scoped styles for the filter panel |

#### carFilter.html

```html
<template>
  <lightning-card title="Filter Cars" icon-name="utility:filterList">
  </lightning-card>
</template>
```

#### carFilter.js

```javascript
import { LightningElement, track } from 'lwc';

export default class CarFilter extends LightningElement {}
```

#### carFilter.js-meta.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
  <apiVersion>66.0</apiVersion>
  <isExposed>true</isExposed>
  <masterLabel>Car Filter</masterLabel>
  <targets>
    <target>lightning__AppPage</target>
    <target>lightning__RecordPage</target>
    <target>lightning__HomePage</target>
  </targets>
</LightningComponentBundle>
```

**Key points:**
- `isExposed: true` — makes the component visible in App Builder's component panel so admins can drag it onto the page
- `targets` — defines which page types the component can be placed on; `lightning__AppPage` is required for this app's template
- `bubbles: true` on the custom event allows it to propagate up through the DOM tree

---

### carTileList

**Component Name:** `carTileList`  
**Placed In:** Center column of the page template  
**Purpose:** The main content area. Queries `Car__c` records via a wire adapter, listens for filter events from `carFilter`, applies the filters, and renders one `carCard` per result using `template iterator`.

#### File Structure

| File | Purpose |
|---|---|
| `carTileList.html` | Iterates over car records, renders `c-car-card` for each |
| `carTileList.js` | Wire service to fetch cars, filter logic, event listener |
| `carTileList.js-meta.xml` | Metadata — exposes to App Builder |
| `carTileList.css` | Grid layout styles for the tile list |

#### carTileList.html

```html
<template>
  <lightning-card title="Cars" icon-name="utility:car">
  </lightning-card>
</template>
```

#### carTileList.js

```javascript
import { LightningElement, wire, track } from 'lwc';
import getCars from '@salesforce/apex/CarController.getCars';

export default class CarTileList extends LightningElement {}
```

#### carTileList.css

```css
.car-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
    gap: 16px;
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
  <apiVersion>66.0</apiVersion>
  <isExposed>true</isExposed>
  <masterLabel>Car Title List</masterLabel>
  <targets>
    <target>lightning__AppPage</target>
    <target>lightning__RecordPage</target>
    <target>lightning__HomePage</target>
  </targets>
</LightningComponentBundle>
```

**Key points:**
- `@wire(getCars)` — declarative data fetching; Salesforce calls the Apex method and re-renders when data changes
- `iterator:it` — used instead of `for:each` when you need access to `it.first` / `it.last` metadata per iteration
- `handleFilterChange` listens for the bubbled `filterchange` event dispatched by `carFilter`
- `applyFilters()` runs client-side filtering on the already-fetched dataset — avoids extra server calls on each filter change

---

### carCard

**Component Name:** `carCard`  
**Placed In:** Inside `carTileList` (child component — not directly in the page template)  
**Purpose:** Displays a single `Car__c` record as a visual card with the car image, name, make, category, and MSRP. Receives the car record as a `@api` property from `carTileList`.

#### File Structure

| File | Purpose |
|---|---|
| `carCard.html` | Card markup — image, name, make, MSRP |
| `carCard.js` | `@api car` property to receive record from parent |
| `carCard.js-meta.xml` | Metadata — `isExposed: false` (child-only component) |
| `carCard.css` | Card visual styles |

#### carCard.html

```html
<template>
  <lightning-card>
  </lightning-card>
</template>
```

#### carCard.js

```javascript
import { LightningElement, api } from 'lwc';

export default class CarCard extends LightningElement {}
```

#### carCard.js-meta.xml

```xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
  <apiVersion>66.0</apiVersion>
  <isExposed>true</isExposed>
  <masterLabel>Car Card</masterLabel>
  <targets>
    <target>lightning__AppPage</target>
    <target>lightning__RecordPage</target>
    <target>lightning__HomePage</target>
  </targets>
</LightningComponentBundle>
```
```

#### carCard.css

```css

```

**Key points:**
- `@api car` — the public property decorator; without `@api` the parent cannot pass data into this component
- `isExposed: false` — `carCard` is a **child-only** component; it should not appear in App Builder's component panel since it is always rendered programmatically by `carTileList`
- `lightning-formatted-number` with `format-style="currency"` handles locale-aware currency formatting automatically

---

### Component Placement in Page Template

The three LWC components map directly to the three regions defined in Commit 1's Aura page template:

```
┌─────────────────────────────────────────────────────────────┐
│                  pageTemplate_2_7_3 (Aura)                  │
├──────────────┬──────────────────────────┬───────────────────┤
│  LEFT (2)    │      CENTER (7)          │    RIGHT (5)      │
│              │                          │                   │
│  carFilter   │     carTileList          │   (Car Card)      │
│              │   ┌──────┐ ┌──────┐      │                   │
│  Make        │   │carCard│ │carCard│    │                   │
│  Category    │   └──────┘ └──────┘      │                   │
│  Max Price   │   ┌──────┐ ┌──────┐      │                   │
│              │   │carCard│ │carCard│    │                   │
│              │   └──────┘ └──────┘      │                   │
└──────────────┴──────────────────────────┴───────────────────┘
```

| Template Region | Size | Component Placed | How Placed |
|---|---|---|---|
| Left | 2 | `carFilter` | Dragged in via Lightning App Builder |
| Center | 7 | `carTileList` | Dragged in via Lightning App Builder |
| Right | 3 | _(reserved for future commit)_ | — |

---

### LWC Bundle File Structure

Every LWC component follows the same standard bundle structure:

| File | Required | Purpose |
|---|---|---|
| `componentName.html` | ✅ Yes | Template markup — must have a single `<template>` root |
| `componentName.js` | ✅ Yes | Component class — extends `LightningElement` |
| `componentName.js-meta.xml` | ✅ Yes | Metadata — API version, `isExposed`, targets |
| `componentName.css` | No | Scoped styles — automatically scoped, no `.THIS` needed |
| `componentName.test.js` | No | Jest unit tests |

**Key differences vs Aura bundle:**

| Aspect | Aura | LWC |
|---|---|---|
| CSS scoping | `.THIS` prefix required | Automatic — no prefix needed |
| Data binding | `{!v.property}` | `{property}` |
| Event handling | `action="{!c.func}"` | `oneventname={handler}` |
| JS structure | Controller + Helper + Renderer (3 files) | Single `.js` class file |
| Public property | `access="global"` on attribute | `@api` decorator |
| Reactive property | `@AuraEnabled` | `@track` / `@api` / `@wire` |

---

### Key Concepts & Exam Notes — LWC

| Concept | Rule |
|---|---|
| `@api` | Makes a property or method publicly accessible from a parent component — required to pass data down |
| `@track` | Makes a property deeply reactive — re-renders when nested object/array properties change |
| `@wire` | Declarative data fetching — auto-calls Apex or wire adapters and re-renders on data change |
| `isExposed: true` | Required for a component to appear in App Builder — always set on page-level components |
| `isExposed: false` | Set on child-only components like `carCard` that are rendered programmatically |
| `targets` | Defines which page types a component can be placed on — must include `lightning__AppPage` for this app |
| `CustomEvent` | Used to communicate from child → parent; use `bubbles: true` to propagate through DOM |
| `iterator:it` | Use instead of `for:each` when you need `it.first` / `it.last` / `it.index` metadata |
| CSS scoping | LWC CSS is automatically scoped — no `.THIS` needed unlike Aura |
| Child component tag | LWC component names use kebab-case in HTML: `carCard` → `<c-car-card>` |

---

*This document is updated with each commit. Add new commits below Commit 2 following the same structure.*
