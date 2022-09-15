# Overview

VirtIO-FS is a shared file system that lets virtual machines access a directory tree on the host. More information on the underlying approach is available at [virtio-fs.gitlab.io](https://virtio-fs.gitlab.io/). VirtIO-FS for Windows is a user mode file system, implemented using [WinFsp framework](https://github.com/billziss-gh/winfsp). VirtIO-FS consists of VirtIO-powered driver and user-space service based on WinFsp.

# Status

VirtIO-FS is at an early stage of development and should be considered as a "Tech Preview" feature. To see what may not work, please check [known problems](#known-problems).

# Setup

## Host

This section shows how to setup VirtIO-FS device on the host side. Two options are described: libvirt and QEMU.

### libvirt

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
      <driver type="virtiofs" queue="1024"/>
      <source dir="/home/user/viofs"/>
      <target dir="mount_tag"/>
      <address type="pci" domain="0x0000" bus="0x06" slot="0x00" function="0x0"/>
    </filesystem>
  </devices>
  ...
</domain>
```

The `<memoryBacking>` is necessary. Element `<source dir="/home/user/viofs"/>` describes host directory to share.

More information on libvirt VirtIO-FS options is provided in [libvirt docs](https://libvirt.org/kbase/virtiofs.html). 

### QEMU

Run virtiofsd daemon:

```
/usr/libexec/virtiofsd --socket-path=/tmp/virtiofs_socket -o source=/home/user/viofs
```

Adjust following QEMU command-line parameters:

* Instantiate the character device for socket communication between QEMU and virtiofsd:
`-chardev socket,id=char0,path=/tmp/virtiofs_socket`
* Instantiate the VirtIO-FS PCI device:
`-device vhost-user-fs-pci,queue-size=1024,chardev=char0,tag=my_virtiofs`
* Force use of memory sharing with virtiofsd (replace `4G` with your desired VM RAM size):
`-m 4G -object memory-backend-file,id=mem,size=4G,mem-path=/dev/shm,share=on -numa node,memdev=mem`

## Guest

### Setup with installer
1. Download and install [WinFSP](https://github.com/billziss-gh/winfsp/releases) with at least "Core" feature enabled.
2. Install VirtIO-FS driver and service from [VirtIO-Win package](https://github.com/virtio-win/virtio-win-pkg-scripts/blob/master/README.md).

### Manual setup (for development purposes)
1. Download and install [WinFSP](https://github.com/billziss-gh/winfsp/releases) with at least "Core" feature enabled. If you plan to make changes to VirtIO-FS driver or service then enable "Core", "Developer" and "Kernel Developer" features in the installer.
2. Install VirtIO-FS driver with Device Manager or `pnputil.exe`.
3. Setup VirtIO-FS service by running `sc create VirtioFsSvc binPath="<path to the binary>\virtiofs.exe" start=auto depend=VirtioFsDrv`. Don't forget to appropriately set `binPath`.
4. You can immediately start the service by running `sc start VirtioFsSvc`. The service will start automatically on boot. VirtIO-FS uses the first available drive letter starting with `Z:` unless no mount-point is specified.

![](https://user-images.githubusercontent.com/8286747/151011858-c9d122d2-d95a-421c-9914-c7d4dd05e2e3.png)

![](https://user-images.githubusercontent.com/8286747/151012458-fb8fa2c9-5059-45ac-994b-4ca03619a27b.png)

![](https://user-images.githubusercontent.com/8286747/151011678-88e804d8-4231-4597-932c-91b18e492492.png)

### Options

VirtIO-FS service can parse following settings from command-line:
```
  -d DebugFlags       [-1: enable all debug logs]
  -D DebugLogFile     [file path]
  -m MountPoint       [X:|* (required if no UNC prefix)]
```

Since command-line arguments can't be assigned to Windows service permanently, VirtIO-FS can parse them from the registry. When command-line arguments are absent the service looks up for `DebugFlags` (DWORD), `DebugLogFile` (String), `MountPoint` (String) under `HKLM\Software\VirtIO-FS`. For example, registry values depicted below correspond to `virtiofs.exe -d -1 -D C:\viofs_debug.log -m X:`. Please note that `-1` corresponds to `0xffffffff` in DWORD value. 
![](https://user-images.githubusercontent.com/8286747/146226495-0d7614ca-8a7d-4465-9aa3-3dc9dc9cb6de.png)

### Multiple VirtIO-FS instances

#### Setup

Support for multiple VirtIO-FS instances is made by WinFSP.Launcher service, so VirtIO-FS own service should not be running:
```
sc stop VirtioFsSvc
sc config VirtioFsSvc start=demand
```
The VirtIO-FS service is now stopped and will not start even after reboot.

VirtIO-FS configuration for WinFsp.Launcher:
```
"C:\Program Files (x86)\WinFsp\bin\fsreg.bat" virtiofs "<path to the binary>\virtiogfs.exe" "-t %1 -m %2"
```
Corresponding data is now available to view and edit under `HKLM\SOFTWARE\WOW6432Node\WinFsp\Services\virtiofs` registry key.

#### Mount

Mount VirtIO-FS with tag `mount_tag0` to `Y:\`:
```
"C:\Program Files (x86)\WinFsp\bin\launchctl-x64.exe" start virtiofs viofsY mount_tag0 Y:"
```
Mount VirtIO-FS with tag `mount_tag1` to `Z:\`:
```
"C:\Program Files (x86)\WinFsp\bin\launchctl-x64.exe" start virtiofs viofsZ mount_tag1 Z:"
```
Here `viofsY` and `viofsZ` are instance names for WinFsp.Launcher. They are selected arbitrary, but must differ between instances.

#### Unmount

Unmount is done by the instance name:
```
"C:\Program Files (x86)\WinFsp\bin\launchctl-x64.exe" stop virtiofs viofsY
"C:\Program Files (x86)\WinFsp\bin\launchctl-x64.exe" stop virtiofs viofsZ
```

# Known limitations

* VirtIO-FS is case-sensitive ([issue#669](https://github.com/virtio-win/kvm-guest-drivers-windows/issues/669))

# Testing

## WinFsp Tests

The following command line is used to execute WinFsp's test suite:

```
winfsp-tests-x64.exe --external --resilient -create_fileattr_test -create_readonlydir_test -create_allocation_test -create_notraverse_test -getfileattr_test -getfileinfo_test -getfileinfo_name_test -setfileinfo_test -setsecurity_test -reparse_guid_test -reparse_nfs_test -stream_*
```

VirtIO-FS for Windows does not currently pass all available tests so some of them are skipped for now:

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
