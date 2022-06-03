---
author: aablackm
title: PairControllerWithUserId
description: Pairs the user with given user ID, to the controller with given controller ID.
ms.assetid: M:Windows.Xbox.xtfuser.IXtfUserClient.PairControllerWithUserId(UINT32,UINT64)
kindex:
- PairControllerWithUserId method
- IXtfUserClient--PairControllerWithUserId method
ms.author: stevenpr
ms.topic: reference
edited: 06/13/2019
quality: good
security: public
---

# IXtfUserClient::PairControllerWithUserId Method
Pairs the user identified by the specified user ID to the controller with the specified controller ID.

## Syntax  

```cpp
public:
HRESULT PairControllerWithUserId(
         UINT32 userId,
         UINT64 controllerId
)  
```

### Parameters  

*userId*  
Type: UINT32 

The user ID of the user to pair with the controller. 

*controllerId*  
Type: UINT64 

The controller ID of the controller. 

### Return value  
Type: HRESULT 

HRESULT success or error code.

## Remarks  

To get the user ID of the user, call [ListUsers](listusers-ixtfuserclient-xtfuser-xbox-windows-m.md). In the returned [XTF_USER_DATA](../../../structures/XTF_USER_DATA-xtfuser-xbox-windows-t.md) structure, examine the *userId* member.

Pairing a controller by using **PairControllerWithUserId** is additive. If a user is already paired with the controller, the user specified by *userName* is also paired with the controller. To create an exclusive pairing, call [PairControllerWithUserIdExclusive]() or [PairControllerWithUserExclusive]().

## Requirements  

**Header:** xtfuser.h  

**Library:** xtfuser.lib  
  
**Supported platforms:** Windows (for Xbox console tools)  
  
## See also

[PairControllerWithUser](paircontrollerwithuser-ixtfuserclient-xtfuser-xbox-windows-m.md)  
[PairControllerWithUserIdExclusive]()  
[PairControllerWithUserExclusive]()  