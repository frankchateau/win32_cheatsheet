# Component Object Model (COM)

## Header files

| Name                   | Description |
| ---------------------- | ----------- |
| accctrl.h              | /           |
| callobj.h              | /           |
| combaseapi.h           | /           |
| comcat.h               | /           |
| ctxtcall.h             | /           |
| dmerror.h              | /           |
| docobj.h               | /           |
| eventsys.h             | /           |
| guiddef.h              | /           |
| iaccess.h              | /           |
| imessagedispatcher.h   | /           |
| messagedispatcherapi.h | /           |
| objbase.h              | /           |
| objidl.h               | /           |
| objidlbase.h           | /           |
| ocidl.h                | /           |
| ole.h                  | /           |
| ole2.h                 | /           |
| oledlg.h               | /           |
| oleidl.h               | /           |
| roapi.h                | /           |
| rpcdce.h               | /           |
| servprov.h             | /           |
| shobjidl.h             | /           |
| txlogpub.h             | /           |
| unknwn.h               | /           |
| unknwnbase.h           | /           |
| urlmon.h               | /           |
| vbinterf.h             | /           |
| winddi.h               | /           |
| winerror.h             | /           |
| wtypesbase.h           | /           |

## CoCreateInstance

Creates and default-initializes a single object of the class associated with a specified `CLSID`.

Call CoCreateInstance when you want to create only one object on the local system. To create a single object on a remote system, call the `CoCreateInstanceEx` function. To create multiple objects based on a single `CLSID`, call the `CoGetClassObject` function.

```cpp
HRESULT hr = CoCreateInstance(...);
```

## IUnknown

Enables clients to get pointers to other interfaces on a given object through the `QueryInterface` method, and manage the existence of the object through the [AddRef](#iunknownaddref) and Release methods.

All other COM interfaces are inherited, directly or indirectly, from `IUnknown`. Therefore, the three methods in `IUnknown` are the first entries in the vtable for every interface.

### IUnknown::AddRef

Increments the reference count for an interface pointer to a COM object. You should call this method whenever you make a copy of an interface pointer.

```cpp
ULONG newRefCount = pUnknown->AddRef();
```

### IUnknown::QueryInterface

Queries a COM object for a pointer to one of its interface; identifying the interface by a reference to its [IID](#iid). If the COM object implements the interface, then it returns a pointer to that interface after calling [IUnknown::AddRef](#iunknownaddref) on it.

```cpp
HRESULT hr = pUnknown->QueryInterface(&pInterface); // template function

HRESULT hr = pUnknown->QueryInterface(riid, &pInterface); // function
```

### IUnknown::Release

Decrements the reference count for an interface on a COM object.

```cpp
ULONG newRefCount = pUnknown->Release();
```

## IEnumUnknown

Enumerates objects with the [IUnknown](#iunknown) interface. It can be used to enumerate through the objects in a component containing multiple objects.

Inherits from the [IUnknown](#iunknown) interface.

### IEnumUnknown::Clone

// TODO

### IEnumUnknown::Next

// TODO

### IEnumUnknown::Reset

// TODO

### IEnumUnknown::Skip

// TODO

## Types

### CLSID

Class identifier.

```c
// guiddef.h

typedef GUID CLSID;
```

### GUID

A `GUID` identifies an object such as a COM interfaces, or a COM class object, or a manager entry-point vector (EPV).

A `GUID` is a 128-bit value consisting of one group of 8 hexadecimal digits, followed by three groups of 4 hexadecimal digits each, followed by one group of 12 hexadecimal digits. The following example GUID shows the groupings of hexadecimal digits in a GUID: `6B29FC40-CA47-1067-B31D-00DD010662DA`.

```c
// guiddef.h

typedef struct _GUID {
    unsigned long  Data1;
    unsigned short Data2;
    unsigned short Data3;
    unsigned char  Data4[8];
} GUID;
```

### IID

Interface identifier.

```c
// guiddef.h

typedef GUID IID;
```

## Macros

### IsEqualCLSID

Evaluates to a [BOOL](./types.md#bool) value that indicates whether two [CLSID](#clsid)s are equal.

```cpp
#include <guiddef.h>

BOOL areEqual = IsEqualCLSID(clsid1, clsid2)
```

### IsEqualGUID

Evaluates to a [BOOL](./types.md#bool) value that indicates whether two [GUID](#guid)s are equal.

Used by the [IsEqualCLSID](#isequalclsid) and [IsEqualIID](#isequaliid) macros.

```cpp
#include <guiddef.h>

BOOL areEqual = isEqualGUID(guid1, guid2);
```

### IsEqualIID

Evaluates to a [BOOL](./types.md#bool) value that indicates whether two [IID](#iid)s are equal.

```cpp
#include <guiddef.h>

BOOL areEqual = IsEqualIID(iid1, iid2);
```
