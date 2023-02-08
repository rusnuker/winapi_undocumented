## NtTerminateProcess, #546 (0x222)

This function causes the specified process and all of its threads to terminate.

## Compatibility

I think Win2K and higher.

## Close Definition
```C
NTSTATUS NtTerminateProcess
(
  IN HANDLE ProcessHandle OPTIONAL, // Supplies a handle to the process to terminate. NULL to selfkill.
  IN NTSTATUS ExitStatus // Supplies the exit status associated with the process. Leave to NULL
)
```
## Calling
To call it, use something like this (IT IS IN C):

```C
#include <windows.h> // Hooking up WinAPI
#include <tlhelp32.h> // Working with PID
#include <ntdef.h> // Some NTDLL definitions

// Function load macro
#define GetLibFunc(a, b) GetProcAddress(LoadLibraryA(a), b)

// Get a process ID from process name.
// Returns a DWORD value containing a destination process' ID
// or NULL on error.
DWORD GetPIDFromName(char * pProcName) {
	HANDLE snapshot_proc = CreateToolhelp32Snapshot(TH32CS_SNAPPROCESS, 0);
	if (snapshot_proc == INVALID_HANDLE_VALUE) return 0; // Maybe the access rights are fucked?

	PROCESSENTRY32 ProcessEntry;
	ProcessEntry.dwSize = sizeof(ProcessEntry);
	DWORD pid;

	if (Process32First(snapshot_proc, &ProcessEntry)) {
	while (Process32Next(snapshot_proc, &ProcessEntry)) {
		// Skip until found
		if (!stricmp(ProcessEntry.szExeFile, pProcName)) { // PID found!
			CloseHandle(snapshot_proc); pid = ProcessEntry.th32ProcessID; return pid;
		}

	}
	}

	CloseHandle(snapshot_proc);
	return 0; // Most likely no process was found.
}

// Open a process with specified access and get its handle.
// Returns a HANDLE value or NULL on error.
// Be careful, make sure to check if
// GetProcessHandleWithAccess returned NULL, because
// process handle 0x00 is a SYSTEM process that cannot be accessed.
HANDLE GetProcessHandleWithAccess(DWORD pid, DWORD access)
{
	HANDLE hProcess = OpenProcess(access, FALSE, pid);

	if (!hProcess) return 0; // Return 0 on error.

	return hProcess;
}

// Kill a process using PID.
// KillProcess(GetPIDFromName("explorer.exe"));
int KillProcess (DWORD pid)
{
	NTSTATUS KillStatus;
	FARPROC NtTerminateProcess = GetLibFunc("ntdll.dll", "NtTerminateProcess");
	HANDLE hProc = GetProcessHandleWithAccess(pid, PROCESS_TERMINATE); // Requesting only the killing access
	KillStatus = NtTerminateProcess(hProc, 0);
	return (NT_SUCCESS(KillStatus) == true ? 0 : 1);
}

int main() {
	KillProcess(GetPIDFromName("wscript.exe")); // Kill a VBScript host process
}
```

## Return value
NTSTATUS. 99% of the time -- STATUS_SUCCESS. Use NT_SUCCESS().
