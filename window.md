# Window

A window is a fundamental unit of the Windows GUI. It represents a rectangular region on the screen.

Windows are not just top-level application windows with a titlebar and borders, but can be any type of UI
control (e.g. buttons, lists...etc).

The type of a window is `HWND` which is an opaque handle to the window which is managed by win32.
It is not meant to be modified directly, but rather passed to various win32 functions like:

```cpp
MoveWindow(hwnd, 100, 100, 800, 600, TRUE);
```

Each window is created from a window class that defines default / shared behavior for all windows created from it.
The window classes in question are not C++ classes, but rather configuration templates for windows specific to win32.

There are predefined built-in classes ([standard](#standard-controls) and [common](#common-controls) controls),
but you can create custom ones using [RegisterClass](#registerclass).

For the main application window, you'd typically register your own custom class.

There are 3 general categories of windows in terms of [ownership](#ownership): top-level, owned top-level, and child window.

Event handling is done via a [message loop](#message-loop) and the [window procedure](#window-procedure) callback.

The main parts of a window are:

- titlebar with icon, caption, window buttons and system menu
- frame or border that can be used for resizing
- client area for rendering the window's content

Titlebar and frame are called non-client area.

| ![Window parts](./window_parts.png) |
| :---------------------------------: |
|            Window parts             |

## RegisterClass

Before creating a custom window, a custom window class must be registered.

Registering a class is required to create a custom window, but also used for setting certain default behavior for windows created from the class.

To do that, we call a `RegisterClass` function and pass it a pointer to a `WNDCLASS` struct.

`RegisterClass` functions:

- RegisterClassA
- RegisterClassW
- RegisterClassExA
- RegisterClassExW - modern, preferred

You can use a macro like `RegisterClass` or `RegisterClassEx` that resolves based on the unicode settings.

`WNDCLASS` structs:

- WNDCLASSA
- WNDCLASSW
- WNDCLASSEXA
- WNDCLASSEXW - modern, preferred

You can use an alias like `WNDCLASS` or `WNDCLASSEX` that resolves based on the unicode settings.

`WNDCLASS` members:

Required:

- `style` - bitmask for setting [class styles](#class-styles).
- `lpfnWndProc` - pointer to the [window procedure](#window-procedure) callback.
- `hInstance` - handle to the executable module, like the hInstance parameter of `WinMain` or acquired with `GetModuleHandle`.
- `lpszClassName` - string that identifies this class. The class name is scoped to the module i.e two different modules can register a class with the same name.
- `cbSize` - the size of the structure in bytes. Set it to `sizeof(WNDCLASSEX)`. Used so that win32 knows
  which version of `WNDCLASS` you're using. Extended only.

Optional:

- `hIcon` - handle to an icon resource used as the [window icon](#icons).
- `hIconSm` - a handle to an icon resource used as the small [window icon](#icons). Extended only.
- `hCursor` - handle to a cursor resource used as the [window cursor](#cursors) icon by default.
- `hbrBackground` - handle to a brush used to paint the window client area background using GDI.
- `lpszMenuName` - string that specifies the resource name of a menu as it appears in your .rc file.
- `cbClsExtra` - number of extra bytes to allocate for per-class [custom app data](#custom-data). Rarely used today.
- `cbWndExtra` - number of extra bytes to allocate for per-window [custom app data](#custom-data).

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

## CreateWindow

To create a window, we call one of the `CreateWindow` functions:

- CreateWindowA
- CreateWindowW
- CreateWindowExA
- CreateWindowExW - modern, preferred

You can use a macro like `CreateWindow` or `CreateWindowEx` that resolves based on the unicode settings.

and pass in these parameters:

Required:

- `lpClassName` - string that identifies a registered class or an ATOM returned by a previous call to RegisterClass wrapped with MAKEINTATOM.
- `X` - horizontal upper-left corner position of the window. For top-level windows, it's relative to the screen,
  and for child windows it's relative to the upper-left corner of the parent's client area.
- `Y` - vertical upper-left corner position of the window. For top-level windows, it's relative to the screen,
  and for child windows it's relative to the upper-left corner of the parent's client area.
- `nWidth` - width of the window in device units.
- `nHeight` - height of the window in device units.
- `hInstance` - handle to executable module. Might not always be required here, but it's safer and there's no harm in passing it - even for standard controls.

Optional:

- `dwExStyle` - bitmask for setting [extended window styles](#extended-window-styles).
- `lpWindowName` - string that appears as the caption for windows with titlebars, button text for standard Button control...etc.
- `dwStyle` - bitmask for setting [window styles](#window-styles).
- `hWndParent` - handle to the parent or owner window. Required for child windows, optional for top-level windows.
- `hMenu` - for top-level windows this should be a handle to a menu or null, for child windows the identifier used to distinguish child controls.
- `lpParam` - arbitrary pointer that will be available as `CREATESTRUCT.lpCreateParams` in `WM_CREATE` `lParam`.
  You can use it to pass custom data to [window procedure](#window-procedure). Required for MDI.

Example creating a top-level window:

```cpp
const wchar_t className[] = L"MyWindowClass";

// WNDCLASS struct creation

ATOM classAtom = RegisterClass(&wc);

HWND myWindow = CreateWindowEx(
  WS_EX_ACCEPTFILES, // dwExStyle
  className, // or MAKEINTATOM(classAtom)
  WS_OVERLAPPEDWINDOW | WS_VISIBLE,
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

Example creating a standard Button control/window:

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

## Ownership

// TODO

## Class styles

These styles are used when [registering a class](#registerclass).

`CS_DBLCLKS` - Enables the windows to receive double click messages (e.g `WM_LBUTTONDBLCLK`).

`CS_NOCLOSE` - Disables the close button on the window.

`CS_DROPSHADOW` - Adds a drop-shadow effect on top-level windows (e.g. menus). Cannot be used with child windows.

`CS_HREDRAW` - Invalidates client area when width changes. Used with GDI.

`CS_VREDRAW` - Invalidates client area when height changes. Used with GDI.

`CS_CLASSDC` - All windows within class share the same DC. Mainly used with GDI.

`CS_OWNDC` - Each window within class gets its own DC. Mainly used with GDI and OpenGL.

`CS_PARENTDC` - Child windows use the parent's DC as an optimization. Mainly used with GDI.

`CS_GLOBALCLASS` - Makes the class globally available to all other modules in the process. Rarely used today.

`CS_SAVEBITS` - Saves a bitmap of the part of screen obscured by this window so when this window is removed,
the pixels that were underneath are restored from the bitmap instead doing a full repaint.
Legacy feature, made obsolete by DWM.

`CS_BYTEALIGNWINDOW` - Forces the window rectangle to start on a byte boundary in memory. Legacy feature.

`CS_BYTEALIGNCLIENT` - Forces the client area rectangle to start on a byte boundary in memory. Legacy feature.

## Window styles

These styles are used when [creating a window](#createwindow).

### Visual styles

There are three base window styles that you can use as a starting point:

- `WS_OVERLAPPED` - top-level windows
- `WS_POPUP` - special top-level windows like dialogs, menus, splash screens...etc.
- `WS_CHILD` - child windows that are visually constrained / clipped in a parent window.

`WS_OVERLAPPED` produces a basic window with a frame and a minimal titlebar. No resizable border or window buttons.

`WS_POPUP` produces a frameless window with just the client area and no borders at all (no rounding).
Client area needs to be painted for the window to be visible. Easiest way to do it:

```cpp
wc.hbrBackground = (HBRUSH)(COLOR_WINDOW + 1); // default, white background
```

`WS_THICKFRAME` adds a resizable border and makes the titlebar taller (if present). Also adds a rounded border when used with `WS_POPUP`. Its alias is `WS_SIZEBOX`.

`WS_SYSMENU` adds the window icon, system menu and window buttons. `WS_MINIMIZEBOX` and `WS_MAXIMIZEBOX` are used
alongside it to add the minimize and maximize buttons.

`WS_OVERLAPPEDWINDOW` is the full, standard overlapped window.
Defined as `WS_OVERLAPPED | WS_CAPTION | WS_SYSMENU | WS_THICKFRAME | WS_MINIMIZEBOX | WS_MAXIMIZEBOX`.

`WS_HSCROLL` and `WS_VSCROLL` are used to add horizontal and vertical scrollbars.

`WS_BORDER` The window has a thin line border.

`WS_DLGFRAME` The window has a dialog-box like border. A window with this style cannot have a title bar.

| ![Window with WS_OVERLAPPEDWINDOW style](./ws_overlappedwindow.png) | ![Window with WS_POPUP style](./ws_popup.png) |
| :-----------------------------------------------------------------: | :-------------------------------------------: |
|            WS_OVERLAPPEDWINDOW with the system menu open            |             WS_POPUP only window              |

### Behavior styles

`WS_VISIBLE` The window is visible initially. Can be modified with `ShowWindow` or `SetWindowPos`.

`WS_DISABLED` The window is disabled initially. Visually, the frame looks the same, but all input to the window is blocked.
Can be modified with `EnableWindow`.

`WS_MINIMIZE` The window is minimized initially. Its alias is `WS_ICONIC`.

`WS_MAXIMIZE` The window is maximized initially.

`WS_CLIPCHILDREN` Child windows are excluded from the window's painting operations. Prevents parent from
overwriting child windows and flicker. Mainly used with GDI.

`WS_CLIPSIBLINGS` Applied to a child window so that it doesn't overwrite the area of the sibling windows it overlaps with,
but only paint its own visible area. Mainly used with GDI.

`WS_GROUP` Makes the child window the beginning of a group of controls. Used for keyboard navigation, focus, and radio button groups. Used with standard controls.

`WS_TABSTOP` Makes a child window focusable via pressing Tab. Used with standard controls.

## Extended window styles

These styles are used when [creating a window](#createwindow).

### Taskbar / titlebar / visual styles

`WS_EX_APPWINDOW` Adds a taskbar icon for owned top-level windows (e.g. dialogs) which would normally be grouped under
their parent window.

`WS_EX_TOOLWINDOW` A tool window doesn't have a taskbar icon, has a smaller titlebar with only a close button and no app icon.

`WS_EX_OVERLAPPEDWINDOW` Defined as `WS_EX_WINDOWEDGE | WS_EX_CLIENTEDGE`.

`WS_EX_PALETTEWINDOW` Defined as `WS_EX_WINDOWEDGE | WS_EX_TOOLWINDOW | WS_EX_TOPMOST`.

| ![Window with WS_OVERLAPPEDWINDOW and WS_EX_TOOLWINDOW](./ws_ex_toolwindow.png) |
| :-----------------------------------------------------------------------------: |
|                    WS_OVERLAPPEDWINDOW and WS_EX_TOOLWINDOW                     |

### Graphics / transparency

`WS_EX_NOREDIRECTIONBITMAP` Tells DWM to not create a GDI backing surface for the window meaning the client area will not be rendered by default. To present the client area, you need to use the DirectComposition API or similar.

`WS_EX_LAYERED` Improves performance, reduces flickering and enables per-pixel alpha blending and transparency effects for the window.
Can be used to create non-rectangular shaped windows (e.g. Winamp skins). Cannot be used with `CS_CLASSDC` or `CS_OWNDC` [class styles](#class-styles).
More on [layered windows](#layered-window).

`WS_EX_COMPOSITED` The window uses double buffering to paint to reduce flicker. Legacy, made obsolete by DWM.

| ![Window with WS_OVERLAPPEDWINDOW and WS_EX_NOREDIRECTIONBITMAP](./ws_overlappedwindow_exnoredirectionbitmap.png) |
| :---------------------------------------------------------------------------------------------------------------: |
|                                 WS_OVERLAPPEDWINDOW and WS_EX_NOREDIRECTIONBITMAP                                 |

### Z-order / activation

`WS_EX_TOPMOST` The window is placed above all non-topmost windows, even when it's deactivated. Can be modified with `SetWindowPos`.

`WS_EX_NOACTIVATE` The window is not activated (brought to the foreground) when clicked on. It doesn't appear on the taskbar by default, but adding `WS_EX_APPWINDOW` forces it. Can be modified with `SetActiveWindow` and `SetForegroundWindow`.

### Frame / border

`WS_EX_WINDOWEDGE` The window has a border with a raised edge. Doesn't seem to have an effect on post-XP Windows.

`WS_EX_CLIENTEDGE` The window has a border with a sunken edge. Has an effect when used with `WS_POPUP` and more pronounced when `WS_DLGFRAME` is added.

`WS_EX_STATICEDGE` The window has a three-dimensional border style. Doesn't seem to have an effect on post-XP Windows.

`WS_EX_DLGMODALFRAME` The window has a double border and can have a titlebar. Doesn't seem to have an effect on post-XP Windows.

| ![Window with WS_POPUP, WS_DLGFRAME, and WS_EX_CLIENTEDGE](./ws_popup_dlgframe_exclientedge.png) |
| :----------------------------------------------------------------------------------------------: |
|                           WS_POPUP \| WS_DLGFRAME and WS_EX_CLIENTEDGE                           |

### Reading order (LTR/RTL) / layout

`WS_EX_LEFTSCROLLBAR` The vertical scrollbar (if present) is to the left of the client area.

`WS_EX_RIGHTSCROLLBAR` The vertical scrollbar (if present) is to the right of the client area. This is the default for `WS_VSCROLL`.

`WS_EX_LAYOUTRTL` Flips the horizontal origin of the window client area. Increasing horizontal values advance to the left.

`WS_EX_LTRREADING` Text is drawn left-to-right. This is the default.

`WS_EX_RTLREADING` Text is drawn right-to-left.

`WS_EX_NOINHERITLAYOUT` The layout is not inherited from parent to child windows.

`WS_EX_LEFT` The window has generic left-aligned properties (?). This is the default.

`WS_EX_RIGHT` The window has generic right-aligned properties (?).

### Other

`WS_EX_ACCEPTFILES` Allows the window to accept files by drag and drop using the shell api and `WM_DROPFILES`.
Can be modified with `DragAcceptFiles`. Mostly legacy and replaced with OLE.

`WS_EX_CONTROLPARENT` The children of this window become included in focus navigation via Tab and Arrow keys instead
of treating the whole window as one focusable item.

`WS_EX_NOPARENTNOTIFY` The parent window is not notified with `WM_PARENTNOTIFY` notifications when child windows are created / destroyed.

`WS_EX_MDICHILD` The window is an MDI child.

`WS_EX_CONTEXTHELP` The window titlebar shows a help icon which when clicked, changes the cursor to a help cursor that can be used
to click around the window to open help popups via `WM_HELP` and `WinHelp`. Doesn't seem to have an effect post Windows 95.

## Message loop

// TODO

## Window procedure

// TODO

## Icons

// TODO

## Cursors

// TODO

## Standard controls

// TODO

## Common controls

// TODO

## Layered window

// TODO

## Custom data

// TODO
