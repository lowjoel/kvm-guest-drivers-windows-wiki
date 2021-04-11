Recommended environment for drivers build: EWDK 1903

Building of drivers for following operating systems is not supported by batch build procedure:
* Windows XP / Server 2003
* Windows Vista / Server 2008

Building drivers for Windows 7 / Server 2008R2 is optional.
Use VIRTIO_WIN_BUILD_LEGACY=Win7 to include them into the build.

Note: latest tag that support building of drivers for legacy operating systems:
https://github.com/virtio-win/kvm-guest-drivers-windows/releases/tag/08.03.2021-last-buldable-point-XP

File to download and mount
* EWDK 1903 ISO https://go.microsoft.com/fwlink/p/?linkid=2086136

Components to install
* WinFsp https://github.com/billziss-gh/sshfs-win/releases/download/v3.5.20024/sshfs-win-3.5.20024-x64.msi
* Cryprographic provider development kit (CPDK) https://www.microsoft.com/en-us/download/details.aspx?id=30688
* .NET Framework version 4.6.1 (required by EWDK)
* Visual Studio 2014 redistributable x86 (required by EWDK/SDV)

Build procedure:
* Run buildAll.bat (for complete build including SDV)
* Run build_AllNoSdv.bat (to build everything excluding SDV)

