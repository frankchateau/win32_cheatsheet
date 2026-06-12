# Window

## RegisterClass

Before creating a window, a custom window class must be registered. 

Registering a class is required to create a window, but also used for setting certain default behavior for a window.

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
- [style](#window-styles) - bitmask for setting additional window behavior(s).
- [lpfnWndProc](#window-procedure) - pointer to the window event handler callback.
- hInstance - handle to the executable module, usually the hInstance argument you get in WinMain (wWinMain)
- hIcon - handle to an icon resource used as the window icon.
- hCursor - handle to a cursor resource use as the window cursor icon.
- hbrBackground - handle to a brush used to paint the window client area background using GDI.
- lpszMenuName - string that specifies the resource name of a menu as it appears in your .rc file.
- lpszClassName - string that identifies this class. Could also be an ATOM returned by a previous call to RegisterClass.

`WNDCLASSEX` only members:
- hIconSm - a handle to an icon resource used as the small window icon.
If this is NULL, hIcon will be used, if it was defined. 

## Window styles

## Window procedure
