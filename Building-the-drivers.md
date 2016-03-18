# Tools needed for driver build

## Building Windows 8 (Windows 2012) drivers and up

* Download and install Visual Studio 2013 (VS12) 
(drivers can be also built with Visual Studio 2012 (VS11) with minor changes) 
and also download and install Windows 8.1 WDK - http://msdn.microsoft.com/en-US/windows/hardware/gg454513

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

* Run buildall.bat either for each driver separately (first build VirtIO library) or buildall.bat in the root directory. The results of builds will be in "Install" directory 

   [Do not try to run from special cmd with VS/DDK environment]

# Known hints / constraints

* Paths hard-coded in *.bat or *.vcxproj

        hard-coded path to VS12:    "C:\Program Files (x86)\Microsoft Visual Studio 12.0"
        hard-coded path to DDK 7.1: "C:\winddk\7600.16385.1"
        hard-coded path to Wdf:     "C:\Program Files (x86)\Windows Kits\8.1\Redist\wdf"

* DDK detection:

        if "%DDKVER%"=="" set DDKVER=7600.16385.1
        BUILDROOT=C:\WINDDK\%DDKVER%
        - or -
        if "%DDKISNTALLROOT%"=="" set DDKISNTALLROOT=C:\WINDDK\
        set BUILDROOT=%DDKISNTALLROOT%%DDKVER%

* env version variables:

        if "%_BUILD_MAJOR_VERSION_%"=="" set _BUILD_MAJOR_VERSION_=101
        if "%_BUILD_MINOR_VERSION_%"=="" set _BUILD_MINOR_VERSION_=58000
        if "%_RHEL_RELEASE_VERSION_%"=="" set _RHEL_RELEASE_VERSION_=61
        STAMPINF_DATE

* SDV/DVL:

        sdv/dvl do not work, but is enable by default
        set %_BUILD_DISABLE_SDV% to disable

* misc

        NetKVM/NDIS5/VirtIO: special copy of VirtIO
        
        pciserial
                only inf/cat files, no sources, no comments
