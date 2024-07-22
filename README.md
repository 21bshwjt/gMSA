# Group Managed Service Account

## [Getting Started with Group Managed Service Accounts](https://docs.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts/)


### Schedule Weekly Twice
```powershell
# Weekly Twice
#Powershell Script for Scheduled task by gMSA. We need to change the red highlighted text as per our requirment.
$scriptfolder = "Bin" # Please change the Foldername.
$taskname = "CABackup" # Please change TaskName.
$taskPath = "\CABackup\"
$scriptCommand = "-File C:\$scriptfolder\$taskname.ps1"
$settings = New-ScheduledTaskSettingsSet -Compatibility WIN8 -ExecutionTimeLimit "01:00"
$action = New-ScheduledTaskAction -Execute 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe' -WorkingDirectory "C:\$scriptfolder" -Argument $scriptCommand
$trigger=New-ScheduledTaskTrigger -Weekly -DaysOfWeek Monday,Thursday -At 14:00 # Please change the day & time
$principle = New-ScheduledTaskPrincipal -UserId "bshwjt\gMSA-PKI01$" -LogonType Password # Please change the gMSA
Register-ScheduledTask "$taskname" -TaskPath $taskPath -Action $action -Trigger $trigger -Principal $principle -Settings $settings -Description "CA Bak"
```

### Schedule Daily Once
```powershell
$scriptfolder = "Bin" # Please change the Foldername.
$taskname = "CA-Backup" # Please change TaskName.
$DT = ([DateTime]::Now)
$Timespan = $DT.AddDays(1) - $DT
$taskPath = "\CABackup\"
$scriptCommand = "-File C:\$scriptfolder\$taskname.ps1"
$settings = New-ScheduledTaskSettingsSet -Compatibility Win8 -ExecutionTimeLimit "00:15:00"
$action = New-ScheduledTaskAction -Execute 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe' -WorkingDirectory "C:\$scriptfolder" -Argument $scriptCommand
$trigger = New-ScheduledTaskTrigger -Once -At "14:00:00" -RepetitionInterval $Timespan 
$principle = New-ScheduledTaskPrincipal -UserId "bshwjt\gMSA-PKI01$" -LogonType Password -RunLevel Highest
Register-ScheduledTask $taskname -TaskPath $taskpath -Action $action -Trigger $trigger -Principal $principle -Settings $settings -Description "Daily CA Backup"
```
### Schedule in every six hours
```powershell
#Update taskPath variable
$scriptfolder = "testcode" # Please change the Foldername.
$taskname = "testcode" # Please change TaskName.
$timeSpan = "6" # Change the Interval for run this multiple times If needed.
$repetitionInterval = New-TimeSpan -Hours $timeSpan
$repetitionDuration = ([TimeSpan]::MaxValue)
$taskPath = "\Admintasks\"
$scriptCommand = "-File C:\TrustedCode\$scriptfolder\$taskname.ps1"
$settings = New-ScheduledTaskSettingsSet -Compatibility WIN8 -ExecutionTimeLimit "01:00"
$action = New-ScheduledTaskAction -Execute 'powershell.exe' -WorkingDirectory "C:\TrustedCode\$scriptfolder" -Argument $scriptCommand
$trigger = New-ScheduledTaskTrigger -Once -At "03:00:00" -RepetitionInterval $repetitionInterval -RepetitionDuration $repetitionDuration # Please change the Start Time
$principle = New-ScheduledTaskPrincipal -UserId contoso\Test-gMSA$ -LogonType Password # Please change the gMSA
Register-ScheduledTask $taskname -TaskPath $taskpath -Action $action -Trigger $trigger -Principal $principle -Settings $settings
```
