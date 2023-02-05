## Control_RunDLL, #255 (0xFF)

Run any .CPL (control panel components) files.

## Compatibility

I think Win2K and higher. I guess that's more than u need, eh?

## Close Definition
```
BOOL Control_RunDLL // Is it really bool? It always returns 1
(
  HWND hWnd, // A handle to a parent window. If this parameter is NULL, the DLL has no owner window.
  HINSTANCE hInstance, // An instance handle of the DLL (???). Always (?) NULL.
  LPCSTR lpCmdLine, // .CPL file name (file path).
  int nCmdShow // Show state. Leave to NULL.
)
```
## Calling
To call it, use something like this (IT IS IN C):

```
#include <windows.h> // Hooking up WinAPI

// Function load macro
#define GetLibFunc(a, b) GetProcAddress(LoadLibraryA(a), b)

int main() {
	FARPROC Control_RunDLL = GetLibFunc("shell32.dll", "Control_RunDLL"); // Get a function
	Control_RunDLL(0, 0, "timedate.cpl", 0); // Show a "Date and time" window
}
```

## Return value
Seems like it's always 1. Maybe it returns nothing, I have no idea.
