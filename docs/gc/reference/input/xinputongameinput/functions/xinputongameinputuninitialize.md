---
author: M-Stahl
title: XInputOnGameInputUninitialize
description: XInputOnGameInputUninitialize API reference
kindex: XInputOnGameInputUninitialize
ms.author: angillie
ms.topic: reference
edited: '08/08/2019'
quality: good
security: public
---

# XInputOnGameInputUninitialize  

Uninitializes the XInput wrapper APIs  

## Syntax  
  
```cpp
void XInputOnGameInputUninitialize(  
)  
```  
  
### Parameters  
  
  
### Return value
Type: void
  
None.  
  
## Remarks

To use this function, you must define the `XINPUT_ON_GAMEINPUT_EXPLICIT_INITIALIZATION` macro. By default, XInput is initialized in the background, and this causes the first call to an XInput function to take significantly more time, because it's bundled with the initialization work. `XINPUT_ON_GAMEINPUT_EXPLICIT_INITIALIZATION` gives you finer control over the initialization and uninitialization of XInput. To learn more and see the benefits of explicit initialization, read [Porting from XInput to GameInput](../../../../input/porting/input-porting-xinput.md#optimizingSection).
  
## Requirements  
  
**Header:** XInputOnGameInput.h
  
**Library:** xgameruntime.lib
  
**Supported platforms:** Xbox One family consoles and Xbox Series consoles  
  
## See also 
[Port XInput to GameInput](../../../../input/porting/input-porting-xinput.md)  
[XInputOnGameInput](../xinputongameinput_members.md)  
[XInputOnGameInputInitialize](xinputongameinputinitialize.md)  
  