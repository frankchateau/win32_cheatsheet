# Graphics device interface (GDI)

## RGB macro

Takes R, G, B in the range 0-255 ([`BYTE`](./types.md#byte)) and returns a [`COLORREF`](./types.md#colorref). 0,0,0 is black; 255,255,255 is white.

```c
COLORREF color = RGB(r, g, b);
```

[`GetRValue`](#getrvalue), [`GetGValue`](#getgvalue), [`GetBValue`](#getbvalue) can be used to extract individual components.

## GetRValue

Takes RGB as a [`DWORD`](./types.md#dword) and returns the red intensity as [`BYTE`](./types.md#byte).

```c
BYTE red = GetRValue(rgb);
```

## GetGValue

Takes RGB as a [`DWORD`](./types.md#dword) and returns the green intensity as [`BYTE`](./types.md#byte).

```c
BYTE green = GetGValue(rgb);
```

## GetBValue

Takes RGB as a [`DWORD`](./types.md#dword) and returns the blue intensity as [`BYTE`](./types.md#byte).

```c
BYTE blue = GetBValue(rgb);
```
