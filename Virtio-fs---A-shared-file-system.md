## Overview

Virtio-fs for Windows is a user mode file system, implemented using [WinFsp](https://github.com/billziss-gh/winfsp), that lets virtual machines access a directory tree on the host.

## Status

Virtio-fs is at an early stage of development and should be considered as a "Tech Preview" feature.

## Testing

### WinFsp Tests

The following command line is used to execute WinFsp's test suite:

```
winfsp-tests-x64.exe --external --resilient -create_fileattr_test -create_readonlydir_test -create_allocation_test -create_notraverse_test -getfileattr_test -getfileinfo_test -getfileinfo_name_test -setfileinfo_test -setsecurity_test -reparse_guid_test -reparse_nfs_test -stream_*
```

Virtio-Fs for Windows does not currently pass all available tests so some of them are skipped for now:

| Test Name | Failure Description |
|---|---|
| create_fileattr_test |   |
| create_readonlydir_test |   |
| create_allocation_test |   |
| create_notraverse_test |   |
| getfileattr_test |   |
| getfileinfo_test |   |
| getfileinfo_name_test  |   |
| setfileinfo_test |   |
| setsecurity_test |   |
| reparse_guid_test |   |
| reparse_nfs_test |   |
| stream_* | NTFS-like file streams are not supported (Need to decide where and how the information is stored. |



