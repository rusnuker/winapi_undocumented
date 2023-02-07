## FrostCrashedWindow, #1764 (0x6E4)

Turn a window into a hung gray "ghost".

![image](https://i.imgur.com/bhILbHH.gif)

## Compatibility

I think Vista (???) and higher.
Didn't find any sources in Windows XP sources, though.

## Close Definition
```C
HWND WINAPI FrostCrashedWindow
(
  HWND hwndToReplace, // A handle to a target window
  HWND hwndErrorReportOwnerWnd // IDK, just leave it NULL
)
```
## Calling
To call it, use something like this (IT IS IN C):

```C
#include <windows.h> // Hooking up WinAPI

// Function load macro
#define GetLibFunc(a, b) GetProcAddress(LoadLibraryA(a), b)

int main() {
	FARPROC FrostCrashedWindow = GetLibFunc("user32.dll", "FrostCrashedWindow"); // Get a function
	FrostCrashedWindow(GetConsoleWindow(), 0); // Freeeeze a console window
}
```

## Return value
Returns a window handle to a newly created "ghost" or NULL on error.
