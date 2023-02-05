## LockWorkStation, #2027 (0x7EB)

Send a LOGON_LOCKWORKSTATION to WinLogon to lock the current Windows session.
Similar to pressing [Win]+[L].

## Compatibility

I think Win2K and higher.

## Close Definition
```C
BOOL _LockWorkStation
(
  VOID // Yeah, no arguments
)
```
## Calling
To call it, use something like this (IT IS IN C):

```C
#include <windows.h> // Hooking up WinAPI

// Function load macro
#define GetLibFunc(a, b) GetProcAddress(LoadLibraryA(a), b)

int main() {
	FARPROC LockWorkStation = GetLibFunc("user32.dll", "LockWorkStation"); // Get a function
	LockWorkStation(); // Lock!
}
```

## Return value
Bool. Returns TRUE (1) ... always?
