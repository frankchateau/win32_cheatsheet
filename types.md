# Types

## APIENTRY

The calling convention for system functions.

```c
// WinDef.h

#define APIENTRY WINAPI
```

## ATOM

An unsigned 16-bit integer that can be used to access a string in the system atom table.

```c
// WinDef.h

typedef WORD ATOM;
```

## BOOL

A Boolean variable (should be TRUE or FALSE).

```c
// WinDef.h

typedef int BOOL;
```

## BOOLEAN

A Boolean variable (should be TRUE or FALSE).

```c
// WinNT.h

typedef BYTE BOOLEAN;
```

## BYTE

A byte (8 bits).

```c
// WinDef.h

typedef unsigned char BYTE;
```

## CALLBACK

The calling convention for callback functions.

```c
// WinDef.h

#define CALLBACK __stdcall
```

## CCHAR

An 8-bit Windows (ANSI) character.

```c
// WinNT.h

typedef char CCHAR;
```

## CHAR

An 8-bit Windows (ANSI) character.

```c
// WinNT.h

typedef char CHAR;
```

## COLORREF

The red, green, blue (RGB) color value (32 bits).

The hexadecimal form is: `0x00bbggrr`.

You can use the [RGB macro](./gdi.md#rgb-macro) to create a `COLOREF`.

```c
// WinDef.h

typedef DWORD COLORREF;
```

## CONST

A variable whose value is to remain constant during execution.

```c
// WinDef.h

#define CONST const
```

## DWORD

A 32-bit unsigned integer. The range is 0 through 4294967295 decimal.

```c
// IntSafe.h

typedef unsigned long DWORD;
```

## DWORDLONG

A 64-bit unsigned integer. The range is 0 through 18446744073709551615 decimal.

```c
// IntSafe.h

typedef unsigned __int64 DWORDLONG;
```

## DWORD_PTR

An unsigned long type for pointer precision. Use when casting a pointer to a long type to perform pointer arithmetic. (Also commonly used for general 32-bit parameters that have been extended to 64 bits in 64-bit Windows.)

```c
// BaseTsd.h

typedef ULONG_PTR DWORD_PTR;
```

## DWORD32

A 32-bit unsigned integer.

```c
// BaseTsd.h

typedef unsigned int DWORD32;
```

## DWORD64

A 64-bit unsigned integer.

```c
// BaseTsd.h

typedef unsigned __int64 DWORD64;
```

## FLOAT

A floating-point variable.

```c
// WinDef.h

typedef float FLOAT;
```

## HACCEL

A handle to an accelerator table.

```c
// WinDef.h

typedef HANDLE HACCEL;
```

## HALF_PTR

Half the size of a pointer. Use within a structure that contains a pointer and two small fields.

```c
// BaseTsd.h

#ifdef _WIN64
 typedef int HALF_PTR;
#else
 typedef short HALF_PTR;
#endif
```

## HANDLE

A handle to an object.

```c
// WinNT.h

typedef PVOID HANDLE;
```

## HBITMAP

A handle to a bitmap.

```c
// WinDef.h

typedef HANDLE HBITMAP;
```

## HBRUSH

A handle to a brush.

```c
// WinDef.h

typedef HANDLE HBRUSH;
```

## HCOLORSPACE

A handle to a color space.

```c
// WinDef.h

typedef HANDLE HCOLORSPACE;
```

## HCONV

A handle to a dynamic data exchange (DDE) conversation.

```c
// Ddeml.h

typedef HANDLE HCONV;
```

## HCONVLIST

A handle to a DDE conversation list.

```c
// Ddeml.h

typedef HANDLE HCONVLIST;
```

## HCURSOR

A handle to a cursor.

```c
// WinDef.h

typedef HICON HCURSOR;
```

## HDC

A handle to a device context (DC).

```c
// WinDef.h

typedef HANDLE HDC;
```

## HDDEDATA

A handle to DDE data.

```c
// Ddeml.h

typedef HANDLE HDDEDATA;
```

## HDESK

A handle to a desktop.

```c
// WinDef.h

typedef HANDLE HDESK;
```

## HDROP

A handle to an internal drop structure.

```c
// ShellApi.h

typedef HANDLE HDROP;
```

## HDWP

A handle to a deferred window position structure.

```c
// WinUser.h

typedef HANDLE HDWP;
```

## HENHMETAFILE

A handle to an enhanced metafile.

```c
// WinDef.h

typedef HANDLE HENHMETAFILE;
```

## HFILE

A handle to a file opened by `OpenFile`, not `CreateFile`.

```c
// WinDef.h

typedef int HFILE;
```

## HFONT

A handle to a font.

```c
// WinDef.h

typedef HANDLE HFONT;
```

## HGDIOBJ

A handle to a [GDI](./gdi.md) object.

```c
// WinDef.h

typedef HANDLE HGDIOBJ;
```

## HGLOBAL

A handle to a global memory block.

```c
// WinDef.h

typedef HANDLE HGLOBAL;
```

## HHOOK

A handle to a hook.

```c
// WinDef.h

typedef HANDLE HHOOK;
```

## HICON

A handle to an icon.

```c
// WinDef.h

typedef HANDLE HICON;
```

## HINSTANCE

A handle to an instance. This is the base address of the module in memory.

[HMODULE](#hmodule) and HINSTANCE are the same today, but represented different things in 16-bit Windows.

```c
// WinDef.h

typedef HANDLE HINSTANCE;
```

## HKEY

A handle to a registry key.

```c
// WinDef.h

typedef HANDLE HKEY;
```

## HKL

An input locale identifier.

```c
// WinDef.h

typedef HANDLE HKL;
```

## HLOCAL

A handle to a local memory block.

```c
// WinDef.h

typedef HANDLE HLOCAL;
```

## HMENU

A handle to a menu.

```c
// WinDef.h

typedef HANDLE HMENU;
```

## HMETAFILE

A handle to a metafile.

```c
// WinDef.h

typedef HANDLE HMETAFILE;
```

## HMODULE

A handle to a module. This is the base address of the module in memory.

HMODULE and [HINSTANCE](#hinstance) are the same in current versions of Windows, but represented different things in 16-bit Windows.

```c
// WinDef.h

typedef HINSTANCE HMODULE;
```

## HMONITOR

A handle to a display monitor.

Only defined for [version](./winver.md) greater or equal to Windows 2000.

```c
// WinDef.h

if(WINVER >= 0x0500) typedef HANDLE HMONITOR; // 0x0500 is Windows 2000
```

## HPALETTE

A handle to a palette.

```c
// WinDef.h

typedef HANDLE HPALETTE;
```

## HPEN

A handle to a pen.

```c
// WinDef.h

typedef HANDLE HPEN;
```

## HRESULT

The return codes used by COM interfaces.

To test an HRESULT value, use the `FAILED` and `SUCCEEDED` macros.

```c
// WinNT.h

typedef LONG HRESULT;
```

## HRGN

A handle to a region.

```c
// WinDef.h

typedef HANDLE HRGN;
```

## HRSRC

A handle to a resource.

```c
// WinDef.h

typedef HANDLE HRSRC;
```

## HSZ

A handle to a DDE string.

```c
// Ddeml.h

typedef HANDLE HSZ;
```

## HWINSTA

A handle to a window station.

```c
// WinDef.h

typedef HANDLE WINSTA;
```

## HWND

A handle to a window.

```c
// WinDef.h

typedef HANDLE HWND;
```

## INT

A 32-bit signed integer. The range is -2147483648 through 2147483647 decimal.

```c
// WinDef.h

typedef int INT;
```

## INT_PTR

A signed integer type for pointer precision. Use when casting a pointer to an integer to perform pointer arithmetic.

```c
// BaseTsd.h

#if defined(_WIN64)
 typedef __int64 INT_PTR;
#else
 typedef int INT_PTR;
#endif
```

## INT8

An 8-bit signed integer.

```c
// BaseTsd.h

typedef signed char INT8;
```

## INT16

A 16-bit signed integer.

```c
// BaseTsd.h

typedef signed short INT16;
```

## INT32

A 32-bit signed integer. The range is -2147483648 through 2147483647 decimal.

```c
// BaseTsd.h

typedef signed int INT32;
```

## INT64

A 64-bit signed integer. The range is -9223372036854775808 through 9223372036854775807 decimal.

```c
// BaseTsd.h

typedef signed __int64 INT64;
```

## LANGID

A language identifier.

```c
// WinNT.h

typedef WORD LANGID;
```

## LCID

A locale identifier.

```c
// WinNT.h

typedef DWORD LCID;
```

## LCTYPE

A locale information type.

```c
// WinNls.h

typedef DWORD LCTYPE;
```

## LGRPID

A language group identifier.

```c
// WinNls.h

typedef DWORD LGRPID;
```

## LONG

A 32-bit signed integer. The range is -2147483648 through 2147483647 decimal.

```c
// WinNT.h

typedef long LONG;
```

## LONGLONG

A 64-bit signed integer. The range is -9223372036854775808 through 9223372036854775807 decimal.

```c
// WinNT.h

#if !defined(_M_IX86)
 typedef __int64 LONGLONG;
#else
 typedef double LONGLONG;
#endif
```

## LONG_PTR

A signed long type for pointer precision. Use when casting a pointer to a long to perform pointer arithmetic.

```c
// BaseTsd.h

#if defined(_WIN64)
 typedef __int64 LONG_PTR;
#else
 typedef long LONG_PTR;
#endif
```

## LONG32

A 32-bit signed integer. The range is -2147483648 through 2147483647 decimal.

```c
// BaseTsd.h

typedef signed int LONG32;
```

## LONG64

A 64-bit signed integer. The range is -9223372036854775808 through 9223372036854775807 decimal.

```c
// BaseTsd.h

typedef __int64 LONG64;
```

## LPARAM

A message parameter.

```c
// WinDef.h

typedef LONG_PTR LPARAM;
```

## LPBOOL

A pointer to a [BOOL](#bool).

```c
// WinDef.h

typedef BOOL far *LPBOOL;
```

## LPBYTE

A pointer to a [BYTE](#byte).

```c
// WinDef.h

typedef BYTE far *LPBYTE;
```

## LPCOLORREF

A pointer to a [COLORREF](#colorref) value.

```c
// WinDef.h

typedef DWORD *LPCOLORREF;
```

## LPCSTR

A pointer to a constant null-terminated string of 8-bit Windows (ANSI) characters.

```c
// WinNT.h

typedef __nullterminated CONST CHAR *LPCSTR;
```

## LPCTSTR

An [LPCWSTR](#lpcwstr) if [UNICODE](./unicode_ansi.md) is defined, an [LPCSTR](#lpcstr) otherwise.

```c
// WinNT.h

#ifdef UNICODE
 typedef LPCWSTR LPCTSTR;
#else
 typedef LPCSTR LPCTSTR;
#endif
```

## LPCVOID

A pointer to a constant of any type.

```c
// WinDef.h

typedef CONST void *LPCVOID;
```

## LPCWSTR

A pointer to a constant null-terminated string of 16-bit Unicode characters.

```c
// WinNT.h

typedef CONST WCHAR *LPCWSTR;
```

## LPDWORD

A pointer to a [DWORD](#dword).

```c
// WinDef.h

typedef DWORD *LPDWORD;
```

## LPHANDLE

A pointer to a [HANDLE](#handle).

```c
// WinDef.h

typedef HANDLE *LPHANDLE;
```

## LPINT

A pointer to an [INT](#int).

```c
// WinDef.h

typedef int *LPINT;
```

## LPLONG

A pointer to a [LONG](#long).

```c
// WinDef.h

typedef long *LPLONG;
```

## LPSTR

A pointer to a null-terminated string of 8-bit Windows (ANSI) characters.

```c
// WinNT.h

typedef CHAR *LPSTR;
```

## LPTSTR

An [LPWSTR](#lpwstr) if [UNICODE](./unicode_ansi.md) is defined, an [LPSTR](#lpstr) otherwise.

```c
// WinNT.h

#ifdef UNICODE
 typedef LPWSTR LPTSTR;
#else
 typedef LPSTR LPTSTR;
#endif
```

## LPVOID

A pointer to any type.

```c
// WinDef.h

typedef void *LPVOID;
```

## LPWORD

A pointer to a [WORD](#word).

```c
// WinDef.h

typedef WORD *LPWORD;
```

## LPWSTR

A pointer to a null-terminated string of 16-bit Unicode characters.

```c
// WinNT.h

typedef WCHAR *LPWSTR;
```

## LRESULT

Signed result of message processing.

```c
// WinDef.h

typedef LONG_PTR LRESULT;
```

## PBOOL

A pointer to a [BOOL](#bool).

```c
// WinDef.h

typedef BOOL *PBOOL;
```

## PBOOLEAN

A pointer to a [BOOLEAN](#boolean).

```c
// WinNT.h

typedef BOOLEAN *PBOOLEAN;
```

## PBYTE

A pointer to a [BYTE](#byte).

```c
// WinDef.h

typedef BYTE *PBYTE;
```

## PCHAR

A pointer to a [CHAR](#char).

```c
// WinNT.h

typedef CHAR *PCHAR;
```

## PCSTR

A pointer to a constant null-terminated string of 8-bit Windows (ANSI) characters.

```c
// WinNT.h

typedef CONST CHAR *PCSTR;
```

## PCTSTR

A [PCWSTR](#pcwstr) if [UNICODE](./unicode_ansi.md) is defined, a [PCSTR](#pcstr) otherwise.

```c
// WinNT.h

#ifdef UNICODE
 typedef LPCWSTR PCTSTR;
#else
 typedef LPCSTR PCTSTR;
#endif
```

## PCWSTR

A pointer to a constant null-terminated string of 16-bit Unicode characters.

```c
// WinNT.h

typedef CONST WCHAR *PCWSTR;
```

## PDWORD

A pointer to a [DWORD](#dword).

```c
// WinDef.h

typedef DWORD *PDWORD;
```

## PDWORDLONG

A pointer to a [DWORDLONG](#dwordlong).

```c
// WinNT.h

typedef DWORDLONG *PDWORDLONG;
```

## PDWORD_PTR

A pointer to a [DWORD_PTR](#dword_ptr).

```c
// BaseTsd.h

typedef DWORD_PTR *PDWORD_PTR;
```

## PDWORD32

A pointer to a [DWORD32](#dword32).

```c
// BaseTsd.h

typedef DWORD32 *PDWORD32;
```

## PDWORD64

A pointer to a [DWORD64](#dword64).

```c
// BaseTsd.h

typedef DWORD64 *PDWORD64;
```

## PFLOAT

A pointer to a [FLOAT](#float).

```c
// WinDef.h

typedef FLOAT *PFLOAT;
```

## PHALF_PTR

A pointer to a [HALF_PTR](#half_ptr).

```c
// BaseTsd.h

#ifdef _WIN64
 typedef HALF_PTR *PHALF_PTR;
#else
 typedef HALF_PTR *PHALF_PTR;
#endif
```

## PHANDLE

A pointer to a [HANDLE](#handle).

```c
// WinNT.h

typedef HANDLE *PHANDLE;
```

## PHKEY

A pointer to an [HKEY](#hkey).

```c
// WinDef.h

typedef HKEY *PHKEY;
```

## PINT

A pointer to an [INT](#int).

```c
// WinDef.h

typedef int *PINT;
```

## PINT_PTR

A pointer to an [INT_PTR](#int_ptr).

```c
// BaseTsd.h

typedef INT_PTR *PINT_PTR;
```

## PINT8

A pointer to an [INT8](#int8).

```c
// BaseTsd.h

typedef INT8 *PINT8;
```

## PINT16

A pointer to an [INT16](#int16).

```c
// BaseTsd.h

typedef INT16 *PINT16;
```

## PINT32

A pointer to an [INT32](#int32).

```c
// BaseTsd.h

typedef INT32 *PINT32;
```

## PINT64

A pointer to an [INT64](#int64).

```c
// BaseTsd.h

typedef INT64 *PINT64;
```

## PLCID

A pointer to an [LCID](#lcid).

```c
// WinNT.h

typedef PDWORD PLCID;
```

## PLONG

A pointer to a [LONG](#long).

```c
// WinNT.h

typedef LONG *PLONG;
```

## PLONGLONG

A pointer to a [LONGLONG](#longlong).

```c
// WinNT.h

typedef LONGLONG *PLONGLONG;
```

## PLONG_PTR

A pointer to a [LONG_PTR](#long_ptr).

```c
// BaseTsd.h

typedef LONG_PTR *PLONG_PTR;
```

## PLONG32

A pointer to a [LONG32](#long32).

```c
// BaseTsd.h

typedef LONG32 *PLONG32;
```

## PLONG64

A pointer to a [LONG64](#long64).

```c
// BaseTsd.h

typedef LONG64 *PLONG64;
```

## POINTER_32

A 32-bit pointer. On a 32-bit system, this is a native pointer. On a 64-bit system, this is a truncated 64-bit pointer.

```c
// BaseTsd.h

#if defined(_WIN64)
#define POINTER_32 __ptr32
#else
#define POINTER_32
#endif
```

## POINTER_64

A 64-bit pointer. On a 64-bit system, this is a native pointer. On a 32-bit system, this is a sign-extended 32-bit pointer.

Note that it is not safe to assume the state of the high pointer bit.

```c
// BaseTsd.h

#if (_MSC_VER >= 1300) // MSVC version 13.00
#define POINTER_64 __ptr64
#else
#define POINTER_64
#endif
```

## POINTER_SIGNED

A signed pointer.

```c
// BaseTsd.h

#define POINTER_SIGNED __sptr
```

## POINTER_UNSIGNED

An unsigned pointer.

```c
// BaseTsd.h

#define POINTER_UNSIGNED __uptr
```

## PSHORT

A pointer to a [SHORT](#short).

```c
// WinNT.h

typedef SHORT *PSHORT;
```

## PSIZE_T

A pointer to a [SIZE_T](#size_t).

```c
// BaseTsd.h

typedef SIZE_T *PSIZE_T;
```

## PSSIZE_T

A pointer to a [SSIZE_T](#ssize_t).

```c
// BaseTsd.h

typedef SSIZE_T *PSSIZE_T;
```

## PSTR

A pointer to a null-terminated string of 8-bit Windows (ANSI) characters.

```c
// WinNT.h

typedef CHAR *PSTR;
```

## PTBYTE

A pointer to a [TBYTE](#tbyte).

```c
// WinNT.h

typedef TBYTE *PTBYTE;
```

## PTCHAR

A pointer to a [TCHAR](#tchar).

```c
// WinNT.h

typedef TCHAR *PTCHAR;
```

## PTSTR

A [PWSTR](#pwstr) if [UNICODE](./unicode_ansi.md) is defined, a [PSTR](#pstr) otherwise.

```c
// WinNT.h

#ifdef UNICODE
 typedef LPWSTR PTSTR;
#else typedef LPSTR PTSTR;
#endif
```

## PUCHAR

A pointer to a [UCHAR](#uchar).

```c
// WinDef.h

typedef UCHAR *PUCHAR;
```

## PUHALF_PTR

A pointer to a [UHALF_PTR](#uhalf_ptr).

```c
// BaseTsd.h

#ifdef _WIN64
 typedef UHALF_PTR *PUHALF_PTR;
#else
 typedef UHALF_PTR *PUHALF_PTR;
#endif
```

## PUINT

A pointer to a [UINT](#uint).

```c
// WinDef.h

typedef UINT *PUINT;
```

## PUINT_PTR

A pointer to a [UINT_PTR](#uint_ptr).

```c
// BaseTsd.h

typedef UINT_PTR *PUINT_PTR;
```

## PUINT8

A pointer to a [UINT8](#uint8).

```c
// BaseTsd.h

typedef UINT8 *PUINT8;
```

## PUINT16

A pointer to a [UINT16](#uint16).

```c
// BaseTsd.h

typedef UINT16 *PUINT16;
```

## PUINT32

A pointer to a [UINT32](#uint32).

```c
// BaseTsd.h

typedef UINT32 *PUINT32;
```

## PUINT64

A pointer to a [UINT64](#uint64).

```c
// BaseTsd.h

typedef UINT64 *PUINT64;
```

## PULONG

A pointer to a [ULONG](#ulong).

```c
// WinDef.h

typedef ULONG *PULONG;
```

## PULONGLONG

A pointer to a [ULONGLONG](#ulonglong).

```c
// WinDef.h

typedef ULONGLONG *PULONGLONG;
```

## PULONG_PTR

A pointer to a [ULONG_PTR](#ulong_ptr).

```c
// BaseTsd.h

typedef ULONG_PTR *PULONG_PTR;
```

## PULONG32

A pointer to a [ULONG32](#ulong32).

```c
// BaseTsd.h

typedef ULONG32 *PULONG32;
```

## PULONG64

A pointer to a [ULONG64](#ulong64).

```c
// BaseTsd.h

typedef ULONG64 *PULONG64;
```

## PUSHORT

A pointer to a [USHORT](#ushort).

```c
// WinDef.h

typedef USHORT *PUSHORT;
```

## PVOID

A pointer to any type.

```c
// WinNT.h

typedef void *PVOID;
```

## PWCHAR

A pointer to a [WCHAR](#wchar).

```c
// WinNT.h

typedef WCHAR *PWCHAR;
```

## PWORD

A pointer to a [WORD](#word).

```c
// WinDef.h

typedef WORD *PWORD;
```

## PWSTR

A pointer to a null-terminated string of 16-bit Unicode characters.

```c
// WinNT.h

typedef WCHAR *PWSTR;
```

## QWORD

A 64-bit unsigned integer (Quad word, 4x [WORD](#word)).

[ULONGLONG](#ulonglong) is preferred today.

```c
// Defined in multiple headers: Sspi.h, WinDNS.h, wmsdkidl.h ...etc.

typedef unsigned __int64 QWORD;
```

## SC_HANDLE

A handle to a service control manager database.

```c
// WinSvc.h

typedef HANDLE SC_HANDLE;
```

## SC_LOCK

A lock to a service control manager database.

```c
// WinSvc.h

typedef LPVOID SC_LOCK;
```

## SERVICE_STATUS_HANDLE

A handle to a service status value.

```c
// WinSvc.h

typedef HANDLE SERVICE_STATUS_HANDLE;
```

## SHORT

A 16-bit integer. The range is -32768 through 32767 decimal.

```c
// WinNT.h

typedef short SHORT;
```

## SIZE_T

The maximum number of bytes to which a pointer can point. Use for a count that must span the full range of a pointer.

```c
// BaseTsd.h

typedef ULONG_PTR SIZE_T;
```

## SSIZE_T

A signed version of [SIZE_T](#size_t).

```c
// BaseTsd.h

typedef LONG_PTR SSIZE_T;
```

## TBYTE

A [WCHAR](#wchar) if [UNICODE](./unicode_ansi.md) is defined, a [CHAR](#char) otherwise.

```c
// BaseTsd.h

#ifdef UNICODE
 typedef WCHAR TBYTE;
#else
 typedef unsigned char TBYTE;
#endif
```

## TCHAR

A [WCHAR](#wchar) if [UNICODE](./unicode_ansi.md) is defined, a [CHAR](#char) otherwise.

```c
// WinNT.h

#ifdef UNICODE
 typedef WCHAR TCHAR;
#else
 typedef char TCHAR;
#endif
```

## UCHAR

An unsigned [CHAR](#char).

```c
// WinDef.h

typedef unsigned char UCHAR;
```

## UHALF_PTR

An unsigned [HALF_PTR](#half_ptr). Use within a structure that contains a pointer and two small fields.

```c
// BaseTsd.h

#ifdef _WIN64
 typedef unsigned int UHALF_PTR;
#else
 typedef unsigned short UHALF_PTR;
#endif
```

## UINT

An unsigned [INT](#int). The range is 0 through 4294967295 decimal.

```c
// WinDef.h

typedef unsigned int UINT;
```

## UINT_PTR

An unsigned [INT_PTR](#int_ptr).

```c
// BaseTsd.h

#if defined(_WIN64)
 typedef unsigned __int64 UINT_PTR;
#else
 typedef unsigned int UINT_PTR;
#endif
```

## UINT8

An unsigned [INT8](#int8).

```c
// BaseTsd.h

typedef unsigned char UINT8;
```

## UINT16

An unsigned [INT16](#int16).

```c
// BaseTsd.h

typedef unsigned short UINT16;
```

## UINT32

An unsigned [INT32](#int32). The range is 0 through 4294967295 decimal.

```c
// BaseTsd.h

typedef unsigned int UINT32;
```

## UINT64

An unsigned [INT64](#int64). The range is 0 through 18446744073709551615 decimal.

```c
// BaseTsd.h

typedef unsigned __int64 UINT64;
```

## ULONG

An unsigned [LONG](#long). The range is 0 through 4294967295 decimal.

```c
// WinDef.h

typedef unsigned long ULONG;
```

## ULONGLONG

A 64-bit unsigned integer. The range is 0 through 18446744073709551615 decimal.

```c
// WinNT.h

#if !defined(_M_IX86)
 typedef unsigned __int64 ULONGLONG;
#else
 typedef double ULONGLONG;
#endif
```

## ULONG_PTR

An unsigned [LONG_PTR](#long_ptr).

```c
// BaseTsd.h

#if defined(_WIN64)
 typedef unsigned __int64 ULONG_PTR;
#else
 typedef unsigned long ULONG_PTR;
#endif
```

## ULONG32

An unsigned [LONG32](#long32). The range is 0 through 4294967295 decimal.

```c
// BaseTsd.h

typedef unsigned int ULONG32;
```

## ULONG64

An unsigned [LONG64](#long64). The range is 0 through 18446744073709551615 decimal.

```c
// BaseTsd.h

typedef unsigned __int64 ULONG64;
```

## UNICODE_STRING

A Unicode string.

```c
// Winternl.h

typedef struct _UNICODE_STRING {
  USHORT  Length;
  USHORT  MaximumLength;
  PWSTR  Buffer;
} UNICODE_STRING;
typedef UNICODE_STRING *PUNICODE_STRING;
typedef const UNICODE_STRING *PCUNICODE_STRING;
```

## USHORT

An unsigned [SHORT](#short). The range is 0 through 65535 decimal.

```c
// WinDef.h

typedef unsigned short USHORT;
```

## USN

An update sequence number (USN).

```c
// WinNT.h

typedef LONGLONG USN;
```

## VOID

Any type.

```c
// WinNT.h

#define VOID void
```

## WCHAR

A 16-bit Unicode character.

```c
// WinNT.h

typedef wchar_t WCHAR;
```

## WINAPI

The calling convention for system functions.

Most functions in the Windows API are declared using WINAPI.

```c
// WinDef.h

#define WINAPI __stdcall
```

## WORD

A 16-bit unsigned integer. The range is 0 through 65535 decimal.

```c
// WinDef.h

typedef unsigned short WORD;
```

## WPARAM

A message parameter.

```c
// WinDef.h

typedef UINT_PTR WPARAM;
```
