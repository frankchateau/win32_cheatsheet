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

The main parts of a window are: 
- titlebar with icon, caption, window buttons and system menu
- frame or border that can be used for resizing
- client area for rendering the window's content

Titlebar and frame are called non-client area.

<figure>
  <img src="./window_parts.png" alt="Anatomy of a window">
  <figcaption>Anatomy of a window</figcaption>
</figure>

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

- [style](#class-styles) - bitmask for setting additional window behavior(s). **Required**.
- [lpfnWndProc](#window-procedure) - pointer to the window event handler callback. **Required**.
- hInstance - handle to the executable module, usually the hInstance argument you get in WinMain (wWinMain). **Required**.
- [hIcon](#icons) - handle to an icon resource used as the window icon.
- [hCursor](#cursors) - handle to a cursor resource use as the window cursor icon.
- hbrBackground - handle to a brush used to paint the window client area background using GDI.
- lpszMenuName - string that specifies the resource name of a menu as it appears in your .rc file.
- lpszClassName - string that identifies this class. Could also be an ATOM returned by a previous call to RegisterClass.
- cbClsExtra - number of extra bytes to allocate for per-class custom app data. Rarely used today.
- cbWndExtra - number of extra bytes to allocate for per-window custom app data.

`WNDCLASSEX` only members:

- cbSize - the size of the structure in bytes. Usually `sizeof(WNDCLASSEX)`. **Required**.
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

## Ownership

// TODO

## Class styles

`CS_DBLCLKS` - Enables the windows to receive double click messages (e.g `WM_LBUTTONDBLCLK`).

`CS_NOCLOSE` - Disables the close button on the window.

`CS_DROPSHADOW` - Adds a drop-shadow effect on top-level windows (e.g. menus). Cannot be used with child windows.

`CS_HREDRAW` - Invalidates client area when width changes. Used with GDI.

`CS_VREDRAW` - Invalidates client area when height changes. Used with GDI.

`CS_CLASSDC` - All class windows share the same DC. Mainly used with GDI.

`CS_OWNDC` - Each class window gets its own DC. Mainly used with GDI and OpenGL.

`CS_PARENTDC` - Child windows use the parent's DC as an optimization. Mainly used with GDI.

`CS_GLOBALCLASS` - Makes the class globally available to all other modules in the process. Rarely used today.

`CS_SAVEBITS` - Saves a bitmap of the part of screen obscured by this window so when this window is removed,
the pixels that were underneath are restored from the bitmap instead doing a full repaint.
Legacy feature, made obsolete by DWM.

`CS_BYTEALIGNWINDOW` - Forces the window rectangle to start on a byte boundary in memory. Legacy feature.

`CS_BYTEALIGNCLIENT` - Forces the client area rectangle to start on a byte boundary in memory. Legacy feature.

## Window styles

### Visual styles

There are three base window styles that you can use as a starting point:

- `WS_OVERLAPPED` - top level windows
- `WS_POPUP` - special top level windows like dialogs, menus, splash screens...etc.
- `WS_CHILD` - child windows that are visually constrained / clipped in a parent window.

`WS_OVERLAPPED` produces a basic window with a frame and a minimal titlebar. No resizable border or window buttons.

`WS_POPUP` produces a frameless window with just the client area and no borders at all (no rounding).
Client area needs to be painted for the window to be visible. Easiest way to do it:

```cpp
wc.hbrBackground = (HBRUSH)(COLOR_WINDOW + 1); // default, white background
```

`WS_THICKFRAME` adds a resizable border and makes the titlebar taller. Its alias is `WS_SIZEBOX`.

`WS_SYSMENU` adds the window icon, system menu and window buttons. `WS_MINMIZEBOX` and `WS_MAXIMIZEBOX` are used
alongside it to add the minimize and maximize buttons.

`WS_OVERLAPPEDWINDOW` is the full, standard overlapped window.
Defined as `WS_OVERLAPPED | WS_CAPTION | WS_SYSMENU | WS_THICKFRAME | WS_MINIMIZEBOX | WS_MAXIMIZEBOX`.

`WS_HSCROLL` and `WS_VSCROLL` are used to add horizontal and vertical scrollbars.

`WS_BORDER` The window has a thin line border.

`WS_DLGFRAME` The window has a dialog-box like border. A window with this style cannot have a title bar.

<div style="display:flex;">
<figure>
  <img src="./ws_overlappedwindow.png" alt="Window with WS_OVERLAPPEDWINDOW style">
  <figcaption>WS_OVERLAPPEDWINDOW with the system menu open</figcaption>
</figure>

<figure>
  <img src="./ws_popup.png" alt="Window with WS_POPUP style">
  <figcaption>WS_POPUP only window</figcaption>
</figure>
</div>

### Behavior styles

`WS_VISIBLE` The window is visible initially.
You can call `ShowWindow` or `SetWindowPos` to show/hide the window.

`WS_DISABLED` The window is disabled initially. Visually, the frame looks the same, but all input to the window is blocked.
You can change this by calling `EnableWindow`.

`WS_MINIMIZE` The window is minimized initially. Its alias is `WS_ICONIC`.

`WS_MAXIMIZE` The window is maximized initially.

`WS_CLIPCHILDREN` Child windows are excluded from the window's painting operations. Prevents parent from
overwriting child windows and flicker. Mainly used with GDI.

`WS_CLIPSIBLINGS` Applied to a child window so that it doesn't overwrite the area of the sibling windows it overlaps with,
but only paint its own visible area. Mainly used with GDI.

`WS_GROUP` Used for grouping of child windows for keyboard navigation, focus and radio button grouping.
First created child window with `WS_GROUP` style marks the beginning of a group.

`WS_TABSTOP` Makes a child window focusable via pressing Tab. Used with GDI.

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

## Standard controls

// TODO

## Common controls

// TODO
