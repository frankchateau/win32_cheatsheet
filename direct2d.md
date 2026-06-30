# Direct2D

To use Direct2D, application should at least include [d2d1.h](#d2d1h) header file
and add `d2d1.lib` to the list of libraries. The Direct2D headers and libraries are part of `Windows SDK`.

[ID2D1Factory](#id2d1factory) interface is the starting point for using Direct2D. It is used for instantiating Direct2D [resources](#id2d1resource). To create an [ID2D1Factory](#id2d1factory), one of [D2D1CreateFactory](#d2d1createfactory) functions is used.

One of the most useful objects a [factory](#id2d1factory) can create is the [ID2D1RenderTarget](#id2d1rendertarget). A render target creates resources for drawing and performs drawing operations.

## Header files

### d2d1.h

Defines C and C++ versions of the primary Direct2D API.

### d2d1helper.h

Defines C++ helper functions, classes, and structures.

### d2dbasetypes.h

Defines drawing primitives for Direct2D, such as points and rectangles. This header is included with [d2d1.h](#d2d1h).

### d2derr.h

Defines the error codes for Direct2D. This header is included with [d2d1.h](#d2d1h).

### d2d1_1.h

Defines C and C++ versions of the primary Direct2D API for Windows 8 and later.

### d2d1_1helper.h

Defines C++ helper functions, classes, and structures for Windows 8 and later.

### d2d1effects.h

Defines C and C++ versions of the image effects part of the Direct2D API for Windows 8 and later.

### d2d1effecthelpers.h

Defines C++ helper functions, classes, and structures of the image effects part of the Direct2D API for Windows 8 and later.

## D2D1CreateFactory

Creates a [factory object](#id2d1factory) that can be used to create Direct2D [resources](#id2d1resource).

```cpp
HRESULT hr = D2D1CreateFactory(
  D2D1_FACTORY_TYPE_SINGLE_THREADED,
  &pFactory
);
```

## ID2D1Factory

Creates Direct2D resources.

Created using one of [D2D1CreateFactory](#d2d1createfactory) functions.

### ID2D1Factory::CreateDCRenderTarget

Creates an [ID2D1DCRenderTarget](#id2d1dcrendertarget) that draws to a Windows [GDI](./gdi.md) device context.

Before you can render with a DC render target, you must use the render target's `BindDC` method to associate it with a [GDI](./gdi.md) DC.

```cpp
HRESULT hr = pFactory->CreateDCRenderTarget(
  D2D1::RenderTargetProperties(),
  &pRenderTarget
);
```

### ID2D1Factory::CreateHwndRenderTarget

Creates an [ID2D1HwndRenderTarget](#id2d1hwndrendertarget), a render target that renders to a [window](./window.md).

```cpp
HRESULT hr = pFactory->CreateHwndRenderTarget(
  D2D1::RenderTargetProperties(),
  D2D1::HwndRenderTargetProperties(hwnd, size),
  &pRenderTarget
);
```

### ID2D1Factory::CreateWicBitmapRenderTarget

Creates a [render target](#id2d1rendertarget) that renders to a Microsoft Windows Imaging Component (WIC) bitmap.

```cpp
HRESULT hr = pFactory->CreateWicBitmapRenderTarget(

);
```

## D2D1_RENDER_TARGET_PROPERTIES

Contains rendering options (hardware or software), pixel format, DPI information, remoting options, and Direct3D support requirements for a render target.

```cpp
typedef struct D2D1_RENDER_TARGET_PROPERTIES {
  D2D1_RENDER_TARGET_TYPE  type;
  D2D1_PIXEL_FORMAT        pixelFormat;
  FLOAT                    dpiX;
  FLOAT                    dpiY;
  D2D1_RENDER_TARGET_USAGE usage;
  D2D1_FEATURE_LEVEL       minLevel;
} D2D1_RENDER_TARGET_PROPERTIES;
```

## D2D1_RENDER_TARGET_TYPE

Describes whether a render target uses hardware or software rendering, or if Direct2D should select the rendering mode.

```cpp
typedef enum D2D1_RENDER_TARGET_TYPE {
  D2D1_RENDER_TARGET_TYPE_DEFAULT = 0,
  D2D1_RENDER_TARGET_TYPE_SOFTWARE = 1,
  D2D1_RENDER_TARGET_TYPE_HARDWARE = 2,
  D2D1_RENDER_TARGET_TYPE_FORCE_DWORD = 0xffffffff
};
```

## D2D1_PIXEL_FORMAT

Contains the [data format](#dxgi_format) and [alpha mode](#d2d1_alpha_mode) for a bitmap or render target.

```cpp
typedef struct D2D1_PIXEL_FORMAT {
  DXGI_FORMAT     format;
  D2D1_ALPHA_MODE alphaMode;
} D2D1_PIXEL_FORMAT;
```

## DXGI_FORMAT

Defines constants that specify resource data formats, including fully-typed and typeless formats. A list of modifiers at the bottom of the page more fully describes each format type.

```cpp
typedef enum DXGI_FORMAT {
  DXGI_FORMAT_UNKNOWN = 0,
  DXGI_FORMAT_R32G32B32A32_TYPELESS = 1,
  DXGI_FORMAT_R32G32B32A32_FLOAT = 2,
  DXGI_FORMAT_R32G32B32A32_UINT = 3,
  DXGI_FORMAT_R32G32B32A32_SINT = 4,
  DXGI_FORMAT_R32G32B32_TYPELESS = 5,
  DXGI_FORMAT_R32G32B32_FLOAT = 6,
  DXGI_FORMAT_R32G32B32_UINT = 7,
  DXGI_FORMAT_R32G32B32_SINT = 8,
  DXGI_FORMAT_R16G16B16A16_TYPELESS = 9,
  DXGI_FORMAT_R16G16B16A16_FLOAT = 10,
  DXGI_FORMAT_R16G16B16A16_UNORM = 11,
  DXGI_FORMAT_R16G16B16A16_UINT = 12,
  DXGI_FORMAT_R16G16B16A16_SNORM = 13,
  DXGI_FORMAT_R16G16B16A16_SINT = 14,
  DXGI_FORMAT_R32G32_TYPELESS = 15,
  DXGI_FORMAT_R32G32_FLOAT = 16,
  DXGI_FORMAT_R32G32_UINT = 17,
  DXGI_FORMAT_R32G32_SINT = 18,
  DXGI_FORMAT_R32G8X24_TYPELESS = 19,
  DXGI_FORMAT_D32_FLOAT_S8X24_UINT = 20,
  DXGI_FORMAT_R32_FLOAT_X8X24_TYPELESS = 21,
  DXGI_FORMAT_X32_TYPELESS_G8X24_UINT = 22,
  DXGI_FORMAT_R10G10B10A2_TYPELESS = 23,
  DXGI_FORMAT_R10G10B10A2_UNORM = 24,
  DXGI_FORMAT_R10G10B10A2_UINT = 25,
  DXGI_FORMAT_R11G11B10_FLOAT = 26,
  DXGI_FORMAT_R8G8B8A8_TYPELESS = 27,
  DXGI_FORMAT_R8G8B8A8_UNORM = 28,
  DXGI_FORMAT_R8G8B8A8_UNORM_SRGB = 29,
  DXGI_FORMAT_R8G8B8A8_UINT = 30,
  DXGI_FORMAT_R8G8B8A8_SNORM = 31,
  DXGI_FORMAT_R8G8B8A8_SINT = 32,
  DXGI_FORMAT_R16G16_TYPELESS = 33,
  DXGI_FORMAT_R16G16_FLOAT = 34,
  DXGI_FORMAT_R16G16_UNORM = 35,
  DXGI_FORMAT_R16G16_UINT = 36,
  DXGI_FORMAT_R16G16_SNORM = 37,
  DXGI_FORMAT_R16G16_SINT = 38,
  DXGI_FORMAT_R32_TYPELESS = 39,
  DXGI_FORMAT_D32_FLOAT = 40,
  DXGI_FORMAT_R32_FLOAT = 41,
  DXGI_FORMAT_R32_UINT = 42,
  DXGI_FORMAT_R32_SINT = 43,
  DXGI_FORMAT_R24G8_TYPELESS = 44,
  DXGI_FORMAT_D24_UNORM_S8_UINT = 45,
  DXGI_FORMAT_R24_UNORM_X8_TYPELESS = 46,
  DXGI_FORMAT_X24_TYPELESS_G8_UINT = 47,
  DXGI_FORMAT_R8G8_TYPELESS = 48,
  DXGI_FORMAT_R8G8_UNORM = 49,
  DXGI_FORMAT_R8G8_UINT = 50,
  DXGI_FORMAT_R8G8_SNORM = 51,
  DXGI_FORMAT_R8G8_SINT = 52,
  DXGI_FORMAT_R16_TYPELESS = 53,
  DXGI_FORMAT_R16_FLOAT = 54,
  DXGI_FORMAT_D16_UNORM = 55,
  DXGI_FORMAT_R16_UNORM = 56,
  DXGI_FORMAT_R16_UINT = 57,
  DXGI_FORMAT_R16_SNORM = 58,
  DXGI_FORMAT_R16_SINT = 59,
  DXGI_FORMAT_R8_TYPELESS = 60,
  DXGI_FORMAT_R8_UNORM = 61,
  DXGI_FORMAT_R8_UINT = 62,
  DXGI_FORMAT_R8_SNORM = 63,
  DXGI_FORMAT_R8_SINT = 64,
  DXGI_FORMAT_A8_UNORM = 65,
  DXGI_FORMAT_R1_UNORM = 66,
  DXGI_FORMAT_R9G9B9E5_SHAREDEXP = 67,
  DXGI_FORMAT_R8G8_B8G8_UNORM = 68,
  DXGI_FORMAT_G8R8_G8B8_UNORM = 69,
  DXGI_FORMAT_BC1_TYPELESS = 70,
  DXGI_FORMAT_BC1_UNORM = 71,
  DXGI_FORMAT_BC1_UNORM_SRGB = 72,
  DXGI_FORMAT_BC2_TYPELESS = 73,
  DXGI_FORMAT_BC2_UNORM = 74,
  DXGI_FORMAT_BC2_UNORM_SRGB = 75,
  DXGI_FORMAT_BC3_TYPELESS = 76,
  DXGI_FORMAT_BC3_UNORM = 77,
  DXGI_FORMAT_BC3_UNORM_SRGB = 78,
  DXGI_FORMAT_BC4_TYPELESS = 79,
  DXGI_FORMAT_BC4_UNORM = 80,
  DXGI_FORMAT_BC4_SNORM = 81,
  DXGI_FORMAT_BC5_TYPELESS = 82,
  DXGI_FORMAT_BC5_UNORM = 83,
  DXGI_FORMAT_BC5_SNORM = 84,
  DXGI_FORMAT_B5G6R5_UNORM = 85,
  DXGI_FORMAT_B5G5R5A1_UNORM = 86,
  DXGI_FORMAT_B8G8R8A8_UNORM = 87,
  DXGI_FORMAT_B8G8R8X8_UNORM = 88,
  DXGI_FORMAT_R10G10B10_XR_BIAS_A2_UNORM = 89,
  DXGI_FORMAT_B8G8R8A8_TYPELESS = 90,
  DXGI_FORMAT_B8G8R8A8_UNORM_SRGB = 91,
  DXGI_FORMAT_B8G8R8X8_TYPELESS = 92,
  DXGI_FORMAT_B8G8R8X8_UNORM_SRGB = 93,
  DXGI_FORMAT_BC6H_TYPELESS = 94,
  DXGI_FORMAT_BC6H_UF16 = 95,
  DXGI_FORMAT_BC6H_SF16 = 96,
  DXGI_FORMAT_BC7_TYPELESS = 97,
  DXGI_FORMAT_BC7_UNORM = 98,
  DXGI_FORMAT_BC7_UNORM_SRGB = 99,
  DXGI_FORMAT_AYUV = 100,
  DXGI_FORMAT_Y410 = 101,
  DXGI_FORMAT_Y416 = 102,
  DXGI_FORMAT_NV12 = 103,
  DXGI_FORMAT_P010 = 104,
  DXGI_FORMAT_P016 = 105,
  DXGI_FORMAT_420_OPAQUE = 106,
  DXGI_FORMAT_YUY2 = 107,
  DXGI_FORMAT_Y210 = 108,
  DXGI_FORMAT_Y216 = 109,
  DXGI_FORMAT_NV11 = 110,
  DXGI_FORMAT_AI44 = 111,
  DXGI_FORMAT_IA44 = 112,
  DXGI_FORMAT_P8 = 113,
  DXGI_FORMAT_A8P8 = 114,
  DXGI_FORMAT_B4G4R4A4_UNORM = 115,
  DXGI_FORMAT_P208 = 130,
  DXGI_FORMAT_V208 = 131,
  DXGI_FORMAT_V408 = 132,
  DXGI_FORMAT_SAMPLER_FEEDBACK_MIN_MIP_OPAQUE = 189,
  DXGI_FORMAT_SAMPLER_FEEDBACK_MIP_REGION_USED_OPAQUE = 190,
  DXGI_FORMAT_A4B4G4R4_UNORM = 191,
  DXGI_FORMAT_FORCE_UINT = 0xffffffff
};
```

## D2D1_ALPHA_MODE

Specifies how the alpha value of a bitmap or render target should be treated.

```cpp
typedef enum D2D1_ALPHA_MODE {
  D2D1_ALPHA_MODE_UNKNOWN = 0,
  D2D1_ALPHA_MODE_PREMULTIPLIED = 1,
  D2D1_ALPHA_MODE_STRAIGHT = 2,
  D2D1_ALPHA_MODE_IGNORE = 3,
  D2D1_ALPHA_MODE_FORCE_DWORD = 0xffffffff
};
```

## D2D1_RENDER_TARGET_USAGE

Describes how a render target is remoted and whether it should be [GDI](./gdi.md)-compatible. This enumeration allows a bitwise combination of its member values.

```cpp
typedef enum D2D1_RENDER_TARGET_USAGE {
  D2D1_RENDER_TARGET_USAGE_NONE = 0x00000000,
  D2D1_RENDER_TARGET_USAGE_FORCE_BITMAP_REMOTING = 0x00000001,
  D2D1_RENDER_TARGET_USAGE_GDI_COMPATIBLE = 0x00000002,
  D2D1_RENDER_TARGET_USAGE_FORCE_DWORD = 0xffffffff
};
```

## D2D1_FEATURE_LEVEL

Describes the minimum DirectX support required for hardware rendering by a render target.

```cpp
typedef enum D2D1_FEATURE_LEVEL {
  D2D1_FEATURE_LEVEL_DEFAULT = 0,
  D2D1_FEATURE_LEVEL_9,
  D2D1_FEATURE_LEVEL_10,
  D2D1_FEATURE_LEVEL_FORCE_DWORD = 0xffffffff
};
```

## ID2D1RenderTarget

Represents an object that can receive drawing commands. Interfaces that inherit from `ID2D1RenderTarget` render the drawing commands they receive in different ways.

Applications should create render targets once and hold on to them for the life of the application or until the render target's `EndDraw` method returns the `D2DERR_RECREATE_TARGET` error. When you receive this error, recreate the render target (and any resources it created).

## ID2D1DCRenderTarget

Issues drawing commands to a [GDI](./gdi.md) [DC](./gdi.md#dc).

Inherits from [ID2D1RenderTarget](#id2d1rendertarget).

Inherited by [ID2D1DCRenderTarget](#id2d1dcrendertarget), [ID2D1HwndRenderTarget](#id2d1hwndrendertarget).

### ID2D1DCRenderTarget::BindDC

Binds the render target to the [DC](./gdi.md#dc) to which it issues drawing commands.

```cpp
// get client area rectangle
RECT rect;
GetClientRect(hwnd, &rect);

HRESULT hr = pRenderTarget->BindDC(hdc, &rect);
```

## ID2D1HwndRenderTarget

Renders drawing instructions to a [window](./window.md).

Inherits from [ID2D1RenderTarget](#id2d1rendertarget).

### ID2D1HwndRenderTarget::CheckWindowState

Indicates whether the [HWND](./types.md#hwnd) associated with this render target is occluded by returning [D2D1_WINDOW_STATE](#d2d1_window_state)

If the window was occluded the last time that `EndDraw` was called, the next time that the render target calls `CheckWindowState`, it will return [D2D1_WINDOW_STATE_OCCLUDED](#d2d1_window_state) regardless of the current window state.

If you want to use `CheckWindowState` to determine the current window state, you should call `CheckWindowState` after every `EndDraw` call and ignore its return value. The next time you call it, it will return the actual window state.

```cpp
D2D1_WINDOW_STATE state = pRenderTarget->CheckWindowState(); // state might be stale here, call CheckWindowState one more time if needed
```

### D2D1_WINDOW_STATE

Describes whether a window is occluded.

```cpp
typedef enum D2D1_WINDOW_STATE {
  D2D1_WINDOW_STATE_NONE = 0x0000000,
  D2D1_WINDOW_STATE_OCCLUDED = 0x0000001,
  D2D1_WINDOW_STATE_FORCE_DWORD = 0xffffffff
};
```

### ID2D1HwndRenderTarget::GetHwnd

Returns the [HWND](./types.md#hwnd) associated with this render target.

```cpp
HWND hwnd = pRenderTarget->GetHwnd();
```

### ID2D1HwndRenderTarget::Resize

Changes the size of the render target to the specified pixel size.

```cpp
HRESULT hr = pRenderTarget->Resize(D2D1::SizeU(width, height));

// or pass a pointer to D2D1_SIZE_U
HRESULT hr = pRenderTarget->Resize(pSize);
```

## ID2D1Resource

Represents a Direct2D drawing resource.

Inherits from the [IUnknown](./com.md#iunknown) interface

### ID2D1Resource::GetFactory

Retrieves the factory associated with this resource.

```cpp
pResource->GetFactory(&pFactory); // pFactory is of type ID2D1Factory*
```
