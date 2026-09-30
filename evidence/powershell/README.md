# PowerShell Investigation Evidence

This folder contains supporting evidence from the Windows PowerShell endpoint investigation.

The investigation focused on Sysmon Event ID 1 process creation, encoded PowerShell command execution, and PowerShell Operational logging.

Key evidence includes:

- Sysmon Event ID 1 process creation
- PowerShell execution using `-NoProfile -EncodedCommand`
- Process and parent-process information
- PowerShell Operational Event IDs 40962, 53504, and 40961
- ScriptBlock logging
- Decoded PowerShell command
- Controlled test execution using `Write-Output`
- `Get-Date` validation of the test activity
