```
PowerShell needs to escape . and space character, either double quotes or single quotes.
PowerShell is not case sensative.
```
## Find a Service
- find by DisplayName `Get-Service -DisplayName [display name]`
## Find file
```
Get-ChildItem -Path '\\oh0co007\ITProdSysDocRepository\RTO Central\Change Requests\2020' 
| Where-Object {$_.LastWriteTime -gt [DateTime]::ParseExact('06-11-2020','MM-dd-yyyy',$null)}
| Sort LastWriteTime -Descending
| Select-Object LastWriteTime,Name
| Out-File 'cr.txt'
```
Find all files from Path where LastWriteTime is greater than 06-11-2020, sorted by descended LastWriteTime, only select
LastWriteTime and Name, then write to the file.
