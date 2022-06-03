---
author: M-Stahl
title: add_remote_user
description: Adds a remote user to the local chat_manager instance.
kindex: add_remote_user
ms.author: jkepner
ms.topic: reference
ms.date: '09/12/2019'
edited: 00/00/0000
quality: good
security: public
applies-to: pc-gdk
---

# chat_manager::add_remote_user  

Adds a remote user to the local chat_manager instance.  

## Syntax  
  
```cpp
chat_user* add_remote_user(  
    PCWSTR xboxUserId,  
    uint64_t locallyUniqueEndpointIdentifier  
)  
```  
  
### Parameters  
  
*xboxUserId* &nbsp;&nbsp;\_In\_  
Type: PCWSTR  
  
The Xbox User ID of the remote user to add.  
  
*locallyUniqueEndpointIdentifier* &nbsp;&nbsp;\_In\_  
Type: uint64_t  
  
An identifier generated by the title used to refer to the endpoint that the remote user is on. And endpoint is a device, such as an Xbox One or a PC. Each endpoint must have a unique identifier in the local instance of Game Chat; the identifier does not need to be globally unique across the network. Remote users on the same endpoint, such as two users playing on the same Xbox One, must be added with the same endpoint identifier. Game Chat will use this identifier to indicate which endpoints data must be sent to in the game_chat_data_frame structure, and must be used by the title to indicate which endpoint data came from in chat_manager::process_incoming_data().  
  
  
### Return value  
Type: chat_user*
  
  
## Remarks  
  
The user will be added unconditionally, unless the total number of local and remote users is equal to the max user count that was indicated in chat_manager\::initialize(). <br /><br /> The return value is a pointer to the chat_user object associated with this user. If the Xbox User ID is invalid, the user's chat_indicator value will be game_chat_user_chat_indicator\::platform_restricted.
  
## Requirements  
  
**Header:** GameChat2.h
  
**Supported platforms:** Windows, Xbox One family consoles and Xbox Series consoles  
  
## See also  
[chat_manager](../chat_manager.md)  
[chat_user](../../chat_user/chat_user.md)  
[chat_manager::add_remote_user](chat_manager_add_remote_user.md)  
[chat_manager::remove_user](chat_manager_remove_user.md)  
[chat_manager::process_incoming_data](chat_manager_process_incoming_data.md)  
[game_chat_data_frame](../../../structs/game_chat_data_frame.md)
  
  