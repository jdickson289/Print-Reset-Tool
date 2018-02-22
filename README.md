# Print-Reset-Tool
The PowerShell script automates resetting the Print Spooler by deleting all print drivers and print queues only for Windows 7 and Windows 2008 R2 Remote Desktop Servers. 

This performs the same cleanup and is the script behind the FixIt for KB 2793718 You experience Print Spooler error messages after installing or upgrading a Third-Party print driver
http://support.microsoft.com/kb/2793718/en-US.   

Supports only Windows 7 and Windows Server 2008 R2 Terminal Servers/Remote Desktop Server Host machines.
This tool will run on Servers with Print Role installed but will not make any changes.
Please find more information about the KB and the FixIT from http://blogs.technet.com/b/askperf/archive/2013/05/22/3482664.aspx?PageIndex=2

The script is not signed so the PowerShell Execution Policy must be set prior to running: 
1. Start PowerShell in Admin mode
2. Type the following command:
```
Set-ExecutionPolicy Unrestricted
```
The following command resets print environment to out of box:

    print-reset.ps1 -full

The following command corrects common printing issues:

    print-reset.ps1 -light

### Explanation of Light Mode and Full Mode
#### -light: Light mode - Corrects common printing issues.
1. Stop spooler service and dependents.
2. Do not backup spooler service configuration: hklm\system\currentcontrolset\services - warn user -printbrm/PMC
3. Do not Backup print configuration: hklm\system\currentcontrolset\control\print
4. Do not backup files in %windir%\system32\spool\printers\ directory - warn user - back up manually
5. Import default print monitors (USB, Local Port, TCP/IP, WSD, and Microsoft Shared Fax registry configuration)
6. Import spooler service default configuration
7. Import print processor configuration for correct machine architecture: hklm\system\currentcontrolset\control\print\environments\(machine architecture)\winprint
8. Detect if spoolsv.exe, spoolss.dll, localspl.dll, or win32spl.dll are not present in %windir%\system32, write error to log.
9. Copy NTPrint.inf from %windir%\driverstore if not present in %windir%\inf. If (also) not present in driverstore, write error to log.
10. Test the path for hklm\system\currentcontrolset\control\print\printers\.DefaultSpoolDirectory. If it is not a valid path, write error to log. Correct this condition by setting it to the default path.
11. Delete all files from the spooler directory
12. Start the spooler service and dependents

#### -full: Full mode. Resets spooler service and print key to defaults.
1. Stop spooler service and dependents.
2. Do not Backup spooler service configuration: hklm\system\currentcontrolset\services
3. Do not Backup print configuration: hklm\system\currentcontrolset\control\print
4. Do not Backup Lanmanserver/Shares key
5. Do not Backup HKCU\Printers\Connections
6. Import default print key for correct machine architecture
7. Import spooler service default configuration
8. Detect if spoolsv.exe, spoolss.dll, localspl.dll, or win32spl.dll are not present in %windir%\system32, write error to log.

The following command line switches are supported :

 - `-light      Light Mode - Corrects common printing issues`
 - `-full       Full mode - Resets spooler service and print registry keys to defaults`
 - `-force      Do not prompt for confirmation`
 - `-quiet      Do not display console output`

A log is created automatically and saved in %windir%\printreset 

System Requirements:
Windows 7 or Windows Server 2008 R2  
PowerShell 2.0 is required  
The Fix it will run on Windows Server 2008 R2 that has the Print Role installed but will NOT make any changes  
