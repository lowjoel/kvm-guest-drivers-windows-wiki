Recommended environment for drivers build: EWDK 2004

Building of drivers for following operating systems is not supported by batch build procedure:
* Windows XP / Server 2003
* Windows Vista / Server 2008

Building drivers for Windows 7 / Server 2008R2 is optional.
Use VIRTIO_WIN_BUILD_LEGACY=Win7 to include them into the build.

Note: latest tag that support building of drivers for legacy operating systems:
https://github.com/virtio-win/kvm-guest-drivers-windows/releases/tag/08.03.2021-last-buldable-point-XP

Components to install
* WinFsp https://github.com/billziss-gh/winfsp/releases/download/v1.8/winfsp-1.8.20304.msi (Core + Developer + Kernel Developer)
* Cryprographic provider development kit (CPDK) Windows 10 https://www.microsoft.com/en-us/download/details.aspx?id=30688
_(Note: The kit was updated, latest one was published 3/22/2021 and installs headers and binaries under ...\Windows Kits\10.0\...)_
* .NET Framework version 4.8
* Visual Studio 2013 redistributable x86 (required by EWDK/SDV)

Preparing the environment:

_Note: The EWDK 2004 has a problem when run dvl.exe to create DVL.XML file._

_As a workaround we do not use mounted EWDK 2004 ISO but copy it to the local drive and modify_
1. Download EWDK 2004 ISO https://go.microsoft.com/fwlink/p/?linkid=2128902
* mount it (for example as E:)
* copy entire content to local directory (for example, c:\2004): xcopy /e e:\\* c:\2004
* If you use different directory than c:\2004, set EWDK_DIR=<Copy_Of_EWDK>
* unmount and delete EWDK 2004 ISO file
2. Download EWDK 1903 ISO https://go.microsoft.com/fwlink/p/?linkid=2086136
* mount it (for example as E:)
* copy /y e:\Program Files\Windows Kits\10\Tools\dvl\\* %EWDK_DIR%\Program Files\Windows Kits\10\Tools\dvl 
* unmount and delete EWDK 1903 ISO file
3. (Only if the build procedure **is not able** to populate files under %EWDK_DIR%) Run once the Tools\SetVsEnv.bat from virtio-win repository.
_(The build procedure will try to copy several required binaries from EWDK 2004 tree to %EWDK_DIR%\BuildEnv if required.)_

Build procedure:
* Run buildAll.bat (for complete build including SDV)
* Run build_AllNoSdv.bat (to build everything excluding SDV)

