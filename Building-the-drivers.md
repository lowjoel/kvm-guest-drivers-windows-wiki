# Tools needed for driver build

## Building Windows 8 (Windows 2012) drivers and up

* Download and install Visual Studio 2015 (VS15) 
and also download and install Windows 8.1 WDK http://msdn.microsoft.com/en-US/windows/hardware/gg454513 and Windows 10 WDK http://go.microsoft.com/fwlink/p/?LinkId=526733. 

* Download and install CPDK (Cryptographic Provider Development Kit) v8.0 available from Microsoft:
https://www.microsoft.com/en-us/download/details.aspx?id=30688

    [Note: After CPDK installation you need to copy the CPDK dir
    to C:\Program Files (x86)\Windows Kits\8.1 from C:\Program Files (x86)\Windows Kits\8.0
    Because by default VS12 $WindowsSdkDir points to C:\Program Files (x86)\Windows Kits\8.1]

* Download and install Windows Assessment and Deployment Kit (Windows ADK) for Windows 8.1 Update
https://www.microsoft.com/en-us/download/details.aspx?id=39982

## Building Windows XP, Windows 2003, Windows Vista, Windows 2008, Windows 7 and Windows 2008R2 drivers

* Download and install old DDK 7600.16385.1 (Windows 7 DDK) 
http://www.microsoft.com/en-us/download/details.aspx?id=11800

# How to build?

* Visual Studio build: load and build .sln either for each driver separately or virtio-win.sln in the root directory.
* Command line build: run buildAll.bat either for each driver separately or buildAll.bat in the root directory. Do not try to run from special cmd with VS/DDK environment. buildAll.bat by default builds the driver(s) for all targets and all architectures. It accepts command line options for more selective builds, see Tools/build.bat for more information. Example: "buildAll.bat Win8 64" will build only Win8 amd64 driver.
* The results of builds will be in "<driver>\Install" directory

# Known hints / constraints

* Paths hard-coded in *.bat or *.vcxproj

        hard-coded path to VS14:    "C:\Program Files (x86)\Microsoft Visual Studio 14.0"
        hard-coded path to DDK 7.1: "C:\winddk\7600.16385.1" (can be overriden, see below)

* DDK detection:

        <!-- Define the legacy DDK directory required for downlevel targets -->
        <DDKINSTALLROOT Condition="'$(DDKINSTALLROOT)' == ''">C:\WINDDK\</DDKINSTALLROOT>
        <DDKVER Condition="'$(DDKVER)' == ''">7600.16385.1</DDKVER>
        <LegacyDDKDir>$(DDKINSTALLROOT)$(DDKVER)</LegacyDDKDir>

* env version variables:

        <!-- Second component of driver version -->
        <_RHEL_RELEASE_VERSION_ Condition="'$(_RHEL_RELEASE_VERSION_)' == ''">6</_RHEL_RELEASE_VERSION_>
        <!-- Third component of driver version -->
        <_BUILD_MAJOR_VERSION_ Condition="'$(_BUILD_MAJOR_VERSION_)' == ''">101</_BUILD_MAJOR_VERSION_>
        <!-- Fourth component of driver version -->
        <_BUILD_MINOR_VERSION_ Condition="'$(_BUILD_MINOR_VERSION_)' == ''">58000</_BUILD_MINOR_VERSION_>
        
        STAMPINF_DATE

* SDV/DVL:

        sdv/dvl do not work, but is enabled by default
        SET _BUILD_DISABLE_SDV=Yes to disable

* misc

        pciserial and fwcfg
                only inf/cat files, no sources, no comments