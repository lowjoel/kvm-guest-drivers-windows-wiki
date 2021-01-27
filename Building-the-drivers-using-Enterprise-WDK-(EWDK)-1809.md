Below are instructions for setting up EWDK 1809 using only cmd prompt.

- @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
- refreshenv
- choco install -y windows-sdk-10-version-1809-all winfsp wget curl
- wget https://download.microsoft.com/download/9/F/9/9F9F709B-C0E6-4B89-90C2-CDE3059C61CF/EWDK_rs5_release_svc_prod2_17763_190129-1747.iso
- powershell mount-diskimage -imagepath "C:\EWDK_rs5_release_svc_prod2_17763_190129-1747.iso"
- powershell "$driveletter = (get-diskimage C:\EWDK_rs5_release_svc_prod2_17763_190129-1747.iso | get-volume).driveletter" ; robocopy $driveletter`:\ c`:\ /E"
- wget https://download.microsoft.com/download/1/7/6/176909B0-50F2-4DF3-B29B-830A17EA7E38/CPDK_RELEASE_UPDATE/cpdksetup.exe -outfile cpdksetup.exe
- cpdksetup.exe /features + /quiet
- start LaunchBuildEnv.cmd

Note: CPDK download is currently unavailable from Microsoft - I am investigating.