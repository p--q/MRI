
# Methods

## Looking at Method Information

An example of methods information is shown like a list below.

|Name|Arguments|Return Type|Exceptions|
|----|----|----|----|
|getName|() |string| |
|supportsService|( [in] string ServiceName )|boolean| |

getName can be called with no arguments and returns string type value. And supportsService method needs a string type argument and its type of returned value is boolean.

You can invoke a method by double clicking the line that it is indicated. But some methods need one or more arguments and it is difficult to pass arguments from MRI to methods. Therefore many methods can not be called, which needs non string, numerical, boolean or enum arguments. 

You want to open IDL Reference written about a method you want to see, click the line (don't have to select hole line only, carret position is used to get which method is selected) and push "Ref." button.

## Calling Special Methods

There are a few important interfaces and their methods are sometimes needed to get something. In the most case, the interface is the one from the container module.

## getByIndex

Sometimes objects are collected into com.sun.star.container.XIndexContainer and we can get an element from it using com.sun.star.container.XIndexAccess interface. When you call getByIndex method through MRI, MRI calls getCount method to get the number of items in the XIndexContainer and let user select an index you want to get a value before invoking getByIndex method. 

Select an item listed in the dialog is shown when you call getByIndex method of the target. MRI uses selected item as the argument to invoke getByIndex method. If a container with no element, empty list box is shown.

## getByName

com.sun.star.container.XNameContainer is used to manage elements and getByName method availabled from com.sun.star.container.XNameAccess is used to get an element from its container. MRI calls getElementNames method to make user select a name of element. Select an item listed in the listbox on the dialog. MRI uses the selected item to call getByName method. If no elements are there in a container, empty list box is shown.

## Calling Methods with Arguments

MRI can call a method that needs only numerical, string or boolean type arguments. For example, these methods can be called.

```
getCellByPosition( [in] long nColumn, [in] long nRow )
setName( [in] string aName )
goRight( [in] short nCount, [in] boolean bExpand )
```

But these methods can not be called because they need structs or interfaces as their arguments.

```
insertDocumentFromURL( [in] string aURL, [in] [].beans.PropertyValue aOptions )
findAll( [in] .util.XSearchDescriptor xDesc )
```

When you call a method taking only numerical, string or boolean type arguments, the dialog box is shown and input arguments. 

You can open your IDL Reference to push the Ref. button on the dialog.
