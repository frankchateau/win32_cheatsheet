# Abbreviations

## ACM

Animation control message

`ACM_OPEN`, `ACM_PLAY`, `ACM_STOP`

## ACN

Animation control notification

`ACN_START`, `ACN_STOP`

## BM or BCM

Button control message

`BM_CLICK`, `BM_GETIMAGE`, `BCM_SETSHIELD`

## BN or BCN

Button control notification

`BCN_DROPDOWN`, `BCN_HOTITEMCHANGE`, `BN_CLICKED`

## C

Constant type

[LPCSTR](./types.md#lpcstr), [LPCVOID](./types.md#lpcvoid)

## cb

Count of bytes

`cbSize` (size of struct), [cbClsExtra](./window.md#wndclass), [cbWndExtra](./window.md#wndclass)

## CB

Clipboard

`WM_ASKCBFORMATNAME`, `WM_CHANGECBCHAIN`

Combobox control message

`CB_ADDSTRING`, `CB_DELETESTRING`, `CB_DIR`

## CBN

Combobox control notification

`CBN_CLOSEUP`, `CBN_DBLCLK`, `CBN_DROPDOWN`

## CBM

ComboboxEx messages

`CBEM_DELETEITEM`, `CBEM_GETCOMBOCONTROL`, `CBEM_GETEDITCONTROL`

## CBEN

ComboboxEx notification

`CBEN_BEGINEDIT`, `CBEN_DELETEITEM`, `CBEN_DRAGBEGIN`

## cch

Count of characters

`cchName`

## CCM

Control message

`CCM_DPISCALE`, `CCM_GETUNICODEFORMAT`, `CCM_GETVERSION`

## CDM

Common dialog box message

`CDM_GETFILEPATH`, `CDM_GETFOLDERIDLIST`, `CDM_GETFOLDERPATH`

## CF

Clipboard format

`CF_TEXT`, `CF_BITMAP`

## CONV

Conversation

`HCONV`, `HCONVLIST` (conversation list).

## CS

Class style

[CS_DBLCLKS](./window.md#class-styles), [CS_PARENTDC](./window.md#class-styles), [CS_VREDRAW](./window.md#class-styles)

## CW

Create window [CreateWindow](./window.md#createwindow)

[CW_USEDEFAULT](./window.md#createwindow)

## D

Double something

[DWORD](./types.md#dword), [DWORDLONG](./types.md#dwordlong), [DWORD_PTR](./types.md#dword_ptr)

## DBLCLK

Double click

`WM_LBUTTONDBLCLK`, `WM_RBUTTONDBLCLK`, `WM_XBUTTONDBLCLK`

## DC

Device context

[HDC](./types.md#hdc)

## DDE

Dynamic data exchange

[HDDEDATA](./types.md#hddedata)

## DESK

Desktop

[HDESK](./types.md#hdesk)

## DL

Drag list box notification

`DL_BEGINDRAG`, `DL_CANCELDRAG`, `DL_DRAGGING`

## DLG

Dialog

`DLGITEMTEMPLATE`, `GetNextDlgGroupItem`, `DLGPROC`

## DM

Dialog box message

`DM_GETDEFID`, `DM_REPOSITION`, `DM_SETDEFID`

## DTM

Date and time picker message

`DTM_CLOSEMONTHCAL`, `DTM_GETDATETIMEPICKERINFO`, `DTM_GETIDEALSIZE`

## DTN

Date and time picker notification

`DTN_CLOSEUP`, `DTN_DATETIMECHANGE`, `DTN_DROPDOWN`

## DWP

Desktop window manager

`WM_DWMCOLORIZATIONCOLORCHANGED`, `DwmGetWindowAttribute`

## DWP

Deferred window position

[HDWP](./types.md#hdwp)

## EM

Edit control message (and rich edit)

`EM_CANUNDO`, `EM_CHARFROMPOS`, `EM_EMPTYUNDOBUFFER`

## EN

Edit control notification (and rich edit)

`EN_HSCROLL`, `EN_KILLFOCUS`, `EN_MAXTEXT`

## ENH

Enhanced

[HENHMETAFILE](./types.md#henhmetafile).

## EX

Extended

[WS_EX_ACCEPTFILES](./window.md#extended-window-styles), [CreateWindowEx](./window.md#createwindow), `TITLEBARINFOEX`

## GDI

Graphics device interface

[HGDIOBJ](./types.md#hgdiobj).

## H

(opaque) Handle

[HWND](./types.md#hwnd), [HINSTANCE](./types.md#hinstance), [HICON](./types.md#hicon)

## hbr

Handle to a brush resource

[hbrBackground](./window.md#wndclass)

## HDM

Header control message

`HDM_HITTEST`, `HDM_INSERTITEM`, `HDM_LAYOUT`

## HDN

Header control notification

`HDN_BEGINDRAG`, `HDN_BEGINFILTEREDIT`, `HDN_BEGINTRACK`

## HKM

Hot key control message

`HKM_GETHOTKEY`, `HKM_SETHOTKEY`, `HKM_SETRULES`

## HT

Hit test

`HTLEFT`, `HTCLIENT`, `HTMINBUTTON`

## IDC

ID of a cursor resource

`IDC_ARROW`, `IDC_HELP`, `IDC_WAIT`

## IDI

ID of an icon resource

`IDI_APPLICATION`, `IDI_ERROR`, `IDI_INFORMATION`

## IPM

IP address message

`IPM_SETADDRESS`, `IPM_SETFOCUS`, `IPM_SETRANGE`

## IPN

IP address notification

`IPN_FIELDCHANGED`.

## KL

Keyboard layout

`HKL`.

## L

Long something

[LPARAM](./types.md#lparam) (long parameter).

Left

`WM_LBUTTONDBLCLK`.

## LB

List box message

`LB_GETTEXT`, `LB_GETTEXTLEN`, `LB_GETTOPINDEX`.

## LBN

List box notification

`LBN_DBLCLK`, `LBN_ERRSPACE`, `LBN_KILLFOCUS`.

## LC

Locale

[LCID](./types.md#lcid), [LCTYPE](./types.md#lctype).

## LGRP

Language group

[LGRPID](./types.md#lgrpid)

## LM

SysLink control message

`LM_GETITEM`, `LM_HITTEST`, `LM_SETITEM`.

## LP

Long pointer

[LPBOOL](./types.md#lpbool), [lpszMenuName](./window.md#wndclass), [LPBYTE](./types.md#lpbyte)

## LVM

List view message

`LVM_DELETEITEM`, `LVM_EDITLABEL`, `LVM_ENABLEGROUPVIEW`.

## LVN

List view notification

`LVN_BEGINSCROLL`, `LVN_COLUMNCLICK`, `LVN_KEYDOWN`.

## MCM

Month calendar message

`MCM_SETRANGE`, `MCM_GETCOLOR`, `MCM_GETRANGE`.

## MCN

Month calendar notification

`MCN_GETDAYSTATE`, `MCN_SELCHANGE`, `MCN_SELECT`.

## MDI

Multiple document interface

`MDICLIENT`, `DefMDIChildProcW`, `CreateMDIWindowW`.

## NC

Non-client

`WM_NCHITTEST`, `WM_NCCALCSIZE`, `WM_NCACTIVATE`.

## NL

National language

`WinNls.h`

## OLE

Object linking and embedding

`OleSetClipboard`, `OLERENDER`, `OleIsRunning`.

## P

Pointer

[PBOOL](./types.md#pbool), [PBYTE](./types.md#pbyte), [PCTSTR](./types.md#pctstr).

## PBM

Progress bar message

`PBM_SETBKCOLOR`, `PBM_SETMARQUEE`, `PBM_SETPOS`.

## PGM

Pager control message

`PGM_GETPOS`, `PGM_RECALCSIZE`, `PGM_SETBKCOLOR`.

## PGN

Pager control notification

`PGN_CALCSIZE`, `PGN_HOTITEMCHANGE`, `PGN_SCROLL`.

## PRF

Print flag

[PRF_CHECKVISIBLE](./window.md#wm_print-and-wm_printclient-flags), [PRF_CLIENT](./window.md#wm_print-and-wm_printclient-flags)

## PSM

Property sheet message

`PSM_ADDPAGE`, `PSM_APPLY`, `PSM_GETRESULT`.

## PSN

Property sheet notification

`PSN_APPLY`, `PSN_GETOBJECT`, `PSN_HELP`.

## PTR

Pointer

[INT_PTR](./types.md#int_ptr), [LONG_PTR](./types.md#long_ptr).

## Q

Quad something

[QWORD](./types.md#qword)

## RB

Rebar control message

`RB_GETPALETTE`, `RB_GETRECT`, `RB_GETROWCOUNT`

## RBN

Rebar control notification

`RBN_AUTOBREAK`, `RBN_AUTOSIZE`, `RBN_BEGINDRAG`

## rc

Rect

`rcPaint`

## RGN

Region

[HRGN](./types.md#hrgn)

## RSRC

Resource

[HRSRC](./types.md#hrsrc)

## SB

Status bar messsage

`SB_GETICON`, `SB_GETPARTS`, `SB_GETRECT`

## SBN

Status bar notification

`SBN_SIMPLEMODECHANGE`.

## SBM

Scroll bar message

`SBM_ENABLE_ARROWS`, `SBM_GETPOS`, `SBM_GETRANGE`

## SC

Service control

[SC_HANDLE](./types.md#sc_handle), `OpenSCManagerW`, [SC_LOCK](./types.md#sc_lock).

## SPI

System parameters info

`SPI_GETSTICKYKEYS`, `SPI_GETDESKWALLPAPER`, `SPI_GETBEEP`

## STM

Static control message

`STM_GETICON`, `STM_GETIMAGE`, `STM_SETICON`

## STN

Static control notification

`STN_CLICKED`, `STN_DBLCLK`, `STN_DISABLE`

## SW

Show window ([ShowWindow](./window.md#showwindow))

[SW_SHOW](./window.md#showwindow-codes), [SW_RESTORE](./window.md#showwindow-codes)

## SWP

Set window position ([SetWindowPos](./window.md#setwindowpos))

[SWP_NOSIZE](./window.md#setwindowpos), [SWP_NOMOVE](./window.md#setwindowpos)

## SZ

String, zero terminated

[HSZ](./types.md#hsz), [lpszClassName](./window.md#wndclass)

Size / sizing

[WMSZ_BOTTOM](./window.md#wm_sizing-edge), [WMSZ_LEFT](./window.md#wm_sizing-edge)

## T

Generic text type

[LPCTSTR](./types.md#lpctstr), [TCHAR](./types.md#tchar).

## TB

Toolbar control message

`TB_SETSTYLE`, `TB_GETRECT`, `TB_AUTOSIZE`

## TBN

Toolbar control notification

`TBN_GETBUTTONINFO`, `TBN_GETDISPINFO`, `TBN_GETINFOTIP`

## TBM

Trackbar control message

`TBM_SETRANGE`, `TBM_CLEARSEL`, `TBM_SETBUDDY`

## TRBN

Trackbar control notification

`TRBN_THUMBPOSCHANGING`.

## TCM

Tab control message

`TCM_DELETEITEM`, `TCM_GETITEM`, `TCM_SETITEM`.

## TCN

Tab control notification

`TCN_FOCUSCHANGE`, `TCN_GETOBJECT`, `TCN_KEYDOWN`.

## TDM

Task dialog message

`TDM_CLICK_BUTTON`, `TDM_UPDATE_ICON`, `TDM_NAVIGATE_PAGE`.

## TDN

Task dialog notification

`TDN_BUTTON_CLICKED`, `TDN_CREATED`, `TDN_DESTROYED`.

## TTM

Tooltip control message

`TTM_GETTOOLINFO`, `TTM_HITTEST`, `TTM_NEWTOOLRECT`.

## TTN

Tooltip control notification

`TTN_LINKCLICK`, `TTN_NEEDTEXT`, `TTN_POP`.

## TVM

Treeview control message

`TVM_EXPAND`, `TVM_GETBKCOLOR`, `TVM_GETCOUNT`.

## TVN

Treeview control notification

`TVN_DELETEITEM`, `TVN_ENDLABELEDIT`, `TVN_GETDISPINFO`.

## U

Unsigned

[UCHAR](./types.md#uchar), [UHALF_PTR](./types.md#uhalf_ptr), [UINT](./types.md#uint)

## UDM

Up-down control message

`UDM_GETBUDDY`, `UDM_GETPOS`, `UDM_GETPOS32`.

## UDN

Up-down control notification

`UDN_DELTAPOS`.

## USN

Update sequence number

`USN`.

## W

Wide char string (Unicode)

[LPCWSTR](./types.md#lpcwstr), [LPWSTR](./types.md#lpwstr),

Word something

[WPARAM](./types.md#wparam) (Word parameter)

## WINSTA

Window station

[HWINSTA](./types.md#hwinsta)

## WM

Window message

[WM_CREATE](./window.md#wm_create), [WM_DESTROY](./window.md#wm_destroy), [WM_PAINT](./window.md#wm_paint)

## WND

Window

[HWND](./types.md#hwnd), `CreateHwndRenderTarget`, [WNDPROC](./types.md#wndproc)

## WS

Window style

[WS_OVERLAPPEDWINDOW](./window.md#window-styles), [WS_CHILD](./window.md#window-styles), [WS_EX_ACCEPTFILES](./window.md#extended-window-styles)

## WVR

Window valid rectangle

[WVR_ALIGNTOP](./window.md#wm_nccalcsize-flags), [WVR_ALIGNRIGHT](./window.md#wm_nccalcsize-flags).
