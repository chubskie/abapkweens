---
title: Format User Address using Function Module in SAP GUI
layout: runbook
---

# Format User Address using Function Module in SAP GUI

To ensure that addresses stored in SAP are formatted correctly, we must use `ADDRESS_INTO_PRINTFORM` function module. If you want to dive deeper into the function module, you may look for it in `SE37`.

## Formatting without using Form Routine

### 1. Create a Structure/Work Area

Before you can use the function module, you must first prepare a structure where the FM can fetch data from. To prepare this, we declare the following variables:

```abap
DATA: ls_adrc                       TYPE adrc,
      ls_address                    TYPE adrs1,
      ls_address_printform_line     TYPE szadr_printform_table_line,
      lt_address_printform_table    TYPE szadr_printform_table,
      lv_formatted_address          TYPE string,
      lv_addrnumber                 TYPE adrc-addrnumber.
```

Next, fetch your `lv_addrnumber` value. This can be fetched from different tables, and it will entirely depend upon your business requirement.

Once you have your `lv_addrnumber` value, you can now proceed with fetching the details of that address number from the `adrc` table.

```abap
SELECT SINGLE *     " Advisable to be as specific as possible with the fields for optimal performance
    FROM adrc
    INTO @ls_adrc
    WHERE addrnumber = @lv_addrnumber.
```

Afterwards, you may now move the details from `ls_adrc` into `ls_address`.

```abap
MOVE-CORRESPONDING ls_adrc TO ls_address.
```

Once you have moved the address details into `ls_address`, you may now proceed to call the function module `ADDRESS_INTO_PRINTFORM`.

There are multiple ways to use it, and there are different parameters you can specify when using it, but for this demonstration, I will call the function like below:

```abap
CALL FUNCTION 'ADDRESS_INTO_PRINTFORM'
    EXPORTING
        address_1               = ls_address
        address_type            = '1'
        sender_country          = space
        street_has_priority     = 'X'       " Force to output street instead of P.O. Box
        no_upper_case_for_city  = 'X'       " City and District will not be UPPERCASE in output
    IMPORTING
        address_printform_table = lt_address_printform_table.
```

After calling the function, we can now prepare our string variable where the formatted output will be stored.

```abap
CLEAR lv_formatted_address.
```

You may choose to iterate through `lt_address_printform_table` depending on your development conventions, but for this demonstration, I will use structure/work area.

```abap
LOOP AT lt_address_printform_table INTO ls_address_printform_line.
    CONCATENATE lv_formatted_address ls_address_printform_line-address_line INTO lv_formatted_address SEPARATED BY cl_abap_char_utilities=>newline.
ENDLOOP.
```

**OPTIONAL:** In some cases, you would like to format the address inline, for that you may use conditional structures inside your loop statement.

```abap
CONSTANTS: lc_newline TYPE c VALUE cl_abap_char_utilities=>newline. " Declare newline constant for brevity

DATA: lv_inline_flag TYPE abap_bool. " Flag to format inline or block.

lv_inline_flag = abap_true. "Declare your flag as you see fit.

LOOP AT lt_address_printform_table INTO ls_address_printform_line.
    IF lv_formatted_address IS INITIAL.
        lv_formatted_address = ls_address_printform_line-address_line.
    ELSE.
        IF lv_inline_flag = abap_true.
            CONCATENATE lv_formatted_address ls_address_printform_line-address_line INTO lv_formatted_address SEPARATED BY space.
        ELSE.
            CONCATENATE lv_formatted_address ls_address_printform_line-address_line INTO lv_formatted_address SEPARATED BY lc_newline.
        ENDIF.
    ENDIF.
ENDLOOP.
```

## Formatting Using Form Routine

For programs that may use address formatting more than once, you can turn this logic into a form routine. Here is my suggested setup for the routine:

```abap
FORM get_formatted_address 
    USING is_adrc 
          iv_inline_flag TYPE abap_bool 
    CHANGING cv_address. 
 
    CONSTANTS: lc_newline TYPE c VALUE cl_abap_char_utilities=>newline. 
 
    DATA: ls_address                 TYPE adrs1, 
          lt_address_printform_table TYPE szadr_printform_table, 
          ls_address_printform_line  TYPE szadr_printform_table_line. 
 
    MOVE-CORRESPONDING is_adrc TO ls_address. 
 
    CALL FUNCTION 'ADDRESS_INTO_PRINTFORM' 
        EXPORTING 
            address_1                     = ls_address 
            address_type                  = '1' 
            sender_country                = space 
            street_has_priority           = 'X'         " Force street instead of PO Box 
            no_upper_case_for_city        = 'X'         " City and District will not be UPPERCASE 
        IMPORTING 
            address_printform_table       = lt_address_printform_table. 
 
    CLEAR cv_address. 

    LOOP AT lt_address_printform_table INTO ls_address_printform_line.
        IF cv_address IS INITIAL.
            cv_address = ls_address_printform_line-address_line.
        ELSE.
            IF iv_inline_flag = abap_true.
                CONCATENATE cv_address ls_address_printform_line-address_line INTO cv_address SEPARATED BY space.
            ELSE.
                CONCATENATE cv_address ls_address_printform_line-address_line INTO cv_address SEPARATED BY lc_newline.
            ENDIF.
        ENDIF.
    ENDLOOP.
ENDFORM. 
```

To use this, you may call it as follows:

```abap
PERFORM get_formatted_address USING ls_adrc lv_inline_flag CHANGING lv_formatted_address.
```