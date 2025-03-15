# Group Managed Service Account

## [Getting Started with Group Managed Service Accounts](https://docs.microsoft.com/en-us/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts/)

Group Managed Service Accounts (gMSA) are a feature in Active Directory that allows the management of service accounts without requiring manual password management. These accounts are primarily used for services and applications running on Windows Server environments.

Here is a list of services and applications that support gMSA:

1. Windows Server Services:
- IIS (Internet Information Services) – gMSA is supported for running websites and web applications securely.
- SQL Server – Supports gMSA for services such as the SQL Server Database Engine, SQL Server Agent, and other related services.
- Active Directory – Used to run domain controllers and other AD-related services.
- Exchange Server – gMSA is supported for running services like the Mailbox, Transport, and other Exchange components.
- File Server – gMSA can be used to manage access and secure file shares.
- Hyper-V – gMSA can be used by Hyper-V services for virtual machine management.
- Distributed File System (DFS) – gMSA is supported in DFS Namespace and DFS Replication.
- Microsoft Dynamics AX/365 – gMSA can be used to manage services running within these environments.
- SharePoint Server – SharePoint services can be run using gMSA.
- Windows Server Update Services (WSUS) – gMSA is supported for WSUS services.
- Remote Desktop Services (RDS) – Supports gMSA for RDS services.
2. Third-Party Applications:
- SAP – Many SAP applications, including SAP NetWeaver, support gMSA for secure service account management.
- SQL-based Applications – Some third-party database applications can leverage gMSA for secure service management.
- Backup Solutions – Some backup solutions (e.g., Veeam) can integrate with gMSA for services running on backup servers.
- Application Servers – Various application servers like Apache Tomcat, and WebLogic support gMSA for running as service accounts.
3. Other Services:
- Kerberos Authentication – gMSA is designed for environments that use Kerberos authentication, ensuring seamless integration for services requiring Kerberos tickets.
- System Center Configuration Manager (SCCM) – gMSA is supported for various services within SCCM.
4. Cloud Integration (Azure):
- Azure Active Directory (Entra ID) – Some integration points allow the use of gMSA in hybrid or fully cloud-based environments where Azure AD is leveraged.
5. Custom Applications:
- Custom-developed applications that support Windows authentication and Kerberos can be configured to use gMSA for secure service account management.
Requirements for gMSA:
- Windows Server 2012 or later.
- Active Directory domain functional level of Windows Server 2012 or higher.
- Servers and services need to support gMSA (most modern Microsoft products do).
- For each service, specific configurations are required to enable gMSA, such as creating a gMSA, configuring permissions, and linking the account to the relevant service.


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
