## PowerShell


This script sends an automated email alert when a remote user logs into an RDP session. Place the script in the Windows root directory (C:\).
In order to run this script you'll need to create a windows event that gets triggered when a remote user logs in, more specifically the icrosoft Windows TerminalServices LocalSessionManager/Operational 
log when event ID 25 have executed.

Copy the code below and place it in a Powershell script (.ps1) and place it in the C:\ directory.

```
$rdpinfo = Get-WinEvent -LogName Microsoft-Windows-TerminalServices-LocalSessionManager/Operational | Where {$_.Id -eq "25"}| select -ExpandProperty Message -First 1


$email = "XXXX@test.com" #Add sender email here 

$sendto = "XXXX@test.com" #Add email recipient
 
$pass = "XXX" #Email Password for sender 
$port = "587"
 
$smtpServer = "smtp.XXX.com" #Add SMTP server from sender

$msg = new-object Net.Mail.MailMessage 
$smtp = new-object Net.Mail.SmtpClient($smtpServer, $port) #also ref here
$smtp.EnableSsl = $true 
$msg.From = "$email"  
$msg.To.Add("$sendto") 
$msg.BodyEncoding = [system.Text.Encoding]::ASCII 
$msg.SubjectEncoding = [system.Text.Encoding]::ASCII
$msg.IsBodyHTML = $true  
$msg.Subject = "RDP Login Alert" 
$msg.Body = "<p>$rdpinfo</p>"
$SMTP.Credentials = New-Object System.Net.NetworkCredential("$email", "$pass"); 
$smtp.Send($msg)
exit
```
