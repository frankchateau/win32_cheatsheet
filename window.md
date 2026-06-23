# Window

A window is a fundamental unit of the Windows GUI. It represents a rectangular region on the screen.

Windows are not just top-level application windows with a titlebar and borders, but can be any type of UI
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

- titlebar with icon, caption, window buttons and system menu
- frame or border that can be used for resizing
- client area for rendering the window's content

Titlebar and frame are called non-client area (NC).

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

| Name                | Description                                                                                                                                                                         |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| WS_OVERLAPPED       | Produces a basic window with a frame and a minimal titlebar. No resizable border or window buttons.                                                                                 |
| WS_POPUP            | Produces a frameless window with just the client area and no rounded corners.<br>Client area needs to be painted by the application for the window to be visible.                   |
| WS_CHILD            | Produces a [child window](#child-window).<br>Window with this style must have a parent window passed to [CreateWindow](#createwindow).<br>Cannot be used with the `WS_POPUP` style. |
| WS_THICKFRAME       | Adds a resizable border and makes the titlebar taller (if present).<br>Also adds a rounded border when used with `WS_POPUP`.<br>Alias is `WS_SIZEBOX`.                              |
| WS_SYSMENU          | Adds the window icon, system menu and window buttons.<br>`WS_MINIMIZEBOX` and `WS_MAXIMIZEBOX` are used alongside it to add the minimize and maximize buttons.                      |
| WS_MINIMIZEBOX      | Adds the minimize window button, requires the `WS_SYSMENU` style                                                                                                                    |
| WS_MAXIMIZEBOX      | Adds the maximize window button, requires the `WS_SYSMENU` style                                                                                                                    |
| WS_OVERLAPPEDWINDOW | The full, standard overlapped window.<br>Defined as `WS_OVERLAPPED \| WS_CAPTION \| WS_SYSMENU \| WS_THICKFRAME \| WS_MINIMIZEBOX \| WS_MAXIMIZEBOX`.                               |
| WS_HSCROLL          | Adds the horizontal scrollbar to the client area.                                                                                                                                   |
| WS_VSCROLL          | Adds the vertical scrollbar to the client area.                                                                                                                                     |
| WS_BORDER           | The window has a thin line border.                                                                                                                                                  |
| WS_DLGFRAME         | The window has a dialog-box like border. A window with this style cannot have a title bar.                                                                                          |
| WS_VISIBLE          | The window is visible initially. Can be modified with [ShowWindow](#showwindow) and [SetWindowPos](#setwindowpos).                                                                  |
| WS_DISABLED         | The window is disabled initially. Visually, the frame looks the same, but all input to the window is blocked.<br>Can be modified with [EnableWindow](#enablewindow).                |
| WS_MINIMIZE         | The window is minimized initially.<br>Alias is `WS_ICONIC`.                                                                                                                         |
| WS_MAXIMIZE         | The window is maximized initially.                                                                                                                                                  |
| WS_CLIPCHILDREN     | Child windows are excluded from the window's painting operations.<br>Prevents parent from overwriting child windows and flicker.<br>Mainly used with [GDI](./gdi.md).               |
| WS_CLIPSIBLINGS     | Applied to a child window so that it doesn't overwrite the area of the sibling windows it overlaps with, but only paint its own visible area.<br>Mainly used with [GDI](./gdi.md).  |
| WS_GROUP            | Makes the child window the beginning of a group of controls. Used for keyboard navigation, focus, and radio button groups. Used with standard [controls](./controls.md).            |
| WS_TABSTOP          | Makes a child window focusable via pressing Tab. Used with standard [controls](./controls.md).                                                                                      |

| ![Window with WS_OVERLAPPEDWINDOW style](./images/ws_overlappedwindow.png) | ![Window with WS_POPUP style](./images/ws_popup.png) |
| :------------------------------------------------------------------------: | :--------------------------------------------------: |
|               WS_OVERLAPPEDWINDOW with the system menu open                |                 WS_POPUP only window                 |

## Extended window styles

These styles are used with [CreateWindow](#createwindow).

| Name                      | Category                           | Description                                                                                                                                                                                                                                                                                                                         |
| ------------------------- | ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| WS_EX_APPWINDOW           | taskbar                            | Adds a taskbar icon for owned top-level windows (e.g. dialogs) which would normally be grouped under their parent window.                                                                                                                                                                                                           |
| WS_EX_TOOLWINDOW          | taskbar, titlebar                  | A tool window doesn't have a taskbar icon, has a smaller titlebar with only a close button and no app icon.                                                                                                                                                                                                                         |
| WS_EX_PALETTEWINDOW       | taskbar, titlebar, z order, border | Defined as `WS_EX_WINDOWEDGE \| WS_EX_TOOLWINDOW \| WS_EX_TOPMOST`.                                                                                                                                                                                                                                                                 |
| WS_EX_NOREDIRECTIONBITMAP | graphics                           | Tells [DWM](./dwm.md) to not create a [GDI](./gdi.md) backing surface for the window meaning the client area will not be rendered by default.<br>To present the client area, you need to use the DirectComposition API or similar.                                                                                                  |
| WS_EX_LAYERED             | graphics, transparency             | Improves performance, reduces flickering and enables per-pixel alpha blending and transparency effects for the window.<br>Can be used to create non-rectangular shaped windows (e.g. Winamp skins).<br>Cannot be used with `CS_CLASSDC` or `CS_OWNDC` [class styles](#class-styles).<br>More on [layered windows](#layered-window). |
| WS_EX_COMPOSITED          | graphics                           | The window uses double buffering for painting to reduce flicker. Legacy, made obsolete by [DWM](./dwm.md).                                                                                                                                                                                                                          |
| WS_EX_TRANSPARENT         | graphics, transparency             | The child window is not painted until siblings underneath it have been painted.<br>This allows for the top window to be blended with windows underneath.<br>This only affects the paint order (which is top-down by default), not the actual transparent rendering.                                                                 |
| WS_EX_TOPMOST             | z order, activation                | The window is placed above all non-topmost windows, even when it's deactivated.<br>Can be modified with [SetWindowPos](#setwindowpos).                                                                                                                                                                                              |
| WS_EX_NOACTIVATE          | activation                         | The window is not activated (brought to the foreground) when clicked on.<br>It doesn't appear on the taskbar by default, but adding `WS_EX_APPWINDOW` forces it.<br>Can be modified with [SetActiveWindow](#setactivewindow) and [SetForegroundWindow](#setforegroundwindow).                                                       |
| WS_EX_WINDOWEDGE          | border                             | The window has a border with a raised edge. Doesn't seem to have an effect on post-XP Windows.                                                                                                                                                                                                                                      |
| WS_EX_CLIENTEDGE          | border                             | The window has a border with a sunken edge. Has an effect when used with `WS_POPUP` and more pronounced when `WS_DLGFRAME` is added.                                                                                                                                                                                                |
| WS_EX_STATICEDGE          | border                             | The window has a 3D border style. Doesn't seem to have an effect on post-XP Windows.                                                                                                                                                                                                                                                |
| WS_EX_OVERLAPPEDWINDOW    | border                             | Defined as `WS_EX_WINDOWEDGE \| WS_EX_CLIENTEDGE`.                                                                                                                                                                                                                                                                                  |
| WS_EX_DLGMODALFRAME       | border                             | The window has a double border and can have a titlebar. Doesn't seem to have an effect on post-XP Windows.                                                                                                                                                                                                                          |
| WS_EX_LEFTSCROLLBAR       | layout                             | The vertical scrollbar (if present) is to the left of the client area.                                                                                                                                                                                                                                                              |
| WS_EX_RIGHTSCROLLBAR      | layout                             | The vertical scrollbar (if present) is to the right of the client area. This is the default for `WS_VSCROLL`.                                                                                                                                                                                                                       |
| WS_EX_LAYOUTRTL           | layout                             | Flips the horizontal origin of the window client area. Increasing horizontal values advance to the left.                                                                                                                                                                                                                            |
| WS_EX_NOINHERITLAYOUT     | layout                             | The layout is not inherited from parent to child windows.                                                                                                                                                                                                                                                                           |
| WS_EX_LEFT                | layout                             | The window has generic left-aligned properties (?). This is the default.                                                                                                                                                                                                                                                            |
| WS_EX_RIGHT               | layout                             | The window has generic right-aligned properties (?).                                                                                                                                                                                                                                                                                |
| WS_EX_LTRREADING          | reading order                      | Text is drawn left-to-right. This is the default.                                                                                                                                                                                                                                                                                   |
| WS_EX_RTLREADING          | reading order                      | Text is drawn right-to-left.                                                                                                                                                                                                                                                                                                        |
| WS_EX_ACCEPTFILES         | shell                              | Allows the window to accept files by drag and drop using the [shell api](./shell.md#shell) and `WM_DROPFILES`.<br>Can be modified with [DragAcceptFiles](./shell.md#dragacceptfiles). Mostly legacy and replaced with OLE.                                                                                                          |
| WS_EX_CONTROLPARENT       | accessibility, child windows       | The children of this window become included in focus navigation via Tab and Arrow keys instead of treating the whole window as one focusable item.                                                                                                                                                                                  |
| WS_EX_NOPARENTNOTIFY      | child windows                      | The parent window is not notified with `WM_PARENTNOTIFY` notifications when child windows are created / destroyed.                                                                                                                                                                                                                  |
| WS_EX_MDICHILD            | mdi                                | The window is an MDI child.                                                                                                                                                                                                                                                                                                         |
| WS_EX_CONTEXTHELP         | help                               | The window titlebar shows a help icon which when clicked, changes the cursor to a help cursor that can be used to click around the window to open help popups via `WM_HELP` and `WinHelp`.<br>Doesn't seem to have an effect post Windows 95.                                                                                       |

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

| When                                                                                                | wParam                                                                                                   | lParam                                                                                                                                                                    | Return value                        |
| --------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| Window of non-active application is about to be activated<br>and active window is being deactivated | [BOOL](./types.md#bool)<br>`TRUE` if window is being activated<br>`FALSE` if window is being deactivated | [DWORD](./types.md#dword) thread id<br>if window is activated, this is the deactivated window's thread<br>if window is deactivated, this is the activated window's thread | 0 if the app processes this message |

### WM_CANCELMODE

| When                                                                                                                                                                                               | wParam | lParam | Return value                        |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ | ------ | ----------------------------------- |
| Modal input loops or continuous input tracking<br>(e.g. mouse capture, drag-drop...etc.)<br> need to be cancelled because something interrupted<br> it (e.g. dialog shown, window disabled...etc.) | /      | /      | 0 if the app processes this message |

### WM_CHILDACTIVATE

| When                                                        | wParam | lParam | Return value                        |
| ----------------------------------------------------------- | ------ | ------ | ----------------------------------- |
| [Child window](#child-window) is activated, moved, or sized | /      | /      | 0 if the app processes this message |

### WM_CLOSE

| When                                                                              | wParam | lParam | Return value                        | Default                                |
| --------------------------------------------------------------------------------- | ------ | ------ | ----------------------------------- | -------------------------------------- |
| Window should close<br>(close button, Alt+F4, [CloseWindow](#closewindow)...etc.) | /      | /      | 0 if the app processes this message | [DestroyWindow](#destroywindow) called |

### WM_COMPACTING

| When                                                                                                           | wParam | lParam | Return value                        | Default | Notes                                    |
| -------------------------------------------------------------------------------------------------------------- | ------ | ------ | ----------------------------------- | ------- | ---------------------------------------- |
| More than 12.5% CPU time over a 30- to 60-second interval<br>is spent on compacting memory i.e. memory is low. | /      | /      | 0 if the app processes this message | /       | Legacy, for 16-bit Windows compatibility |

### WM_CREATE

| When                                                                                                       | wParam | lParam                                | Return value                                                                                           | Default                                                                             |
| ---------------------------------------------------------------------------------------------------------- | ------ | ------------------------------------- | ------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------- |
| During [CreateWindow](#createwindow) call,<br>after the window is created,<br>but before window is visible | /      | [CREATESTRUCT](#createstruct) pointer | 0 to continue creation <br>-1 the window is destroyed and [CreateWindow](#createwindow) returns `NULL` | Creation proceeds,<br>[CreateWindow](#createwindow) returns [HWND](./types.md#hwnd) |

### WM_DESTROY

| When                                        | wParam | lParam | Return value                        | Default | Notes                                                                                          |
| ------------------------------------------- | ------ | ------ | ----------------------------------- | ------- | ---------------------------------------------------------------------------------------------- |
| After the window is removed from the screen | /      | /      | 0 if the app processes this message | /       | App must call [PostQuitMessage](#postquitmessage),<br>otherwise program keeps running forever. |

### WM_ENABLE

| When                                                                                 | wParam                                                              | lParam | Return value                        | Default |
| ------------------------------------------------------------------------------------ | ------------------------------------------------------------------- | ------ | ----------------------------------- | ------- |
| Before [EnableWindow](#enablewindow) returns,<br>after the enabled state is changed. | [BOOL](./types.md#bool)<br>`TRUE` if enabled<br>`FALSE` if disabled | /      | 0 if the app processes this message | /       |

### WM_ENTERSIZEMOVE

| When                                                                                                | wParam | lParam | Return value                        | Default |
| --------------------------------------------------------------------------------------------------- | ------ | ------ | ----------------------------------- | ------- |
| After window enters sizing or move loop<br>(e.g. titlebar drag, border resize, system menu...etc.). | /      | /      | 0 if the app processes this message | /       |

### WM_EXITSIZEMOVE

| When                                                                                               | wParam | lParam | Return value                        | Default |
| -------------------------------------------------------------------------------------------------- | ------ | ------ | ----------------------------------- | ------- |
| After window exits sizing or move loop<br>(e.g. titlebar drag, border resize, system menu...etc.). | /      | /      | 0 if the app processes this message | /       |

### WM_GETICON

| When                                                                                                | wParam                                              | lParam                                                                                     | Return value              | Default                                                                       |
| --------------------------------------------------------------------------------------------------- | --------------------------------------------------- | ------------------------------------------------------------------------------------------ | ------------------------- | ----------------------------------------------------------------------------- |
| After window enters sizing or move loop<br>(e.g. titlebar drag, border resize, system menu...etc.). | [Icon type](#wm_geticon-icon-type) being retrieved. | DPI of the icon being retrieved.<br>Used to provide different icons depending on the size. | [HICON](./types.md#hicon) | `hIcon`/`hIconSm` from [WNDCLASS](#wndclass) returned if set,<br>otherwise 0. |

### WM_GETICON icon type

| Name        | Description                                                                                                                                               |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ICON_BIG    | Retrieve the large icon for the window.                                                                                                                   |
| ICON_SMALL  | Retrieve the small icon for the window.                                                                                                                   |
| ICON_SMALL2 | Retrieves the small icon provided by the application. If the application does not provide one, the system uses the system-generated icon for that window. |

### WM_GETMINMAXINFO

| When                                                                                                                                                                 | wParam | lParam                                                                                            | Return value                        | Default |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ | ------------------------------------------------------------------------------------------------- | ----------------------------------- | ------- |
| Size or position of the window is about to change.<br>Used to override default maximized size/position and<br>default tracking (border resize) minimum/maximum size. | /      | [MINMAXINFO](#minmaxinfo) pointer.<br>App can override defaults by setting this struct's members. | 0 if the app processes this message | /       |

### WM_INPUTLANGCHANGE

| When                                                                 | wParam                                                                                                                                                          | lParam                                                                                                            | Return value                              | Default                                          |
| -------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- | ----------------------------------------- | ------------------------------------------------ |
| Input language (keyboard layout) changed<br>for the window's thread. | [BYTE](./types.md#byte) font character set for input language.<br>Only needed if you used [ANSI](./unicode_ansi.md) version of [RegisterClass](#registerclass). | [HKL](./types.md#hkl) input locale id.<br>Low word contains a language id.<br>High word contains a device handle. | nonzero if the app processes this message | Message passed to all first-level child windows. |

### WM_NCCREATE

| When                                                           | wParam | lParam                                | Return value                                                                                                    | Default |
| -------------------------------------------------------------- | ------ | ------------------------------------- | --------------------------------------------------------------------------------------------------------------- | ------- |
| Before [WM_CREATE](#wm_create),<br>after the window is created | /      | [CREATESTRUCT](#createstruct) pointer | `TRUE` to continue creation<br>`FALSE` the window is destroyed and [CreateWindow](#createwindow) returns `NULL` | /       |

### WM_DISPLAYCHANGE

| When                       | wParam                          | lParam                                                                                                         | Return value                        |
| -------------------------- | ------------------------------- | -------------------------------------------------------------------------------------------------------------- | ----------------------------------- |
| Display resolution changed | Image depth of display in `bpp` | [DWORD](./types.md#dword)<br>Horizontal resolution in low-order word<br>Vertical resolution in high-order word | 0 if the app processes this message |

### WM_NCPAINT

| When                      | wParam                                                                              | lParam | Return value                        |
| ------------------------- | ----------------------------------------------------------------------------------- | ------ | ----------------------------------- |
| The frame must be painted | [HRGN](./types.md#hrgn) update region of the window <br>clipped to the window frame | /      | 0 if the app processes this message |

### WM_PAINT

| When                                                                                                                                                         | wParam | lParam | Return value                        |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------ | ------ | ----------------------------------- |
| Request is made to paint a portion of the window<br>via [UpdateWindow](#updatewindow), [RedrawWindow](#redrawwindow), or [DispatchMessage](#dispatchmessage) | /      | /      | 0 if the app processes this message |

### WM_PRINT

| When                                                                      | wParam                               | lParam                                          | Return value |
| ------------------------------------------------------------------------- | ------------------------------------ | ----------------------------------------------- | ------------ |
| Request sent to window to draw itself<br>in the specified device context. | [HDC](./types.md#hdc) device context | Bitmask of drawing option [flags](#print-flags) | /            |

### WM_PRINTCLIENT

| When                                                                               | wParam                               | lParam                                          | Return value |
| ---------------------------------------------------------------------------------- | ------------------------------------ | ----------------------------------------------- | ------------ |
| Request sent to window to draw its client area<br>in the specified device context. | [HDC](./types.md#hdc) device context | Bitmask of drawing option [flags](#print-flags) | /            |

### Print flags

| Name             | Description                                      |
| ---------------- | ------------------------------------------------ |
| PRF_CHECKVISIBLE | Draws the window only if it is visible.          |
| PRF_CHILDREN     | Draws all visible children windows.              |
| PRF_CLIENT       | Draws the client area of the window.             |
| PRF_ERASEBKGND   | Erases the background before drawing the window. |
| PRF_NONCLIENT    | Draws the nonclient area of the window.          |
| PRF_OWNED        | Draws all owned windows.                         |

### WM_SETREDRAW

| When                                                                                                                                                                             | wParam | lParam | Return value                        |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------ | ------ | ----------------------------------- |
| Sent by application via [SendMessage](#sendmessage)<br>to a window (usually [control](./controls.md)) to enable/disable redrawing.<br>Can be used for batching multiple updates. | /      | /      | 0 if the app processes this message |

### WM_SYNCPAINT

| When                                                                                                   | wParam | lParam | Return value                        |
| ------------------------------------------------------------------------------------------------------ | ------ | ------ | ----------------------------------- |
| A window changed (hidden, moved, resized...),<br>so top-level windows from other threads are notified. | /      | /      | 0 if the app processes this message |

## Icons

// TODO

## Cursor

// TODO

## Layered window

// TODO

## Custom data

// TODO

## SetWindowPos

Changes the position, size, and Z order of a window.

| Name            | Type                    | Description                                                                                                   |
| --------------- | ----------------------- | ------------------------------------------------------------------------------------------------------------- |
| hwnd            | [HWND](./types.md#hwnd) | The window handle.                                                                                            |
| hWndInsertAfter | [HWND](./types.md#hwnd) | The window that this window should be in front of or one of the [z order codes](#setwindowpos-z-order-codes). |
| X               | `int`                   | The new position of the left side of the window.                                                              |
| Y               | `int`                   | The new position of the top of the window.                                                                    |
| cx              | `int`                   | The new width of the window, in pixels.                                                                       |
| cy              | `int`                   | The new position of the top of the window.                                                                    |
| uFlags          | [UINT](./types.md#uint) | Bitmask for window sizing and positioning [flags](#setwindowpos-flags).                                       |

| Success | Error                                                       |
| ------- | ----------------------------------------------------------- |
| `TRUE`  | `FALSE`, [GetLastError](./errors.md#getlasterror) available |

```c
BOOL didSucceed = SetWindowPos(hwnd, HWND_TOP, 10, 10, 500, 500, SWP_SHOWWINDOW | SWP_NOREDRAW);
```

## SetWindowPos Z order codes

| Name           | Description                                                                                                                                       |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| HWND_BOTTOM    | Bottom of the z order.                                                                                                                            |
| HWND_NOTOPMOST | Above all non-topmost windows.                                                                                                                    |
| HWND_TOP       | Top of the window's z order "layer"<br>if window is topmost, goes to the top of the z order,<br>otherwise goes to the top of non-topmost windows. |
| HWND_TOPMOST   | Top of the z order.                                                                                                                               |

### SetWindowPos Flags

| Name               | Description                                                                                                                                                                                                                                                                                                                                                                                                           |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SWP_ASYNCWINDOWPOS | If the calling thread and the thread that owns the window are attached to different input queues,<br>the system posts the request to the thread that owns the window.<br>This prevents the calling thread from blocking its execution while other threads process the request.                                                                                                                                        |
| SWP_DEFERERASE     | Prevents generation of the [WM_SYNCPAINT](#wm_syncpaint) message.                                                                                                                                                                                                                                                                                                                                                     |
| SWP_DRAWFRAME      | Draws a frame (defined in the window's class description) around the window.                                                                                                                                                                                                                                                                                                                                          |
| SWP_FRAMECHANGED   | Applies new frame styles set using the `SetWindowLong` function.<br>Sends a `WM_NCCALCSIZE` message to the window, even if the window's size is not being changed.<br>If this flag is not specified, `WM_NCCALCSIZE` is sent only when the window's size is being changed.                                                                                                                                            |
| SWP_HIDEWINDOW     | Hides the window.                                                                                                                                                                                                                                                                                                                                                                                                     |
| SWP_NOACTIVATE     | Does not activate the window.<br>If this flag is not set, the window is activated and moved to the top of either the topmost or non-topmost group (depending on the setting of the `hWndInsertAfter` parameter).                                                                                                                                                                                                      |
| SWP_NOCOPYBITS     | Discards the entire contents of the client area.<br>If this flag is not specified, the valid contents of the client area are saved and copied back into the client area after the window is sized or repositioned.                                                                                                                                                                                                    |
| SWP_NOMOVE         | Retains the current position (ignores X and Y parameters).                                                                                                                                                                                                                                                                                                                                                            |
| SWP_NOOWNERZORDER  | Does not change the owner window's position in the Z order. Alias is `SWP_NOREPOSITION`.                                                                                                                                                                                                                                                                                                                              |
| SWP_NOREDRAW       | Does not redraw changes.<br>If this flag is set, no repainting of any kind occurs.<br>This applies to the client area, the nonclient area (including the title bar and scroll bars), and any part of the parent window uncovered as a result of the window being moved.<br>When this flag is set, the application must explicitly invalidate or redraw any parts of the window and parent window that need redrawing. |
| SWP_NOSENDCHANGING | Prevents the window from receiving the `WM_WINDOWPOSCHANGING` message.                                                                                                                                                                                                                                                                                                                                                |
| SWP_NOSIZE         | Retains the current size (ignores the cx and cy parameters).                                                                                                                                                                                                                                                                                                                                                          |
| SWP_NOZORDER       | Retains the current Z order (ignores the hWndInsertAfter parameter).                                                                                                                                                                                                                                                                                                                                                  |
| SWP_SHOWWINDOW     | Displays the window.                                                                                                                                                                                                                                                                                                                                                                                                  |

## ShowWindow

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

## ShowWindow codes

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

## SetActiveWindow

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

## SetForegroundWindow

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
| cx             | `int`                             | Width of the new window in px.                                                                                                                                                                                             |
| y              | `int`                             | Y-coordinate of the [upper-left corner](#ownership) of the new window.                                                                                                                                                     |
| x              | `int`                             | Y-coordinate of the [upper-left corner](#ownership) of the new window                                                                                                                                                      |
| style          | [LONG](./types.md#long)           | [Window styles](#window-styles).                                                                                                                                                                                           |
| lpszName       | [LPCWSTR](./types.md#lpcwstr)     | Window name string defined as [lpWindowName](#createwindow).                                                                                                                                                               |
| lpszClass      | [LPCWSTR](./types.md#lpcwstr)     | Class name string or [ATOM](./types.md#atom) defined in [WNDCLASS](#wndclass).                                                                                                                                             |
| dwExStyle      | [DWORD](./types.md#dword)         | [Extended window styles](#extended-window-styles).                                                                                                                                                                         |

## DefWindowProc

Default window procedure that should be called in [window procedure](#window-procedure) for messages (or conditions) the application doesn't handle.

```c
// in our windowProc

return DefWindowProcW(hwnd, uMsg, wParam, lParam);
```
