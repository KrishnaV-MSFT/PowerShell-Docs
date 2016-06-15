---
external help file: PSITPro4_WSMan.xml
online version: http://go.microsoft.com/fwlink/p/?linkid=294037
schema: 2.0.0
---

# Enable-WSManCredSSP
## SYNOPSIS
Enables Credential Security Support Provider (CredSSP) authentication on a client or on a server computer.

## SYNTAX

```
Enable-WSManCredSSP [-Role] <String> [[-DelegateComputer] <String[]>] [-Force]
```

## DESCRIPTION
The Enable-WSManCredSPP cmdlet enables CredSSP authentication on a client or on a server computer.
When CredSSP authentication is used, the user's credentials are passed to a remote computer to be authenticated.
This type of authentication is designed for commands that create a remote session from within another remote session.
For example, you use this type of authentication if you want to run a background job on a remote computer.

This cmdlet is used to enable CredSSP on the client by specifying Client in the Role parameter.
The cmdlet then performs the following:

- Enables CredSSP on the client. The WS-Management setting \<localhost|computername\>\Client\Auth\CredSSP is set to true.
- Sets the Windows CredSSP policy AllowFreshCredentials to WSMan/Delegate on the client.
- Note: These settings allow the client to delegate explicit credentials to a server when server authentication is achieved.

This cmdlet is used to enable CredSSP on the server by specifying Server in the Role parameter.
The cmdlet then performs the following:

- Enables CredSSP on the server. The WS-Management setting \<localhost|computername\>\Service\Auth\CredSSP is set to true.
- Note: This policy setting allows the server to act as a delegate for clients.

Caution: CredSSP authentication delegates the user's credentials from the local computer to a remote computer.
This practice increases the security risk of the remote operation.
If the remote computer is compromised, when credentials are passed to it, the credentials can be used to control the network session.

To disable CredSSP authentication, use the Disable-WSManCredSSP cmdlet.

## EXAMPLES

### -------------------------- EXAMPLE 1 --------------------------
```
PS C:\>enable-wsmancredssp -role client -delegatecomputer server02.accounting.fabrikam.com
cfg         : http://schemas.microsoft.com/wbem/wsman/1/config/client/auth
lang        : en-US
Basic       : true
Digest      : true
Kerberos    : true
Negotiate   : true
Certificate : true
CredSSP     : true
```

This command allows the client credentials to be delegated to the server02 computer.

### -------------------------- EXAMPLE 2 --------------------------
```
PS C:\>enable-wsmancredssp -role client -delegatecomputer *.accounting.fabrikam.com
cfg         : http://schemas.microsoft.com/wbem/wsman/1/config/client/auth
lang        : en-US
Basic       : true
Digest      : true
Kerberos    : true
Negotiate   : true
Certificate : true
CredSSP     : true
```

This command allows the client credentials to be delegated to all the computers in the accounting.fabrikam.com domain.

### -------------------------- EXAMPLE 3 --------------------------
```
PS C:\>enable-wsmancredssp -role client -delegatecomputer server02.accounting.fabrikam.com, server03.accounting.fabrikam.com, server04.accounting.fabrikam.com
cfg         : http://schemas.microsoft.com/wbem/wsman/1/config/client/auth
lang        : en-US
Basic       : true
Digest      : true
Kerberos    : true
Negotiate   : true
Certificate : true
CredSSP     : true
```

This command allows the client credentials to be delegated to multiple computers.

### -------------------------- EXAMPLE 4 --------------------------
```
PS C:\>enable-wsmancredssp -role server
```

This command allows a computer to act as a delegate for another.
The Enable-WSManCredSSP cmdlet (shown in the earlier examples) only enables CredSSP authentication on the client, and specifies the remote computers that can act on it's behalf.
In order for the remote computer to act as a delegate for the client, the CredSSP item in the Service node of WSMan must be set to true.
This example sets the the CredSSP item in the Service node of WSMan to true.

### -------------------------- EXAMPLE 5 --------------------------
```
PS C:\>connect-wsman server02
set-item wsman:\server02\service\auth\credSSP -value $true
```

This command allows a computer to act as a delegate for another computer.
The Enable-WSManCredSSP commands that are shown in the earlier examples enable CredSSP authentication only on the client computer, and they specify the remote computers that can act on behalf of the client computer.
For the remote computer to act as a delegate for the client computer, the CredSSP item in the Service directory of the WSMan provider must be set to true.

In this example, the first command creates a connection to the remote server02 computer.

The second command sets the credSSP value on the remote server02 computer, which allows the remote computer to act as a delegate.

## PARAMETERS

### -DelegateComputer
Allows the client credentials to be delegated to the server or servers that are specified by this parameter.
The value of this parameter should be a fully qualified domain name.

If the Role parameter specifies Client, the DelegateComputer parameter is mandatory.

If the Role parameter specifies Server, the DelegateComputer parameter is not allowed.

```yaml
Type: String[]
Parameter Sets: (All)
Aliases: 

Required: False
Position: 2
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -Force
Enables CredSSP without first prompting the user.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: 

Required: False
Position: Named
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

### -Role
Accepts one of two possible values: Client or Server. 
These values specify whether CredSSP should be enabled as a client or as a server.

If the Role parameter specifies Client, the cmdlet performs the following:

- Enables CredSSP on the client. The WS-Management setting \<localhost|computername\>\Client\Auth\CredSSP is set to true.
- Sets the Windows CredSSP policy AllowFreshCredentials to WSMan/Delegate on the client.
- Note: These settings allow the client to delegate explicit credentials to a server when server authentication is achieved.

If the Role parameter specifies the Server, the cmdlet performs the following:

- Enables CredSSP on the server. The WS-Management setting \<localhost|computername\>\Service\Auth\CredSSP is set to true.
- Note: This policy setting allows the server to act as a delegate for clients.

```yaml
Type: String
Parameter Sets: (All)
Aliases: 

Required: True
Position: 1
Default value: 
Accept pipeline input: False
Accept wildcard characters: False
```

## INPUTS

### None
This cmdlet does not accept any input.

## OUTPUTS

### System.Xml.XmlElement
If CredSSP authentication is successfully enabled, this cmdlet generates an XMLElement object.

## NOTES

## RELATED LINKS

[Connect-WSMan](74e46714-497f-4306-be84-109ab5b654cc)

[Disable-WSManCredSSP](01c110d4-056e-48d2-b9a0-ea62c85a2c0e)

[Disconnect-WSMan](6d7ef9f8-ac28-46b1-a3ab-e0820c440c01)

[Get-WSManCredSSP](985673c4-eb15-47be-a2a2-22f2080d3242)

[Get-WSManInstance](06dae292-bd46-4f6a-a246-c7c7c057db90)

[Invoke-WSManAction](2b565381-48a7-4b3e-b0a5-61a53d320a9a)

[New-WSManInstance](3b68a31e-0b27-41e5-ad6f-83f243655651)

[New-WSManSessionOption](b8d84d86-a913-4aa6-8c72-80fe7938d782)

[Remove-WSManInstance](8061efbd-5747-4e33-952b-ec3e2d07f20f)

[Set-WSManInstance](c7af8b30-3ca0-4330-8f24-60e2bf94053a)

[Set-WSManQuickConfig](6a0e74db-94a7-445a-8485-f64ca1a4948a)

[Test-WSMan](b8c6fb53-48fb-411b-a989-618a74a68067)
