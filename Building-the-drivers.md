# Tools needed for driver build

## Building Windows 7 (Windows 2008R2) drivers and up

* Download and install Visual Studio 2013 (drivers can be also built with Visual Studio 2012 with minor changes) and also download and install Windows 8.1 WDK - http://msdn.microsoft.com/en-US/windows/hardware/gg454513

## Building Windows XP, Windows 2003, Windows Vista and Windows 2008 drivers

* Download and install old DDK (Windows 7 DDK) - http://www.microsoft.com/en-us/download/details.aspx?id=11800


# How to build?

* Run buildall.bat either for each driver separatly (first build VirtIO library) or buildall.bat in the root directory. The results of builds will be in "Install" directory 