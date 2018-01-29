## PowerShell


This script sends an automated email alert when a remote user logs into an RDP session. Place the script in the Windows root directory (C:\).
In order to run this script, you'll need to create a windows event that gets triggered when a remote user logs in, more specifically the Microsoft Windows TerminalServices LocalSessionManager/Operational 
log when event ID 25 have executed.

Copy the code below and place it in a Powershell script (.ps1) then copy to the C:\ directory.

Fill in your information such as sender email, email recipient, sender pass and smtp server form sender.
(I made an email for the sole purpose of sending these alerts, I would recommend the same. Unless you want a legit email to get backlisted which could happen easily).

p.s I know the password shouldn't be in cleartext, when I get some free time ill fix this.

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
Next open up the Computer Management console and browse to 'Task Scheduler'. On the right-hand pane click 'Create Basic Task'.

1. Give the task a name, in our case 'Rdp Login Alert' will do. Click Next.
2. For the trigger option select 'when a specific event is logged', click next.
   (Now here is the part you must pay close attention to!) Click the drop-down menu for the log selection, click 'Microsoft-Windows-        TerminalServices-LocalSessionManager/operational'
   Next select 'TerminalServices-LocalSessionManager' for source.
   For event ID type in 25, click next.
3. When asked what action would you like performed select 'Start a program', click next.
4.  The next page will ask you for a 'program/script', 'arguments' and 'start in'.
    For program script type in 'PowerShell' and for arguments type in the path to the script 'C:\.\RDP_Login_Alert.ps1' or whatever you     called the script (just make sure it’s in the C:\ directory). Leave start in blank, click next and finish.

5.One final thing is to enable under properties click 'Run weather user is logged on not' and tick the box 'Run with highest privileges', be sure to click 'Ok'.

Then the script should run when a remote user logs in. 

I made this tutorial as there was very little material out about how to set it up from start to finish, I hope this helps.
