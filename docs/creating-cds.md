---
title: Creating Core Data Services (CDS) Views
layout: runbook
---

# Creating Core Data Services (CDS) Views

*A step-by-step guide for creating Basic, Analytical, and Consumption CDS Views in Eclipse (ADT) and activating them in SAP GUI.*

---

## 1. Introduction

In developing CDS-based reports in SAP S/4HANA using ABAP Development Tools (ADT), three CDS views are typically required:

1. **Basic CDS View** - Defines table joins, fields, and associations.
2. **Analytical CDS View** - Creates the analytical cube structure.
3. **Consumption CDS View** - Exposes the query to Fiori and OData; consumed by Functional teams.

This manual provides a detailed walkthrough of creating and activating these views.

---

## 2. Creating the Basic CDS View

### 2.1 Create a New Data Definition
1. Open **Eclipse (ADT)**.
2. Navigate to your package → **Core Data Services** → **Data Definitions** → **New Data Definition**.

<a href="{{ '/media/creating-cds-1.png' | relative_url }}">
<img src="{{ '/media/creating-cds-1.png' | relative_url }}"
    width="800" />
</a>

<a href="{{ '/media/creating-cds-2.png' | relative_url }}">
<img src="{{ '/media/creating-cds-2.png' | relative_url }}"
    width="800" />
</a>

### 2.2 Naming Convention

Your team might have their own naming convention for CDS views, but for this demonstration, you may use this pattern:

```
ZDDL_<Domain>_<FreeText>
```

### 2.3 Template Selection

Click **Next** → choose the correct transport request → **Next** → select `defineViewEntity` → **Finish**.

### 2.4 Insert Basic View Annotations

Before the `define view entity` statement, insert:

```
@VDM.viewType: #BASIC
```

Replace `data_source_name` with the **main table** you will join.

<a href="{{ '/media/creating-cds-3.png' | relative_url }}">
<img src="{{ '/media/creating-cds-3.png' | relative_url }}"
    width="800" />
</a>

<a href="{{ '/media/creating-cds-4.png' | relative_url }}">
<img src="{{ '/media/creating-cds-4.png' | relative_url }}"
    width="800" />
</a>

### 2.5 Define Table Joins

Perform all **INNER JOINs** prior to the opening bracket `{ }`.

<a href="{{ '/media/creating-cds-5.png' | relative_url }}">
<img src="{{ '/media/creating-cds-5.png' | relative_url }}"
    width="800" />
</a>

Follow the table join mapping based on your functional document (FDS). Ensure the join conditions are consistent with the technical mapping provided.

<a href="{{ '/media/creating-cds-6.png' | relative_url }}">
<img src="{{ '/media/creating-cds-6.png' | relative_url }}"
    width="800" />
</a>

### 2.6 Add Value Help Associations (If Required)

Example:
```
association to I_PlantStdVH as _PlantVH
    on _PlantVH.Plant = $projection.Plant
```

<a href="{{ '/media/creating-cds-7.png' | relative_url }}">
<img src="{{ '/media/creating-cds-7.png' | relative_url }}"
    width="800" />
</a>

### 2.7 Perform Data Mapping Inside the Brackets

Select and map all required fields.

<a href="{{ '/media/creating-cds-8.png' | relative_url }}">
<img src="{{ '/media/creating-cds-8.png' | relative_url }}"
    width="800" />
</a>

<a href="{{ '/media/creating-cds-9.png' | relative_url }}">
<img src="{{ '/media/creating-cds-9.png' | relative_url }}"
    width="800" />
</a>

Insert annotations **above each field definition**, for example:

```
@ObjectModel.foreignKey.association: '_AccAssgmtCategoryVH'
matdoc.knttp_gr as Acc_Ass_Category,
```

### 2.8 Unit and Currency Annotations

```
@Semantics.quantity.unitOfMeasure: 'XXX'
@Semantics.amount.currencyCode: 'XXX'
```

---

## 3. Creating the Analytical CDS View

### 3.1 Create a New Data Definition Based on the Basic CDS

Right-click your **Basic CDS View** → **New Data Definition**.

### 3.2 Naming Convention

```
ZR_<Domain>_<FreeText>
```

### 3.3 Insert Analytical Annotations

Add the following immediately after: `@Metadata.ignorePropagatedAnnotations: true`

```
@Analytics.dataCategory: #CUBE
@ObjectModel.supportedCapabilities: [ #ANALYTICAL_PROVIDER ]
@ObjectModel.modelingPattern: #ANALYTICAL_CUBE
@VDM.viewType: #COMPOSITE
```

<a href="{{ '/media/creating-cds-10.png' | relative_url }}">
<img src="{{ '/media/creating-cds-10.png' | relative_url }}"
    width="800" />
</a>

### 3.4 Add Value Help Associations (If Required)

Each field requiring value help must include:

```
@ObjectModel.foreignKey.association: 'XXXX'
```

Associations must also be listed at the end of the CDS.

<a href="{{ '/media/creating-cds-11.png' | relative_url }}">
<img src="{{ '/media/creating-cds-11.png' | relative_url }}"
    width="800" />
</a>

---

## 4. Creating the Consumption CDS View

### 4.1 Create the Consumption View

Right-click on the **Analytical CDS** → **New Data Definition**.

### 4.2 Naming Conventions

```
ZC_<Domain>_<FreeText>
```

### 4.3 Insert Consumption Annotations

Add these after the label annotation:

```
@VDM.viewType: #CONSUMPTION
@Analytics.query: true
@OData.publish: true
```

<a href="{{ '/media/creating-cds-12.png' | relative_url }}">
<img src="{{ '/media/creating-cds-12.png' | relative_url }}"
    width="800" />
</a>

### 4.4 Add Query and Fiori-Related Annotations

#### Row Characteristics
```
@AnalyticsDetails.query.axis: #ROWS
```

#### Units & Currency
```
@Semantics.quantity.unitOfMeasure: 'XXX'
@Semantics.amount.currencyCode: 'XXX'
```

#### Filters
```
@Consumption.filter: { selectionType: #SINGLE, multipleSelections: true, mandatory: false }
```

#### Default Filter Value
```
@Consumption.filter: { selectionType: #SINGLE, multipleSelections: true, mandatory: false, defaultValue: 'XXXX' }
```

#### Value Help Annotation
```
@ObjectModel.foreignKey.association: 'XXXX'
```

As in the previous layers, associations must be listed at the end of your CDS view.

<a href="{{ '/media/creating-cds-13.png' | relative_url }}">
<img src="{{ '/media/creating-cds-13.png' | relative_url }}"
    width="800" />
</a>

<a href="{{ '/media/creating-cds-14.png' | relative_url }}">
<img src="{{ '/media/creating-cds-14.png' | relative_url }}"
    width="800" />
</a>

---

## 5. Activating the CDS in SAP GUI

### 5.1 Open the Service Maintenance Transaction

Go to:

```
/n/iwfnd/maint_service
```

### 5.2 Check if Your CDS Service Exists

Look for your Consumption CDS in the Service Catalog.

<a href="{{ '/media/creating-cds-15.png' | relative_url }}">
<img src="{{ '/media/creating-cds-15.png' | relative_url }}"
    width="800" />
</a>

If not found:

#### Add the Service

1. Click **Add Service**.
2. Enter:
    - **System Alias**: `LOCAL`
    - **Technical Service Name:
    ```
    ZC_<Domain>_<FreeText>_CDS
    ```
    <a href="{{ '/media/creating-cds-16.png' | relative_url }}">
    <img src="{{ '/media/creating-cds-16.png' | relative_url }}"
        width="800" />
    </a>
3. Click **Get Services** → select your entry → **Add Selected Services**.
    <a href="{{ '/media/creating-cds-17.png' | relative_url }}">
    <img src="{{ '/media/creating-cds-17.png' | relative_url }}"
        width="800" />
    </a>
4. For Package assignment, choose the correct package.

### 5.3 Activate the ICF Node

If the service is not active:

1. Select your CDS service
2. Choose **ICF Node** → **Activate**.

<a href="{{ '/media/creating-cds-18.png' | relative_url }}">
<img src="{{ '/media/creating-cds-18.png' | relative_url }}"
    width="800" />
</a>

<a href="{{ '/media/creating-cds-19.png' | relative_url }}">
<img src="{{ '/media/creating-cds-19.png' | relative_url }}"
    width="800" />
</a>

If you encounter the error:
**"ICF Service '__' not enabled"**

<a href="{{ '/media/creating-cds-20.png' | relative_url }}">
<img src="{{ '/media/creating-cds-20.png' | relative_url }}"
    width="800" />
</a>

Then:

1. Go to **ICF Node** → **Configure (SICF)**
    <a href="{{ '/media/creating-cds-21.png' | relative_url }}">
    <img src="{{ '/media/creating-cds-21.png' | relative_url }}"
        width="800" />
    </a>
2. Navigate to your service
3. Right-click → **Activate Service**
    <a href="{{ '/media/creating-cds-22.png' | relative_url }}">
    <img src="{{ '/media/creating-cds-22.png' | relative_url }}"
        width="800" />
    </a>

---

## 6. Additional Notes
- Sometimes a CDS view activated in your development system does not reflect in the testing system upon transporting.
  - In that case, repeat the ICF activation steps in the testing system as well.