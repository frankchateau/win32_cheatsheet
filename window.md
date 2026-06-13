# Window

A window is a fundamental unit of the Windows GUI. It represents a rectangular region on the screen. 

Windows are not just top level application windows with a titlebar and borders, but can be any type of UI
control (e.g. buttons, lists...etc).

The type of a window is `HWND` which is an opaque handle to the window which is managed by Windows OS.
It is not meant to be modified directly, but rather passed to various win32 functions. (e.g. `MoveWindow(hwnd, 100, 100, 800, 600, TRUE);`)

Each window is created from a window class that defines default / shared behavior for all windows created from it. 
The window classes in question are not C++ classes, but rather configuration templates for windows specific to win32.

There are predefined built-in classes ([standard](#standard-controls) and [common](#common-controls) controls), 
but you can create custom ones using [RegisterClass](#registerclass).

For the main application window, you'd typically register your own custom class.

There are 3 general categories of windows in terms of [ownership](#ownership): top level, top level with parent, and child window.

Event handling is done via a [message loop](#message-loop) and the [window procedure](#window-procedure) callback.

![Window parts](./window.png "Window parts")

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
- [lpfnWndProc](#window-procedure) - pointer to the window event handler callback.
- hInstance - handle to the executable module, usually the hInstance argument you get in WinMain (wWinMain)
- [hIcon](#icons) - handle to an icon resource used as the window icon.
- [hCursor](#cursors) - handle to a cursor resource use as the window cursor icon.
- hbrBackground - handle to a brush used to paint the window client area background using GDI.
- lpszMenuName - string that specifies the resource name of a menu as it appears in your .rc file.
- lpszClassName - string that identifies this class. Could also be an ATOM returned by a previous call to RegisterClass.
- cbClsExtra - number of extra bytes to allocate for per-class custom app data. Rarely used today.
- cbWndExtra - number of extra bytes to allocate for per-window custom app data.

`WNDCLASSEX` only members:
- cbSize - the size of the structure in bytes. Usually `sizeof(WNDCLASSEX)`.
- [hIconSm](#icons) - a handle to an icon resource used as the small window icon.

Example:
```cpp
LRESULT CALLBACK windowProc(HWND hwnd, UINT uMsg, WPARAM wParam, LPARAM lParam); // implemented elsewhere

// the main entry function
int WINAPI wWinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, PWSTR pCmdLine, int nCmdShow) {
  WNDCLASSEX wc = {0};
  wc.cbSize = sizeof(WNDCLASSEX);
  wc.lpfnWndProc = windowProc; // callback defined elsewhere
  wc.hInstance = hInstance;
  wc.lpszClassName = L"MyWindowClass"; 

  ATOM classAtom = RegisterClassEx(&wc);

  if (classAtom == 0) {
    return EXIT_FAILURE;
  }

  // Proceed with creating windows...etc.
}

```

## CreateWindow

// TODO

## Standard controls

// TODO

## Common controls

// TODO

## Ownership

// TODO

## Styles

`WS_OVERLAPPEDWINDOW`

This flag alone gives you the "standard" window with a titlebar, minimize, maximize, close buttons,
resizable borders and paints the client area white by default.



`WS_POPUP` 

Used for frameless windows. Cannot be used together with `WS_CHILD`.



## Message loop

// TODO

## Window procedure

// TODO

## Icons

// TODO

## Cursors

// TODO

## Custom data

// TODO
