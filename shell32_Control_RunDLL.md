## Control_RunDLL, #255 (0xFF)

Run any .CPL (control panel components) files.

## Compatibility

I think Win2K and higher. I guess that's more than u need, eh?

## Close Definition
```
BOOL Control_RunDLL // Is it really bool? It always returns 1
(
  HWND hWnd,
  HINSTANCE hInstance, // An instance handle of the DLL (???). Always (?) NULL.
  LPCSTR lpCmdLine, // CPL file name (file path) and arguments separated by a comma.
  int nCmdShow // Show state. Leave to NULL.
)

STDAPI_(void) // I was right, it returns NOTHING
Control_RunDLL
(
  HWND hwndStub, // A handle to a parent window. If this parameter is NULL, the CPL has no owner window.
  HINSTANCE hAppInstance, // Always NULL
  LPSTR pszCmdLine, // A (full) path to the CPL file.
  int nCmdShow // This thing travels across multiple function calls, and eventually being passed to ShellExecuteEx. BUT IT DOESN'T MAKE ENY SENSE. LEAVE TO NULL
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
~~Seems like it's always 1. Maybe it returns nothing, I have no idea.~~
No return value! VOID
