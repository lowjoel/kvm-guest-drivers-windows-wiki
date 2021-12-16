## Overview

VirtIOFS is a shared file system that lets virtual machines access a directory tree on the host. More information on the underlying approach is available [here](https://virtio-fs.gitlab.io/).

## VirtIOFS for Windows

VirtIOFS for Windows is a user mode file system, implemented using [WinFsp](https://github.com/billziss-gh/winfsp). VirtIOFS for Windows consists of VirtIO-powered driver and user space service.

## Status

VirtIOFS is at an early stage of development and should be considered as a "Tech Preview" feature.

## Setup

### Host

Following XML should be added to your libvirt VM descrition:

```xml
<domain>
  ...
  <memoryBacking>
    <source type='memfd'/>
    <access mode='shared'/>
  </memoryBacking>
  ...
  <devices>
    <filesystem type="mount" accessmode="passthrough">
      <driver type="virtiofs"/>
      <source dir="/home/user/viofs"/>
      <target dir="mount_tag"/>
      <address type="pci" domain="0x0000" bus="0x06" slot="0x00" function="0x0"/>
    </filesystem>
  </devices>
  ...
</domain>
```

The `<memoryBacking>` is necessary. Element `<source dir="/home/user/viofs"/>` describes host directory to share.

More information on libvirt VirtIOFS options is provided in [libvirt docs](https://libvirt.org/kbase/virtiofs.html).

### Guest

VirtIOFS service can parse settings from command-line and from registry. When command-line arguments are absent it gets `DebugFlags` (DWORD), `DebugLogFile` (String), `MountPoint` (String) from `HKLM\Software\VirtIO-FS`. 

For example, registry values depicted below correspond to `virtiofs.exe -d -1 -D C:\viofs_debug.log -m X:`
![](https://user-images.githubusercontent.com/8286747/146226495-0d7614ca-8a7d-4465-9aa3-3dc9dc9cb6de.png)

Please note that `-1` in arguments corresponds to `0xffffffff` in DWORD value. 

More information on setup of WinFSP, VirtIOFS driver and service is [here](https://virtio-fs.gitlab.io/howto-windows.html).

## Testing

### WinFsp Tests

The following command line is used to execute WinFsp's test suite:

```
winfsp-tests-x64.exe --external --resilient -create_fileattr_test -create_readonlydir_test -create_allocation_test -create_notraverse_test -getfileattr_test -getfileinfo_test -getfileinfo_name_test -setfileinfo_test -setsecurity_test -reparse_guid_test -reparse_nfs_test -stream_*
```

Virtio-Fs for Windows does not currently pass all available tests so some of them are skipped for now:

| Test Name | Failure Description |
|---|---|
| create_fileattr_test | Can't set FILE_ATTRIBUTE_SYSTEM. |
| create_readonlydir_test | Can't create a file on a read-only directory. |
| create_allocation_test | Can't set a file allocation to zero. |
| create_notraverse_test |  |
| getfileattr_test | Not all Windows' file attributes exists. |
| getfileinfo_test | File name length is longer than expected. File system is mounted as network drive? |
| getfileinfo_name_test |  |
| setfileinfo_test | Can't remove FILE_ATTRIBUTE_ARCHIVE as it is hard-coded. |
| setsecurity_test | Not all Window's security descriptors are implemented.  |
| reparse_guid_test |  |
| reparse_nfs_test |  |
| stream_* | NTFS-like file streams are not supported. Need to decide where and how the information is stored. |
