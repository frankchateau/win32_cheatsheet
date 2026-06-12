# Window

A window is a fundamental unit of the Windows GUI. It represents a rectangular region on the screen. 

The window type is `HWND` which is an opaque handle to the window which is managed by Windows OS.
It is not meant to be modified directly, but rather passed to various Windows functions. (e.g. `MoveWindow(hwnd, 100, 100, 800, 600, TRUE);`)

Each window is created from a class that defines default / shared behavior for all windows created from it. 
There are predefined built-in classes ([standard](#standard-controls) and [common](#common-controls) controls), 
but you can create custom ones using [RegisterClass](#registerclass).

For an application window, you'd typically register your own custom class.

## RegisterClass

Before creating a custom window, a custom window class must be registered. 

Registering a class is required to create a custom window, but also used for setting certain default behavior for windows created from the class.

To do that, we call a `RegisterClass` function and pass it a pointer to a `WNDCLASS` struct.

`RegisterClass` functions:
- RegisterClassA 
- RegisterClassW
- RegisterClassExA
- RegisterClassExW - modern, preferred

`WNDCLASS` structs:
- WNDCLASSA
- WNDCLASSW
- WNDCLASSEXA
- WNDCLASSEXW - modern, preferred

`WNDCLASS` members:
- [style](#styles) - bitmask for setting additional window behavior(s).
- [lpfnWndProc](#procedure) - pointer to the window event handler callback.
- hInstance - handle to the executable module, usually the hInstance argument you get in WinMain (wWinMain)
- [hIcon](#icons) - handle to an icon resource used as the window icon.
- [hCursor](#cursors) - handle to a cursor resource use as the window cursor icon.
- hbrBackground - handle to a brush used to paint the window client area background using GDI.
- lpszMenuName - string that specifies the resource name of a menu as it appears in your .rc file.
- lpszClassName - string that identifies this class. Could also be an ATOM returned by a previous call to RegisterClass.
- cbClsExtra - number of extra bytes to allocate for per-class custom app data. Rarely used today.
- cbWndExtra - number of extra bytes to allocate for per-window custom app data.

`WNDCLASSEX` only members:
- cbSize - the size of the structure in bytes.
- [hIconSm](#icons) - a handle to an icon resource used as the small window icon.
If this is NULL, hIcon will be used, if it was defined.

## Standard controls

// TODO

## Common controls

// TODO

## Styles

// TODO

## Procedure

// TODO

## Icons

// TODO

## Cursors

// TODO

## Custom data

// TODO
