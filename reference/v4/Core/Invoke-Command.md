---
external help file: PSITPro4_Core.xml
online version: http://go.microsoft.com/fwlink/p/?linkid=289592
schema: 2.0.0
---

# Invoke-Command
## SYNOPSIS
Runs commands on local and remote computers.

## SYNTAX

### UNNAMED_PARAMETER_SET_1
```
Invoke-Command [-ScriptBlock] <ScriptBlock> [-ArgumentList <Object[]>] [-InputObject <PSObject>] [-NoNewScope]
```

### UNNAMED_PARAMETER_SET_2
```
Invoke-Command [[-ConnectionUri] <Uri[]>] [-ScriptBlock] <ScriptBlock> [-AllowRedirection]
 [-ArgumentList <Object[]>] [-AsJob] [-Authentication <AuthenticationMechanism>]
 [-CertificateThumbprint <String>] [-ConfigurationName <String>] [-Credential <PSCredential>]
 [-EnableNetworkAccess] [-HideComputerName] [-InDisconnectedSession] [-InputObject <PSObject>]
 [-JobName <String>] [-SessionOption <PSSessionOption>] [-ThrottleLimit <Int32>]
```

### UNNAMED_PARAMETER_SET_3
```
Invoke-Command [[-ConnectionUri] <Uri[]>] [-FilePath] <String> [-AllowRedirection] [-ArgumentList <Object[]>]
 [-AsJob] [-Authentication <AuthenticationMechanism>] [-ConfigurationName <String>]
 [-Credential <PSCredential>] [-EnableNetworkAccess] [-HideComputerName] [-InDisconnectedSession]
 [-InputObject <PSObject>] [-JobName <String>] [-SessionOption <PSSessionOption>] [-ThrottleLimit <Int32>]
```

### UNNAMED_PARAMETER_SET_4
```
Invoke-Command [[-ComputerName] <String[]>] [-FilePath] <String> [-ApplicationName <String>]
 [-ArgumentList <Object[]>] [-AsJob] [-Authentication <AuthenticationMechanism>] [-ConfigurationName <String>]
 [-Credential <PSCredential>] [-EnableNetworkAccess] [-HideComputerName] [-InDisconnectedSession]
 [-InputObject <PSObject>] [-JobName <String>] [-Port <Int32>] [-SessionName <String[]>]
 [-SessionOption <PSSessionOption>] [-ThrottleLimit <Int32>] [-UseSSL]
```

### UNNAMED_PARAMETER_SET_5
```
Invoke-Command [[-ComputerName] <String[]>] [-ScriptBlock] <ScriptBlock> [-ApplicationName <String>]
 [-ArgumentList <Object[]>] [-AsJob] [-Authentication <AuthenticationMechanism>]
 [-CertificateThumbprint <String>] [-ConfigurationName <String>] [-Credential <PSCredential>]
 [-EnableNetworkAccess] [-HideComputerName] [-InDisconnectedSession] [-InputObject <PSObject>]
 [-JobName <String>] [-Port <Int32>] [-SessionName <String[]>] [-SessionOption <PSSessionOption>]
 [-ThrottleLimit <Int32>] [-UseSSL]
```

### UNNAMED_PARAMETER_SET_6
```
Invoke-Command [[-Session] <PSSession[]>] [-FilePath] <String> [-ArgumentList <Object[]>] [-AsJob]
 [-HideComputerName] [-InputObject <PSObject>] [-JobName <String>] [-ThrottleLimit <Int32>]
```

### UNNAMED_PARAMETER_SET_7
```
Invoke-Command [[-Session] <PSSession[]>] [-ScriptBlock] <ScriptBlock> [-ArgumentList <Object[]>] [-AsJob]
 [-HideComputerName] [-InputObject <PSObject>] [-JobName <String>] [-ThrottleLimit <Int32>]
```

## DESCRIPTION
The Invoke-Command cmdlet runs commands on a local or remote computer and returns all output from the commands, including errors.
With a single Invoke-Command command, you can run commands on multiple computers.

To run a single command on a remote computer, use the ComputerName parameter.
To run a series of related commands that share data, use the New-PSSession cmdlet to create a PSSession (a persistent connection) on the remote computer, and then use the Session parameter of Invoke-Command to run the command in the PSSession.
To run a command in a disconnected session, use the InDisconnectedSession parameter.
To run a command in a background job, use the AsJob parameter.

You can also use Invoke-Command on a local computer to evaluate or run a string in a script block as a command.
Windows PowerShell converts the script block to a command and runs the command immediately in the current scope, instead of just echoing the string at the command line.

To start an interactive session with a remote computer, use the Enter-PSSession cmdlet.
To establish a persistent connection to a remote computer, use the New-PSSession cmdlet.

Before using Invoke-Command to run commands on a remote computer, read about_Remote (http://go.microsoft.com/fwlink/?LinkID=135182).

## EXAMPLES

### -------------------------- EXAMPLE 1 --------------------------
```
PS C:\>Invoke-Command -FilePath c:\scripts\test.ps1 -ComputerName Server01
```

This command runs the Test.ps1 script on the Server01 computer.

The command uses the FilePath parameter to specify a script that is located on the local computer.
The script runs on the remote computer and the results are returned to the local computer.

### -------------------------- EXAMPLE 2 --------------------------
```
PS C:\>Invoke-Command -ComputerName server01 -Credential domain01\user01 -ScriptBlock {Get-Culture}
```

This command runs a Get-Culture command on the Server01 remote computer.

It uses the ComputerName parameter to specify the computer name and the Credential parameter to run the command in the security context of "Domain01\User01," a user with permission to run commands.
It uses the ScriptBlock parameter to specify the command to be run on the remote computer.

In response, Windows PowerShell displays a dialog box that requests the password and an authentication method for the User01 account.
It then runs the command on the Server01 computer and returns the result.

### -------------------------- EXAMPLE 3 --------------------------
```
PS C:\>$s = New-PSSession -ComputerName Server02 -Credential Domain01\User01
PS C:\>Invoke-Command -Session $s -ScriptBlock {Get-Culture}
```

This example runs the same "Get-Culture" command in a session (a persistent connection) on the Server02 remote computer.
Typically, you create a session only when you are running a series of commands on the remote computer.

The first command uses the New-PSSession cmdlet to create a session on the Server02 remote computer.
Then, it saves the session in the $s variable.

The second command uses the Invoke-Command cmdlet to run the Get-Culture command on Server02.
It uses the Session parameter to specify the session  saved in the $s variable.

In response, Windows PowerShell runs the command in the session on the Server02 computer.

### -------------------------- EXAMPLE 4 --------------------------
```
PS C:\>Invoke-Command -ComputerName Server02 -ScriptBlock {$p = Get-Process PowerShell}
PS C:\>Invoke-Command -ComputerName Server02 -ScriptBlock {$p.VirtualMemorySize}
PS C:\>
PS C:\>$s = New-PSSession -ComputerName Server02
PS C:\>Invoke-Command -Session $s -ScriptBlock {$p = Get-Process PowerShell}
PS C:\>Invoke-Command -Session $s -ScriptBlock {$p.VirtualMemorySize}
17930240
```

This example compares the effects of using ComputerName and Session parameters of Invoke-Command.
It shows how to use a session to run a series of commands that share the same data.

The first two commands use the ComputerName parameter of Invoke-Command to run commands on the Server02 remote computer.
The first command uses the Get-Process cmdlet to get the PowerShell process on the remote computer and to save it in the $p variable.
The second command gets the value of the VirtualMemorySize property of the PowerShell process.

The first command succeeds.
But the second command fails, because when you use the ComputerName parameter, Windows PowerShell creates a connection just to run the command.
Then, it closes the connection when the command is complete.
The $p variable was created in one connection, but it does not exist in the connection created for the second command.

The problem is solved by creating a session (a persistent connection) on the remote computer and by running both of the related commands in the same session.

The third command uses the New-PSSession cmdlet to create a session on the Server02 computer.
Then it saves the session in the $s variable.
The fourth and fifth commands repeat the series of commands used in the first set, but in this case, the Invoke-Command command uses the Session parameter to run both of the commands in the same session.

In this case, because both commands run in the same session, the commands succeed, and the $p value remains active in the $s session for later use.

### -------------------------- EXAMPLE 5 --------------------------
```
PS C:\>$command = { Get-EventLog -log "Windows PowerShell" | where {$_.Message -like "*certificate*"} }
PS C:\>Invoke-Command -ComputerName S1, S2 -ScriptBlock $command
```

This example shows how to enter a command that is saved in a local variable.

When the entire command is saved in a local variable, you can specify the variable as the value of the ScriptBlock parameter.
You do not have to use the "param" keyword or the ArgumentList variable to submit the value of the local variable.

The first command saves a Get-EventLog command in the $command variable.
The command is formatted as a script block.

The second command uses the Invoke-Command cmdlet to run the command in $command on the S1 and S2 remote computers.

### -------------------------- EXAMPLE 6 --------------------------
```
PS C:\>Invoke-Command -ComputerName Server01, Server02, TST-0143, localhost -ConfigurationName MySession.PowerShell -ScriptBlock {Get-EventLog "Windows PowerShell"}
```

This example demonstrates how to use the Invoke-Command cmdlet to run a single command on multiple computers.

The command uses the ComputerName parameter to specify the computers.
The computer names are presented in a comma-separated list.
The list of computers includes the "localhost" value, which represents the local computer.

The command uses the ConfigurationName parameter to specify an alternate session configuration for Windows PowerShell and the ScriptBlock parameter to specify the command.

In this example, the command in the script block gets the events in the Windows PowerShell event log on each remote computer.

### -------------------------- EXAMPLE 7 --------------------------
```
PS C:\>$version = Invoke-Command -ComputerName (Get-Content Machines.txt) -ScriptBlock {(Get-Host).Version}
```

This command gets the version of the Windows PowerShell host program running on 200 remote computers.

Because only one command is run, it is not necessary to create persistent connections (sessions) to each of the computers.
Instead, the command uses the ComputerName parameter to indicate the computers.

The command uses the Invoke-Command cmdlet to run a Get-Host command.
It uses dot notation to get the Version property of the Windows PowerShell host.

To specify the computers, it uses the Get-Content cmdlet to get the contents of the Machine.txt file, a file of computer names.

These commands run synchronously (one at a time).
When the commands complete, the output of the commands from all of the computers is saved in the $version variable.
The output includes the name of the computer from which the data originated.

### -------------------------- EXAMPLE 8 --------------------------
```
PS C:\>$s = New-PSSession -ComputerName Server01, Server02
PS C:\>Invoke-Command -Session $s -ScriptBlock {Get-EventLog system} -AsJob

Id   Name    State      HasMoreData   Location           Command
---  ----    -----      -----         -----------        ---------------
1    Job1    Running    True          Server01,Server02  Get-EventLog system

PS C:\>$j = Get-Job

PS C:\>$j | Format-List -Property *

HasMoreData   : True
StatusMessage :
Location      : Server01,Server02
Command       : Get-EventLog system
JobStateInfo  : Running
Finished      : System.Threading.ManualResetEvent
InstanceId    : e124bb59-8cb2-498b-a0d2-2e07d4e030ca
Id            : 1
Name          : Job1
ChildJobs     : {Job2, Job3}
Output        : {}
Error         : {}
Progress      : {}
Verbose       : {}
Debug         : {}
Warning       : {}
StateChanged  :

PS C:\>$results = $j | Receive-Job
```

These commands run a background job on two remote computers.
Because the Invoke-Command command uses the AsJob parameter, the commands run on the remote computers, but the job actually resides on the local computer and the results are transmitted to the local computer.

The first command uses the New-PSSession cmdlet to create sessions on the Server01 and Server02 remote computers.

The second command uses the Invoke-Command cmdlet to run a background job in each of the sessions.
The command uses the AsJob parameter to run the command as a background job.
This command returns a job object that contains two child job objects, one for each of the jobs run on the two remote computers.

The third command uses a Get-Job command to save the job object in the $j variable.

The fourth command uses a pipeline operator (|) to send the value of the $j variable to the Format-List cmdlet, which displays all properties of the job object in a list.

The fifth command gets the results of the jobs.
It pipes the job object in $j to the Receive-Job cmdlet and stores the results in the $results variable.

### -------------------------- EXAMPLE 9 --------------------------
```
PS C:\>$MWFO_Log = "Microsoft-Windows-Forwarding/Operational"
PS C:\>Invoke-Command -ComputerName Server01 -ScriptBlock {Get-EventLog -LogName $Using:MWFO_Log -Newest 10}
```

This example shows how to include the values of local variables in a command run on a remote computer.
The command uses the Using scope modifier to identify a local variable in a remote command.
By default, all variables are assumed to be defined in the remote session.
The Using scope modifier was introduced in Windows PowerShell 3.0.
For more information about the Using scope modifier, see about_Remote_Variables http://go.microsoft.com/fwlink/?LinkID=252653.

The first command saves the name of the Microsoft-Windows-Forwarding/Operational event log in the $MWFO_Log variable.

The second command uses the Invoke-Command cmdlet to run a Get-EventLog command on the Server01 remote computer that gets the 10 newest events from the Microsoft-Windows-Forwarding/Operational event log on Server01.
The value of the LogName parameter is the $MWFO_Log variable, which is prefixed by the Using scope modifier to indicate that it was created in the local session, not in the remote session.

### -------------------------- EXAMPLE 10 --------------------------
```
PS C:\>Invoke-Command -ComputerName S1, S2 -ScriptBlock {Get-Process PowerShell}

PSComputerName    Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id   ProcessName
--------------    -------  ------    -----      ----- -----   ------     --   -----------
S1                575      15        45100      40988   200     4.68     1392 PowerShell
S2                777      14        35100      30988   150     3.68     67   PowerShell

PS C:\>Invoke-Command -ComputerName S1, S2 -ScriptBlock {Get-Process PowerShell} -HideComputerName

Handles  NPM(K)    PM(K)      WS(K) VM(M)   CPU(s)     Id   ProcessName
-------  ------    -----      ----- -----   ------     --   -----------
575      15        45100      40988   200     4.68     1392 PowerShell
777      14        35100      30988   150     3.68     67   PowerShell
```

This example shows the effect of using the HideComputerName parameter of Invoke-Command.

The first two commands use the Invoke-Command cmdlet to run a Get-Process command for the PowerShell process.
The output of the first command includes the PsComputerName property, which contains the name of the computer on which the command ran.
The output of the second command, which uses the HideComputerName parameter, does not include the PsComputerName column.

Using the HideComputerName parameter does not change the object; it just changes the display.
You can still use the Format cmdlets to display the PsComputerName property of any of the affected objects.

### -------------------------- EXAMPLE 11 --------------------------
```
PS C:\>Invoke-Command -ComputerName (Get-Content Servers.txt) -FilePath C:\Scripts\Sample.ps1 -ArgumentList Process, Service
```

This example uses the Invoke-Command cmdlet to run the Sample.ps1 script on all of the computers listed in the Servers.txt file.
The command uses the FilePath parameter to specify the script file.
This command allows you to run the script on the remote computers, even if the script file is not accessible to the remote computers.

When you submit the command, the content of the Sample.ps1 file is copied into a script block and the script block is run on each of the remote computers.
This procedure is equivalent to using the ScriptBlock parameter to submit the contents of the script.

### -------------------------- EXAMPLE 12 --------------------------
```
PS C:\>$LiveCred = Get-Credential

PS C:\>Invoke-Command -ConfigurationName Microsoft.Exchange -ConnectionUri https://ps.exchangelabs.com/PowerShell -Credential $LiveCred -Authentication Basic -ScriptBlock {Set-Mailbox Dan -DisplayName "Dan Park"}
```

This example shows how to run a command on a remote computer that is identified by a URI (Internet address).
This particular example runs a Set-Mailbox command on a remote Exchange server.
The backtick (\`) in the command is the Windows PowerShell continuation character.

The first command uses the Get-Credential cmdlet to store Windows Live ID credentials in the $LiveCred variable.
A credentials dialog box prompts the user to enter Windows Live ID credentials.

The second command uses the Invoke-Command cmdlet to run a Set-Mailbox command.
The command uses the ConfigurationName parameter to specify that the command should run in a session that uses the Microsoft.Exchange session configuration.
The ConnectionURI parameter specifies the URL of the Exchange server endpoint.

The Credential parameter specifies the Windows Live credentials stored in the $LiveCred variable.
The AuthenticationMechanism parameter specifies the use of basic authentication.
The ScriptBlock parameter specifies a script block that contains the command.

### -------------------------- EXAMPLE 13 --------------------------
```
PS C:\>$max = New-PSSessionOption -MaximumRedirection 1

PS C:\>Invoke-Command -ConnectionUri https://ps.exchangelabs.com/PowerShell -ScriptBlock {Get-Mailbox dan} -AllowRedirection -SessionOption $max
```

This command shows how to use the AllowRedirection and SessionOption parameters to manage URI redirection in a remote command.

The first command uses the New-PSSessionOption cmdlet to create a PSSessionOpption object that it saves in the $Max variable.
The command uses the MaximumRedirection parameter to set the MaximumConnectionRedirectionCount property of the PSSessionOption object to 1.

The second command uses the Invoke-Command cmdlet to run a Get-Mailbox command on a remote server running Microsoft Exchange Server.
The command uses the AllowRedirection parameter to provide explicit permission to redirect the connection to an alternate endpoint.
It also uses the SessionOption parameter to specify the session object in the $max variable.

As a result, if the remote computer specified by the ConnectionURI parameter returns a redirection message, Windows PowerShell will redirect the connection, but if the new destination returns another redirection message, the redirection count value of 1 is exceeded, and Invoke-Command returns a non-terminating error.

### -------------------------- EXAMPLE 14 --------------------------
```
PS C:\>$so = New-PSSessionOption -SkipCACheck
PS C:\>Invoke-Command -Session $s -ScriptBlock { Get-Hotfix } -SessionOption $so -Credential server01\user01
```

This example shows how to create and use a SessionOption parameter.

The first command uses the New-PSSessionOption cmdlet to create a session option.
It saves the resulting SessionOption object in the $so parameter.

The second command uses the Invoke-Command cmdlet to run a Get-HotFix command remotely.
The value of the SessionOption parameter is the SessionOption object in the $so variable.

### -------------------------- EXAMPLE 15 --------------------------
```
PS C:\>Enable-WSManCredSSP -Delegate Server02
PS C:\>Connect-WSMan Server02
PS C:\>Set-Item WSMan:\Server02*\Service\Auth\CredSSP -Value $true
PS C:\>$s = New-PSSession Server02
PS C:\>Invoke-Command -Session $s -ScriptBlock {Get-Item \\Net03\Scripts\LogFiles.ps1} -Authentication CredSSP -Credential Domain01\Admin01
```

This example shows how to access a network share from within a remote session.

The command requires that CredSSP delegation be enabled in the client settings on the local computer and in the service settings on the remote computer.
To run the commands in this example, you must be a member of the Administrators group on the local computer and the remote computer.

The first command uses the Enable-WSManCredSSP cmdlet to enable CredSSP delegation from the Server01 local computer to the Server02 remote computer.
This configures the CredSSP client setting on the local computer.

The second command uses the Connect-WSMan cmdlet to connect to the Server02 computer.
This action adds a node for the Server02 computer to the WSMan: drive on the local computer, allowing you to view and change the WS-Management settings on the Server02 computer.

The third command uses the Set-Item cmdlet to change the value of the CredSSP item in the Service node of the Server02 computer to True.
This action enables CredSSP in the service settings on the remote computer.

The fourth command uses the New-PSSession cmdlet to create a PSSession on the Server02 computer.
It saves the PSSession in the $s variable.

The fifth command uses the Invoke-Command cmdlet to run a Get-Item command in the session in $s that gets a script from the Net03\Scripts network share.
The command uses the Credential parameter and it uses the Authentication parameter with a value of CredSSP.

### -------------------------- EXAMPLE 16 --------------------------
```
PS C:\>Invoke-Command -ComputerName (Get-Content Servers.txt) -InDisconnectedSession -FilePath \\Scripts\Public\ConfigInventory.ps1 -SessionOption @{OutputBufferingMode="Drop";IdleTimeout=43200000}
```

This command runs a script on more than a hundred computers.
To minimize the impact on the local computer, it connects to each computer, starts the script, and then disconnects from each computer.
The script continues to run in the disconnected sessions.

The command uses the Invoke-Command cmdlet to run the script.
The value of the ComputerName parameter is a Get-Content command that gets the names of the remote computers from a text file.
The InDisconnectedSession parameter disconnects the sessions as soon as it starts the command.
The value of the FilePath parameter is the script that Invoke-Command runs on each computer that is named in the Servers.txt file.

The value of the SessionOption parameter is a hash table that sets the value of the OutputBufferingMode option to Drop and the value of the IdleTimeout option to 43200000 milliseconds (12 hours).

To get the results of commands and scripts that run in disconnected sessions, use the Receive-PSSession cmdlet.

## PARAMETERS

### -AllowRedirection
Allows redirection of this connection to an alternate Uniform Resource Identifier (URI).

When you use the ConnectionURI parameter, the remote destination can return an instruction to redirect to a different URI.
By default, Windows PowerShell does not redirect connections, but you can use this parameter to allow it to redirect the connection.

You can also limit the number of times the connection is redirected by changing the MaximumConnectionRedirectionCount session option value.
Use the  MaximumRedirection parameter of the New-PSSessionOption cmdlet or set the MaximumConnectionRedirectionCount property of the $PSSessionOption preference variable.
The default value is 5.

```yaml
Type: SwitchParameter
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3
Aliases: 

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### -ApplicationName
Specifies the application name segment of the connection URI.
Use this parameter to specify the application name when you are not using the ConnectionURI parameter in the command.

The default value is the value of the $PSSessionApplicationName preference variable on the local computer.
If this preference variable is not defined, the default value is WSMAN.
This value is appropriate for most uses.
For more information, see about_Preference_Variables (http://go.microsoft.com/fwlink/?LinkID=113248).

The WinRM service uses the application name to select a listener to service the connection request.
The value of this parameter should match the value of the URLPrefix property of a listener on the remote computer.

```yaml
Type: String
Parameter Sets: UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: 

Required: False
Position: Named
Default value: WSMAN
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -ArgumentList
Supplies the values of local variables in the command.
The variables in the command are replaced by these values before the command is run on the remote computer.
Enter the values in a comma-separated list.
Values are associated with variables in the order that they are listed.
The alias for ArgumentList is "Args".

The values in ArgumentList can be actual values, such as "1024", or they can be references to local variables, such as "$max".

To use local variables in a command, use the following command format:

{param($\<name1\>\[, $\<name2\>\]...) \<command-with-local-variables\>} -ArgumentList \<value\> -or- \<local-variable\>

The "param" keyword lists the local variables that are used in the command.
The ArgumentList parameter supplies the values of the variables, in the order that they are listed.

```yaml
Type: Object[]
Parameter Sets: (All)
Aliases: Args

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -AsJob
Runs the command as a background job on a remote computer.
Use this parameter to run commands that take an extensive time to complete.

When you use AsJob, the command returns an object that represents the job, and then displays the command prompt.
You can continue to work in the session while the job completes. 
To manage the job, use the Job cmdlets.
To get the job results, use the Receive-Job cmdlet.

The AsJob parameter is similar to using the Invoke-Command cmdlet to run a Start-Job command remotely.
However, with AsJob, the job is created on the local computer, even though the job runs on a remote computer, and the results of the remote job are automatically returned to the local computer.

For more information about Windows PowerShell background jobs, see about_Jobs (http://go.microsoft.com/fwlink/?LinkID=113251) and about_Remote_Jobs (http://go.microsoft.com/fwlink/?LinkID=135184).

```yaml
Type: SwitchParameter
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5, UNNAMED_PARAMETER_SET_6, UNNAMED_PARAMETER_SET_7
Aliases: 

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### -Authentication
Specifies the mechanism that is used to authenticate the user's credentials. 
Valid values are Default, Basic, Credssp, Digest, Kerberos, Negotiate, and NegotiateWithImplicitCredential. 
The default value is Default.

CredSSP authentication is available only in Windows Vista, Windows Server 2008, and later versions of Windows.

For information about the values of this parameter, see the description of the System.Management.Automation.Runspaces.AuthenticationMechanism enumeration in MSDN.

CAUTION: Credential Security Support Provider (CredSSP) authentication, in which the user's credentials are passed to a remote computer to be authenticated, is designed for commands that require authentication on more than one resource, such as accessing a remote network share.
This mechanism increases the security risk of the remote operation.
If the remote computer is compromised, the credentials that are passed to it can be used to control the network session.

```yaml
Type: AuthenticationMechanism
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: 

Required: False
Position: Named
Default value: Default
Accept pipeline input: False
Accept wildcard characters: False
```

### -CertificateThumbprint
Specifies the digital public key certificate (X509) of a user account that has permission to perform this action.
Enter the certificate thumbprint of the certificate.

Certificates are used in client certificate-based authentication.
They can only be mapped to local user accounts; they do not work with domain accounts.

To get a certificate, use the Get-Item or Get-ChildItem commands in the Windows PowerShell Cert: drive.

```yaml
Type: String
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_5
Aliases: 

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### -ComputerName
Specifies the computers on which the command runs.
The default is the local computer.

When you use the ComputerName parameter, Windows PowerShell creates a temporary connection that is used only to run the specified command and is then closed.
If you need a persistent connection, use the Session parameter.

Type the NETBIOS name, IP address, or fully-qualified domain name of one or more computers in a comma-separated list.
To specify the local computer, type the computer name, "localhost", or a dot (.).

To use an IP address in the value of the ComputerName parameter, the command must include the Credential parameter.
Also, the computer must be configured for HTTPS transport or the IP address of the remote computer must be included in the WinRM TrustedHosts list on the local computer.
For instructions for adding a computer name to the TrustedHosts list, see "How to Add  a Computer to the Trusted Host List" in about_Remote_Troubleshooting.

Note:  On Windows Vista, and later versions of Windows, to include the local computer in the value of the ComputerName parameter, you must open Windows PowerShell with the "Run as administrator" option.

```yaml
Type: String[]
Parameter Sets: UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: Cn

Required: False
Position: 1
Default value: Local computer
Accept pipeline input: False
Accept wildcard characters: False
```

### -ConfigurationName
Specifies the session configuration that is used for the new PSSession.

Enter a configuration name or the fully qualified resource URI for a session configuration.
If you specify only the configuration name, the following schema URI is prepended:  http://schemas.microsoft.com/PowerShell.

The session configuration for a session is located on the remote computer.
If the specified session configuration does not exist on the remote computer, the command fails.

The default value is the value of the $PSSessionConfigurationName preference variable on the local computer.
If this preference variable is not set, the default is Microsoft.PowerShell.
For more information, see about_preference_variables.

```yaml
Type: String
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: 

Required: False
Position: Named
Default value: Microsoft.PowerShell
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -ConnectionUri
Specifies a Uniform Resource Identifier (URI) that defines the connection endpoint of the session.
The URI must be fully qualified.

The format of this string is as follows:

\<Transport\>://\<ComputerName\>:\<Port\>/\<ApplicationName\>

The default value is as follows:

http://localhost:5985/WSMAN

If you do not specify a connection URI, you can use the UseSSL and Port  parameters to specify the connection URI values.

Valid values for the Transport segment of the URI are HTTP and HTTPS.
If you specify a connection URI with a Transport segment, but do not specify a port, the session is created with standards ports: 80 for HTTP and 443 for HTTPS.
To use the default ports for Windows PowerShell remoting, specify port 5985 for HTTP or 5986 for HTTPS.

If the destination computer redirects the connection to a different URI, Windows PowerShell prevents the redirection unless you use the AllowRedirection parameter in the command.

```yaml
Type: Uri[]
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3
Aliases: URI,CU

Required: False
Position: 1
Default value: Http://localhost:5985/WSMAN
Accept pipeline input: False
Accept wildcard characters: False
```

### -Credential
Specifies a user account that has permission to perform this action.
The default is the current user.

Type a user name, such as "User01" or "Domain01\User01", or enter a variable that contains a PSCredential object, such as one generated by the Get-Credential cmdlet.
When you type a user name, you will be prompted for a password.

```yaml
Type: PSCredential
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: 

Required: False
Position: Named
Default value: Current user
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

### -EnableNetworkAccess
Adds an interactive security token to loopback sessions.
The interactive token lets you run commands in the loopback session that get data from other computers.
For example, you can run a command in the session that copies XML files from a remote computer to the local computer.

A "loopback session" is a PSSession that originates and terminates on the same computer.
To create a loopback session, omit the ComputerName parameter or set its value to ".", "localhost", or the name of the local computer.

By default, loopback sessions are created with a network token, which might not provide sufficient permission to authenticate to remote computers.

The EnableNetworkAccess parameter is effective only in loopback sessions.
If you use the EnableNetworkAccess parameter when creating a session on a remote computer, the command succeeds, but the parameter is ignored.

You can also allow remote access in a loopback session by using the CredSSP value of the Authentication parameter, which delegates the session credentials to other computers.

To protect the computer from malicious access, disconnected loopback sessions that have interactive tokens (those created with the EnableNetworkAccess parameter) can be reconnected only from the computer on which the session was created.
Disconnected sessions that use CredSSP authentication can be reconnected from other computers.
For more information, see Disconnect-PSSession.

This parameter is introduced in Windows PowerShell 3.0.

```yaml
Type: SwitchParameter
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: 

Required: False
Position: Named
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -FilePath
Runs the specified local script on one or more remote computers.
Enter the path and file name of the script, or pipe a script path to Invoke-Command.
The script must reside on the local computer or in a directory that the local computer can access.
Use the ArgumentList parameter to specify the values of parameters in the script.

When you use this parameter, Windows PowerShell converts the contents of the specified script file to a script block, transmits the script block to the remote computer, and runs it on the remote computer.

```yaml
Type: String
Parameter Sets: UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_6
Aliases: PSPath

Required: True
Position: 2
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -HideComputerName
Omits the computer name of each object from the output display.
By default, the name of the computer that generated the object appears in the display.

This parameter affects only the output display.
It does not change the object.

```yaml
Type: SwitchParameter
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5, UNNAMED_PARAMETER_SET_6, UNNAMED_PARAMETER_SET_7
Aliases: HCN

Required: False
Position: Named
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -InDisconnectedSession
Runs a command or script in a disconnected session.

When you use the InDisconnectedSession parameter, Invoke-Command creates a persistent session on each remote computer, starts the command specified by the ScriptBlock or FilePath parameter, and then disconnects from the session.
The commands continue to run in the disconnected sessions.The InDisconnectedSession parameter enables you to run commands without maintaining a connection to the remote sessions.
Also, because the session is disconnected before any results are returned, the InDisconnectedSession parameter assures that all command results are returned to the reconnected session, instead of being split between sessions.

You cannot use the InDisconnectedSession parameter with the Session parameter or the AsJob parameter.

Commands that use the InDisconnectedSession parameter return a PSSession object that represents the disconnected session.
They do not return the command output.
To connect to the disconnected session, use the Connect-PSSession or Receive-PSSession cmdlets.
To get the results of commands that ran in the session, use the Receive-PSSession cmdlet.To run commands that generate output in a disconnected session, set the value of the OutputBufferingMode session option to Drop.
If you intend to connect to the disconnected session, set the idle timeout in the session so that it provides sufficient time for you to connect before deleting the session.

You can set the output buffering mode and idle timeout in the SessionOption parameter or in the $PSSessionOption preference variable.
For more information about session options, see New-PSSessionOption and about_Preference_Variables.

For more information about the Disconnected Sessions feature, see about_Remote_Disconnected_Sessions.

This parameter is introduced in Windows PowerShell 3.0.

```yaml
Type: SwitchParameter
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: Disconnected

Required: False
Position: Named
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -InputObject
Specifies input to the command.
Enter a variable that contains the objects or type a command or expression that gets the objects.

When using InputObject, use the $input automatic variable in the value of the ScriptBlock parameter to represent the input objects.

```yaml
Type: PSObject
Parameter Sets: (All)
Aliases: 

Required: False
Position: Named
Default value: 
Accept pipeline input: True (ByValue)
Accept wildcard characters: False
```

### -JobName
Specifies a friendly name for the background job.
By default, jobs are named "Job\<n\>", where \<n\> is an ordinal number.

If you use the JobName parameter in a command, the command is run as a job, and Invoke-Command returns a job object, even if you do not include the AsJob parameter in the command.

For more information about Windows PowerShell background jobs, see about_Jobs (http://go.microsoft.com/fwlink/?LinkID=113251).

```yaml
Type: String
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5, UNNAMED_PARAMETER_SET_6, UNNAMED_PARAMETER_SET_7
Aliases: 

Required: False
Position: Named
Default value: Job<n>
Accept pipeline input: False
Accept wildcard characters: False
```

### -Port
Specifies the network port  on the remote computer used for this command.
To connect to a remote computer, the remote computer must be listening on the port that the connection uses. 
The default ports are 5985 (the WinRM port for HTTP) and 5986 (the WinRM port for HTTPS).

Before using an alternate port, configure the WinRM listener on the remote computer to listen at that port.
To configure the listener, type the following two commands at the Windows PowerShell prompt:

Remove-Item -Path WSMan:\Localhost\listener\listener* -Recurse

New-Item -Path WSMan:\Localhost\listener -Transport http -Address * -Port \<port-number\>

Do not use the Port parameter unless you must.
The port that is set in the command applies to all computers or sessions on which the command runs.
An alternate port setting might prevent the command from running on all computers.

```yaml
Type: Int32
Parameter Sets: UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: 

Required: False
Position: Named
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -ScriptBlock
Specifies the commands to run.
Enclose the commands in braces ( { } ) to create a script block.
This parameter is required.

By default, any variables in the command are evaluated on the remote computer.
To include local variables in the command, use the ArgumentList parameter.

```yaml
Type: ScriptBlock
Parameter Sets: UNNAMED_PARAMETER_SET_1, UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_5, UNNAMED_PARAMETER_SET_7
Aliases: Command

Required: True
Position: 1
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -Session
Runs the command in the specified Windows PowerShell sessions (PSSessions).
Enter a variable that contains the PSSessions or a command that creates or gets the PSSessions, such as a New-PSSession or Get-PSSession command.

Runs the command in the specified Windows PowerShell sessions (PSSessions).
Enter a variable that contains the PSSessions or a command that creates or gets the PSSessions, such as a New-PSSession or Get-PSSession command.

When you create a PSSession, Windows PowerShell establishes a persistent connection to the remote computer.
Use a PSSession to run a series of related commands that share data.
To run a single command or a series of unrelated commands, use the ComputerName parameter.

To create a PSSession, use the New-PSSession cmdlet.
For more information, see about_PSSessions.

```yaml
Type: PSSession[]
Parameter Sets: UNNAMED_PARAMETER_SET_6, UNNAMED_PARAMETER_SET_7
Aliases: 

Required: False
Position: 1
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -SessionOption
Sets advanced options for the session. 
Enter a SessionOption object, such as one that you create by using the New-PSSessionOption cmdlet, or a hash table in which the keys are session option names and the values are session option values.

The default values for the options are determined by the value of the $PSSessionOption preference variable, if it is set.
Otherwise, the default values are established by options set in the session configuration.

The session option values take precedence over default values for sessions set in the $PSSessionOption preference variable and in the session configuration.
However, they do not take precedence over maximum values, quotas or limits set in the session configuration.

For a description of the session options, including the default values, see New-PSSessionOption.
For information about the $PSSessionOption preference variable, see about_Preference_Variables (http://go.microsoft.com/fwlink/?LinkID=113248).
For more information about session configurations, see about_Session_Configurations (http://go.microsoft.com/fwlink/?LinkID=145152).

```yaml
Type: PSSessionOption
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: 

Required: False
Position: Named
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -ThrottleLimit
Specifies the maximum number of concurrent connections that can be established to run this command.
If you omit this parameter or enter a value of 0, the default value, 32, is used.

The throttle limit applies only to the current command, not to the session or to the computer.

```yaml
Type: Int32
Parameter Sets: UNNAMED_PARAMETER_SET_2, UNNAMED_PARAMETER_SET_3, UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5, UNNAMED_PARAMETER_SET_6, UNNAMED_PARAMETER_SET_7
Aliases: 

Required: False
Position: Named
Default value: 32
Accept pipeline input: False
Accept wildcard characters: False
```

### -UseSSL
Uses the Secure Sockets Layer (SSL) protocol to establish a connection to the remote computer.
By default, SSL is not used.

WS-Management encrypts all Windows PowerShell content transmitted over the network.
UseSSL is an additional protection that sends the data across an HTTPS, instead of HTTP.

If you use this parameter, but SSL is not available on the port used for the command, the command fails.

```yaml
Type: SwitchParameter
Parameter Sets: UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: 

Required: False
Position: Named
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -NoNewScope
Runs the specified command in the current scope.
By default, Invoke-Command runs commands in their own scope.

This parameter is valid only in commands that are run in the current session, that is, commands that omit both the ComputerName and Session parameters.

This parameter is introduced in Windows PowerShell 3.0.

```yaml
Type: SwitchParameter
Parameter Sets: UNNAMED_PARAMETER_SET_1
Aliases: 

Required: False
Position: Named
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -SessionName
Specifies a friendly name for a disconnected session.
You can use the name to refer to the session in subsequent commands, such as a Get-PSSession command.
This parameter is valid only with the InDisconnectedSession parameter.

This parameter is introduced in Windows PowerShell 3.0.

```yaml
Type: String[]
Parameter Sets: UNNAMED_PARAMETER_SET_4, UNNAMED_PARAMETER_SET_5
Aliases: 

Required: False
Position: Named
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

## INPUTS

### System.Management.Automation.ScriptBlock
You can pipe a command in a script block to Invoke-Command.
Use the $input automatic variable to represent the input objects in the command.

## OUTPUTS

### System.Management.Automation.PSRemotingJob, System.Management.Automation.Runspaces.PSSession, or the output of the invoked command
When you use the AsJob parameter, Invoke-Command returns a job object.
When you use the InDisconnectedSession parameter, Invoke-Command returns a PSSession object.
Otherwise, it returns the output of the invoked command (the value of the ScriptBlock parameter).

## NOTES
On Windows Vista, and later versions of Windows, to use the ComputerName parameter of Invoke-Command to run a command on the local computer, you must open Windows PowerShell with the "Run as administrator" option.

When you run commands on multiple computers, Windows PowerShell connects to the computers in the order in which they appear in the list.
However, the command output is displayed in the order that it is received from the remote computers, which might be different.

Errors that result from the command that Invoke-Command runs are included in the command results.
Errors that would be terminating errors in a local command are treated as non-terminating errors in a remote command.
This strategy ensures that terminating errors on one computer do not terminate the command on all computers on which it is run.
This practice is used even when a remote command is run on a single computer.

If the remote computer is not in a domain that the local computer trusts, the computer might not be able to authenticate the user's credentials.
To add the remote computer to the list of "trusted hosts" in WS-Management, use the following command in the WSMAN provider, where \<Remote-Computer-Name\> is the name of the remote computer:

Set-Item -Path WSMan:\Localhost\Client\TrustedHosts -Value \<Remote-Computer-Name\>

In Windows PowerShell 2.0, you cannot use the Select-Object cmdlet to select the PSComputerName property of the object that Invoke-Command returns.
Instead, to display the value of the PSComputerName property, use the dot method to get the PSComputerName property value ($result.PSComputerName), use a Format cmdlet, such as the Format-Table cmdlet, to display the value of the PSComputerName property, or use a Select-Object command where the value of the property parameter is a calculated property that has a label other than "PSComputerName".

This limitation does not apply to Windows PowerShell 3.0 or later versions of Windows PowerShell.

When you disconnect a PSSession, such as by using the InDisconnectedSession parameter, the session state is Disconnected and the availability is None.

The value of the State property is relative to the current session.
Therefore, a value of Disconnected means that the PSSession is not connected to the current session.
However, it does not mean that the PSSession is disconnected from all sessions.
It might be connected to a different session.
To determine whether you can connect or reconnect to the session, use the Availability property.

An Availability value of None indicates that you can connect to the session.
A value of Busy indicates that you cannot connect to the PSSession because it is connected to another session.

For more information about the values of the State property of sessions, see "RunspaceState Enumeration" in MSDN at http://msdn.microsoft.com/library/windows/desktop/system.management.automation.runspaces.runspacestate(v=VS.85).aspxhttp://msdn.microsoft.com/library/windows/desktop/system.management.automation.runspaces.runspacestate(v=VS.85).aspx.

For more information about the values of the Availability property of sessions, see RunspaceAvailability Enumeration at http://msdn.microsoft.com/library/windows/desktop/system.management.automation.runspaces.runspaceavailability(v=vs.85).aspxhttp://msdn.microsoft.com/library/windows/desktop/system.management.automation.runspaces.runspaceavailability(v=vs.85).aspx.

## RELATED LINKS

[Enter-PSSession](4e1e012b-51df-4fea-9ff2-dc859eee13fe)

[Exit-PSSession](f13cbf38-6bd1-4db3-9ef8-52388237adc7)

[Get-PSSession](b2b10531-d0df-4746-b877-e75c09955cb6)

[Invoke-Item](00000000-0000-0000-0000-000000000000)

[New-PSSession](76f6628c-054c-4eda-ba7a-a6f28daaa26f)

[Remove-PSSession](a48e762a-80d9-4545-92e3-745f4e992e22)

[WSMan Provider](4c3d8d36-4f7a-4211-996f-64110e4b2eb7)

[about_PSSessions](7a9b4e0e-fa1b-47b0-92f6-6e2995d70acb)

[about_Remote](9b4a5c87-9162-4adf-bdfe-fbc80b9b8970)

[about_Remote_Disconnected_Sessions](adc4b4b4-3d51-4e01-9be2-5a74f530e3f3)

[about_Remote_Variables](a31e2e7f-7c66-492c-86ef-d588912feb7d)

[about_Scopes](807a5b29-3f02-4b97-8eed-854869936017)
