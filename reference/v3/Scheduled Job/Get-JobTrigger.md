---
external help file: PSITPro3_ScheduledJob.xml
online version: http://go.microsoft.com/fwlink/?LinkID=223915
schema: 2.0.0
---

# Get-JobTrigger
## SYNOPSIS
Gets the job triggers of scheduled jobs.

## SYNTAX

### UNNAMED_PARAMETER_SET_1
```
Get-JobTrigger [-InputObject] <ScheduledJobDefinition> [[-TriggerId] <Int32[]>]
```

### UNNAMED_PARAMETER_SET_2
```
Get-JobTrigger [-Id] <Int32> [[-TriggerId] <Int32[]>]
```

### UNNAMED_PARAMETER_SET_3
```
Get-JobTrigger [-Name] <String> [[-TriggerId] <Int32[]>]
```

## DESCRIPTION
The Get-JobTrigger cmdlet gets the job triggers of scheduled jobs.
You can use this command to examine the job triggers or to pipe the job triggers to other cmdlets.

A "job trigger" defines a recurring schedule or conditions for starting a scheduled job.
Job triggers are not saved to disk independently; they are part of a scheduled job.
To get a job trigger, specify the scheduled job that the trigger starts.

Use the parameters of the Get-JobTrigger cmdlet to identify the scheduled jobs.
You can identify the scheduled jobs by their names or identification numbers, or by entering or piping  ScheduledJob objects, such as the those that are returned by the Get-ScheduledJob cmdlet, to Get-JobTrigger.

Get-JobTrigger is one of a collection of job scheduling cmdlets in the PSScheduledJob module that is included in Windows PowerShell.

For more information about Scheduled Jobs, see the About topics in the PSScheduledJob module.
Import the PSScheduledJob module and then type: Get-Help about_Scheduled* or see about_Scheduled_Jobs.

This cmdlet is introduced in Windows PowerShell 3.0.

## EXAMPLES

### Example 1: Get a job trigger by scheduled job name
```
PS C:\>Get-JobTrigger -Name BackupJob
```

The command uses the Name parameter of Get-JobTrigger to get the job triggers of the BackupJob scheduled job.

### Example 2: Get a job trigger by ID
```
The first command uses the Get-ScheduledJob cmdlet to display the scheduled jobs on the local computer. The display includes the IDs of the scheduled jobs.
PS C:\>Get-ScheduledJob
Id         Name            Triggers        Command                                  Enabled
--         ----            --------        -------                                  -------
1          ArchiveProjects {1}             \\Server\Share\Archive-Projects.ps1      True
2          Backup          {1,2}           \\Server\Share\Run-Backup.ps1            True
3          Test-HelpFiles  {1}             \\Server\Share\Test-HelpFiles.ps1        True
4          TestJob         {}              \\Server\Share\Run-AllTests.ps1          True

The second command uses the Get-JobTrigger cmdlet to get the job trigger for the Test-HelpFiles job (ID = 3)
PS C:\>Get-JobTrigger -ID 3
```

The example uses the ID parameter of Get-JobTrigger to get the job triggers of a scheduled job.

### Example 3: Get job triggers by piping a job
```
PS C:\>Get-ScheduledJob -Name *Backup*, *Archive* | Get-JobTrigger
```

This command gets the job triggers of all jobs that have "Backup" or "Archive" in their names.

### Example 4: Get the job trigger of a job on a remote computer
```
PS C:\>Invoke-Command -ComputerName Server01 { Get-ScheduledJob Backup | Get-JobTrigger -TriggerID 2 }
```

This command gets one of the two job triggers of a scheduled job on a remote computer.

The command uses the Invoke-Command cmdlet to run a command on the Server01 computer.
It uses the Get-ScheduledJob cmdlet to get the Backup scheduled job, which it pipes to the Get-JobTrigger cmdlet.
It uses the TriggerID parameter to get only the second trigger.

### Example 5: Get all job triggers
```
PS C:\>Get-ScheduledJob | Get-JobTrigger | For mat-Table -Property ID, Frequency, At, DaysOfWeek, Enabled, @{Label="ScheduledJob";Expression={$_.JobDefinition.Name}} -AutoSize
Id Frequency At                    DaysOfWeek Enabled ScheduledJob
-- --------- --                    ---------- ------- ------------
1    Weekly  9/28/2011 3:00:00 AM  {Monday}      True Backup 
1    Daily  9/27/2011 11:00:00 PM                True Test-HelpFiles
```

This command gets all job triggers of all scheduled jobs on the local computer.

The command uses the Get-ScheduledJob to get the scheduled jobs on the local computer and pipes them to Get-JobTrigger, which gets the job trigger of each scheduled job (if any).

To add the name of the scheduled job to the job trigger display, the command uses the "calculated property" feature of the Format-Table cmdlet.
In addition to the job trigger properties that are displayed by default, the command creates a new "ScheduledJob" property that displays the name of the scheduled job.

### Example 6: Get the job trigger property of a scheduled job
```
The command uses the Get-ScheduledJob cmdlet to get the Test-HelpFiles scheduled job. Then it uses the dot method (.) to get the JobTriggers property of the Test-HelpFiles scheduled job.
PS C:\>(Get-ScheduledJob Test-HelpFiles).JobTriggers

The second command uses the Get-ScheduledJob cmdlet to get all scheduled jobs on the local computer. It uses the Foreach-Object cmdlet to get the value of the JobTrigger property of each scheduled job.
PS C:\>Get-ScheduledJob | foreach {$_.JobTriggers}
```

The job triggers of a scheduled job are stored in the JobTriggers property of the job.
This example shows alternatives to using the Get-JobTrigger cmdlet to get job triggers.
The results are identical to using the Get-JobTrigger cmdlet and the techniques can be used interchangeably.

### Example 7: Compare job triggers
```
The first command gets the job trigger of the ArchiveProjects scheduled job. The command pipes the job trigger to the Tee-Object cmdlet, which saves the job trigger in the $t1 variable and displays it at the command line.
PS C:\>Get-ScheduledJob -Name ArchiveProjects | Get-JobTrigger | Tee-Object -Variable t1
Id         Frequency       Time                   DaysOfWeek              Enabled
--         ---------       ----                   ----------              -------
0          Daily           9/26/2011 3:00:00 AM                           True

The second command gets the job trigger of the Test-HelpFiles scheduled job. The command pipes the job trigger to the Tee-Object cmdlet, which saves the job trigger in the $t2 variable and displays it at the command line.
PS C:\>Get-ScheduledJob -Name Test-HelpFiles | Get-JobTrigger | Tee-Object -Variable t2
Id         Frequency       Time                   DaysOfWeek              Enabled
--         ---------       ----                   ----------              -------
0          Daily           9/26/2011 3:00:00 AM                           True

The third command compares the job triggers in the $t1 and $t2 variables. It uses the Get-Member cmdlet to get the properties of the job trigger in the $t1 variable. It pipes the properties to the ForEach-Object cmdlet, which compares each property to the properties of the job trigger in the $t2 variable by name. The command then pipes the differing properties to the Format-List cmdlet, which displays them in a list.The output indicates that, although the job triggers appear to be the same, the HelpFiles job trigger includes a random delay of three (3) minutes.
PS C:\>$t1 | Get-Member -Type property | ForEach-Object { Compare-Object $t1 $t2 -Property $_.Name}
RandomDelay                                                 SideIndicator
-----------                                                 -------------
00:00:00                                                    =>
00:03:00                                                    <=
```

This example shows how to compare the job triggers of two scheduled jobs.

## PARAMETERS

### -Id
Specifies the identification number of a scheduled job.
Get-JobTrigger gets the job trigger of the specified scheduled job.

To get the identification number of scheduled jobs on the local computer or a remote computer, use the Get-ScheduledJob cmdlet.

```yaml
Type: Int32
Parameter Sets: UNNAMED_PARAMETER_SET_2
Aliases: 

Required: True
Position: 1
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -InputObject
Specifies a scheduled job.
Enter a variable that contains  ScheduledJob objects or type a command or expression that gets ScheduledJob objects, such as a Get-ScheduledJob command.
You can also pipe ScheduledJob objects to Get-JobTrigger.

```yaml
Type: ScheduledJobDefinition
Parameter Sets: UNNAMED_PARAMETER_SET_1
Aliases: 

Required: True
Position: 1
Default value: 
Accept pipeline input: True (ByValue)
Accept wildcard characters: False
```

### -Name
Specifies the name of a scheduled job.
Get-JobTrigger gets the job trigger of the specified scheduled job.
Wildcards are supported.

To get the names of scheduled jobs on the local computer or a remote computer, use the Get-ScheduledJob cmdlet.

```yaml
Type: String
Parameter Sets: UNNAMED_PARAMETER_SET_3
Aliases: 

Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: True
```

### -TriggerId
Gets the specified job triggers.
Enter the trigger IDs of one or more job triggers of a scheduled job.
Use this parameter when the scheduled job that is specified by the Name, ID, or InputObject parameters has multiple job triggers.

```yaml
Type: Int32[]
Parameter Sets: (All)
Aliases: 

Required: False
Position: 2
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

## INPUTS

### Microsoft.PowerShell.ScheduledJob.ScheduledJobDefinition
You can pipe a scheduled job from Get-ScheduledJob to Get-JobTrigger.

## OUTPUTS

### Microsoft.PowerShell.ScheduledJob.ScheduledJobTrigger

## NOTES

## RELATED LINKS

[about_Scheduled_Jobs](3b546629-703c-4939-b44f-52dd567bce92)

[Add-JobTrigger](d0ab1b5d-ca22-4518-a3dc-88ffb4578b62)

[Disable-JobTrigger](6387c972-06c8-4f7c-a1fe-35e0ed03b3cb)

[Disable-ScheduledJob](0f633d57-fe67-4fb8-b6f1-875395c76042)

[Enable-JobTrigger](a76ca67d-298b-4674-be43-af3f781eb570)

[Enable-ScheduledJob](e61a922e-b575-4b19-b29c-2b2b6f58ebcf)

[Get-JobTrigger](60c56495-7aa1-49fd-835a-09dfad27bdf8)

[Get-ScheduledJob](ff366cbf-5e3b-49fb-b987-d1a8d9822172)

[Get-ScheduledJobOption](00b922bf-0816-4817-bd07-0534538e5d68)

[New-JobTrigger](605eb27a-e8ff-4167-b94c-988b2b893696)

[New-ScheduledJobOption](a3262ad6-5b68-452e-80da-f53173bfa89f)

[Register-ScheduledJob](47e8f0de-6a42-447b-a024-9a20da5a852f)

[Remove-JobTrigger](46cc6c3a-d929-40d7-bf3f-0eb38d203879)

[Set-JobTrigger](d79c9eef-5446-40c2-a29c-28c13dffcd7c)

[Set-ScheduledJob](f759d9ff-6807-45d5-af98-7e56efd9615c)

[Set-ScheduledJobOption](5fe666db-ceed-4261-89ec-376dd01712f9)

[Unregister-ScheduledJob](a76ff3d0-1496-46a8-885a-b54552eda897)
