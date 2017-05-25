
# How to Run MRI

MRI can be called from any languages and Add-Ons menu entries.

This help system does not allow to keep indentation in code, please see following page: https://github.com/hanya/MRI/wiki/RunMRI

## Run MRI from Add-Ons Menu

MRI can be run from Add-Ons menu entry, choose Tools - Add-Ons - MRI entry, 
then the MRI window opens with its target as return 
value of the getCurrentComponent method of the com.sun.star.frame.XDesktop 
interface of the com.sun.star.frame.Desktop service.

In this case, MRI is executed with the current document as its target and 
it can be written in OOo Basic like the following:

```basic
oTarget = StarDesktop.getCurrentComponent()
Mri oTarget
```

oTarget may be the same as the return value of the ThisComponent function.

## Run MRI from Add-Ons Menu with the Current Selection

Choose Tools - Add-Ons - MRI <- selection menu entry to run MRI 
with its target with current selected object in the current document.
In the OOo Basic, you can write like below:

```basic
 oTarget = StarDesktop.getCurrentComponent().getCurrentSelection()
 Mri oTarget
```

<!-- {{Tip|You can use this menu to get information of an object that 
can be selected which is placed on the document (e.g. Writer: text, 
text objects or and so on, Calc: a cell, a cell range, cell ranges, 
drawing objects or ...}} -->

<!-- {{Note|When you select a drawing object and then call MRI with the selection, 
drawing objects are collected in com.sun.star.drawing.ShapeCollection service.
Therefore you need to get a desired object from it using getByIndex method. 
Some selectable objects are the same as it.}} -->

## Run MRI from Languages

If you want to use MRI from languages that you want to use, instantiate "mytools.Mri" service. And call inspect method with a target as its argument.

inspect is a method of com.sun.star.beans.XIntrospection interface and you may query this interface depending on the language that you use. Normal inspect method returns com.sun.star.beans.XIntrospectionAccess interface and MRI's it returns the same interface taken from com.sun.star.beans.Introspection service.

If you pass non-UNO objects to inspect method, MRI does not work correctly.

```
 com.sun.star.beans.XIntrospectionAccess inspect( [in] any aObject )
```

Since 0.8.0, MRI is supported to instantiate with createInstanceWithArgumentsAndContext method of css.lang.XSingleComponentFactory interface. You can inspect without calling the inspect method at creation time of the MRI instance. Put a target as first element into the sequence of the argument for the method.


### OpenOffice.org Basic

The extension includes "MRILib" library of OOo Basic. And Mri subroutine placed in the MRILib library helps you to call MRI from OOo Basic easily. Pass an object to the Mri like written below (in this case, ThisComponent is passed as a target).

```basic
 Mri ThisComponent
```

An argument of Mri subroutine is optional. When Mri called without an argument, Mri subroutine passes StarDesktop to MRI as a target using OOo Basic runtime function.

```basic
 Mri  ' StarDesktop is passed to MRI by Mri subroutine
```

Before you call Mri first time, need MRILib loaded.

```basic
 Globalscope.BasicLibraries.LoadLibrary( "MRILib" )
```

Then you can call MRI like written below from OOo Basic without the Mri helper subroutine.

```basic
 oMRI = CreateUnoService( "mytools.Mri" )
 oMRI.inspect( MriTargetObject )
```

Or like the followoing: 

```basic
 CreateUnoServiceWithArguments( "mytools.Mri", Array( MriTargetObject ) )
```

The control of the program is returned after Mri subroutine passes to service of MRI. Therefore you need to breakpoints of Basic IDE debugger if you want to inspect a object temporary created or you want to inspect the target well.

If you passed to a target to Mri that is part of a document opened on the StarDesktop and it lives after your Basic code finished, you can travel in it (passed value is a copy of the target but references are the same them of the original target).

Since MRI 1.3.0, you can call constructors as follows: 

```basic
 oMRI = mytools.Mri.create()
 oMRI.inspect( MriTargetObject )
```

Or calling another constructor with a target:

```
 mytools.Mri.inspect( MriTargetObject )
```

### BeanShell

This is an example of how to use MRI from BeanShell macros.

```java
 import com.sun.star.uno.UnoRuntime;
 import com.sun.star.uno.XComponentContext;
 import com.sun.star.lang.XMultiComponentFactory;
 import com.sun.star.beans.XIntrospection;
 //import com.sun.star.beans.XIntrospectionAccess;

 XComponentContext xContext = XSCRIPTCONTEXT.getComponentContext();
 XMultiComponentFactory xMCF = xContext.getServiceManager();
 try {
   oMRI = xMCF.createInstanceWithContext( "mytools.Mri", xContext );
 } catch (com.sun.star.uno.Exception e) {
   System.out.println( e.Message );
 }
 XIntrospection xIntrospection = (XIntrospection) UnoRuntime.queryInterface( 
                                       XIntrospection.class, oMRI );
 Object oDoc = XSCRIPTCONTEXT.getDocument();
 Object oIAccess= xIntrospection.inspect(oDoc);
```

### JavaScript

This code is an example to run MRI from Javascript macro.

```javascript
 importClass(Packages.com.sun.star.uno.UnoRuntime);
 importClass(Packages.com.sun.star.beans.XIntrospection);
 
 oDoc = XSCRIPTCONTEXT.getDocument();
 
 xContext = XSCRIPTCONTEXT.getComponentContext();
 xMCF = xContext.getServiceManager();
 oMRI = xMCF.createInstanceWithContext("mytools.Mri", xContext);
 xIntrospection = UnoRuntime.queryInterface(XIntrospection, oMRI);
 xIntrospection.inspect(oDoc);
```

### Java

```java
 import com.sun.star.beans.XIntrospection;
 
 try {
  Object oMRI = xMultComponentFactory.createInstanceWithContext( 
      "mytools.Mri", xContext );
  XIntrospection xIntrospection = (XIntrospection) UnoRuntime.queryInterface(
     XIntrospection.class, oMRI);
 
  xIntrospection.inspect(oShape);
 } catch (com.sun.star.uno.Exception e) {
   System.err.println();
 }
```

### Python

This example shows how to use MRI from Python macro.

```python
 def Mri_test():
    ctx = XSCRIPTCONTEXT.getComponentContext()
    document = XSCRIPTCONTEXT.getDocument()
    
    mri(ctx,document)
 
 def mri(ctx, target):
    mri = ctx.ServiceManager.createInstanceWithContext(
        "mytools.Mri",ctx)
    mri.inspect(target)
```

And you can also use MRI through Python-bridge.

```python
 import uno
  
 def connect():
    try:
        localctx = uno.getComponentContext()
        resolver = localctx.ServiceManager.createInstanceWithContext(
            "com.sun.star.bridge.UnoUrlResolver",localctx)
        ctx = resolver.resolve(
           "uno:socket,host=localhost,port=2002;urp;StarOffice.ComponentContext")
    except:
        return None
    return ctx
  
  
 def mri(ctx, target):
    mri = ctx.ServiceManager.createInstanceWithContext(
        "mytools.Mri",ctx)
    mri.inspect(target)

 if __name__=="__main__":
    ctx = connect()
    if ctx == None:
        print "Failed to connect."
        import sys
        sys.exit()
    smgr = ctx.ServiceManager
    desktop = smgr.createInstanceWithContext("com.sun.star.frame.Desktop",ctx)
    model = desktop.loadComponentFromURL("private:factory/scalc","_default",0,())
    mri(ctx,model)
    ctx.ServiceManager
```

### VB Script

MRI easily can be run from automation.

```basic
 Set oSM = WScript.CreateObject("com.sun.star.ServiceManager")
 Set oDesktop = oSM.createInstance("com.sun.star.frame.Desktop")
 
 Dim aArgs() 
 Set oDoc = oDesktop.loadComponentFromURL("private:factory/scalc","_blank",0,aArgs)
 
 Set oMRI = oSM.createInstance("mytools.Mri") 
 oMRI.inspect(oDoc)
```

### ooRexx

ooRexx (with Vienna BSF4Rexx).

```rexx
 /*  */
 xScriptContext = uno.getScriptContext()
 xContext = xScriptContext~getComponentContext()
 xServiceManager = xContext~getServiceManager()
 
 oDoc = xScriptContext~getDocument
 
 oMRI = xServiceManager~createInstanceWithContext("mytools.Mri", xContext)
 oMRI~XIntrospection~inspect(oDoc)
 
 ::requires UNO.CLS
```

## Creating Custom Menu Entries

If you want to add menu entries or toolbar buttons of MRI, use this URL.

```
 service:mytools.Mri?current
```

If current argument is passed, MRI gets CurrentComponent of Desktop as its target. MRI run with a selection of a CurrentComponent of the Desktop.

```
 service:mytools.Mri?selection
```

