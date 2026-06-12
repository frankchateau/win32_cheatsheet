# Abbreviations

`EX` Extended

Examples: `WS_EX_ACCEPTFILES`, `CreateWindowEx`, `TITLEBARINFOEX`.

`D` Double something type

Examples `DWORD` (double of `WORD`), `DWORDLONG`, `DWORD_PTR`.

`L` Long something type

Examples `LPARAM` (long parameter).

`LP` Long pointer

Examples: `LPBOOL` (long pointer to BOOL), `LPBYTE`, `LPCOLORREF`.

`T` Generic text type

Examples: `LPCTSTR` (resolves to `LPCWSTR` for Unicode and `LPCSTR` for ANSI),
`TCHAR` (`WCHAR` for Unicode, `CHAR` for ANSI).

`C` Constant something type

Examples: `LPCSTR` (constant string), `LPCVOID` (constant any). 

`W` Wide char string (Unicode)

Examples: `LPCWSTR`, `LPWSTR`, 

`P` Pointer

Examples: `PBOOL`, `PBYTE`, `PCTSTR`.

`U` Unsigned

Examples: `UCHAR`, `UHALF_PTR`, `UINT`.

`PTR` Pointer

Examples: `INT_PTR` (used to type something that can hold either an int or a pointer), `LONG_PTR`.

`LC` Locale

Examples `LCID`, `LCTYPE`.

`LGRP` Language group

Examples: `LGRPID`.

`H` opaque handle

Examples: `HWND` (window), `HINSTANCE` (instance/module), `HICON` (icon), `HDC` (device context), `HDWP` (deferred window position).

`CONV` Conversation

Examples: `HCONV`, `HCONVLIST` (conversation list).

`DC` Device context

Examples: `HDC`.

`DDE` Dynamic data exchange

Examples: `HDDEDATA`.

`DESK` Desktop

Examples: `HDESK`.

`DWP` Deferred window position

Examples: `HDWP`.

`ENH` Enhanced

Examples: `HENHMETAFILE`.

`GDI` Graphics device interface

Examples: `HGDIOBJ`.

`KL` Keyboard layout

Examples: `HKL`.

`RGN` Region

Examples: `HRGN`.

`RSRC` Resource

Examples `HRSRC`.

`SZ` String, zero terminated

Examples: `HSZ`.

`WINSTA` Window station

Examples: `HWINSTA`.

`WND` Window

Examples: `HWND`, `CreateHwndRenderTarget`.

`USN` Update sequence number

Examples: `USN`.

`cb` Count of bytes

Examples: `cbSize` (size of struct), `cbClsExtra` (WNDCLASS), `cbWndExtra` (WNDCLASS).

`hbr` Handle to a brush resource

Examples: `hbrBackground` (WNDCLASS).

`WS` Window style

Examples: `WS_OVERLAPPEDWINDOW`, `WS_CHILD`, `WS_EX_ACCEPTFILES`.

`CW` Create window

Examples: `CW_USEDEFAULT`.

`IDC` ID of a cursor resource

Examples: `IDC_ARROW`, `IDC_HELP`, `IDC_WAIT`.

`IDI` ID of an icon resource

Examples: `IDI_APPLICATION`, `IDI_ERROR`, `IDI_INFORMATION`.

`ACM` Animation control message

Examples: `ACM_OPEN`, `ACM_PLAY`, `ACM_STOP`.

`ACN` Animation control notification

Examples: `ACN_START`, `ACN_STOP`.

`BM` or `BCM` Button control message

Examples: `BM_CLICK`, `BM_GETIMAGE`, `BCM_SETSHIELD`.

`BN` or `BCN` Button control notification

Examples: `BCN_DROPDOWN`, `BCN_HOTITEMCHANGE`, `BN_CLICKED`.

`CB` Combobox control message

Examples: `CB_ADDSTRING`, `CB_DELETESTRING`, `CB_DIR`.

`CBN` Combobox control notification

Examples: `CBN_CLOSEUP`, `CBN_DBLCLK`, `CBN_DROPDOWN`.

`CBM` ComboboxEx messages

Examples: `CBEM_DELETEITEM`, `CBEM_GETCOMBOCONTROL`, `CBEM_GETEDITCONTROL`.

`CBN` ComboboxEx notification

Examples: `CBEN_BEGINEDIT`, `CBEN_DELETEITEM`, `CBEN_DRAGBEGIN`.

`CCM` Control message

Examples: `CCM_DPISCALE`, `CCM_GETUNICODEFORMAT`, `CCM_GETVERSION`.

`CDM` Common dialog box message

Examples: `CDM_GETFILEPATH`, `CDM_GETFOLDERIDLIST`, `CDM_GETFOLDERPATH`.

`DL` Drag list box notification

Examples: `DL_BEGINDRAG`, `DL_CANCELDRAG`, `DL_DRAGGING`.

`DM` Dialog box message

Examples: `DM_GETDEFID`, `DM_REPOSITION`, `DM_SETDEFID`.

`DTM` Date and time picker message

Examples: `DTM_CLOSEMONTHCAL`, `DTM_GETDATETIMEPICKERINFO`, `DTM_GETIDEALSIZE`.

`DTN` Date and time picker notification

Examples: `DTN_CLOSEUP`, `DTN_DATETIMECHANGE`, `DTN_DROPDOWN`.

`EM` Edit control message (and rich edit)

Examples: `EM_CANUNDO`, `EM_CHARFROMPOS`, `EM_EMPTYUNDOBUFFER`.

`EN` Edit control notification (and rich edit)

Examples: `EN_HSCROLL`, `EN_KILLFOCUS`, `EN_MAXTEXT`.

`HDM` Header control message

Examples: `HDM_HITTEST`, `HDM_INSERTITEM`, `HDM_LAYOUT`.

`HDN` Header control notification

Examples: `HDN_BEGINDRAG`, `HDN_BEGINFILTEREDIT`, `HDN_BEGINTRACK`.

`HKM` Hot key control message

Examples: `HKM_GETHOTKEY`, `HKM_SETHOTKEY`, `HKM_SETRULES`.

`IPM` IP address message

Examples: `IPM_SETADDRESS`, `IPM_SETFOCUS`, `IPM_SETRANGE`.

`IPN` IP address notification

Examples: `IPN_FIELDCHANGED`.

`LB` List box message

Examples: `LB_GETTEXT`, `LB_GETTEXTLEN`, `LB_GETTOPINDEX`.

`LBN` List box notification

Examples: `LBN_DBLCLK`, `LBN_ERRSPACE`, `LBN_KILLFOCUS`.

`LM` SysLink control message

Examples: `LM_GETITEM`, `LM_HITTEST`, `LM_SETITEM`.

`LVM` List view message

Examples: `LVM_DELETEITEM`, `LVM_EDITLABEL`, `LVM_ENABLEGROUPVIEW`.

`LVN` List view notification

Examples: `LVN_BEGINSCROLL`, `LVN_COLUMNCLICK`, `LVN_KEYDOWN`.

`MCM` Month calendar message

Examples: `MCM_SETRANGE`, `MCM_GETCOLOR`, `MCM_GETRANGE`.

`MCN` Month calendar notification

Examples: `MCN_GETDAYSTATE`, `MCN_SELCHANGE`, `MCN_SELECT`.

`PBM` Progress bar message

Examples: `PBM_SETBKCOLOR`, `PBM_SETMARQUEE`, `PBM_SETPOS`.

`PGM` Pager control message

Examples: `PGM_GETPOS`, `PGM_RECALCSIZE`, `PGM_SETBKCOLOR`.

`PGN` Pager control notification

Examples: `PGN_CALCSIZE`, `PGN_HOTITEMCHANGE`, `PGN_SCROLL`.

`PSM` Property sheet message

Examples: `PSM_ADDPAGE`, `PSM_APPLY`, `PSM_GETRESULT`.

`PSN` Property sheet notification

Examples: `PSN_APPLY`, `PSN_GETOBJECT`, `PSN_HELP`.

`RB` Rebar control message

Examples: `RB_GETPALETTE`, `RB_GETRECT`, `RB_GETROWCOUNT`.

`RBN` Rebar control notification

Examples: `RBN_AUTOBREAK`, `RBN_AUTOSIZE`, `RBN_BEGINDRAG`

`SB` Status bar messsage

Examples: `SB_GETICON`, `SB_GETPARTS`, `SB_GETRECT`.

`SBN` Status bar notification

Examples: `SBN_SIMPLEMODECHANGE`.

`SBM` Scroll bar message

Examples: `SBM_ENABLE_ARROWS`, `SBM_GETPOS`, `SBM_GETRANGE`.

`STM` Static control message

Examples: `STM_GETICON`, `STM_GETIMAGE`, `STM_SETICON`.

`STN` Static control notification

Examples: `STN_CLICKED`, `STN_DBLCLK`, `STN_DISABLE`.

`TB` Toolbar control message

Examples: `TB_SETSTYLE`, `TB_GETRECT`, `TB_AUTOSIZE`.

`TBN` Toolbar control notification

Examples: `TBN_GETBUTTONINFO`, `TBN_GETDISPINFO`, `TBN_GETINFOTIP`.

`TBM` Trackbar control message

Examples: `TBM_SETRANGE`, `TBM_CLEARSEL`, `TBM_SETBUDDY`.

`TRBN` Trackbar control notification

Examples: `TRBN_THUMBPOSCHANGING`.

`TCM` Tab control message

Examples: `TCM_DELETEITEM`, `TCM_GETITEM`, `TCM_SETITEM`.

`TCN`Tab control notification

Examples: `TCN_FOCUSCHANGE`, `TCN_GETOBJECT`, `TCN_KEYDOWN`.

`TDM` Task dialog message

Examples: `TDM_CLICK_BUTTON`, `TDM_UPDATE_ICON`, `TDM_NAVIGATE_PAGE`.

`TDN` Task dialog notification

Examoles: `TDN_BUTTON_CLICKED`, `TDN_CREATED`, `TDN_DESTROYED`.

`TTM` Tooltip control message

Examples: `TTM_GETTOOLINFO`, `TTM_HITTEST`, `TTM_NEWTOOLRECT`.

`TTN` Tooltip control notification

Examples: `TTN_LINKCLICK`, `TTN_NEEDTEXT`, `TTN_POP`.

`TVM` Treeview control message

Examples: `TVM_EXPAND`, `TVM_GETBKCOLOR`, `TVM_GETCOUNT`.

`TVN` Treeview control notification

Examples: `TVN_DELETEITEM`, `TVN_ENDLABELEDIT`, `TVN_GETDISPINFO`.

`UDM` Up-down control message

Examples: `UDM_GETBUDDY`, `UDM_GETPOS`, `UDM_GETPOS32`.

`UDN` Up-down control notification

Examples: `UDN_DELTAPOS`.

`WM` Window message

Examples: `WM_CREATE`, `WM_DESTROY`, `WM_PAINT`, `WM_PASTE`.
