# Ordering of Function Calls in Sequence

The code block below shows the suggested ordering of VixDiskLib function calls in sequence, including older function calls in combination with newer ones \(Ex and Access\).

For backup, the open, read, and close calls are made on virtual machine snapshots.

```text
VixDiskLib_InitEx()
VixDiskLib_PrepareForAccess()
VixDiskLib_ConnectEx()
VixDiskLib_Open()
VixDiskLib_Read()
VixDiskLib_Close()
VixDiskLib_Disconnect() 
VixDiskLib_EndAccess()
VixDiskLib_CleanUp()
VixDiskLib_Exit()
```

