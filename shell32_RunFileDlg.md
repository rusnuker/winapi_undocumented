## RunFileDlg, #61 (0x3D)
This function has no name, but it has an ordinary value.
Also note that this function is <b>undocumented</b>. That means: a) it has no Microsoft Docs description and b) it could be removed in future releases of Windows.

### Note: This function accepts only WIDECHAR!

## Compatibility

I think Win2K and higher. I guess that's more than u need, eh?

## Close Definition
Yes, I took it from ReactOS docs. Any problems?
```
int RunFileDlg
(
  HWND hwndOwner, // The owner window handle (could be NULL, no parent window)
  HICON hIcon, // The icon handle. Use LoadIcon (not LoadImage) to set it or set it to NULL for a default icon.
  LPCWSTR lpstrDirectory, // A [full] path to the [working?] directory. Leave to NULL for [???]
  LPCWSTR lpstrTitle, // A title. Leave to NULL for default title.
  LPCWSTR lpstrDescription, // A text. Leave to NULL for default text.
  UINT uFlags // Flags (later on)
)
```
Flags:
```
#define RFF_NOBROWSE 1       // Removes the browse button.
#define RFF_NODEFAULT 2      // No default item (recent command) selected.
#define RFF_CALCDIRECTORY 4  // Calculates the working directory from the file name.
#define RFF_NOLABEL 8        // Removes the edit box label.
#define RFF_NOSEPARATEMEM 14 // Removes the Separate Memory Space check box (Windows NT only).
```
## Calling
To call it, use something like that (IT IS IN C):

```
// OEM resources defined in winuser.h
#define OEMRESOURCE

#include <windows.h> // Hooking up WinAPI

// Function load macro
#define GetLibFunc(a, b) GetProcAddress(LoadLibraryA(a), b)

// No recent command selected
#define RFF_NODEFAULT 2


int main() {
	HICON hIcon = LoadIcon(0, MAKEINTRESOURCE(OIC_NOTE)); // Load an OEM "information" icon
	FARPROC RunFileDlg = GetLibFunc("shell32", MAKEINTRESOURCE(61)); // Get a function with its ordinary value (remember, RunFileDlg has no name)
	
	RunFileDlg(0, // No owner window
		hIcon, // A loaded icon
		0, // Default directory or something
		L"uhhhhh what", // title
		L"press something and ok to run", // text
		RFF_NODEFAULT // No recent command selected
	);
}
```

## Return value
It is normal integer; 0 on success and non-zero on error. No idea if GetLastError() will work.
