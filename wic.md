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
IWICBitmap *pBitmap;

HRESULT hr = pFactory->CreateBitmap(
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
  WICBitmapNoCache = 0, // no caching, pixels are read from the source every time
  WICBitmapCacheOnDemand = 0x1, // lazy caching of pixels, but only of the pixel region(s) you actually use
  WICBitmapCacheOnLoad = 0x2, // caches the entire source when created
  WICBITMAPCREATECACHEOPTION_FORCE_DWORD = 0x7fffffff
};
```

### IWICImagingFactory::CreateBitmapClipper

Creates a new instance of an [IWICBitmapClipper](#iwicbitmapclipper) object.

```cpp
IWICBitmapClipper *pBitmapClipper;

HRESULT hr = pFactory->CreateBitmapClipper(&pBitmapClipper);
```

### IWICImagingFactory::CreateBitmapFlipRotator

Creates a new instance of an [IWICBitmapFlipRotator](#iwicbitmapfliprotator) object.

```cpp
IWICBitmapFlipRotator *pBitmapFlipRotator;

HRESULT hr = pFactory->CreateBitmapFlipRotator(&pBitmapFlipRotator);
```

### IWICImagingFactory::CreateBitmapFromHBITMAP

Creates an [IWICBitmap](#iwicbitmap) from a bitmap handle.

For a non-palletized bitmap, set `NULL` for the `hPalette` parameter.

```cpp
IWICBitmap *pBitmap;

HRESULT hr = pFactory->CreateBitmapFromHBITMAP(
  hBitmap,
  hPalette,
  WICBitmapUseAlpha,
  &pBitmap
);
```

### WICBitmapAlphaChannelOption

Specifies the desired alpha channel usage.

```cpp
typedef enum WICBitmapAlphaChannelOption {
  WICBitmapUseAlpha = 0,
  WICBitmapUsePremultipliedAlpha = 0x1,
  WICBitmapIgnoreAlpha = 0x2,
  WICBITMAPALPHACHANNELOPTIONS_FORCE_DWORD = 0x7fffffff
};
```

### IWICImagingFactory::CreateBitmapFromHICON

Creates an [IWICBitmap](#iwicbitmap) from an icon handle.

```cpp
IWICBitmap *pBitmap;

HRESULT hr = pFactory->CreateBitmapFromHICON(
  hIcon,
  &pBitmap
);
```

### IWICImagingFactory::CreateBitmapFromMemory

Creates an [IWICBitmap](#iwicbitmap) from a memory block.

The size of the [IWICBitmap](#iwicbitmap) to be created must be smaller than or equal to the size of the image in `pbBuffer`.

The stride of the destination bitmap will equal the stride of the source data, regardless of the width and height specified.

The `pixelFormat` parameter defines the pixel format for both the input data and the output bitmap.

```cpp
IWICBitmap *pBitmap;
BYTE *buffer; // should contain pixel data laid out according to pixel format and stride.

HRESULT hr = pFactory->CreateBitmapFromMemory(
  width,
  height,
  GUID_WICPixelFormat32bppPBGRA, // pixel format
  width * 4, // cbStride, bytes per scanline (row)
  width * height * 4, // cbBufferSize, buffer size in bytes
  buffer, // pBuffer
  &pBitmap
);
```

### IWICImagingFactory::CreateBitmapFromSource

Creates a [IWICBitmap](#iwicbitmap) from a [IWICBitmapSource](#iwicbitmapsource).

```cpp
IWICBitmapFrameDecode *pBitmapFrameDecode; // should contain a decoded frame (e.g. via pDecoder->GetFrame).
IWICBitmap *pBitmap;

HRESULT hr = pFactory->CreateBitmapFromSource(
  pBitmapFrameDecode, // pIBitmapSource
  WICBitmapCacheOnDemand, // cache option
  &pBitmap
);
```

### IWICImagingFactory::CreateBitmapFromSourceRect

Creates an [IWICBitmap](#iwicbitmap) from a specified rectangle of an [IWICBitmapSource](#iwicbitmapsource).

Providing a rectangle that is larger than the source will produce undefined results.

This method always creates a separate copy of the source image, similar to the cache option [WICBitmapCacheOnLoad](#wicbitmapcreatecacheoption).

```cpp
IWICBitmapFrameDecode *pBitmapFrameDecode; // should contain a decoded frame (e.g. via pDecoder->GetFrame).
IWICBitmap *pBitmap;

HRESULT hr = pFactory->CreateBitmapFromSource(
  pBitmapFrameDecode, // pIBitmapSource
  0, // x
  0, // y
  100, // width
  100, // width
  &pBitmap
);
```

### IWICImagingFactory::CreateBitmapScaler

Creates a new instance of an [IWICBitmapScaler](#iwicbitmapscaler).

```cpp
IWICBitmapScaler *pBitmapScaler;

HRESULT hr = pFactory->CreateBitmapScaler(&pBitmapScaler);
```

### IWICImagingFactory::CreateColorContext method

Creates a new instance of the [IWICColorContext](#iwiccolorcontext) class.

```cpp
IWICColorContext *pColorContext;

HRESULT hr = pFactory->CreateColorContext(&pColorContext);
```

### IWICImagingFactory::CreateColorTransformer

Creates a new instance of the [IWICColorTransform](#iwiccolortransform) class.

```cpp
IWICColorTransform  *pColorTransform;

HRESULT hr = pFactory->CreateColorTransformer(&pColorTransform);
```

### IWICImagingFactory::CreateComponentEnumerator

Creates an [IEnumUnknown](./com.md#ienumunknown) object of the specified component types.

```cpp
IEnumUnknown *pComponentEnumerator;

HRESULT hr = pFactory->CreateComponentEnumerator(
  WICDecoder | WICPixelFormat, // componentTypes
  WICComponentEnumerateRefresh | WICComponentEnumerateBuiltInOnly, // options
  &pComponentEnumerator
);
```

### WICComponentType

Specifies the type of WIC component.

```cpp
typedef enum WICComponentType {
  WICDecoder = 0x1,
  WICEncoder = 0x2,
  WICPixelFormatConverter = 0x4,
  WICMetadataReader = 0x8,
  WICMetadataWriter = 0x10,
  WICPixelFormat = 0x20,
  WICAllComponents = 0x3f,
  WICCOMPONENTTYPE_FORCE_DWORD = 0x7fffffff
};
```

### WICComponentEnumerateOptions

Specifies component enumeration options.

```cpp
typedef enum WICComponentEnumerateOptions {
  WICComponentEnumerateDefault = 0, // Enumerate any components that are not disabled.
  WICComponentEnumerateRefresh = 0x1, // Force a read of the registry before enumerating components.
  WICComponentEnumerateDisabled = 0x80000000, // Include disabled components in the enumeration.
  WICComponentEnumerateUnsigned = 0x40000000, // Include unsigned components in the enumeration. This option has no effect.
  WICComponentEnumerateBuiltInOnly = 0x20000000, // At the end of component enumeration, filter out any components that are not Windows provided.
  WICCOMPONENTENUMERATEOPTIONS_FORCE_DWORD = 0x7fffffff
};
```

### IWICImagingFactory::CreateComponentInfo

Creates a new instance of the [IWICComponentInfo](#iwiccomponentinfo) class for the given component class identifier (`CLSID`).

```cpp
IWICComponentInfo *pComponentInfo;

HRESULT hr = pFactory->CreateComponentInfo(
  CLSID_WICPngDecoder, // built-in PNG decoder class id, could be your custom WIC class id...etc.
  &pComponentInfo
);
```

### IWICImagingFactory::CreateDecoder

Creates a new instance of [IWICBitmapDecoder](#iwicbitmapdecoder).

Other values may be available for both `guidContainerFormat` and `pguidVendor` depending on the installed WIC-enabled encoders. The values listed for [container formats](#guid-container-format) and [vendors](#guid-vendor) are those that are natively supported by the operating system.

```cpp
IWICBitmapDecoder *pDecoder;

HRESULT hr = pFactory->CreateDecoder(
  GUID_ContainerFormatPng, // guidContainerFormat
  GUID_VendorMicrosoft, // pguidVendor
  &pDecoder
);
```

### IWICImagingFactory::CreateDecoderFromFileHandle

Creates a new instance of the [IWICBitmapDecoder](#iwicbitmapdecoder) based on the given file handle.

When a decoder is created using this method, the file handle must remain alive during the lifetime of the decoder.

```cpp
IWICBitmapDecoder *pDecoder;

HRESULT hr = pFactory->CreateDecoderFromFileHandle(
  hFile, // file handle
  GUID_VendorMicrosoftBuiltIn, // pguidVendor
  WICDecodeMetadataCacheOnLoad, // metadataOptions
  &pDecoder
);
```

### WICDecodeOptions

Specifies decode options.

```cpp
typedef enum WICDecodeOptions {
  WICDecodeMetadataCacheOnDemand = 0, // Cache metadata when needed (lazily).
  WICDecodeMetadataCacheOnLoad = 0x1, // Cache metadata when decoder is loaded.
  WICMETADATACACHEOPTION_FORCE_DWORD = 0x7fffffff
};
```

### IWICImagingFactory::CreateDecoderFromFilename

Creates a new instance of the [IWICBitmapDecoder](#iwicbitmapdecoder) class based on the given file.

```cpp
IWICBitmapDecoder *pDecoder;

HRESULT hr = CreateDecoderFromFilename(
  L"some_file.jpeg",
  NULL, // pguidVendor, NULL means no preference for vendor
  GENERIC_READ | GENERIC_WRITE, // dwDesiredAccess, can be read, write, or both
  WICDecodeMetadataCacheOnDemand, // metadataOptions
  &pDecoder
);
```

### IWICImagingFactory::CreateDecoderFromStream

Creates a new instance of the [IWICBitmapDecoder](#iwicbitmapdecoder) class based on the given `IStream`.

```cpp
IStream *pStream;
IWICBitmapDecoder *pDecoder;

HRESULT hr = pFactory->CreateDecoderFromStream(
  pStream,
  GUID_VendorMicrosoftBuiltIn, // pguidVendor
  WICDecodeMetadataCacheOnLoad, // metadataOptions
  &pDecoder
);
```

### IWICImagingFactory::CreateEncoder

Creates a new instance of the [IWICBitmapEncoder](#iwicbitmapencoder) class.

Other values may be available for both `guidContainerFormat` and `pguidVendor` depending on the installed WIC-enabled encoders. The values listed for [container formats](#guid-container-format) and [vendors](#guid-vendor) are those that are natively supported by the operating system.

```cpp
IWICBitmapEncoder *pEncoder;

HRESULT hr = pFactory->CreateEncoder(
  GUID_ContainerFormatGif, // guidContainerFormat
  NULL, // pguidVendor
  &pEncoder
);
```

### IWICImagingFactory::CreateFastMetadataEncoderFromDecoder

Creates a new instance of [IWICFastMetadataEncoder](#iwicfastmetadataencoder) encoder based on the given [IWICBitmapDecoder](#iwicbitmapdecoder).

The Windows provided codecs do not support fast metadata encoding at the decoder level, and only support fast metadata encoding at the frame level. To create a fast metadata encoder from a frame, use [CreateFastMetadataEncoderFromFrameDecode](#iwicimagingfactorycreatefastmetadataencoderfromframedecode).

```cpp
IWICFastMetadataEncoder *pFastEncoder;

HRESULT hr = pFactory->CreateFastMetadataEncoderFromDecoder(
  pDecoder,
  &pFastEncoder
);
```

### IWICImagingFactory::CreateFastMetadataEncoderFromFrameDecode

Creates a new instance of [IWICFastMetadataEncoder](#iwicfastmetadataencoder) based on the given image frame.

```cpp
IWICFastMetadataEncoder *pEncoder;

HRESULT hr = pFactory->CreateFastMetadataEncoderFromFrameDecode(
  pFrameDecoder,
  &pFastEncoder
);
```

### IWICImagingFactory::CreateFormatConverter

Creates a new instance of the [IWICFormatConverter](#iwicformatconverter) class.

```cpp
IWICFormatConverter *pFormatConverter;

HRESULT hr = pFactory->CreateFormatConverter(&pFormatConverter);
```

### IWICImagingFactory::CreatePalette

Creates a new instance of the [IWICPalette](#iwicpalette) class.

```cpp
IWICPalette *pPalette;

HRESULT hr = pFactory->CreatePalette(&pPalette);
```

### IWICImagingFactory::CreateQueryWriter

Creates a new instance of a [IWICMetadataQueryWriter](#iwicmetadataquerywriter).

```cpp
IWICMetadataQueryWriter *pQueryWriter;

HRESULT hr = pFactory->CreateQueryWriter(
  GUID_MetadataFormatXMP, // guidMetadataFormat
  GUID_VendorMicrosoft, // pguidVendor
  &pQueryWriter
);
```

### IWICImagingFactory::CreateQueryWriterFromReader

Creates a new instance of a [IWICMetadataQueryWriter](#iwicmetadataquerywriter) based on the given [IWICMetadataQueryReader](#iwicmetadataqueryreader). The [IWICMetadataQueryWriter](#iwicmetadataquerywriter) will be pre-populated with metadata from the [IWICMetadataQueryReader](#iwicmetadataqueryreader).

```cpp
IWICMetadataQueryReader *pQueryReader; // should already be instantiated
IWICMetadataQueryWriter *pQueryWriter;

HRESULT hr = pFactory->CreateQueryWriterFromReader(
  pQueryReader,
  GUID_VendorMicrosoft, // pguidVendor
  &pQueryWriter
);
```

### IWICImagingFactory::CreateStream

Creates a new instance of the [IWICStream](#iwicstream) class.

```cpp
IWICStream *pStream;

HRESULT hr = pFactory->CreateStream(&pStream);
```

## IWICBitmap

Defines methods that add the concept of writeability and static in-memory representations of bitmaps to [IWICBitmapSource](#iwicbitmapsource).

Inherits from [IWICBitmapSource](#iwicbitmapsource).

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
IWICPalette *pPalette;

HRESULT hr = pBitmap->SetPalette(pPalette);
```

### IWICBitmap::SetResolution

Changes the physical resolution (dots per inch) of the image.

```cpp
HRESULT hr = pBitmap->SetResolution(
  96.0, // dpiX
  96.0 // dpiY
);
```

## IWICBitmapLock

Exposes methods that support the [IWICBitmap::Lock](#iwicbitmaplock) method.

The bitmap lock is simply an abstraction for a rectangular memory window into the bitmap. For the simplest case, a system memory bitmap, this is simply a pointer to the top left corner of the rectangle and a stride value.

To release the exclusive lock set by Lock method and the associated [IWICBitmapLock](#iwicbitmaplock-1) object, call [IUnknown::Release](./com.md#iunknownrelease) on the `IWICBitmapLock` object.

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

## IWICPalette

Exposes methods for accessing and building a color table, primarily for indexed pixel formats.

### IWICPalette::GetColorCount

```cpp
UINT colorCount;

HRESULT hr = pPalette->GetColorCount(&colorCount);
```

### IWICPalette::GetColors

Fills out the supplied color array with the colors from the internal color table. The color array should be sized according to the return results from [GetColorCount](#iwicpalettegetcolorcount).

```cpp
WICColor *colors = new WICColor[256]; // will be filled with palette colors when GetColors returns

UINT actualColorCount; // will contain the actual palette color count when GetColors returns

HRESULT hr = pPalette->GetColors(
  256, // cCount, how many colors we want, this can also be the result of GetColorCount if you want the exact number of colors
  colors,
  &actualColorCount
);
```

### IWICPalette::GetType

Retrieves the [WICBitmapPaletteType](#wicbitmappalettetype) that describes the palette.

[WICBitmapPaletteTypeCustom](#wicbitmappalettetype) is used for palettes initialized from both `InitializeCustom` and `InitializeFromBitmap`. There is no distinction made between optimized and custom palettes.

```cpp
WICBitmapPaletteType paletteType;

HRESULT hr = pPalette->GetType(&paletteType);
```

### IWICPalette::HasAlpha

Indicates whether the palette contains an entry that is non-opaque (that is, an entry with an alpha that is less than 1).

Various image formats support alpha in different ways. PNG has full alpha support by supporting partially transparent palette entries. GIF stores colors as 24bpp, without alpha, but allows one palette entry to be specified as fully transparent. If a palette has multiple fully transparent entries (0x00RRGGBB), GIF will use the last one as its transparent index.

```cpp
BOOL hasAlpha;

HRESULT hr = pPalette->HasAlpha(&hasAlpha);
```

### IWICPalette::InitializeCustom

Initializes a palette to the custom color entries provided.

If a transparent color is required, provide it as part of the custom entries. To add a transparent value to the palette, its alpha value must be 0 (0x00RRGGBB).

The entry count is limited to 256.

```cpp
HRESULT hr = pPalette->InitializeCustom(
  colors, // array filled with colors to initialize palette from
  colorCount // number of colors in the array
);
```

### IWICPalette::InitializeFromBitmap

Initializes a palette using a computed optimized values based on the reference [bitmap](#iwicbitmapsource).

The resulting palette contains the specified number of colors which best represent the colors present in the bitmap. The algorithm operates on the opaque RGB color value of each pixel in the reference bitmap and hence ignores any alpha values. If a transparent color is required, set the `fAddTransparentColor` parameter to `TRUE` and one fewer optimized color will be computed, reducing the colorCount, and a fully transparent color entry will be added.

```cpp
HRESULT hr = pPalette->InitializeFromBitmap(
  pBitmapSource,
  128, // cCount, number of colors to initialize the palette with
  FALSE // fAddTransparentColor, whether to add a transparent color
);
```

### IWICPalette::InitializeFromPalette

Initialize the palette based on a given palette.

```cpp
HRESULT hr = pPalette->InitializeFromPalette(pOtherPalette);
```

### IWICPalette::InitializePredefined

Initializes the palette to one of the pre-defined palettes specified by [WICBitmapPaletteType](#wicbitmappalettetype) and optionally adds a transparent color.

If a transparent color is added to a palette, the palette is no longer predefined and is returned as [WICBitmapPaletteTypeCustom](#wicbitmappalettetype). For palettes with less than 256 entries, the transparent entry is added to the end of the palette (that is, a 16-color palette becomes a 17-color palette). For palettes with 256 colors, the transparent palette entry will replace the last entry in the pre-defined palette.

```cpp
HRESULT hr = pPalette->InitializePredefined(
  WICBitmapPaletteTypeMedianCut, // ePaletteType
  TRUE // fAddTransparentColor
);
```

### IWICPalette::IsBlackWhite

Retrieves a value that describes whether the palette is black and white.

A palette is considered to be black and white only if it contains exactly two entries, one full black (0xFF000000) and one full white (0xFFFFFFF).

```cpp
BOOL isBlackWhite;

HRESULT hr = pPalette->IsBlackWhite(&isBlackWhite);
```

### IWICPalette::IsGrayscale

Retrieves a value that describes whether a palette is grayscale.

A palette is considered grayscale only if, for every entry, the alpha value is 0xFF and the red, green and blue values match.

```cpp
BOOL isGrayscale;

HRESULT hr = pPalette->IsGrayscale(&isGrayscale);
```

### WICBitmapPaletteType

```cpp
typedef enum WICBitmapPaletteType {
  WICBitmapPaletteTypeCustom = 0, // An arbitrary custom palette provided by caller.
  WICBitmapPaletteTypeMedianCut = 0x1, // An optimal palette generated using a median-cut algorithm. Derived from the colors in an image.
  WICBitmapPaletteTypeFixedBW = 0x2, // A black and white palette.
  WICBitmapPaletteTypeFixedHalftone8 = 0x3, // A palette that has its 8-color on-off primaries and the 16 system colors added. With duplicates removed, 16 colors are available.
  WICBitmapPaletteTypeFixedHalftone27 = 0x4, // A palette that has 3 intensity levels of each primary: 27-color on-off primaries and the 16 system colors added. With duplicates removed, 35 colors are available.
  WICBitmapPaletteTypeFixedHalftone64 = 0x5, // A palette that has 4 intensity levels of each primary: 64-color on-off primaries and the 16 system colors added. With duplicates removed, 72 colors are available.
  WICBitmapPaletteTypeFixedHalftone125 = 0x6, // A palette that has 5 intensity levels of each primary: 125-color on-off primaries and the 16 system colors added. With duplicates removed, 133 colors are available.
  WICBitmapPaletteTypeFixedHalftone216 = 0x7, // A palette that has 6 intensity levels of each primary: 216-color on-off primaries and the 16 system colors added. With duplicates removed, 224 colors are available. This is the same as WICBitmapPaletteFixedHalftoneWeb.
  WICBitmapPaletteTypeFixedWebPalette, // A palette that has 6 intensity levels of each primary: 216-color on-off primaries and the 16 system colors added. With duplicates removed, 224 colors are available. This is the same as WICBitmapPaletteTypeFixedHalftone216.
  WICBitmapPaletteTypeFixedHalftone252 = 0x8, // A palette that has its 252-color on-off primaries and the 16 system colors added. With duplicates removed, 256 colors are available.
  WICBitmapPaletteTypeFixedHalftone256 = 0x9, // A palette that has its 256-color on-off primaries and the 16 system colors added. With duplicates removed, 256 colors are available.
  WICBitmapPaletteTypeFixedGray4 = 0xa, // A palette that has 4 shades of gray.
  WICBitmapPaletteTypeFixedGray16 = 0xb, // A palette that has 16 shades of gray.
  WICBitmapPaletteTypeFixedGray256 = 0xc, // A palette that has 256 shades of gray.
  WICBITMAPPALETTETYPE_FORCE_DWORD = 0x7fffffff
};
```

## IWICBitmapSource

Inherited by [IWICBitmap](#iwicbitmap), [IWICBitmapFrameDecode](#iwicbitmapframedecode), [IWICFormatConverter](#iwicformatconverter), [IWICBitmapScaler](#iwicbitmapscaler), [IWICBitmapClipper](#iwicbitmapclipper), [IWICBitmapFlipRotator](#iwicbitmapfliprotator), [IWICColorTransform](#iwiccolortransform), [IWICPalette](#iwicpalette).

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

## Supported metadata formats

| Name  | Spec. version       | Image format | Supports fast encoding | Notes                                                                                |
| ----- | ------------------- | ------------ | ---------------------- | ------------------------------------------------------------------------------------ |
| App0  | JFIF 1.02           | JPEG         | No                     |                                                                                      |
| App1  | JFIF 1.02           | JPEG, TIFF   | No                     |                                                                                      |
| App13 | Unknown             | JPEG, TIFF   | No                     |                                                                                      |
| IFD   | TIFF 6.0            | JPEG, TIFF   | Yes                    |                                                                                      |
| IRB   | Unknown             | JPEG, TIFF   | No                     |                                                                                      |
| Exif  | Exif 2.2            | JPEG, TIFF   | Yes                    |                                                                                      |
| XMP   | XMP 1.0 (Sept 2005) | JPEG, TIFF   | Yes                    |                                                                                      |
| GPS   | Exif 2.2            | JPEG, TIFF   | Yes                    |                                                                                      |
| IPTC  | IPTC 4.0            | JPEG, TIFF   | Yes                    | IPTC only supports FME if the blocks grow in size, as IPTC does not support padding. |
| tEXt  | PNG 1.2             | PNG          | No                     |                                                                                      |

## CLSID decoder and encoder

| Name                 |
| -------------------- |
| CLSID_WICAdngDecoder |
| CLSID_WICBmpDecoder  |
| CLSID_WICGifDecoder  |
| CLSID_WICHeifDecoder |
| CLSID_WICIcoDecoder  |
| CLSID_WICJpegDecoder |
| CLSID_WICPngDecoder  |
| CLSID_WICTiffDecoder |
| CLSID_WICWmpDecoder  |
| CLSID_WICBmpEncoder  |
| CLSID_WICGifEncoder  |
| CLSID_WICHeifEncoder |
| CLSID_WICJpegEncoder |
| CLSID_WICPngEncoder  |
| CLSID_WICTiffEncoder |
| CLSID_WICWmpEncoder  |

## CLSID component identifier

| Name                            |
| ------------------------------- |
| CLSID_WICImagingCategories      |
| CLSID_WICBitmapDecoders         |
| CLSID_WICBitmapEncoders         |
| CLSID_WICPixelFormats           |
| CLSID_WICFormatConverters       |
| CLSID_WICMetadataReader         |
| CLSID_WICMetadataWriter         |
| CLSID_WICHeifMetadataReader     |
| CLSID_WICHeifMetadataWriter     |
| CLSID_WICWebpAnimMetadataReader |
| CLSID_WICWebpAnmfMetadataReader |

## CLSID format converter

| Name                              |
| --------------------------------- |
| CLSID_WICDefaultFormatConverter   |
| CLSID_WICFormatConverterHighColor |
| CLSID_WICFormatConverterNChannel  |
| CLSID_WICFormatConverterWMPhoto   |

## GUID container format

| Name                     |
| ------------------------ |
| GUID_ContainerFormatAdng |
| GUID_ContainerFormatBmp  |
| GUID_ContainerFormatPng  |
| GUID_ContainerFormatIco  |
| GUID_ContainerFormatJpeg |
| GUID_ContainerFormatTiff |
| GUID_ContainerFormatGif  |
| GUID_ContainerFormatWmp  |
| GUID_ContainerFormatHeif |
| GUID_ContainerFormatWebp |

## GUID metadata handler

| Name                                  |
| ------------------------------------- |
| GUID_MetadataFormatUnknown            |
| GUID_MetadataFormatIfd                |
| GUID_MetadataFormatSubIfd             |
| GUID_MetadataFormatExif               |
| GUID_MetadataFormatGps                |
| GUID_MetadataFormatInterop            |
| GUID_MetadataFormatApp0               |
| GUID_MetadataFormatApp1               |
| GUID_MetadataFormatApp13              |
| GUID_MetadataFormatIPTC               |
| GUID_MetadataFormatIRB                |
| GUID_MetadataFormat8BIMIPTC           |
| GUID_MetadataFormat8BIMResolutionInfo |
| GUID_MetadataFormat8BIMIPTCDigest     |
| GUID_MetadataFormatXMP                |
| GUID_MetadataFormatThumbnail          |
| GUID_MetadataFormatChunktEXt          |
| GUID_MetadataFormatXMPStruct          |
| GUID_MetadataFormatXMPBag             |
| GUID_MetadataFormatXMPSeq             |
| GUID_MetadataFormatXMPAlt             |
| GUID_MetadataFormatLSD                |
| GUID_MetadataFormatIMD                |
| GUID_MetadataFormatGCE                |
| GUID_MetadataFormatAPE                |
| GUID_MetadataFormatJpegChrominance    |
| GUID_MetadataFormatJpegLuminance      |
| GUID_MetadataFormatJpegComment        |
| GUID_MetadataFormatGifComment         |
| GUID_MetadataFormatChunkgAMA          |
| GUID_MetadataFormatChunkbKGD          |
| GUID_MetadataFormatChunkiTXt          |
| GUID_MetadataFormatChunkcHRM          |
| GUID_MetadataFormatChunkhIST          |
| GUID_MetadataFormatChunkiCCP          |
| GUID_MetadataFormatChunksRGB          |
| GUID_MetadataFormatChunktIME          |
| GUID_MetadataFormatHeif               |
| GUID_MetadataFormatWebpANIM           |
| GUID_MetadataFormatWebpANMF           |

### GUID vendor

| Name                        | Description                                                   |
| --------------------------- | ------------------------------------------------------------- |
| GUID_VendorMicrosoft        | Uses both built-in Windows codecs and from 3rd party add-ons. |
| GUID_VendorMicrosoftBuiltIn | Uses only codecs that ship with Windows.                      |

## Types

### WICColor

Hexadecimal format: `0xAARRGGBB`.

A helper like this can be used to create `WICColor`:

```cpp
inline WICColor MakeWICColor(BYTE r, BYTE g, BYTE b, BYTE a)
{
  return (a << 24) | (r << 16) | (g << 8) | b;
}
```

```cpp
// wincodec.h

typedef UINT32 WICColor;
```
