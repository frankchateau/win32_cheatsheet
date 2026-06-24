```c
MSG msg;
while (GetMessage(&msg, NULL, 0, 0)) {
    if (!TranslateAccelerator(...)) {
        TranslateMessage(&msg);
        DispatchMessage(&msg);
    }

    // Exit condition: mouse up, ESC, WM_CANCELMODE, etc.
    if (doneTracking)
        break;
}


SetCapture(hwnd);
BOOL dragging = TRUE;
while (dragging && GetMessage(&msg, NULL, 0, 0)) {
    if (msg.message == WM_MOUSEMOVE) { UpdateDragVisuals(); }
    else if (msg.message == WM_LBUTTONUP) { dragging = FALSE; Drop(); }
    else if (msg.message == WM_CANCELMODE) { dragging = FALSE; AbortDrag(); }
    TranslateMessage(&msg);
    DispatchMessage(&msg);
}
ReleaseCapture();


case WM_WINDOWPOSCHANGING:
{
    WINDOWPOS* wp = (WINDOWPOS*)lParam;
    if (wp->cx < MIN_WIDTH)  wp->cx = MIN_WIDTH;
    if (wp->cy < MIN_HEIGHT) wp->cy = MIN_HEIGHT;
    if (wp->cx > MAX_WIDTH)  wp->cx = MAX_WIDTH;
    if (wp->cy > MAX_HEIGHT) wp->cy = MAX_HEIGHT;
    break;
}

```

SetCapture / ReleaseCapture
TrackPopupMenu / TrackPopupMenuEx
DialogBox / DialogBoxParam

WinRT

- Globalization and Input
- Sensors and Devices
- Networking and Communication
- UI and Shell integration
- Storage and App Data
- Media and Graphics
- App Lifecycle and Contracts
- Security and Identity

Store

MSIX package, AppxManifest.xml required
