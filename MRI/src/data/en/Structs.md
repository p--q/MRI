
# Structs

MRI can show elements of a struct and gets their value.

## Properties Scope

Structs is not UNO object and they have only element attribute. So, MRI shows structs' element in the Properties scope.

For example, com.sun.star.lang.Locale struct is shown like this below.

|Name|Value Type|Value|Mode|
|----|----|----|----|
|Country|string|US| |
|Language|string|en| |
|Variant|string|""| |

You can get a value of an element by the double clicking on the information edit.

## Services Scope

Structs do not supports any services but MRI shows structs name in the information edit at the Services scope. This behavior allows to open its IDL Reference page.
