---
author: M-Stahl
title: GameInput readings
description: GameInput readings
kindex: GameInput readings
ms.author: chrism
ms.topic: conceptual
edited: 07/19/2019
security: public
---

# GameInput readings


<a id="introductionSection"></a>

The raw input packets received from every device are encapsulated in "reading" objects. Readings contain the original raw packet data, and (usually) one or more transformations of the raw data into higher-level formats. In addition to their role as data containers, readings also serve as identifiers that reference a specific position within the input stream.


<a id="acquisitionSection"></a>
## Acquiring readings

The GameInput API provides two ways to acquire readings. The most common way is to access them directly from the input stream, via methods on the [IGameInput](../../reference/input/gameinput/interfaces/igameinput/igameinput.md) interface.

```c++
HRESULT GetCurrentReading(
    _In_ GameInputKind inputKind,
    _In_opt_ IGameInputDevice * device,
    _COM_Outptr_ IGameInputReading ** reading);
```

Calling [GetCurrentReading](../../reference/input/gameinput/interfaces/igameinput/methods/igameinput_getcurrentreading.md) retrieves the most recent reading from the input stream. An optional [GameInputKind](../../reference/input/gameinput/enums/gameinputkind.md) filter can be passed in that limits the returned readings to specific kinds of input, such as gamepad or keyboard. An optional [IGameInputDevice](../../reference/input/gameinput/interfaces/igameinputdevice/igameinputdevice.md) filter can also be passed in that limits the returned readings to only the ones that were generated by the specified device. These filters can be applied individually or together.

[IGameInputReading](../../reference/input/gameinput/interfaces/igameinputreading/igameinputreading.md) instances are reference-counted singletons. Retrieving a reading is an extremely fast and lightweight operation&mdash;no memory allocations or copies are performed, and the API call is lock-free with no kernel mode transitions. Because readings are singletons, an application can compare the reading pointers for equality to tell whether two calls to [GetCurrentReading](../../reference/input/gameinput/interfaces/igameinput/methods/igameinput_getcurrentreading.md) have returned the same reading (meaning no new input was generated).

Games with less complex input needs might simply poll for new input once per frame, and compare the state stored in the two readings for differences (if they aren't the same reading). Games with more complex input needs, however, might need to walk the input stream to get a complete picture of all the input state changes that have occurred since the previous frame. This is enabled by the [GetNextReading](../../reference/input/gameinput/interfaces/igameinput/methods/igameinput_getnextreading.md) and [GetPreviousReading](../../reference/input/gameinput/interfaces/igameinput/methods/igameinput_getpreviousreading.md) methods, which allow the same filters as [GetCurrentReading](../../reference/input/gameinput/interfaces/igameinput/methods/igameinput_getcurrentreading.md). The input stream maintains the last half second of historical readings in its buffer.

```c++
HRESULT GetNextReading(
    _In_ IGameInputReading * referenceReading,
    _In_ GameInputKind inputKind,
    _In_opt_ IGameInputDevice * device,
    _COM_Outptr_ IGameInputReading ** reading);

HRESULT GetPreviousReading(
    _In_ IGameInputReading * referenceReading,
    _In_ GameInputKind inputKind,
    _In_opt_ IGameInputDevice * device,
    _COM_Outptr_ IGameInputReading ** reading);
```

Alternatively, applications can register a callback that's invoked whenever input is generated. Like the previous synchronous methods, several filters can be applied to control what kinds of readings are returned, and from which devices. For more information, see [GameInput callbacks](../advanced/input-callbacks.md) in the Advanced GameInput topics section.


<a id="gettingStateSection"></a>
## Getting data from readings

While every reading contains the raw input packet data from the device, readings also typically contain one or more high-level transformations of that data. For example, input received from a gamepad is also parsed into a standard fixed-format structure with well-known button and thumbstick identifiers. Readings often contain several different representations of the same raw input data, allowing applications to choose which format best suits their needs.

Applications can query which kinds of data a reading contains by calling its [GetInputKind](../../reference/input/gameinput/interfaces/igameinputreading/methods/igameinputreading_getinputkind.md) method. This returns one or more flag values from the [GameInputKind](../../reference/input/gameinput/enums/gameinputkind.md) enumeration.

```c++
typedef enum GameInputKind
{
    GameInputKindUnknown         = 0x00000000,
    GameInputKindRawDeviceReport = 0x00000001,
    GameInputKindController      = 0x00000002,
    GameInputKindKeyboard        = 0x00000004,
    GameInputKindMouse           = 0x00000008,
    GameInputKindTouch           = 0x00000100,
    GameInputKindMotion          = 0x00001000,
    GameInputKindArcadeStick     = 0x00010000,
    GameInputKindFlightStick     = 0x00020000,
    GameInputKindGamepad         = 0x00040000,
    GameInputKindRacingWheel     = 0x00080000,
    GameInputKindUiNavigation    = 0x01000000,
    GameInputKindAny             = 0x0FFFFFFF
} GameInputKind;
```

The kinds of data available in a reading depend on the input device and its physical properties. For example, a reading from a standard keyboard might only contain keyboard data, whereas a reading from a keyboard with an integrated trackball might contain both keyboard and mouse data.

Nearly all game controllers produce readings that contain generic "controller" data, which is simply a collection of anonymous axis and button states. This enables broad device support for applications with an input mapping UI. However, many game controllers (like gamepads) also expose familiar fixed-format state in their readings, which is much easier for typical games to consume.

```c++
typedef struct GameInputGamepadState
{
    GameInputGamepadButtons buttons;
    float leftTrigger;
    float rightTrigger;
    float leftThumbstickX;
    float leftThumbstickY;
    float rightThumbstickX;
    float rightThumbstickY;
} GameInputGamepadState;
```

The [IGameInputReading](../../reference/input/gameinput/interfaces/igameinputreading/igameinputreading.md) interface contains methods for retrieving state in any of the formats the reading supports. All the different representations available from readings are precomputed, so these methods simply copy a few bytes of data and return.


<a id="sampleSection"></a>
## A simple gamepad input loop

The following sample code is one example of a fully functional input loop for gamepads. One thing to notice about this sample is that there's no explicit device enumeration. The only use of [IGameInputDevice](../../reference/input/gameinput/interfaces/igameinputdevice/igameinputdevice.md) is as a device identifier. None of its methods are ever called. This illustrates the input-centric nature of the GameInput API, and how it can simplify code for common input scenarios.

```c++
IGameInput* g_gameInput = nullptr;
IGameInputDevice* g_gamepad = nullptr;

HRESULT InitializeInput()
{
    return GameInputCreate(&g_gameInput);
}

void ShutdownInput()
{
    if (g_gamepad) g_gamepad->Release();
    if (g_gameInput) g_gameInput->Release();
}

void PollGamepadInput()
{
    // Ask for the latest reading from devices that provide fixed-format
    // gamepad state. If a device has been assigned to g_gamepad, filter
    // readings to just the ones coming from that device. Otherwise, if
    // g_gamepad is null, it will allow readings from any device.
    IGameInputReading * reading;
    if (SUCCEEDED(g_gameInput->GetCurrentReading(GameInputKindGamepad, g_gamepad, &reading)))
    {
        // If no device has been assigned to g_gamepad yet, set it
        // to the first device we receive input from. (This must be
        // the one the player is using because it's generating input.)
        if (!g_gamepad) reading->GetDevice(&g_gamepad);

        // Retrieve the fixed-format gamepad state from the reading.
        GameInputGamepadState state;
        reading->GetGamepadState(&state);
        reading->Release();

        // Application-specific code to process the gamepad state goes here.
    }

    // If an error is returned from GetCurrentReading(), it means the
    // gamepad we were reading from has disconnected. Reset the
    // device pointer, and go back to looking for an active gamepad.
    else if (g_gamepad)
    {
        g_gamepad->Release();
        g_gamepad = nullptr;
    }
}
```


<a id="seeAlsoSection"></a>
## See also

[GameInput fundamentals](input-fundamentals.md)

[Advanced GameInput topics](../advanced/input-advanced-topics.md)

[GameInput API reference](../../reference/input/gc-reference-input-toc.md)