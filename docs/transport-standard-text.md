---
title: "How to Transport Standard Texts Between Systems"
layout: runbook
---

# How to Transport Standard Texts Between Systems
This guide provides a clear and consistent process for transporting SAP **Standard Texts (ST)** from one system to another using transaction **`SE38`** and program **`RSTXTRAN`**.

---

## **1. Open Program RSTXTRAN**
1. Go to **`SE38`**.
2. Enter the program name **`RSTXTRAN`**.
3. Click **Execute**.

<a href="{{ '/media/transport-standard-text-1.png' | relative_url }}">
<img src="{{ '/media/transport-standard-text-1.png' | relative_url }}"
    width="800" />
</a>

---

## **2. Fill in the Text Transport Details**
- **Task** → Enter the Child Transport Request (TR).
- **Text Object** → Typically `TEXT`.
- **Text Name** → Specify the standard text name or use `*` for multiple texts.
- **Text ID** → Usually `ST`.
- **Language** → e.g., `EN`.

<a href="{{ '/media/transport-standard-text-2.png' | relative_url }}">
<img src="{{ '/media/transport-standard-text-2.png' | relative_url }}"
    width="800" />
</a>

---

## **3. Execute and Select Texts**
1. Click **Execute**.
2. A provisional list of texts will appear. Select all.
3. Press **Enter**.

<a href="{{ '/media/transport-standard-text-3.png' | relative_url }}">
<img src="{{ '/media/transport-standard-text-3.png' | relative_url }}"
    width="800" />
</a>

---

## **4. Release Texts to the Transport Request**
1. Review the final list of texts.
2. Click the **Release** icon.

<a href="{{ '/media/transport-standard-text-4.png' | relative_url }}">
<img src="{{ '/media/transport-standard-text-4.png' | relative_url }}"
    width="800" />
</a>

---

## **5. Verify the Transport in SE09**
Go to **`SE09`** and confirm that the Standard Texts are included under the Child TR.

<a href="{{ '/media/transport-standard-text-5.png' | relative_url }}">
<img src="{{ '/media/transport-standard-text-5.png' | relative_url }}"
    width="800" />
</a>

---

# **Summary**
You have now successfully transported standard texts using **`RSTXTRAN`** and validated them via **`SE09`**.