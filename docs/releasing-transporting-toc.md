---
title: Checking and Releasing SAP Transport Requests and Transport of Copies (TOC)
layout: runbook
---

# Checking and Releasing SAP Transport Requests and Transport of Copies (TOC)

## Checking Transport Requests

To check transport requests, go to `SE09` and make sure the correct SAP username is entered in the **User** field.

<a href="{{ '/media/releasing-transporting-toc-1.png' | relative_url }}">
<img src="{{ '/media/releasing-transporting-toc-1.png' | relative_url }}"
    width="800" />
</a>

From there, select which request types you want shown that are associated with the User.

You may also change the request statuses that you want shown. By default, you will see modifiable (unreleased) requests.

Click **Display** and you will be redirected to a screen where all transport requests based on your filter will be listed.

## Creating Releasing Transport of Copies

To create **Transport of Copies (TOC)**, proceed to `SE09`.

Click on the "Blank Paper" icon at the upper-left part of the screen. When you hover over the icon, it should show "Create".

In the selection screen, choose **Transport of Copies** then click **Save**.

An example of the TOC description can be:

```
TOC_<Parent TR>_<Domain>_<Free Text Description of Parent>_<Version>
```

Enter the target details of where you want to transport your TOC.

> NOTE: Ensure that the Target detail consists of both the system name and the client number (e.g., `R10.100`).

Upon creating the TOC, highlight or single-click on the TOC's number.

To include your child TR from the parent TR, Go to **Request Task** (upper-left menu) > **Object List** > **Include Object** > [Type in the Child TR] > **Save**. You may also highlight the TOC number and press `CTRL + F11`.

### Checks To Do Before Releasing TOC

1. Ensure that the target system and client are correct and properly indicated. Not indicating any of the two will **cause traffic that you need to manually fix** in `STMS`.
2. Go to **Request Task** > **Complete Check** and do the **first three checks** listed in the submenu.
3. Go to your **parent TR** and ensure that the child TR that you want to tranport is already "released". If not, click the child TR and then press the "Truck" icon at the upper part of the screen. You may also hit `F9` on your keyboard.

### Releasing the TOC

To release the TOC, do the same thing as Step 3 above. Select your TOC number, and then press the "Truck" icon or hit `F9`.