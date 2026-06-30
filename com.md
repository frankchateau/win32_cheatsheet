# Component Object Model (COM)

## Header files

| Name                   | Description |
| ---------------------- | ----------- |
| accctrl.h              |             |
| callobj.h              |             |
| combaseapi.h           |             |
| comcat.h               |             |
| ctxtcall.h             |             |
| dmerror.h              |             |
| docobj.h               |             |
| eventsys.h             |             |
| guiddef.h              |             |
| iaccess.h              |             |
| imessagedispatcher.h   |             |
| messagedispatcherapi.h |             |
| objbase.h              |             |
| objidl.h               |             |
| objidlbase.h           |             |
| ocidl.h                |             |
| ole.h                  |             |
| ole2.h                 |             |
| oledlg.h               |             |
| oleidl.h               |             |
| roapi.h                |             |
| rpcdce.h               |             |
| servprov.h             |             |
| shobjidl.h             |             |
| txlogpub.h             |             |
| unknwn.h               |             |
| unknwnbase.h           |             |
| urlmon.h               |             |
| vbinterf.h             |             |
| winddi.h               |             |
| winerror.h             |             |
| wtypesbase.h           |             |

## CoCreateInstance

Creates and default-initializes a single object of the class associated with a specified `CLSID`.

Call CoCreateInstance when you want to create only one object on the local system. To create a single object on a remote system, call the `CoCreateInstanceEx` function. To create multiple objects based on a single `CLSID`, call the `CoGetClassObject` function.

```cpp
HRESULT hr = CoCreateInstance(...);
```

## IUnknown

Enables clients to get pointers to other interfaces on a given object through the `QueryInterface` method, and manage the existence of the object through the `AddRef` and Release methods.

All other COM interfaces are inherited, directly or indirectly, from `IUnknown`. Therefore, the three methods in `IUnknown` are the first entries in the vtable for every interface.

### IUnknown::AddRef

Increments the reference count for an interface pointer to a COM object. You should call this method whenever you make a copy of an interface pointer.

```cpp
ULONG newRefCount = pUnknown->AddRef();
```

### IUnknown::QueryInterface

Queries a COM object for a pointer to one of its interface; identifying the interface by a reference to its interface identifier (IID). If the COM object implements the interface, then it returns a pointer to that interface after calling [IUnknown::AddRef](#iunknownaddref) on it.

```cpp
HRESULT hr = pUnknown->QueryInterface(&pInterface); // template function

HRESULT hr = pUnknown->QueryInterface(riid, &pInterface); // function
```

### IUnknown::Release

Decrements the reference count for an interface on a COM object.

```cpp
ULONG newRefCount = pUnknown->Release();
```
