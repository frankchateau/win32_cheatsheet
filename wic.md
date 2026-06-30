# Windows Imaging Component (WIC)

The Windows Imaging Component (WIC) is an extensible platform that provides low-level API for digital images.

WIC supports the standard web image formats, high dynamic range images, and raw camera data.

## Headers and IDL files

| Name             | Description                                          |
| ---------------- | ---------------------------------------------------- |
| wincodec.h       | Defines C and C++ versions of the primary WIC APIs.  |
| wincodec.idl     | Defines the primary WIC interfaces.                  |
| wincodecsdk.h    | Defines C and C++ versions of the metadata WIC APIs. |
| wincodecsdk.idl  | Defines the WIC metadata interfaces.                 |
| wincodec_proxy.h | Defines the WIC proxy exports.                       |

## Library files

| Name              | Description                                                            |
| ----------------- | ---------------------------------------------------------------------- |
| windowscodecs.lib | Import library provided by the Windows Software Development Kit (SDK). |
| windowscodecs.dll | Stock implementation library provided by the operating system.         |

## Class factories

| Name                                      | Description                                                                                                                                             |
| ----------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [IWICImagingFactory](#iwicimagingfactory) | Primary class factory for application development using WIC components. This factory creates components such as image decoders, encoders and streams.   |
| `IWICComponentFactory`                    | Class factory targeted for WIC component developers. Components created from this factory are primarily used in codec and metadata handler development. |

To create either class factory, use the [CoCreateInstance](./com.md#cocreateinstance) COM function.

## Imaging components

| Name                    | Description                                                                                 |
| ----------------------- | ------------------------------------------------------------------------------------------- |
| [Bitmap](#iwicbitmap)   | Represents a writable in-memory representation of an [IWICBitmapSource](#iwicbitmapsource). |
| `Decoder`               | Used to decode image data from a stream into a format that is useful for image processing.  |
| `Encoder`               | Writes image data to a stream.                                                              |
| `Stream`                | Used to read and write data from a file, network resource, a block of memory, and so on.    |
| `Format Converter`      | Used to convert from one pixel format to another.                                           |
| `Metadata Query Reader` | Used to read metadata of an image or image frame.                                           |
| `Metadata Query Writer` | Used to write metadata to an image or image frame.                                          |

## Supported codecs

| Codec                                                            | Mime Types                       | Decoders | Encoders |
| ---------------------------------------------------------------- | -------------------------------- | -------- | -------- |
| BMP (Windows Bitmap Format), BMP Specification v5.               | image/bmp                        | ✅       | ✅       |
| GIF (Graphics Interchange Format 89a), GIF Specification 89a/89m | image/gif                        | ✅       | ✅       |
| ICO (Icon Format)                                                | image/ico                        | ✅       | ❌       |
| JPEG (Joint Photographic Experts Group), JFIF Specification 1.02 | image/jpeg, image/jpe, image/jpg | ✅       | ✅       |
| JPEG XR (JPEG Extended Range)                                    | image/jxr                        | ✅       | ✅       |
| PNG (Portable Network Graphics), PNG Specification 1.2           | image/png                        | ✅       | ✅       |
| TIFF (Tagged Image File Format), TIFF Specification 6.0          | image/bmp                        | ✅       | ✅       |
| Windows Media Photo                                              | image/vnd.ms-photo               | ✅       | ✅       |
| DDS (DirectDraw Surface)                                         | image/vnd.ms-dds                 | ✅       | ✅       |

## IWICImagingFactory

Exposes methods used to create components for the WIC such as decoders, encoders and pixel format converters.

Inherits from the [IUnknown](./com.md#iunknown) interface.

### IWICImagingFactory::CreateBitmap

Creates an [IWICBitmap](#iwicbitmap) object.

[WICBitmapNoCache](#wicbitmapcreatecacheoption) cache option is not supported when the `pixelFormat` is a [native pixel format](#native-pixel-formats) provided by WIC.

```cpp
IWICBitmap* pBitmap;

IWICImagingFactory::CreateBitmap(
  width,
  height,
  GUID_WICPixelFormat32bppPBGRA, // pixelFormat
  WICBitmapCacheOnLoad, // cache option
  &pBitmap
);
```

### WICBitmapCreateCacheOption

Specifies the desired cache usage.

```cpp
typedef enum WICBitmapCreateCacheOption {
  WICBitmapNoCache = 0,
  WICBitmapCacheOnDemand = 0x1,
  WICBitmapCacheOnLoad = 0x2,
  WICBITMAPCREATECACHEOPTION_FORCE_DWORD = 0x7fffffff
};
```

## IWICBitmap

Defines methods that add the concept of writeability and static in-memory representations of bitmaps to [IWICBitmapSource](#iwicbitmapsource).

The IWICBitmap interface inherits from [IWICBitmapSource](#iwicbitmapsource).

### IWICBitmap::Lock

Provides access to a rectangular area of the bitmap.

Locks are exclusive for writing but can be shared for reading. You cannot call `CopyPixels` while the [IWICBitmap](#iwicbitmap) is locked for writing. Doing so will return an error, since locks are exclusive.

```cpp
HRESULT hr = pBitmap->Lock(&rcLock, WICBitmapLockWrite, &pLock);
```

### WICBitmapLockFlags

Specifies access to an [IWICBitmap](#iwicbitmap).

```cpp
typedef enum WICBitmapLockFlags {
  WICBitmapLockRead = 0x1,
  WICBitmapLockWrite = 0x2,
  WICBITMAPLOCKFLAGS_FORCE_DWORD = 0x7fffffff
};
```

### IWICBitmap::SetPalette

Provides access for palette modifications.

```cpp
HRESULT hr = pBitmap->SetPalette();
```

### IWICBitmap::SetResolution

## IWICBitmapSource

// TODO

## IWICBitmapLock

Exposes methods that support the [Lock](#iwicbitmaplock) method.

The bitmap lock is simply an abstraction for a rectangular memory window into the bitmap. For the simplest case, a system memory bitmap, this is simply a pointer to the top left corner of the rectangle and a stride value.

To release the exclusive lock set by Lock method and the associated `IWICBitmapLock` object, call `IUnknown::Release` on the `IWICBitmapLock` object.

### IWICBitmapLock::GetDataPointer

Gets the pointer to the top left pixel in the locked rectangle.

The pointer provided by this method should not be used outside of the lifetime of the lock itself.

`GetDataPointer` is not available in multi-threaded apartment applications.

```cpp
UINT cbBufferSize;
BYTE *pTopLeftPx;

HRESULT hr = pLock->GetDataPointer(&cbBufferSize, &pTopLeftPx);
```

### IWICBitmapLock::GetPixelFormat

Gets the pixel format of for the locked area of pixels. This can be used to compute the number of bytes-per-pixel in the locked area.

```cpp
WICPixelFormatGUID pixelFormat;

HRESULT hr = pLock->GetPixelFormat(&pixelFormat);
```

### IWICBitmapLock::GetSize

Retrieves the width and height, in pixels, of the locked rectangle.

```cpp
UINT width, height;

HRESULT hr = pLock->GetSize(&width, &height);
```

### IWICBitmapLock::GetStride

Provides access to the stride value for the memory.

Note the stride value is specific to the [IWICBitmapLock](#iwicbitmaplock-1), not the [bitmap](#iwicbitmap). For example, two consecutive locks on the same rectangle of a [bitmap](#iwicbitmap) may return different pointers and stride values, depending on internal implementation.

```cpp
UINT cbStride;

HRESULT hr = pLock->GetStride(&cbStride);
```

## Native pixel formats

| Name                                     | Channel count | Bits per channel       | Bits per pixel | Storage type              | Notes                                                   |
| ---------------------------------------- | ------------- | ---------------------- | -------------- | ------------------------- | ------------------------------------------------------- |
| GUID_WICPixelFormatUndefined             | /             | /                      | /              | /                         | When there is no format specified.                      |
| GUID_WICPixelFormatDontCare              | /             | /                      | /              | /                         | When the format is unimportant for the image operation. |
| GUID_WICPixelFormat1bppIndexed           | 1             | 1                      | 1              | [UINT](./types.md#uint)   | Indexed                                                 |
| GUID_WICPixelFormat2bppIndexed           | 1             | 2                      | 2              | [UINT](./types.md#uint)   | Indexed                                                 |
| GUID_WICPixelFormat4bppIndexed           | 1             | 4                      | 4              | [UINT](./types.md#uint)   | Indexed                                                 |
| GUID_WICPixelFormat8bppIndexed           | 1             | 8                      | 8              | [UINT](./types.md#uint)   | Indexed                                                 |
| GUID_WICPixelFormat16bppBGR555           | 3             | 5                      | 16             | [UINT](./types.md#uint)   | Packed                                                  |
| GUID_WICPixelFormat16bppBGR565           | 3             | 5(B)/6(G)/5(R)         | 16             | [UINT](./types.md#uint)   | Packed                                                  |
| GUID_WICPixelFormat16bppBGRA555          | 4             | 5(B)/5(G)/5(R)/1(A)    | 16             | [UINT](./types.md#uint)   | Packed                                                  |
| GUID_WICPixelFormat32bppBGR101010        | 3             | 10                     | 32             | [UINT](./types.md#uint)   | Packed                                                  |
| GUID_WICPixelFormat32bppRGBA1010102      | 4             | 10(R)/10(G)/10(B)/2(A) | 32             | [UINT](./types.md#uint)   | Packed                                                  |
| GUID_WICPixelFormat32bppRGBA1010102XR    | 4             | 10(R)/10(G)/10(B)/2(A) | 32             | [UINT](./types.md#uint)   | Packed                                                  |
| GUID_WICPixelFormat32bppR10G10B10A2      | 4             | 10(R)/10(G)/10(B)/2(A) | 32             | [UINT](./types.md#uint)   | Packed                                                  |
| GUID_WICPixelFormat32bppR10G10B10A2HDR10 | 4             | 10(R)/10(G)/10(B)/2(A) | 32             | [UINT](./types.md#uint)   | Packed                                                  |
| GUID_WICPixelFormatBlackWhite            | 1             | 1                      | 1              | [UINT](./types.md#uint)   | Grayscale                                               |
| GUID_WICPixelFormat2bppGray              | 1             | 2                      | 2              | [UINT](./types.md#uint)   | Grayscale                                               |
| GUID_WICPixelFormat4bppGray              | 1             | 4                      | 4              | [UINT](./types.md#uint)   | Grayscale                                               |
| GUID_WICPixelFormat8bppGray              | 1             | 8                      | 8              | [UINT](./types.md#uint)   | Grayscale                                               |
| GUID_WICPixelFormat16bppGray             | 1             | 16                     | 16             | [UINT](./types.md#uint)   | Grayscale                                               |
| GUID_WICPixelFormat16bppGrayFixedPoint   | 1             | 16                     | 16             | `Fixed`                   | Grayscale                                               |
| GUID_WICPixelFormat16bppGrayHalf         | 1             | 16                     | 16             | `Float`                   | Grayscale                                               |
| GUID_WICPixelFormat32bppGrayFloat        | 1             | 32                     | 32             | `Float`                   | Grayscale                                               |
| GUID_WICPixelFormat32bppGrayFixedPoint   | 1             | 32                     | 32             | `Fixed`                   | Grayscale                                               |
| GUID_WICPixelFormat24bppRGB              | 3             | 8                      | 24             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat24bppBGR              | 3             | 8                      | 24             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat32bppBGR              | 3             | 8                      | 32             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat32bppRGBA             | 4             | 8                      | 32             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat32bppBGRA             | 4             | 8                      | 32             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat32bppRGBE             | 4             | 8                      | 32             | `Float`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat32bppPRGBA            | 4             | 8                      | 32             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat32bppPBGRA            | 4             | 8                      | 32             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat48bppRGB              | 3             | 16                     | 48             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat48bppBGR              | 3             | 16                     | 48             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat48bppRGBFixedPoint    | 3             | 16                     | 48             | `Fixed`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat48bppBGRFixedPoint    | 3             | 16                     | 48             | `Fixed`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat48bppRGBHalf          | 3             | 16                     | 48             | `Float`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat64bppRGBA             | 4             | 16                     | 64             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat64bppBGRA             | 4             | 16                     | 64             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat64bppPRGBA            | 4             | 16                     | 64             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat64bppPBGRA            | 4             | 16                     | 64             | [UINT](./types.md#uint)   | RGB/BGR                                                 |
| GUID_WICPixelFormat64bppRGBFixedPoint    | 3             | 16                     | 64             | `Fixed`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat64bppRGBAFixedPoint   | 4             | 16                     | 64             | `Fixed`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat64bppBGRAFixedPoint   | 4             | 16                     | 64             | `Fixed`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat64bppRGBHalf          | 3             | 16                     | 64             | `Float`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat64bppRGBAHalf         | 4             | 16                     | 64             | `Float`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat96bppRGBFixedPoint    | 3             | 32                     | 96             | `Fixed`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat128bppRGBFloat        | 3             | 32                     | 128            | `Float`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat128bppRGBAFloat       | 4             | 32                     | 128            | `Float`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat128bppPRGBAFloat      | 4             | 32                     | 128            | `Float`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat128bppRGBFixedPoint   | 3             | 32                     | 128            | `Fixed`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat128bppRGBAFixedPoint  | 4             | 32                     | 128            | `Fixed`                   | RGB/BGR                                                 |
| GUID_WICPixelFormat32bppRGB              | 3             | 8                      | 32             | [UINT](./types.md#uint)   | RGB/BGR, >= Windows 8                                   |
| GUID_WICPixelFormat64bppRGB              | 3             | 16                     | 64             | [UINT](./types.md#uint)   | RGB/BGR, >= Windows 8                                   |
| GUID_WICPixelFormat96bppRGBFloat         | 3             | 32                     | 96             | [FLOAT](./types.md#float) | RGB/BGR, >= Windows 8                                   |
| GUID_WICPixelFormat64bppPRGBAHalf        | 4             | 16                     | 64             | [FLOAT](./types.md#float) | RGB/BGR, >= Windows 8                                   |
| GUID_WICPixelFormat32bppCMYK             | 4             | 8                      | 32             | [UINT](./types.md#uint)   | CMYK                                                    |
| GUID_WICPixelFormat64bppCMYK             | 4             | 16                     | 64             | [UINT](./types.md#uint)   | CMYK                                                    |
| GUID_WICPixelFormat40bppCMYKAlpha        | 5             | 8                      | 40             | [UINT](./types.md#uint)   | CMYK                                                    |
| GUID_WICPixelFormat80bppCMYKAlpha        | 5             | 16                     | 80             | [UINT](./types.md#uint)   | CMYK                                                    |
| GUID_WICPixelFormat24bpp3Channels        | 3             | 8                      | 24             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat48bpp3Channels        | 3             | 16                     | 48             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat32bpp3ChannelsAlpha   | 4             | 8                      | 32             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat64bpp3ChannelsAlpha   | 4             | 16                     | 64             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat32bpp4Channels        | 4             | 8                      | 32             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat64bpp4Channels        | 4             | 16                     | 64             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat40bpp4ChannelsAlpha   | 5             | 8                      | 40             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat80bpp4ChannelsAlpha   | 5             | 16                     | 80             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat40bpp5Channels        | 5             | 8                      | 40             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat80bpp5Channels        | 5             | 16                     | 80             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat48bpp5ChannelsAlpha   | 6             | 8                      | 48             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat96bpp5ChannelsAlpha   | 6             | 16                     | 96             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat48bpp6Channels        | 6             | 8                      | 48             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat96bpp6Channels        | 6             | 16                     | 96             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat56bpp6ChannelsAlpha   | 7             | 8                      | 56             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat112bpp6ChannelsAlpha  | 7             | 16                     | 112            | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat56bpp7Channels        | 7             | 8                      | 56             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat112bpp7Channels       | 7             | 16                     | 112            | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat64bpp7ChannelsAlpha   | 8             | 8                      | 64             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat128bpp7ChannelsAlpha  | 8             | 16                     | 128            | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat64bpp8Channels        | 8             | 8                      | 64             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat128bpp8Channels       | 8             | 16                     | 128            | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat72bpp8ChannelsAlpha   | 9             | 8                      | 72             | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat144bpp8ChannelsAlpha  | 9             | 16                     | 144            | [UINT](./types.md#uint)   | n-channel                                               |
| GUID_WICPixelFormat8bppAlpha             | 1             | 8                      | 32             | [UINT](./types.md#uint)   | Alpha-only                                              |
| GUID_WICPixelFormat8bppY                 | 1             | 8                      | 8              | [UINT](./types.md#uint)   | Y’CbCr                                                  |
| GUID_WICPixelFormat8bppCb                | 1             | 8                      | 8              | [UINT](./types.md#uint)   | Y’CbCr                                                  |
| GUID_WICPixelFormat8bppCr                | 1             | 8                      | 8              | [UINT](./types.md#uint)   | Y’CbCr                                                  |
| GUID_WICPixelFormat16bppCbCr             | 2             | 8                      | 16             | [UINT](./types.md#uint)   | Y’CbCr                                                  |
