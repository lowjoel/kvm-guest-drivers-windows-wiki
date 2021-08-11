Recommended environment for drivers build: Windows 11 EWDK

**Note:**
* **At the moment of transition to EWDK, the drivers will be built using Windows 11 EWDK.**
* **Currently usage of Windows 11 EWDK is blocked by the following issues: the need to separate SDV build (all the drivers should have SDV logs), CodeQL analysis errors, and ARM64 build error.**

The building of drivers for the following operating systems is not supported by the batch build procedure:
* Windows XP / Server 2003
* Windows Vista / Server 2008

Building drivers for Windows 7 / Server 2008R2 is optional.
Use VIRTIO_WIN_BUILD_LEGACY=Win7 to include them into the build.

Note: latest tag that supports the building of drivers for legacy operating systems:
https://github.com/virtio-win/kvm-guest-drivers-windows/releases/tag/08.03.2021-last-buldable-point-XP

Components to install
* WinFsp https://github.com/billziss-gh/winfsp/releases/download/v1.8/winfsp-1.8.20304.msi (Core + Developer + Kernel Developer)
* Cryptographic provider development kit (CPDK) Windows 10 https://www.microsoft.com/en-us/download/details.aspx?id=30688
_(Note: The kit was updated, the latest one was published 3/22/2021 and installs headers and binaries under ...\Windows Kits\10.0\...)_
* .NET Framework version 4.8 https://dotnet.microsoft.com/download/dotnet-framework/thank-you/net48-web-installer
* Visual Studio 2013 Redistributable x86 (required by EWDK/SDV) [KB 3138367](http://download.microsoft.com/download/c/c/2/cc2df5f8-4454-44b4-802d-5ea68d086676/vcredist_x86.exe)
* CodeQL tool for the Static Tools Logo Test

Preparing the environment:
1. Download Windows 11 EWDK ISO https://go.microsoft.com/fwlink/?linkid=2165855
* mount it (for example as E:)
* copy entire content to local directory (for example c:\ewdk11): `mkdir c:\ewdk11 && xcopy /e e:\* c:\ewdk11`
* unmount and delete Windows 11 EWDK ISO file
* if the name of the local directory is different from the default (c:\ewdk11) `set EWDK11_DIR=<Copy_Of_EWDK 11>`

2. [CodeQL Windows Setup](https://docs.microsoft.com/en-us/windows-hardware/drivers/devtest/static-tools-and-codeql#codeql-windows-setup)
* mkdir C:\codeql-home
* Navigate to the Github [CodeQL Download Page](https://github.com/github/codeql-cli-binaries/releases/tag/v2.4.6)
* Download version **2.4.6** of the zip file if you are certifying a driver for the Windows Hardware Compatibility Program in 2021. For example for 64 bit Windows "codeql-win64.zip". 
* Unzip the codeql folder in the zip file to `C:\codeql-home\codeql\` directory.
* Confirm that the CodeQL command works by displaying the help. `C:\codeql-home\codeql\>codeql --help`

3. [Clone the repository to access the driver-specific queries](https://docs.microsoft.com/en-us/windows-hardware/drivers/devtest/static-tools-and-codeql#clone-the-repository-to-access-the-driver-specific-queries)
* Navigate to the [Microsoft CodeQL GitHub repository](https://github.com/microsoft/Windows-Driver-Developer-Supplemental-Tools).
* [Clone](https://github.com/git-guides/git-clone) the repository to download all CodeQL queries and [query suites](https://codeql.github.com/docs/codeql-cli/creating-codeql-query-suites/) with driver-specific queries.  
```command
C:\codeql-home\>git clone https://github.com/microsoft/Windows-Driver-Developer-Supplemental-Tools.git --recursive -b RELEASE_BRANCH
```

Replace RELEASE_BRANCH with the appropriate branch depending on the OS you are certifying for, per the following table:

| Release                         | Branch to use   |
|---------------------------------|-----------------|
| Windows Server 2022             | WHCP_21H2       |
| Windows 11                      | WHCP_21H2       |


Build procedure:
* Run buildAll.bat (for complete build including SDV)
* Run build_AllNoSdv.bat (to build everything excluding SDV)

Build procedure customization:
* Set VIRTIO_WIN_RUN_CODEQL=1 to enable CodeQL checks as a part of SDV running
* Set VIRTIO_WIN_SDV_2022=1 to build DVL for system device drivers.
