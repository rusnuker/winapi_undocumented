
## ntdll.dll,NtRaiseHardError, #448 (0x1С0)
This function sends a HARDERROR_MSG message to the LPC listener (csrss.exe) that shows a message box.

LOL, just kidding, this function can do much more!! For example, it can... bluescreen the system.

**Note:** be careful with this. It's hard to believe, but this function raises a **real** BSOD on your system.

## Compatibility

I think Win2K and higher. I guess that's more than u need, eh?

## Close Definition
Yes, I took it from ReactOS docs. Any problems?
```
NTSYSAPI NTSTATUS NTAPI NtRaiseHardError
(
  IN NTSTATUS ErrorStatus, // Error code (later on about them)
  IN ULONG NumberOfParameters, // Leave to 1 for a text string, or 0 for no text.
  IN ULONG UnicodeStringParameterMask, // Leave to 1 for a text string, or 0 for no text.
  IN PULONG_PTR Parameters, // A pointer to an array with text (leave to NULL for no text)
  IN ULONG ValidResponseOptions, // Leave to 6 for a reboot lol
  OUT PULONG Response // A pointer to DWORD that's gonna get a response value.
)
```
#### Hard error codes:
```
0xC0000022 -- Access denied (MEMZ)
0xC0000069 -- Deleting Last Admin account (???)
0xC0000144 -- Unhandled application exception (HACKERV tutorial)
0xC0000228 -- Stack overflow read (???)
0xC0000420 -- Assertion failed 
```
Note that 0xF4 and 0x7F won't work (i tried).

## Calling
To call it, use something like that (IT IS IN C):

```
#include<Windows.h>   // Load up WinAPI defs
#include <ntdef.h>		// NTAPI functions
#include <stdbool.h>  // bool values for C99

#define HARDERR_MEMZ		0xC0000022 // Access Denied
#define HARDERR_6IX_9INE	0xC0000069 // Deleting Last Admin
#define HARDERR_HACKERV_TUTOR	0xC0000144 // Unhandled Exception 
#define HARDERR_TWOTWOEIGHT	0xC0000228 // Stack Overflow Read
#define HARDERR_GET_REKT_NOOB	0xC0000420 // Assertion Failed

// Function load macro
#define GetLibFunc(a, b) GetProcAddress(LoadLibraryA(a), b)

void AdjTokPriv(LPCSTR lpszPriv) // by Napalm
{ 
	HANDLE hToken;
	TOKEN_PRIVILEGES tkprivs;
	// Get current process token so we could do shit
	OpenProcessToken(GetCurrentProcess(), (TOKEN_ADJUST_PRIVILEGES | TOKEN_QUERY), &hToken);
	// Get a privilege name
	LookupPrivilegeValue(NULL, lpszPriv, &tkprivs.Privileges[0].Luid);
	tkprivs.PrivilegeCount = 1;
	tkprivs.Privileges[0].Attributes = SE_PRIVILEGE_ENABLED; // Set as ENABLED
	// Get a token
	AdjustTokenPrivileges(hToken, false, &tkprivs, sizeof(tkprivs), NULL, NULL);
	CloseHandle(hToken); // fuck off
}

void CauseBlueScreen(NTSTATUS code) {
	DWORD tmp2; // this one is gonna hold a response
	// Load up NTDLL and get some functions
	FARPROC RtlInitUnicodeString = GetLibFunc("ntdll", "RtlInitUnicodeString");
	FARPROC NtRaiseHardError = GetLibFunc("ntdll", "NtRaiseHardError");
	UNICODE_STRING uText;
	// init custom strings
	RtlInitUnicodeString(&uText, L"\r TEXT1\nTEST TEST\n"); // init a string with custom text
	ULONG Parameters[] = {(ULONG)&uText}; // include params
	AdjTokPriv(SE_SHUTDOWN_NAME); // get a privilege to halt the system
	NtRaiseHardError(code, 1, 1, Parameters, 6, &tmp2); // slap csrss with a message
	// uh :skull:
}

int main() {
	CauseBlueScreen(HARDERR_MEMZ); // Call a custom function with custom eror code
}
```

## Return value
It is DWORD; 0x0 on success and non-zero on error. I guess you can use GetLastError() (but why tho??)
