# Error Handling

These functions enhance the usefulness of error messages.

## Return Error Description Text

VixDiskLib\_GetErrorText\(\) returns the textual description of a numeric error code.

```text
char* msg = VixDiskLib_GetErrorText(errCode, NULL);
```

## Free Error Description Text

VixDiskLib\_FreeErrorText\(\) deallocates space associated with the error description text.

```text
VixDiskLib_FreeErrorText(msg);
```

