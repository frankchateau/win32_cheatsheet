# Terminology

## Opaque handle

A reference to an internal operating system object. 

Their internal structure and memory layout are not exposed and their lifecycle is managed by the OS.

For each type of opaque handle there is a creation function (e.g. `CreateWindow`) and a destruction function (e.g. `DestroyWindow`).

The base type for handles is `HANDLE`. Every other handle type is an alias to the base type and starts with the letter `H`.

Examples: `HWND` (window), `HINSTANCE` (instance/module), `HICON` (icon), `HDC` (device context), `HDWP` (deferred window position).
