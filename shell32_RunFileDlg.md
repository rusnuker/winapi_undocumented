## RunFileDlg, #61 (0x3D)
This function has no name, but it has an ordinary value.
Also note that this function is <b>undocumented</b>. That means: a) it has no Microsoft Docs description and b) it could be removed in future releases of Windows.

### Note: This function accepts only WIDECHAR!

## Compatibility

I think Win2K and higher. I guess that's more than u need, eh?

## Close Definition
All the arguments can be NULL if you want to.
```
STDAPI_(int) RunFileDlg
(
  HWND hwndParent, // A handle to the parent window. Set to NULL to no parent window.
  HICON hIcon, // Custom icon handle generated with LoadIcon (not LoadImage) or NULL for the default icon.
  LPCWSTR pszWorkingDir, // I DON'T THINK THIS MEANS ANYTHING. Set to NULL.
  LPCWSTR pszTitle, // Custom title or NULL for default.
  LPCWSTR pszPrompt, // Custom description or NULL for default.
  DWORD dwFlags // Flags. 
)
```
Flags:
```
#define RFF_NOBROWSE 1       // Removes the "Browse..." button.
#define RFF_NODEFAULT 2      // No default item (recent command) selected.
#define RFF_CALCDIRECTORY 4  // Calculates the working directory from the file name.
#define RFF_NOLABEL 8        // ~~Removes the edit box label~~ Removes the "Open: " lable.
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

// No recent command selected.
#define RFF_NODEFAULT 2

// Removes the "Browse..." button.
#define RFF_NOBROWSE 1

int main() {
	HICON hIcon = LoadIcon(0, MAKEINTRESOURCE(OIC_NOTE)); // Load an OEM "information" icon
	FARPROC RunFileDlg = GetLibFunc("shell32", MAKEINTRESOURCE(61)); // Get a function with its ordinary value (remember, RunFileDlg has no name)
	
	RunFileDlg(0, // No owner window
		hIcon, // A loaded icon
		0, // Default directory or something
		L"uhhhhh what", // title
		L"press something and ok to run", // text
		RFF_NODEFAULT | RFF_NOBROWSE // No recent command selected and no Browse button.
	);
}
```

## Return value
It is normal integer; 0 on success and non-zero on error.
