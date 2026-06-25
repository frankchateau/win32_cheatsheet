# Window

A window is a fundamental unit of the Windows GUI. It represents a rectangular region on the screen.

Windows are not just top-level application windows with a title bar and borders, but can be any type of UI
control (e.g. buttons, lists...etc).

The type of a window is [`HWND`](./types.md#hwnd) which is an opaque handle to the window which is managed by win32.
It is not meant to be modified directly, but rather passed to various win32 functions like:

```cpp
MoveWindow(hwnd, 100, 100, 800, 600, TRUE);
```

Each window is created from a window class that defines default / shared behavior for all windows created from it.
The window classes in question are not C++ classes, but rather configuration templates for windows specific to win32.

There are predefined built-in classes in the [control library](./controls.md),
but you can create custom ones using [RegisterClass](#registerclass).

For the main application window, you'd typically register your own custom class.

There are 3 general categories of windows in terms of [ownership](#ownership): top-level, owned top-level, and child window.

Window event handling is done via a [message loop](#message-loop) and the [window procedure](#window-procedure) callback.

The main parts of a window are:

- title bar with icon, caption, window buttons and system menu
- frame (border) that can be used for resizing
- client area for rendering the window's content

Title bar and frame are called non-client area (NC).

| ![Window parts](./images/window_parts.png) |
| :----------------------------------------: |
|                Window parts                |

## Ownership

There are 3 types of windows in terms of ownership:

- [Top-level](#top-level-window)
- [Owned top-level](#owned-top-level-window)
- [Child](#child-window)

### Top-level window

| Coordinates                             | Clipped | Examples                   |
| --------------------------------------- | ------- | -------------------------- |
| Relative to upper-left corner of screen | /       | Main application window(s) |

### Owned top-level window

| Coordinates                             | Clipped | Parent dependency                                                                                                                                  | Examples                                                                |
| --------------------------------------- | ------- | -------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Relative to upper-left corner of screen | No      | Always in front of parent.<br>Minimized and restored with parent except in special cases (e.g. modal dialogs).<br>Destroyed when parent destroyed. | [dialogs](./dialogs.md), [menus](./menus.md), [tooltips](./tooltips.md) |

### Child window

| Coordinates                                           | Clipped | Examples                                |
| ----------------------------------------------------- | ------- | --------------------------------------- |
| Relative to upper-left corner of parent's client area | Yes     | Many standard [controls](./controls.md) |

| ![Window ownership](./images/window_ownership.png) |
| :------------------------------------------------: |
|                  Window ownership                  |

## RegisterClass

Registers a custom window class and defines default behavior for use in subsequent [CreateWindow](#createwindow) calls.

This function has [extended](./extended.md) and [string](./unicode_ansi.md) variants.

| Name     | Type        | Description                      |
| -------- | ----------- | -------------------------------- |
| wndClass | `WNDCLASS*` | Pointer to [WNDCLASS](#wndclass) |

| Success                              | Error                                                 |
| ------------------------------------ | ----------------------------------------------------- |
| [ATOM](./types.md#atom) of new class | 0, [GetLastError](./errors.md#getlasterror) available |

Example:

```cpp
LRESULT CALLBACK windowProc(HWND hwnd, UINT uMsg, WPARAM wParam, LPARAM lParam); // implemented elsewhere

// the main entry function
int WINAPI wWinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, PWSTR pCmdLine, int nCmdShow) {
  WNDCLASSEX wc = {0};
  wc.cbSize = sizeof(WNDCLASSEX);
  ws.style = CS_HREDRAW | CS_VREDRAW;
  wc.lpfnWndProc = windowProc; // callback declared above
  wc.hInstance = hInstance;
  wc.lpszClassName = L"MyWindowClass"; // L to treat it as WCHAR (Unicode)

  ATOM classAtom = RegisterClassEx(&wc);

  if (classAtom == 0) {
    return EXIT_FAILURE;
  }

  // Proceed with creating window...etc.
}

```

## WNDCLASS

This struct is used with [RegisterClass](#registerclass).

This struct has [extended](./extended.md) and [string](./unicode_ansi.md) variants.

| Name          | Type                              | Description                                                                                                                                           |
| ------------- | --------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| cbSize        | [UINT](./types.md#uint)           | The size of the structure in bytes. Set it to `sizeof(WNDCLASSEX)`. Used so that win32 knows which version of `WNDCLASS` you're using. Extended only. |
| style         | [UINT](./types.md#uint)           | Bitmask for setting [class styles](#class-styles).                                                                                                    |
| lpfnWndProc   | [WNDPROC](./types.md#wndproc)     | Pointer to the [window procedure](#window-procedure) callback.                                                                                        |
| cbClsExtra    | `int`                             | Number of extra bytes to allocate for per-class [custom app data](#custom-data). Rarely used today.                                                   |
| cbWndExtra    | `int`                             | Number of extra bytes to allocate for per-window [custom app data](#custom-data).                                                                     |
| hInstance     | [HINSTANCE](./types.md#hinstance) | Handle to the executable module, like the hInstance parameter of `WinMain` or acquired with `GetModuleHandle`.                                        |
| hIcon         | [HICON](./types.md#hicon)         | Handle to an icon resource used as the [window icon](#icons).                                                                                         |
| hCursor       | [HCURSOR](./types.md#hcursor)     | Handle to a cursor resource used as the [window cursor](#cursor) icon by default.                                                                     |
| hbrBackground | [HBRUSH](./types.md#hbrush)       | Handle to a brush used to paint the window client area background using [GDI](./gdi.md).                                                              |
| lpszMenuName  | [LPCWSTR](./types.md#lpcwstr)     | String that specifies the resource name of a menu as it appears in your .rc file.                                                                     |
| lpszClassName | [LPCWSTR](./types.md#lpcwstr)     | String that identifies this class. The class name is scoped to the module i.e two different modules can register a class with the same name.          |
| hIconSm       | [HICON](./types.md#hicon)         | Handle to an icon resource used as the small [window icon](#icons). Extended only.                                                                    |

## Class styles

These styles are set on [WNDCLASS](#wndclass).

| Name               | Description                                                                                                                                                                                                                                     |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CS_DBLCLKS         | Enables the windows to receive double click messages (e.g `WM_LBUTTONDBLCLK`).                                                                                                                                                                  |
| CS_NOCLOSE         | Disables the close button on the window.                                                                                                                                                                                                        |
| CS_DROPSHADOW      | Adds a drop-shadow effect on top-level windows (e.g. menus). Cannot be used with child windows.                                                                                                                                                 |
| CS_HREDRAW         | Invalidates client area when width changes. Used with [GDI](./gdi.md).                                                                                                                                                                          |
| CS_VREDRAW         | Invalidates client area when height changes. Used with [GDI](./gdi.md).                                                                                                                                                                         |
| CS_CLASSDC         | All windows within class share the same DC. Mainly used with [GDI](./gdi.md).                                                                                                                                                                   |
| CS_OWNDC           | Each window within class gets its own DC. Mainly used with [GDI](./gdi.md) and OpenGL.                                                                                                                                                          |
| CS_PARENTDC        | Child windows use the parent's DC as an optimization. Mainly used with [GDI](./gdi.md).                                                                                                                                                         |
| CS_GLOBALCLASS     | Makes the class globally available to all other modules in the process. Rarely used today.                                                                                                                                                      |
| CS_SAVEBITS        | Saves a bitmap of the part of screen obscured by this window so when this window is removed,<br>the pixels that were underneath are restored from the bitmap instead doing a full repaint.<br>Legacy feature, made obsolete by [DWM](./dwm.md). |
| CS_BYTEALIGNWINDOW | Forces the window rectangle to start on a byte boundary in memory. Legacy feature.                                                                                                                                                              |
| CS_BYTEALIGNCLIENT | Forces the client area rectangle to start on a byte boundary in memory. Legacy feature.                                                                                                                                                         |

## CreateWindow

This function has [extended](./extended.md) and [string](./unicode_ansi.md) variants.

| Name         | Type                              | Description                                                                                                                                                                                                                                               |
| ------------ | --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| dwExStyle    | [DWORD](./types.md#dword)         | Bitmask for setting [extended window styles](#extended-window-styles).                                                                                                                                                                                    |
| lpClassName  | [LPCWSTR](./types.md#lpcwstr)     | String that identifies a registered class or an [ATOM](./types.md#atom) returned by a previous call to [RegisterClass](#registerclass) wrapped with `MAKEINTATOM`.                                                                                        |
| lpWindowName | [LPCWSTR](./types.md#lpcwstr)     | A general title string that appears as the caption for windows with titlebars, button text for [standard Button control](./controls.md#button)...etc.                                                                                                     |
| dwStyle      | [DWORD](./types.md#dword)         | Bitmask for setting [window styles](#window-styles).                                                                                                                                                                                                      |
| X            | `int`                             | Horizontal [upper-left corner](#ownership) position of the window.                                                                                                                                                                                        |
| Y            | `int`                             | Vertical [upper-left corner](#ownership) position of the window.                                                                                                                                                                                          |
| nWidth       | `int`                             | Width of the window in device units.                                                                                                                                                                                                                      |
| nHeight      | `int`                             | Height of the window in device units.                                                                                                                                                                                                                     |
| hWndParent   | [HWND](./types.md#hwnd)           | [Handle](./types.md#hwnd) to the parent or owner window. Required for [child windows](#child-window), optional for [top-level windows](#top-level-window).                                                                                                |
| hMenu        | [HMENU](./types.md#hmenu)         | For [top-level windows](#top-level-window) this should be a handle to a menu or null, for [child windows](#child-window) the identifier used to distinguish child controls.                                                                               |
| hInstance    | [HINSTANCE](./types.md#hinstance) | [Handle](./types.md#hinstance) to executable module. Might not always be required here, but it's safer and there's no harm in passing it - even for [standard controls](./controls.md).                                                                   |
| lpParam      | [LPVOID](./types.md#lpvoid)       | Arbitrary pointer that will be available in [CREATESTRUCT](#createstruct) as `lParam` of [WM_CREATE](#wm_create) and [WM_NCCREATE](#wm_nccreate) messages. You can use it to pass custom data to [window procedure](#window-procedure). Required for MDI. |

| Success                                     | Error                                                      |
| ------------------------------------------- | ---------------------------------------------------------- |
| [HWND](./types.md#hwnd) New window's handle | `NULL`, [GetLastError](./errors.md#getlasterror) available |

Example creating a top-level window:

```cpp
const wchar_t className[] = L"MyWindowClass";

// WNDCLASS struct creation

ATOM classAtom = RegisterClass(&wc);

HWND myWindow = CreateWindowEx(
  WS_EX_ACCEPTFILES, // dwExStyle
  className, // or MAKEINTATOM(classAtom)
  L"Hello World", // lpWindowName
  WS_OVERLAPPEDWINDOW | WS_VISIBLE, // standard window, visible initially (no need to call ShowWindow)
  100, // x
  100, // y
  1024, // width
  768, // height
  nullptr, // parent
  nullptr, // menu
  hInstance, // maybe we could pass null since we already registered the class with it (?)
  nullptr, // lParam
)

if (!myWindow) {
  return EXIT_FAILURE;
}

// Proceed with the message loop, rendering...etc.
```

Example creating a child window ([standard Button control](./controls.md#button)):

```cpp
// we don't need to register the class for a standard control

HWND myButton = CreateWindowEx(
  0, // no extended styles
  L"BUTTON", // predefined standard class
  L"Click Me", // button text
  WS_CHILD | WS_VISIBLE | WS_TABSTOP | BS_PUSHBUTTON, // child window, visible initially, tabbable, push button (classic button)
  10, // x
  10, // y
  100, // width
  30, // height
  myWindow, // parent window
  (HMENU)1, // control ID
  hInstance, // probably could pass null for a standard control (?)
  nullptr, // lpParam not used for standard controls
);
```

## Window styles

These styles are used with [CreateWindow](#createwindow).

There are three base window styles that you can use as a starting point:

- `WS_OVERLAPPED` - top-level windows
- `WS_POPUP` - special top-level windows like [dialogs](./dialogs.md), [menus](./menus.md), splash screens...etc.
- `WS_CHILD` - child windows.

| Name                       | Description                                                                                                                                                                         |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| WS_OVERLAPPED              | Produces a basic window with a frame and a minimal title bar. No resizable border or window buttons.                                                                                |
| WS_POPUP                   | Produces a frameless window with just the client area and no rounded corners.<br>Client area needs to be painted by the application for the window to be visible.                   |
| WS_CHILD                   | Produces a [child window](#child-window).<br>Window with this style must have a parent window passed to [CreateWindow](#createwindow).<br>Cannot be used with the `WS_POPUP` style. |
| WS_THICKFRAME (WS_SIZEBOX) | Adds a resizable border and makes the title bar taller (if present).<br>Also adds a rounded border when used with `WS_POPUP`.                                                       |
| WS_SYSMENU                 | Adds the window icon, system menu and window buttons.<br>`WS_MINIMIZEBOX` and `WS_MAXIMIZEBOX` are used alongside it to add the minimize and maximize buttons.                      |
| WS_MINIMIZEBOX             | Adds the minimize window button, requires the `WS_SYSMENU` style                                                                                                                    |
| WS_MAXIMIZEBOX             | Adds the maximize window button, requires the `WS_SYSMENU` style                                                                                                                    |
| WS_OVERLAPPEDWINDOW        | The full, standard overlapped window.<br>Defined as `WS_OVERLAPPED \| WS_CAPTION \| WS_SYSMENU \| WS_THICKFRAME \| WS_MINIMIZEBOX \| WS_MAXIMIZEBOX`.                               |
| WS_HSCROLL                 | Adds the horizontal scrollbar to the client area.                                                                                                                                   |
| WS_VSCROLL                 | Adds the vertical scrollbar to the client area.                                                                                                                                     |
| WS_BORDER                  | The window has a thin line border.                                                                                                                                                  |
| WS_DLGFRAME                | The window has a dialog-box like border. A window with this style cannot have a title bar.                                                                                          |
| WS_VISIBLE                 | The window is visible initially. Can be modified with [ShowWindow](#showwindow) and [SetWindowPos](#setwindowpos).                                                                  |
| WS_DISABLED                | The window is disabled initially. Visually, the frame looks the same, but all input to the window is blocked.<br>Can be modified with [EnableWindow](#enablewindow).                |
| WS_MINIMIZE (WS_ICONIC)    | The window is minimized initially.                                                                                                                                                  |
| WS_MAXIMIZE                | The window is maximized initially.                                                                                                                                                  |
| WS_CLIPCHILDREN            | Child windows are excluded from the window's painting operations.<br>Prevents parent from overwriting child windows and flicker.<br>Mainly used with [GDI](./gdi.md).               |
| WS_CLIPSIBLINGS            | Applied to a child window so that it doesn't overwrite the area of the sibling windows it overlaps with, but only paint its own visible area.<br>Mainly used with [GDI](./gdi.md).  |
| WS_GROUP                   | Makes the child window the beginning of a group of controls. Used for keyboard navigation, focus, and radio button groups. Used with standard [controls](./controls.md).            |
| WS_TABSTOP                 | Makes a child window focusable via pressing Tab. Used with standard [controls](./controls.md).                                                                                      |

| ![Window with WS_OVERLAPPEDWINDOW style](./images/ws_overlappedwindow.png) | ![Window with WS_POPUP style](./images/ws_popup.png) |
| :------------------------------------------------------------------------: | :--------------------------------------------------: |
|               WS_OVERLAPPEDWINDOW with the system menu open                |                 WS_POPUP only window                 |

## Extended window styles

These styles are used with [CreateWindow](#createwindow).

| Name                      | Category                            | Description                                                                                                                                                                                                                                                                                                                         |
| ------------------------- | ----------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| WS_EX_APPWINDOW           | taskbar                             | Adds a taskbar icon for owned top-level windows (e.g. dialogs) which would normally be grouped under their parent window.                                                                                                                                                                                                           |
| WS_EX_TOOLWINDOW          | taskbar, title bar                  | A tool window doesn't have a taskbar icon, has a smaller title bar with only a close button and no app icon.                                                                                                                                                                                                                        |
| WS_EX_PALETTEWINDOW       | taskbar, title bar, z order, border | Defined as `WS_EX_WINDOWEDGE \| WS_EX_TOOLWINDOW \| WS_EX_TOPMOST`.                                                                                                                                                                                                                                                                 |
| WS_EX_NOREDIRECTIONBITMAP | graphics                            | Tells [DWM](./dwm.md) to not create a [GDI](./gdi.md) backing surface for the window meaning the client area will not be rendered by default.<br>To present the client area, you need to use the DirectComposition API or similar.                                                                                                  |
| WS_EX_LAYERED             | graphics, transparency              | Improves performance, reduces flickering and enables per-pixel alpha blending and transparency effects for the window.<br>Can be used to create non-rectangular shaped windows (e.g. Winamp skins).<br>Cannot be used with `CS_CLASSDC` or `CS_OWNDC` [class styles](#class-styles).<br>More on [layered windows](#layered-window). |
| WS_EX_COMPOSITED          | graphics                            | The window uses double buffering for painting to reduce flicker. Legacy, made obsolete by [DWM](./dwm.md).                                                                                                                                                                                                                          |
| WS_EX_TRANSPARENT         | graphics, transparency              | The child window is not painted until siblings underneath it have been painted.<br>This allows for the top window to be blended with windows underneath.<br>This only affects the paint order (which is top-down by default), not the actual transparent rendering.                                                                 |
| WS_EX_TOPMOST             | z order, activation                 | The window is placed above all non-topmost windows, even when it's deactivated.<br>Can be modified with [SetWindowPos](#setwindowpos).                                                                                                                                                                                              |
| WS_EX_NOACTIVATE          | activation                          | The window is not activated (brought to the foreground) when clicked on.<br>It doesn't appear on the taskbar by default, but adding `WS_EX_APPWINDOW` forces it.<br>Can be modified with [SetActiveWindow](#setactivewindow) and [SetForegroundWindow](#setforegroundwindow).                                                       |
| WS_EX_WINDOWEDGE          | border                              | The window has a border with a raised edge. Doesn't seem to have an effect on post-XP Windows.                                                                                                                                                                                                                                      |
| WS_EX_CLIENTEDGE          | border                              | The window has a border with a sunken edge. Has an effect when used with `WS_POPUP` and more pronounced when `WS_DLGFRAME` is added.                                                                                                                                                                                                |
| WS_EX_STATICEDGE          | border                              | The window has a 3D border style. Doesn't seem to have an effect on post-XP Windows.                                                                                                                                                                                                                                                |
| WS_EX_OVERLAPPEDWINDOW    | border                              | Defined as `WS_EX_WINDOWEDGE \| WS_EX_CLIENTEDGE`.                                                                                                                                                                                                                                                                                  |
| WS_EX_DLGMODALFRAME       | border                              | The window has a double border and can have a title bar. Doesn't seem to have an effect on post-XP Windows.                                                                                                                                                                                                                         |
| WS_EX_LEFTSCROLLBAR       | layout                              | The vertical scrollbar (if present) is to the left of the client area.                                                                                                                                                                                                                                                              |
| WS_EX_RIGHTSCROLLBAR      | layout                              | The vertical scrollbar (if present) is to the right of the client area. This is the default for `WS_VSCROLL`.                                                                                                                                                                                                                       |
| WS_EX_LAYOUTRTL           | layout                              | Flips the horizontal origin of the window client area. Increasing horizontal values advance to the left.                                                                                                                                                                                                                            |
| WS_EX_NOINHERITLAYOUT     | layout                              | The layout is not inherited from parent to child windows.                                                                                                                                                                                                                                                                           |
| WS_EX_LEFT                | layout                              | The window has generic left-aligned properties (?). This is the default.                                                                                                                                                                                                                                                            |
| WS_EX_RIGHT               | layout                              | The window has generic right-aligned properties (?).                                                                                                                                                                                                                                                                                |
| WS_EX_LTRREADING          | reading order                       | Text is drawn left-to-right. This is the default.                                                                                                                                                                                                                                                                                   |
| WS_EX_RTLREADING          | reading order                       | Text is drawn right-to-left.                                                                                                                                                                                                                                                                                                        |
| WS_EX_ACCEPTFILES         | shell                               | Allows the window to accept files by drag and drop using the [shell api](./shell.md#shell) and `WM_DROPFILES`.<br>Can be modified with [DragAcceptFiles](./shell.md#dragacceptfiles). Mostly legacy and replaced with OLE.                                                                                                          |
| WS_EX_CONTROLPARENT       | accessibility, child windows        | The children of this window become included in focus navigation via Tab and Arrow keys instead of treating the whole window as one focusable item.                                                                                                                                                                                  |
| WS_EX_NOPARENTNOTIFY      | child windows                       | The parent window is not notified with `WM_PARENTNOTIFY` notifications when child windows are created / destroyed.                                                                                                                                                                                                                  |
| WS_EX_MDICHILD            | mdi                                 | The window is an MDI child.                                                                                                                                                                                                                                                                                                         |
| WS_EX_CONTEXTHELP         | help                                | The window title bar shows a help icon which when clicked, changes the cursor to a help cursor that can be used to click around the window to open help popups via `WM_HELP` and `WinHelp`.<br>Doesn't seem to have an effect post Windows 95.                                                                                      |

| ![Window with WS_OVERLAPPEDWINDOW and WS_EX_TOOLWINDOW](./images/ws_ex_toolwindow.png) | ![Window with WS_OVERLAPPEDWINDOW and WS_EX_NOREDIRECTIONBITMAP](./images/ws_overlappedwindow_exnoredirectionbitmap.png) |
| :------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------: |
|                        WS_OVERLAPPEDWINDOW and WS_EX_TOOLWINDOW                        |                                    WS_OVERLAPPEDWINDOW and WS_EX_NOREDIRECTIONBITMAP                                     |

| ![Window with WS_POPUP, WS_DLGFRAME, and WS_EX_CLIENTEDGE](./images/ws_popup_dlgframe_exclientedge.png) |
| :-----------------------------------------------------------------------------------------------------: |
|                              WS_POPUP \| WS_DLGFRAME and WS_EX_CLIENTEDGE                               |

## Message loop

// TODO

## Window procedure

Window procedure is a callback function of type [WNDPROC](./types.md#wndproc) that processes messages sent to a window.

It is set for a window class on [WNDCLASS](#wndclass) which is passed to [RegisterClass](#registerclass).

| Name   | Type                        | Description                                                                                                      |
| ------ | --------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| hwnd   | [HWND](./types.md#hwnd)     | The window handle.                                                                                               |
| uMsg   | [UINT](./types.md#uint)     | The [message](#window-messages) code.                                                                            |
| wParam | [WPARAM](./types.md#wparam) | Additional [message](#window-messages) information. The contents depend on the [message](#window-messages) code. |
| lParam | [LPARAM](./types.md#lparam) | Additional [message](#window-messages) information. The contents depend on the [message](#window-messages) code. |

It should return an [LRESULT](./types.md#lresult) code that depends on the message that was handled.

If a message (or condition) is not handled by the window procedure, [default window procedure](#defwindowproc) should be called and
its result returned from the window procedure.

| wParam                                                                                                | lParam                                                                                                  |
| ----------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| [UINT_PTR](./types.md#uint_ptr) (unsigned)                                                            | [LONG_PTR](./types.md#long_ptr) (signed)                                                                |
| Typically small values, flags, or identifiers <br>like control IDs, key codes, or notification codes  | Typically used for larger values or pointers <br>like passing a struct pointer, coordinates, or handles |
| Means "Word parameter" because on 16‑bit Windows <br>it was typed as a 16-bit [WORD](./types.md#word) | Means "Long parameter" because on 16-bit Windows <br>it was typed as a 32-bit [LONG](./types.md#long).  |

Minimal example:

```c
LRESULT CALLBACK windowProc(HWND hwnd, UINT uMsg, WPARAM wParam, LPARAM lParam) {
  switch(uMsg) {
    case WM_CREATE:
    {
      BOOL initSuccess = initialize(); // initialize is some example function

      if(!initSuccess) {
        return -1; // signal to destroy window
      }
      else {
        return 0; // proceed with creation
      }
    }
    case WM_DESTROY:
    {
      PostQuitMessage(EXIT_SUCCESS);
      return 0; // we handled it
    }
  }

  return DefWindowProc(hwnd, uMsg, wParam, lParam); // default handler for messages we don't handle
}
```

## Window messages

| Name                    | Range                         | Notes                                                                                                                                            |
| ----------------------- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| System reserved         | `0x0000` - `0x03FF`           | Names start with `WM_` ([WM_CREATE](#wm_create), [WM_PAINT](#wm_paint)...etc.).                                                                  |
| Custom per window class | `0x0400` (WM_USER) - `0x7FFF` | Use in your window classes,<br>not for standard [controls](./controls.md) since they use the range as well<br>(BM_GETCHECK, EM_GETLINE...etc. ). |
| Custom per app          | `0x8000` (WM_APP) - `0xBFFF`  | Use across your app.<br>Standard [controls](./controls.md) do not use this range.<br>Min. [Windows NT 4.0](./winver.md).                         |
| Custom system-wide      | `0xC000` - `0xFFFF`           | Use for messages between applications.<br>Registered via [RegisterWindowMessage](#RegisterWindowMesesage).                                       |

### WM_ACTIVATEAPP

|                  |                                                                                                                                                                           |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Window of non-active application is about to be activated<br>and active window is being deactivated                                                                       |
| **wParam**       | [BOOL](./types.md#bool)<br>`TRUE` if window is being activated<br>`FALSE` if window is being deactivated                                                                  |
| **lParam**       | [DWORD](./types.md#dword) thread id<br>if window is activated, this is the deactivated window's thread<br>if window is deactivated, this is the activated window's thread |
| **Return value** | 0 if the app processes this message                                                                                                                                       |
| **Default**      | /                                                                                                                                                                         |
| **Notes**        | /                                                                                                                                                                         |

### WM_CANCELMODE

|                  |                                                                                                                                                                                             |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Modal input loops or continuous input tracking (e.g. mouse capture, drag-drop...etc.)<br> need to be cancelled because something interrupted it (e.g. dialog shown, window disabled...etc.) |
| **wParam**       | /                                                                                                                                                                                           |
| **lParam**       | /                                                                                                                                                                                           |
| **Return value** | 0 if the app processes this message                                                                                                                                                         |
| **Default**      | /                                                                                                                                                                                           |
| **Notes**        | /                                                                                                                                                                                           |

### WM_CHILDACTIVATE

|                  |                                                             |
| ---------------- | ----------------------------------------------------------- |
| **When**         | [Child window](#child-window) is activated, moved, or sized |
| **wParam**       | /                                                           |
| **lParam**       | /                                                           |
| **Return value** | 0 if the app processes this message                         |
| **Default**      | /                                                           |
| **Notes**        | /                                                           |

### WM_CLOSE

|                  |                                                                                |
| ---------------- | ------------------------------------------------------------------------------ |
| **When**         | Window should close (close button, Alt+F4, [CloseWindow](#closewindow)...etc.) |
| **wParam**       | /                                                                              |
| **lParam**       | /                                                                              |
| **Return value** | 0 if the app processes this message                                            |
| **Default**      | [DestroyWindow](#destroywindow) called                                         |
| **Notes**        | /                                                                              |

### WM_COMPACTING

|                  |                                                                                                                |
| ---------------- | -------------------------------------------------------------------------------------------------------------- |
| **When**         | More than 12.5% CPU time over a 30- to 60-second interval<br>is spent on compacting memory i.e. memory is low. |
| **wParam**       | /                                                                                                              |
| **lParam**       | /                                                                                                              |
| **Return value** | 0 if the app processes this message                                                                            |
| **Default**      | /                                                                                                              |
| **Notes**        | Legacy, for 16-bit Windows compatibility                                                                       |

### WM_CREATE

|                  |                                                                                                        |
| ---------------- | ------------------------------------------------------------------------------------------------------ |
| **When**         | During [CreateWindow](#createwindow) call, after the window is created, but before window is visible   |
| **wParam**       | /                                                                                                      |
| **lParam**       | [CREATESTRUCT](#createstruct) pointer                                                                  |
| **Return value** | 0 to continue creation <br>-1 the window is destroyed and [CreateWindow](#createwindow) returns `NULL` |
| **Default**      | Creation proceeds, [CreateWindow](#createwindow) returns [HWND](./types.md#hwnd)                       |
| **Notes**        | /                                                                                                      |

### WM_DESTROY

|                  |                                                                                                             |
| ---------------- | ----------------------------------------------------------------------------------------------------------- |
| **When**         | After the window is removed from the screen, before the [child windows](#child-window) have been destroyed. |
| **wParam**       | /                                                                                                           |
| **lParam**       | /                                                                                                           |
| **Return value** | 0 if the app processes this message                                                                         |
| **Default**      | /                                                                                                           |
| **Notes**        | App must call [PostQuitMessage](#postquitmessage), otherwise program keeps running forever.                 |

### WM_ENABLE

|                  |                                                                                  |
| ---------------- | -------------------------------------------------------------------------------- |
| **When**         | Before [EnableWindow](#enablewindow) returns, after the enabled state is changed |
| **wParam**       | [BOOL](./types.md#bool)<br>`TRUE` if enabled<br>`FALSE` if disabled              |
| **lParam**       | /                                                                                |
| **Return value** | 0 if the app processes this message                                              |
| **Default**      | /                                                                                |
| **Notes**        | /                                                                                |

### WM_ENTERSIZEMOVE

|                  |                                                                                                              |
| ---------------- | ------------------------------------------------------------------------------------------------------------ |
| **When**         | After window enters sizing or move loop (e.g. title bar drag, border resize, system menu Size / Move...etc.) |
| **wParam**       | /                                                                                                            |
| **lParam**       | /                                                                                                            |
| **Return value** | 0 if the app processes this message                                                                          |
| **Default**      | /                                                                                                            |
| **Notes**        | /                                                                                                            |

### WM_EXITSIZEMOVE

|                  |                                                                                                             |
| ---------------- | ----------------------------------------------------------------------------------------------------------- |
| **When**         | After window exits sizing or move loop (e.g. title bar drag, border resize, system menu Size / Move...etc.) |
| **wParam**       | /                                                                                                           |
| **lParam**       | /                                                                                                           |
| **Return value** | 0 if the app processes this message                                                                         |
| **Default**      | /                                                                                                           |
| **Notes**        | /                                                                                                           |

### WM_GETICON

|                  |                                                                                                        |
| ---------------- | ------------------------------------------------------------------------------------------------------ |
| **When**         | System needs to retrieve the small or large icon for display (title bar, Alt+Tab view, taskbar...etc.) |
| **wParam**       | [Icon type](#wm_geticon-icon-type) being retrieved                                                     |
| **lParam**       | DPI of the icon being retrieved. Used to provide different icons depending on the size.                |
| **Return value** | [HICON](./types.md#hicon)                                                                              |
| **Default**      | `hIcon`/`hIconSm` from [WNDCLASS](#wndclass) returned if set, otherwise 0.                             |
| **Notes**        | /                                                                                                      |

### WM_GETMINMAXINFO

|                  |                                                                                                                                                                   |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Size or position of the window is about to change.<br>Used to override default maximized size/position and default tracking (border resize) minimum/maximum size. |
| **wParam**       | /                                                                                                                                                                 |
| **lParam**       | [MINMAXINFO](#minmaxinfo) pointer. App can override defaults by setting this struct's members.                                                                    |
| **Return value** | 0 if the app processes this message                                                                                                                               |
| **Default**      | /                                                                                                                                                                 |
| **Notes**        | /                                                                                                                                                                 |

### WM_INPUTLANGCHANGE

|                  |                                                                                                                                                                     |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Input locale (keyboard layout) changed for the window's thread                                                                                                      |
| **wParam**       | [BYTE](./types.md#byte) font character set for input language.<br>Only needed if you used the [ANSI](./unicode_ansi.md) version of [RegisterClass](#registerclass). |
| **lParam**       | [HKL](./types.md#hkl) input locale id.<br>Language id in the [LOWORD](#loword).<br>Device handle in the [HIWORD](#hiword).                                          |
| **Return value** | Nonzero if the app processes this message                                                                                                                           |
| **Default**      | Message passed to all first-level child windows.                                                                                                                    |
| **Notes**        | /                                                                                                                                                                   |

### WM_INPUTLANGCHANGEREQUEST

|                  |                                                                                                                                                                                                                             |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | User chooses a new input locale (keyboard layout) (e.g. Alt+Shift, taskbar language indicator...etc.).<br>App can accept the change by passing the message to [DefWindowProc](#defwindowproc) or reject by returning early. |
| **wParam**       | Bitmask of [flags](#wm_inputlangchangerequest-flags) describing the change                                                                                                                                                  |
| **lParam**       | [HKL](./types.md#hkl) input locale id.<br>Language id in the [LOWORD](#loword).<br>Device handle in the [HIWORD](#hiword).                                                                                                  |
| **Return value** | Ignored, return 0 if not calling [DefWindowProc](#defwindowproc).                                                                                                                                                           |
| **Default**      | New input locale accepted, [WM_INPUTLANGCHANGE](#wm_inputlangchange) sent.                                                                                                                                                  |
| **Notes**        | /                                                                                                                                                                                                                           |

### WM_MOVE

|                  |                                                                                                                                                                               |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | After the window has been moved                                                                                                                                               |
| **wParam**       | /                                                                                                                                                                             |
| **lParam**       | [DWORD](./types.md#dword)<br>X coordinate of upper-left corner of client area in [LOWORD](#loword).<br>Y coordinate of upper-left corner of client area in [HIWORD](#hiword). |
| **Return value** | 0 if the app processes this message                                                                                                                                           |
| **Default**      | /                                                                                                                                                                             |
| **Notes**        | /                                                                                                                                                                             |

### WM_MOVING

|                  |                                                                                                                                                                               |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Continuosly while the user is moving the window                                                                                                                               |
| **wParam**       | /                                                                                                                                                                             |
| **lParam**       | [RECT](./types.md#rect) pointer that represents the current position of the window.<br>App can change the members of the struct to change the position of the drag rectangle. |
| **Return value** | `TRUE` if the app processes this message                                                                                                                                      |
| **Default**      | /                                                                                                                                                                             |
| **Notes**        | /                                                                                                                                                                             |

### WM_NCACTIVATE

|                  |                                                                                                                                                                                                                                                                                                                                                                                                             |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Nonclient area (title bar and icon) needs to be drawn to<br>indicate active or inactive state                                                                                                                                                                                                                                                                                                               |
| **wParam**       | [BOOL](./types.md#bool)<br>`TRUE` if active state needs to be drawn.<br>`FALSE` if inactive state needs to be drawn.                                                                                                                                                                                                                                                                                        |
| **lParam**       | If -1 [DefWindowProc](#defwindowproc) does not repaint the nonclient area to reflect the state change.<br>Otherwise,<br>if `wParam` is `TRUE`, this param is [HWND](./types.md#hwnd) to previously active window,<br>if `wParam` is `FALSE`, this param is [HWND](./types.md#hwnd) to window about to be activated.<br>`NULL` if previously active window or about-to-be-active window is from another app. |
| **Return value** | When `wParam` is `FALSE`, app should return `TRUE` for the system to proceed with default processing, or `FALSE` to prevent the change.<br>When `wParam` is `TRUE`, return value is ignored.                                                                                                                                                                                                                |
| **Default**      | /                                                                                                                                                                                                                                                                                                                                                                                                           |
| **Notes**        | If the window is minimized when this message is received, the app should pass the message to [DefWindowProc](#defwindowproc).                                                                                                                                                                                                                                                                               |

### WM_NCCALCSIZE

|                  |                                                                                                                                                                                                                                                                                    |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | The size and position of window's client area must be calculated                                                                                                                                                                                                                   |
| **wParam**       | /                                                                                                                                                                                                                                                                                  |
| **lParam**       | If `wParam` is `TRUE`,<br>points to [NCCALCSIZE_PARAMS](#nccalcsize_params) struct that can be used to calculate the new size and position of the client rect.<br>If `wParam` is `FALSE`,<br>points to [RECT](./types.md#rect) struct that contains the proposed window rectangle. |
| **Return value** | If `wParam` is `FALSE`, app should return 0.<br>If `wParam` is `TRUE` app should return bitmask of [flags](#wm_nccalcsize-flags) or 0.                                                                                                                                             |
| **Default**      | /                                                                                                                                                                                                                                                                                  |
| **Notes**        | /                                                                                                                                                                                                                                                                                  |

### WM_NCCREATE

|                  |                                                                                                                  |
| ---------------- | ---------------------------------------------------------------------------------------------------------------- |
| **When**         | Before [WM_CREATE](#wm_create), after the window is created                                                      |
| **wParam**       | /                                                                                                                |
| **lParam**       | [CREATESTRUCT](#createstruct) pointer                                                                            |
| **Return value** | `TRUE` to continue creation,<br>`FALSE` the window is destroyed and [CreateWindow](#createwindow) returns `NULL` |
| **Default**      | /                                                                                                                |
| **Notes**        | /                                                                                                                |

### WM_NCDESTROY

|                  |                                                                                                                                                |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Nonclient area of window is being destroyed, after [WM_DESTROY](#wm_destroy) and after the [child windows](#child-window) have been destroyed. |
| **wParam**       | /                                                                                                                                              |
| **lParam**       | /                                                                                                                                              |
| **Return value** | 0 if the app processes this message                                                                                                            |
| **Default**      | /                                                                                                                                              |
| **Notes**        | This message frees any memory internally allocated for the window.                                                                             |

### WM_NULL

|                  |                                                                                                                                 |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | App can send it as a "placeholder" or a "no-op" message that will be ignored by the window.                                     |
| **wParam**       | /                                                                                                                               |
| **lParam**       | /                                                                                                                               |
| **Return value** | 0 if the app processes this message                                                                                             |
| **Default**      | /                                                                                                                               |
| **Notes**        | Typically used to check whether the window and its message queue are still alive via [SendMessageTimeout](#sendmessagetimeout). |

### WM_QUERYDRAGICON

|                  |                                                                                                                                                                                |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **When**         | A minimized window icon (on the desktop) is about to be dragged, and no icon was defined on [WNDCLASS](#wndclass).                                                             |
| **wParam**       | /                                                                                                                                                                              |
| **lParam**       | /                                                                                                                                                                              |
| **Return value** | [HCURSOR](./types.md#hcursor) or [HICON](./types.md#hicon) to display as the cursor while the user drags the window icon.<br>Returning `NULL` will display the default cursor. |
| **Default**      | `hIcon` or `hCursor` from [WNDCLASS](#wndclass) used (?).                                                                                                                      |
| **Notes**        | Legacy, for Windows before the taskbar was introduced (before Windows 95?).                                                                                                    |

### WM_QUERYOPEN

|                  |                                                                                                                      |
| ---------------- | -------------------------------------------------------------------------------------------------------------------- |
| **When**         | The user requests for the window to be restored to its previous size and position via minimized icon on the desktop. |
| **wParam**       | /                                                                                                                    |
| **lParam**       | /                                                                                                                    |
| **Return value** | `TRUE` to allow restore, `FALSE` to prevent restore.                                                                 |
| **Default**      | /                                                                                                                    |
| **Notes**        | Legacy, for Windows before the taskbar was introduced (before Windows 95?).                                          |

### WM_QUIT

|                  |                                                                                                                                                                                                                                                                                                                                                                                                         |
| ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Request to terminate the app generated when app calls [PostQuitMessage](#postquitmessage).                                                                                                                                                                                                                                                                                                              |
| **wParam**       | The exit code given in [PostQuitMessage](#postquitmessage).                                                                                                                                                                                                                                                                                                                                             |
| **lParam**       | /                                                                                                                                                                                                                                                                                                                                                                                                       |
| **Return value** | No return value because it stops the [message loop](#message-loop) before the message is sent to [window procedure](#window-procedure).                                                                                                                                                                                                                                                                 |
| **Default**      | /                                                                                                                                                                                                                                                                                                                                                                                                       |
| **Notes**        | This message causes [GetMessage](#getmessage) to return zero.<br>This message is not associated with a window and will never be received through a window's [window procedure](#window-procedure). It can be retrieved only via [GetMessage](#getmessage) and [PeekMessage](#peekmessage).<br>This message should be posted using [PostQuitMessage](#postquitmessage), not [PostMessage](#postmessage). |

### WM_SHOWWINDOW

|                  |                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Window is about to be hidden or shown.                                                                                                                                                                                                                                                                                                                                                                                 |
| **wParam**       | `TRUE` if window is being shown,<br>`FALSE` if window is being hidden.                                                                                                                                                                                                                                                                                                                                                 |
| **lParam**       | Status [code](#wm_showwindow-status) or reason why the window is shown / hidden.<br>It's 0 when the reason is a call to [ShowWindow](#showwindow).                                                                                                                                                                                                                                                                     |
| **Return value** | 0 if the app processes this message                                                                                                                                                                                                                                                                                                                                                                                    |
| **Default**      | /                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **Notes**        | If window was created with [WS_VISIBLE](#window-styles) style, it receives this message after it is created, but before it is displayed.<br>This message is not sent when [ShowWindow](#showwindow) is called with [SW_SHOWNORMAL](#showwindow-codes) code.<br>This message is not sent when a top-level, overlapped window is created with the [WS_MAXIMIZE](#window-styles) or [WS_MINIMIZE](#window-styles) styles. |

### WM_SIZE

|                  |                                                                                                                                                    |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | After the window's size has changed.                                                                                                               |
| **wParam**       | The [type](#wm_size-type) code of resizing requested.                                                                                              |
| **lParam**       | [DWORD](./types.md#dword)<br>New width of the client area in the [LOWORD](#loword).<br>New height of the client area in the [HIWORD](#hiword).     |
| **Return value** | 0 if the app processes this message                                                                                                                |
| **Default**      | /                                                                                                                                                  |
| **Notes**        | [DefWindowProc](#defwindowproc) sends `WM_SIZE` and [WM_MOVE](#wm_move) when it processes the [WM_WINDOWPOSCHANGED](#wm_windowposchanged) message. |

### WM_SIZING

|                  |                                                                                                                                                                               |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Continuosly while the user is resizing the window                                                                                                                             |
| **wParam**       | /                                                                                                                                                                             |
| **lParam**       | [RECT](./types.md#rect) pointer that represents the current position of the window.<br>App can change the members of the struct to change the position of the drag rectangle. |
| **Return value** | `TRUE` if the app processes this message                                                                                                                                      |
| **Default**      | /                                                                                                                                                                             |
| **Notes**        | /                                                                                                                                                                             |

### WM_STYLECHANGED

|                  |                                                                        |
| ---------------- | ---------------------------------------------------------------------- |
| **When**         | Window style(s) changed via [SetWindowLong](#setwindowlong).           |
| **wParam**       | Window style type [code](#wm_stylechanged-code) (extended or regular). |
| **lParam**       | [STYLESTRUCT](#stylestruct) pointer. Read-only.                        |
| **Return value** | 0 if the app processes this message                                    |
| **Default**      | /                                                                      |
| **Notes**        | /                                                                      |

### WM_DISPLAYCHANGE

|                  |                                                                                                                       |
| ---------------- | --------------------------------------------------------------------------------------------------------------------- |
| **When**         | Display resolution changed                                                                                            |
| **wParam**       | Image depth of display in `bpp` (bits per pixel)                                                                      |
| **lParam**       | [DWORD](./types.md#dword)<br>Horizontal resolution in [LOWORD](#loword).<br>Vertical resolution in [HIWORD](#hiword). |
| **Return value** | 0 if the app processes this message                                                                                   |
| **Default**      | /                                                                                                                     |
| **Notes**        | /                                                                                                                     |

### WM_NCPAINT

|                  |                                                                                 |
| ---------------- | ------------------------------------------------------------------------------- |
| **When**         | The frame must be painted                                                       |
| **wParam**       | [HRGN](./types.md#hrgn) update region of the window clipped to the window frame |
| **lParam**       | /                                                                               |
| **Return value** | 0 if the app processes this message                                             |
| **Default**      | /                                                                               |
| **Notes**        | /                                                                               |

### WM_PAINT

|                  |                                                                                                                                                           |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Request is made to paint a portion of the window via [UpdateWindow](#updatewindow), [RedrawWindow](#redrawwindow), or [DispatchMessage](#dispatchmessage) |
| **wParam**       | /                                                                                                                                                         |
| **lParam**       | /                                                                                                                                                         |
| **Return value** | 0 if the app processes this message                                                                                                                       |
| **Default**      | /                                                                                                                                                         |
| **Notes**        | /                                                                                                                                                         |

### WM_PRINT

|                  |                                                                        |
| ---------------- | ---------------------------------------------------------------------- |
| **When**         | Request sent to window to draw itself in the specified device context. |
| **wParam**       | [HDC](./types.md#hdc) device context                                   |
| **lParam**       | Bitmask of drawing option [flags](#wm_print-and-wm_printclient-flags)  |
| **Return value** | /                                                                      |
| **Default**      | /                                                                      |
| **Notes**        | /                                                                      |

### WM_PRINTCLIENT

|                  |                                                                                 |
| ---------------- | ------------------------------------------------------------------------------- |
| **When**         | Request sent to window to draw its client area in the specified device context. |
| **wParam**       | [HDC](./types.md#hdc) device context                                            |
| **lParam**       | Bitmask of drawing option [flags](#wm_print-and-wm_printclient-flags)           |
| **Return value** | /                                                                               |
| **Default**      | /                                                                               |
| **Notes**        | /                                                                               |

### WM_SETREDRAW

|                  |                                                                                                                                                                               |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **When**         | Sent by application via [SendMessage](#sendmessage) to a window (usually [control](./controls.md)) to enable/disable redrawing.<br>Can be used for batching multiple updates. |
| **wParam**       | /                                                                                                                                                                             |
| **lParam**       | /                                                                                                                                                                             |
| **Return value** | 0 if the app processes this message                                                                                                                                           |
| **Default**      | /                                                                                                                                                                             |
| **Notes**        | /                                                                                                                                                                             |

### WM_SYNCPAINT

|                  |                                                                                                        |
| ---------------- | ------------------------------------------------------------------------------------------------------ |
| **When**         | A window changed (hidden, moved, resized...),<br>so top-level windows from other threads are notified. |
| **wParam**       | /                                                                                                      |
| **lParam**       | /                                                                                                      |
| **Return value** | 0 if the app processes this message                                                                    |
| **Default**      | /                                                                                                      |
| **Notes**        | /                                                                                                      |

## Icons

// TODO

## Cursor

// TODO

## Layered window

// TODO

## Custom data

// TODO

## Functions

### DefWindowProc

Default window procedure that should be called in [window procedure](#window-procedure) for messages (or conditions) the application doesn't handle.

```c
// in our windowProc

return DefWindowProcW(hwnd, uMsg, wParam, lParam);
```

### SetWindowPos

Changes the position, size, and Z order of a window.

| Name            | Type                    | Description                                                                                             |
| --------------- | ----------------------- | ------------------------------------------------------------------------------------------------------- |
| hwnd            | [HWND](./types.md#hwnd) | The window handle.                                                                                      |
| hWndInsertAfter | [HWND](./types.md#hwnd) | The window that this window should be in front of or one of the [z order codes](#window-z-order-codes). |
| X               | `int`                   | The new position of the left side of the window.                                                        |
| Y               | `int`                   | The new position of the top of the window.                                                              |
| cx              | `int`                   | The new width of the window, in pixels.                                                                 |
| cy              | `int`                   | The new position of the top of the window.                                                              |
| uFlags          | [UINT](./types.md#uint) | Bitmask for window sizing and positioning [flags](#setwindowpos-flags).                                 |

| Success | Error                                                       |
| ------- | ----------------------------------------------------------- |
| `TRUE`  | `FALSE`, [GetLastError](./errors.md#getlasterror) available |

```c
BOOL didSucceed = SetWindowPos(hwnd, HWND_TOP, 10, 10, 500, 500, SWP_SHOWWINDOW | SWP_NOREDRAW);
```

### ShowWindow

Sets the specified window's show state.

The first time the application calls this, second argument is ignored if `STARTUPINFO` was provided,
otherwise the first time this is called the second argument should be the `nCmdShow` parameter of `WinMain`.

In subsequent calls, any [code](#showwindow-codes) can be provided.

| Name     | Type                    | Description                           |
| -------- | ----------------------- | ------------------------------------- |
| hWnd     | [HWND](./types.md#hwnd) | The window handle.                    |
| nCmdShow | `int`                   | Show state [code](#showwindow-codes). |

| Success                                                   | Error |
| --------------------------------------------------------- | ----- |
| `TRUE` if window was visible before,<br>`FALSE` otherwise | /     |

```c
BOOL wasVisible = ShowWindow(hwnd, SW_HIDE); // hides the window
```

### SetActiveWindow

Activates a window. The window must be attached to the calling thread's message queue.

Only works if application is already in the foreground.

| Name | Type                    | Description       |
| ---- | ----------------------- | ----------------- |
| hWnd | [HWND](./types.md#hwnd) | The window handle |

| Success                                                   | Error                                                      |
| --------------------------------------------------------- | ---------------------------------------------------------- |
| [HWND](./types.md#hwnd) Previously active window's handle | `NULL`, [GetLastError](./errors.md#getlasterror) available |

```c
HWND prevActiveWindowOrNull = SetActiveWindow(hwnd);
```

### SetForegroundWindow

Brings the window to the foreground and activates it.

| Name | Type                    | Description       |
| ---- | ----------------------- | ----------------- |
| hWnd | [HWND](./types.md#hwnd) | The window handle |

| Success                                                              | Error |
| -------------------------------------------------------------------- | ----- |
| `TRUE` if window was brought to the foreground,<br>`FALSE` otherwise | /     |

```c
BOOL windowWasBroughtToForeground = SetForegroundWindow(hwnd);
```

## Codes and flags

### Window Z order codes

| Name           | Description                                                                                                                                       |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| HWND_BOTTOM    | Bottom of the z order.                                                                                                                            |
| HWND_NOTOPMOST | Above all non-topmost windows.                                                                                                                    |
| HWND_TOP       | Top of the window's z order "layer"<br>if window is topmost, goes to the top of the z order,<br>otherwise goes to the top of non-topmost windows. |
| HWND_TOPMOST   | Top of the z order.                                                                                                                               |

### SetWindowPos Flags

| Name                                 | Description                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SWP_ASYNCWINDOWPOS                   | If the calling thread and the thread that owns the window are attached to different input queues,<br>the system posts the request to the thread that owns the window.<br>This prevents the calling thread from blocking its execution while other threads process the request.                                                                                                                                        |
| SWP_DEFERERASE                       | Prevents generation of the [WM_SYNCPAINT](#wm_syncpaint) message.                                                                                                                                                                                                                                                                                                                                                     |
| SWP_DRAWFRAME                        | Draws a frame (defined in the window's class description) around the window.                                                                                                                                                                                                                                                                                                                                          |
| SWP_FRAMECHANGED                     | Applies new frame styles set using the `SetWindowLong` function.<br>Sends a `WM_NCCALCSIZE` message to the window, even if the window's size is not being changed.<br>If this flag is not specified, `WM_NCCALCSIZE` is sent only when the window's size is being changed.                                                                                                                                            |
| SWP_HIDEWINDOW                       | Hides the window.                                                                                                                                                                                                                                                                                                                                                                                                     |
| SWP_NOACTIVATE                       | Does not activate the window.<br>If this flag is not set, the window is activated and moved to the top of either the topmost or non-topmost group (depending on the setting of the `hWndInsertAfter` parameter).                                                                                                                                                                                                      |
| SWP_NOCOPYBITS                       | Discards the entire contents of the client area.<br>If this flag is not specified, the valid contents of the client area are saved and copied back into the client area after the window is sized or repositioned.                                                                                                                                                                                                    |
| SWP_NOMOVE                           | Retains the current position (ignores X and Y parameters).                                                                                                                                                                                                                                                                                                                                                            |
| SWP_NOOWNERZORDER (SWP_NOREPOSITION) | Does not change the owner window's position in the Z order.                                                                                                                                                                                                                                                                                                                                                           |
| SWP_NOREDRAW                         | Does not redraw changes.<br>If this flag is set, no repainting of any kind occurs.<br>This applies to the client area, the nonclient area (including the title bar and scroll bars), and any part of the parent window uncovered as a result of the window being moved.<br>When this flag is set, the application must explicitly invalidate or redraw any parts of the window and parent window that need redrawing. |
| SWP_NOSENDCHANGING                   | Prevents the window from receiving the `WM_WINDOWPOSCHANGING` message.                                                                                                                                                                                                                                                                                                                                                |
| SWP_NOSIZE                           | Retains the current size (ignores the cx and cy parameters).                                                                                                                                                                                                                                                                                                                                                          |
| SWP_NOZORDER                         | Retains the current Z order (ignores the hWndInsertAfter parameter).                                                                                                                                                                                                                                                                                                                                                  |
| SWP_SHOWWINDOW                       | Displays the window.                                                                                                                                                                                                                                                                                                                                                                                                  |

### ShowWindow codes

These codes are used with [ShowWindow](#showwindow).

| Name                             | Description                                                                                                                                                                                                                              |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SW_HIDE                          | Hides the window and activates another window.                                                                                                                                                                                           |
| SW_SHOWNORMAL and SW_NORMAL      | Activates and displays a window. If the window is minimized, maximized, or arranged, the system restores it to its original size and position.<br>An application should specify this flag when displaying the window for the first time. |
| SW_SHOWMINIMIZED                 | Activates the window and displays it as a minimized window.                                                                                                                                                                              |
| SW_SHOWMAXIMIZED and SW_MAXIMIZE | Activates the window and displays it as a maximized window.                                                                                                                                                                              |
| SW_SHOWNOACTIVATE                | Displays a window in its most recent size and position.<br>This value is similar to `SW_SHOWNORMAL`, except that the window is not activated.                                                                                            |
| SW_SHOW                          | Activates the window and displays it in its current size and position.                                                                                                                                                                   |
| SW_MINIMIZE                      | Minimizes the specified window and activates the next top-level window in the Z order.                                                                                                                                                   |
| SW_SHOWMINNOACTIVE               | Displays the window as a minimized window. This value is similar to `SW_SHOWMINIMIZED`, except the window is not activated.                                                                                                              |
| SW_SHOWNA                        | Displays the window in its current size and position. This value is similar to `SW_SHOW`, except that the window is not activated.                                                                                                       |
| SW_RESTORE                       | Activates and displays the window. If the window is minimized, maximized, or arranged, the system restores it to its original size and position.<br>An application should specify this flag when restoring a minimized window.           |
| SW_SHOWDEFAULT                   | Sets the show state based on the SW\_ value specified in the `STARTUPINFO` structure passed to the `CreateProcess` function by the program that started the application.                                                                 |
| SW_FORCEMINIMIZE                 | Minimizes a window, even if the thread that owns the window is not responding. This flag should only be used when minimizing windows from a different thread.                                                                            |

### WM_GETICON icon type

| Name        | Description                                                                                                                                               |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ICON_BIG    | Retrieve the large icon for the window.                                                                                                                   |
| ICON_SMALL  | Retrieve the small icon for the window.                                                                                                                   |
| ICON_SMALL2 | Retrieves the small icon provided by the application. If the application does not provide one, the system uses the system-generated icon for that window. |

### WM_INPUTLANGCHANGEREQUEST flags

| Name                       | Description                                                                                                                                                      |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| INPUTLANGCHANGE_BACKWARD   | A hot key was used to choose the previous input locale in the installed list of input locales. This flag cannot be used with the `INPUTLANGCHANGE_FORWARD` flag. |
| INPUTLANGCHANGE_FORWARD    | A hot key was used to choose the next input locale in the installed list of input locales. This flag cannot be used with the `INPUTLANGCHANGE_BACKWARD` flag.    |
| INPUTLANGCHANGE_SYSCHARSET | The new input locale's keyboard layout can be used with the system character set.                                                                                |

### WM_NCCALCSIZE flags

| Name            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| WVR_ALIGNTOP    | Specifies that the client area of the window is to be preserved and aligned with the top of the new position of the window.<br>For example, to align the client area to the upper-left corner, return the `WVR_ALIGNTOP` and `WVR_ALIGNLEFT` values.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| WVR_ALIGNRIGHT  | Specifies that the client area of the window is to be preserved and aligned with the right side of the new position of the window.<br>For example, to align the client area to the lower-right corner, return the `WVR_ALIGNRIGHT` and `WVR_ALIGNBOTTOM` values.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| WVR_ALIGNLEFT   | Specifies that the client area of the window is to be preserved and aligned with the left side of the new position of the window.<br>For example, to align the client area to the lower-left corner, return the `WVR_ALIGNLEFT` and `WVR_ALIGNBOTTOM` values.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| WVR_ALIGNBOTTOM | Specifies that the client area of the window is to be preserved and aligned with the bottom of the new position of the window.<br>For example, to align the client area to the top-left corner, return the `WVR_ALIGNTOP` and `WVR_ALIGNLEFT` values.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| WVR_HREDRAW     | Used in combination with any other values, except `WVR_VALIDRECTS`, causes the window to be completely redrawn if the client rectangle changes size horizontally.<br>This value is similar to [CS_HREDRAW](#class-styles) class style                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| WVR_VREDRAW     | Used in combination with any other values, except `WVR_VALIDRECTS`, causes the window to be completely redrawn if the client rectangle changes size vertically.<br>This value is similar to [CS_VREDRAW](#class-styles) class style                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| WVR_REDRAW      | This value causes the entire window to be redrawn. It is a combination of `WVR_HREDRAW` and `WVR_VREDRAW` values.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| WVR_VALIDRECTS  | This value indicates that, upon return from [WM_NCCALCSIZE](#wm_nccalcsize), the rectangles specified by the rgrc[1] and rgrc[2] members of the [NCCALCSIZE_PARAMS](#nccalcsize_params) structure contain valid destination and source area rectangles, respectively.<br>The system combines these rectangles to calculate the area of the window to be preserved.<br>The system copies any part of the window image that is within the source rectangle and clips the image to the destination rectangle.<br>Both rectangles are in parent-relative or screen-relative coordinates.<br>This flag cannot be combined with any other flags.<br>This return value allows an application to implement more elaborate client-area preservation strategies, such as centering or preserving a subset of the client area. |

### WM_PRINT and WM_PRINTCLIENT flags

| Name             | Description                                      |
| ---------------- | ------------------------------------------------ |
| PRF_CHECKVISIBLE | Draws the window only if it is visible.          |
| PRF_CHILDREN     | Draws all visible children windows.              |
| PRF_CLIENT       | Draws the client area of the window.             |
| PRF_ERASEBKGND   | Erases the background before drawing the window. |
| PRF_NONCLIENT    | Draws the nonclient area of the window.          |
| PRF_OWNED        | Draws all owned windows.                         |

### WM_SHOWWINDOW status

| Name             | Description                                                                        |
| ---------------- | ---------------------------------------------------------------------------------- |
| SW_OTHERUNZOOM   | The window is being uncovered because a maximize window was restored or minimized. |
| SW_OTHERZOOM     | The window is being covered by another window that has been maximized.             |
| SW_PARENTCLOSING | The window's owner window is being minimized.                                      |
| SW_PARENTOPENING | The window's owner window is being restored.                                       |

### WM_SIZE type

| Name           | Description                                                                                                                                                                           |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SIZE_MAXHIDE   | Message is sent to all pop-up windows when some other window is maximized.                                                                                                            |
| SIZE_MAXIMIZED | The window has been maximized.                                                                                                                                                        |
| SIZE_MAXSHOW   | Message is sent to all pop-up windows when some other window has been restored to its former size.                                                                                    |
| SIZE_MINIMIZED | The window has been minimized.                                                                                                                                                        |
| SIZE_RESTORED  | The window has been resized, but neither the SIZE_MINIMIZED nor SIZE_MAXIMIZED value applies.<br>This is a "generic" resize type (e.g. when [SetWindowPos](#setwindowpos) is called). |

### WM_SIZING edge

| Name             |
| ---------------- |
| WMSZ_BOTTOM      |
| WMSZ_BOTTOMLEFT  |
| WMSZ_BOTTOMRIGHT |
| WMSZ_LEFT        |
| WMSZ_RIGHT       |
| WMSZ_TOP         |
| WMSZ_TOPLEFT     |
| WMSZ_TOPRIGHT    |

## Structs

## CREATESTRUCT

Window initialization parameters passed to [WM_CREATE](#wm_create) and [WM_NCCREATE](#wm_nccreate).

This struct has [string](./unicode_ansi.md) variants.

| Name           | Type                              | Description                                                                                                                                                                                                                |
| -------------- | --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| lpCreateParams | [LPVOID](./types.md#lpvoid)       | Contains [lpParam](#createwindow) passed to [CreateWindow](#createwindow),<br>or if window is an MDI client window a pointer to `CLIENTRECTSTRUCT`,<br>or if window is an MDI child window a pointer to `MDICREATESTRUCT`. |
| hInstance      | [HINSTANCE](./types.md#hinstance) | Handle to the module that owns the new window.                                                                                                                                                                             |
| hMenu          | [HMENU](./types.md#hmenu)         | Handle to the menu used by the new window.                                                                                                                                                                                 |
| hwndParent     | [HWND](./types.md#hwnd)           | Handle to the parent window (if any).                                                                                                                                                                                      |
| cy             | `int`                             | Height of the new window in pixels.                                                                                                                                                                                        |
| cx             | `int`                             | Width of the new window in pixels.                                                                                                                                                                                         |
| y              | `int`                             | Y-coordinate of the [upper-left corner](#ownership) of the new window.                                                                                                                                                     |
| x              | `int`                             | X-coordinate of the [upper-left corner](#ownership) of the new window                                                                                                                                                      |
| style          | [LONG](./types.md#long)           | [Window styles](#window-styles).                                                                                                                                                                                           |
| lpszName       | [LPCWSTR](./types.md#lpcwstr)     | Window name string that was defined as [lpWindowName](#createwindow).                                                                                                                                                      |
| lpszClass      | [LPCWSTR](./types.md#lpcwstr)     | Class name string or [ATOM](./types.md#atom) defined in [WNDCLASS](#wndclass).                                                                                                                                             |
| dwExStyle      | [DWORD](./types.md#dword)         | [Extended window styles](#extended-window-styles).                                                                                                                                                                         |

## MINMAXINFO

| Name           | Type                      | Description                                                                                                                                                                                                                                                                       |
| -------------- | ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ptReserved     | [POINT](./types.md#point) | Reserved; do not use.                                                                                                                                                                                                                                                             |
| ptMaxSize      | [POINT](./types.md#point) | The maximized width (x member) and the maximized height (y member) of the window.<br>For top-level windows, this value is based on the width of the primary monitor.                                                                                                              |
| ptMaxPosition  | [POINT](./types.md#point) | The position of the left side of the maximized window (x member) and the position of the top of the maximized window (y member).<br>For top-level windows, this value is based on the position of the primary monitor.                                                            |
| ptMinTrackSize | [POINT](./types.md#point) | The minimum tracking width (x member) and the minimum tracking height (y member) of the window.<br>This value can be obtained programmatically from the system metrics `SM_CXMINTRACK` and `SM_CYMINTRACK` via `GetSystemMetrics`.                                                |
| ptMaxTrackSize | [POINT](./types.md#point) | The maximum tracking width (x member) and the maximum tracking height (y member) of the window.<br>This value is based on the size of the virtual screen and can be obtained programmatically from the system metrics `SM_CXMAXTRACK` and `SM_CYMAXTRACK` via `GetSystemMetrics`. |

## NCCALCSIZE_PARAMS

Contains information that an application can use while processing the [WM_NCCALCSIZE](#wm_nccalcsize) message to calculate the size, position, and valid contents of the client area of a window.

| Name  | Type                       | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| ----- | -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| rgrc  | [RECT](./types.md#rect)[3] | When [WM_NCCALCSIZE](#wm_nccalcsize) is received,<br>first rect is new proposed coordinates of window,<br>second rect is coordinates of window before it was moved or resized,<br>third rect is coordinates of window's client area before it was moved or resized.<br>When [window procedure](#window-procedure) returns,<br>first rect is new coordinates of client area,<br>second rect is destination coordinates of client area,<br>third rect is source coordinates of client area. |
| lppos | `PWINDOWPOS`               | Pointer to [WINDOWPOS](#windowpos) struct.                                                                                                                                                                                                                                                                                                                                                                                                                                                |

## WINDOWPOS

Contains information about the size and position of a window.

| Name            | Type                    | Description                                                                                             |
| --------------- | ----------------------- | ------------------------------------------------------------------------------------------------------- |
| hwnd            | [HWND](./types.md#hwnd) | The window handle.                                                                                      |
| hwndInsertAfter | [HWND](./types.md#hwnd) | The window that this window should be in front of or one of the [z order codes](#window-z-order-codes). |
| x               | `int`                   | The position of the left edge of the window.                                                            |
| y               | `int`                   | The position of the top edge of the window.                                                             |
| cx              | `int`                   | The window width, in pixels.                                                                            |
| cy              | `int`                   | The window height, in pixels.                                                                           |
| flags           | [UINT](./types.md#uint) | Bitmask of window sizing and positioning [flags](#setwindowpos-flags).                                  |

### STYLESTRUCT

Contains the styles for a window.

Passed as `lParam` to [WM_STYLECHANGED](#wm_stylechanged) and [WM_STYLECHANGING](#wm_stylechanging) messages.

| Name     | Type                      | Description                       |
| -------- | ------------------------- | --------------------------------- |
| styleOld | [DWORD](./types.md#dword) | The previous styles for a window. |
| styleNew | [DWORD](./types.md#dword) | The new styles for a window.      |

## Macros

### LOWORD

Retrieves the low-order [word](./types.md#word) from the specified 32-bit [DWORD](./types.md#dword).

```c
WORD lowWord = LOWORD(dword);
```

### HIWORD

Retrieves the high-order [word](./types.md#word) from the specified 32-bit [DWORD](./types.md#dword).

```c
WORD highWord = HIWORD(dword);
```

### LOBYTE

Retrieves the low-order [byte](./types.md#byte) from the specified 16-bit [WORD](./types.md#word).

```c
BYTE lowByte = LOBYTE(word);
```

### HIBYTE

Retrieves the high-order [byte](./types.md#byte) from the given 16-bit [WORD](./types.md#word).

```c
BYTE highByte = HIBYTE(word);
```

### GET_X_LPARAM

Retrieves the signed x-coordinate from the specified [LPARAM](./types.md#lparam) value.

Used instead of [LOWORD](#loword) when we expect signed values (e.g. mouse messages).

`windowsx.h` must be included.

```c
int x = GET_X_LPARAM(lParam);
```

### GET_Y_LPARAM

Retrieves the signed y-coordinate from the given [LPARAM](./types.md#lparam) value.

Used instead of [HIWORD](#hiword) when we expect signed values (e.g. mouse messages).

`windowsx.h` must be included.

```c
int y = GET_Y_LPARAM(lParam);
```
