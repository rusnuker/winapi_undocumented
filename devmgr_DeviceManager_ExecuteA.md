## DeviceManager_ExecuteA, #9 (0x09)

Show a Device manager.

## Compatibility

I think Win2K and higher.

## Close Definition
```C
BOOL
DeviceManager_Execute
(
  HWND      hwndStub, // Parent window. Set to NULL for no parent window.
  HINSTANCE hAppInstance, // Instance handle. Leave to NULL.
  LPCTSTR   lpMachineName, // Current machine name. Leave to NULL.
  int       nCmdShow // SW_* show mode. Leave to NULL.
)
```
## Calling
To call it, use something like this (IT IS IN C):

```C
#include <windows.h> // Hooking up WinAPI

// Function load macro
#define GetLibFunc(a, b) GetProcAddress(LoadLibraryA(a), b)

int main() {
	FARPROC DeviceManager_ExecuteA = GetLibFunc("devmgr.dll", "DeviceManager_ExecuteA"); // Get a function
	DeviceManager_ExecuteA(0, 0, 0, 0); // Run it.
}
```

## Return value
Returns TRUE (1) on success and zero on fail.
