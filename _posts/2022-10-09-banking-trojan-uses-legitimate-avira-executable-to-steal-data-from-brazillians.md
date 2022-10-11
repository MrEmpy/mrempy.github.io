![](https://miro.medium.com/max/720/0*E6WigKr7-cB62f75.jpg)

In the year 2021, a wave of email phishing attacks were made, the trojan named as Javali is one of them. It was mainly distributed in Latin America, and it is a banking trojan. The malware uses a technique known as DLL Side-Loading, accompanied by an obfuscator to bypass antivirus.

## Trojan Analysis
The malware initiates its attack through phishing email, where there is a message referring to a receipt of an invoice.

![](https://miro.medium.com/max/720/0*y1XtTNME5QuFq44z)

After clicking the button, a ZIP file is downloaded to the machine called “ATT00001.zip”. Opening the file, another ZIP file is found, but with a password. The name of this file is “Convocação-tkqmmejowrwnsomeilngpffwp.zip”.

![](https://miro.medium.com/max/640/1*YRZfd4g7SeXbvBZOTb7EKg.png)

The file’s password was in its description.

After sending the password, the zip file stores an MSI file named “Intimação 239378612781861371863277523766832.msi”

![](https://miro.medium.com/max/640/1*SIGPeskuUimljsU3iNffhg.png)

When running the MSI file, nothing very relevant appears on the victim’s screen.

![](https://miro.medium.com/max/498/1*85ZAwz1t-YEHry5cxbDMhw.png)

Analyzing the network traffic, it is possible to find a communication with a server through the HTTP protocol, the request is made at the endpoint “/pdx.7/pdx7.png”.

![](https://miro.medium.com/max/720/1*UJD3Cl0jJa1OpotjlJIAuw.png)

![](https://miro.medium.com/max/720/1*SnmRauSrEU4wFFgA4_FkGw.png)

The file is called “pdx7.png”, however it is a ZIP file, it will be extracted in some user directory.

Using Process Monitor, we detected that the ZIP file was placed in the “C:/Users/Public/Videos” directory. The file was renamed to “TAFGBNELMT_TAFGBNELMT_TAFGBNELMT.zip”. Then it is extracted in the same folder where it was placed.

![](https://miro.medium.com/max/720/1*AWcY6jk6kZJcSnAlb19T7Q.png)

![](https://miro.medium.com/max/720/1*RahgeqYHPe8fjqsngvfi5g.png)

The extracted files were these:

![](https://miro.medium.com/max/720/1*1TObopmcF_42V6m_zQ14Sg.png)

The malware maintains persistence through the Windows directory known as auto-execution of executables, a registry is also created to maintain persistence.

![](https://miro.medium.com/max/720/1*36POMvEfA316F4m4jaPFtg.png)

![](https://miro.medium.com/max/720/1*q1ZSmf4ZQpLmuwzDkRg4Sw.png)

There is only one executable file called “CWRBGQVWKSZCBBMñPBDXNICNYNWHSJHn.exe”. Analyzing it on VirusTotal, no antivirus detected it as a virus.

![](https://miro.medium.com/max/720/1*xqzDN03G80yxjO9ibVha3w.png)

The reason for this to have happened is that this executable is legitimate from Avira. We can also see through the signature.

![](https://miro.medium.com/max/640/1*CaveMYbYl-c9hizTXqq0DQ.png)

Looking at the property of the DLL file called “Avira.OE.NativeCore.dll”, we see that its size is more than 600MB, something that is suspicious, because it is compressed in a ZIP file and the result is different.

![](https://miro.medium.com/max/640/1*7KW1kFoR7NSoDyWD8ooPBA.png)

![](https://miro.medium.com/max/640/1*7KW1kFoR7NSoDyWD8ooPBA.png)

When running the EXE file and analyzing the network traffic, the victim’s machine makes a request to “https://ipinfo.io/json", and then makes a DNS request to “pedrinhooperador.duckdns.org” and “pedrexpgbl .duckdns.org”.

![](https://miro.medium.com/max/720/1*HYH6PzGkLrSu07ah7fmgcQ.png)

About all the information that was found, I found that the DLL “Avira.OE.NativeCore.dll” was the malware in this scenario, and that it was executed after the user ran the legitimate Avira EXE, which would lead to the execution of a technique called DLL Side-Loading.

Analyzing some information about the DLL, the presence of an obfuscator (Enigma) and an anti-debugger (Obsidium) was detected.

![](https://miro.medium.com/max/720/0*9xW3dLT1HQoTwJfj.png)

In searching for information about how the obfuscator worked, I discovered that it uses the VirtualAlloc function a lot, which made me interpret that this obfuscator opens spaces in memory to gradually add a few bytes of malware. On this theory, I ran a program to see machine processes and DLLs in each executable that was present in the tasks table. I was successfully able to extract the malicious DLL, now with a smaller size.

![](https://miro.medium.com/max/720/0*X8Lxw6Pt32_alzku.png)

After replacing the original DLL to the unzipped DLL and running the malware, I got an error message about the DLL. My extraction method was not the best.

I started reverse engineering the uncompressed DLL using Ghidra.

![](https://miro.medium.com/max/376/1*qNVf5QITtgNT-EB0cnVsbw.png)

At first glance, I noticed that there were several sections in the malware body. I started analyzing them one by one and discovered something interesting about the malware.

![](https://miro.medium.com/max/720/1*X3H-k-AcXo2RdRzqeDutJA.png)

![](https://miro.medium.com/max/640/1*rRRNp2K0WsyhEqbR4d7EXw.png)

According to the Central Bank’s security standards for transactions via Internet Banking, the security plugin is being updated to adapt to the system.

I left the malware running on the machine for a long time and I didn’t see this screen, maybe something needed to be done for it to appear.

There wasn’t just this screen, there were others.

![](https://miro.medium.com/max/640/1*sW_Y4qPhWWDG0DEYe4z2CQ.png)

![](https://miro.medium.com/max/640/1*OaOyBrCk92l9sDhqrYzIpQ.png)

![](https://miro.medium.com/max/640/1*ktDp_hu31JkANlgqYuZT3Q.png)

All these images are related to Banks, mainly some from Brazil. I continued with the analysis, I had found features of the malware itself but I could not proceed with the analysis.

```
?GetOldOeSettingsPath@OeProductInfo@NativeCore@OE@Avira@@ABE?AV?$basic_string
?GetOeSettingsPath@OeProductInfo@NativeCore@OE@Avira@@ABE?AV?$basic_string@_W
?GetOeSettingsPath@OeProductInfo@NativeCore@OE@Avira@@ABE?AV?$basic_string@_W
?GetOeSettingsContent@OeProductInfo@NativeCore@OE@Avira@@ABE?AV?$basic_string
?GetLanguage@OeProductInfo@NativeCore@OE@Avira@@QBE?AV?$basic_string@_WU?$cha
?GetDownloadSource@OeProductInfo@NativeCore@OE@Avira@@QBE?AV?$basic_string@_W
??4OeProductInfo@NativeCore@OE@Avira@@QAEAAV0123@ABV0123@@Z
```

Later, I obtained the IP address of “pedrinhooperador.duckdns.org” to get information about.

![](https://miro.medium.com/max/720/1*aWsy3kqVMLT4PU-4d5axig.png)

I started a mapping using Nmap on the target.

```
Nmap scan report for pedrinhooperador.duckdns.org (20.206.70.181)
Host is up (0.017s latency).
Not shown: 992 closed ports
PORT      STATE SERVICE          VERSION
135/tcp   open  msrpc            Microsoft Windows RPC
139/tcp   open  netbios-ssn      Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds?
2869/tcp  open  http             Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-aspnet-debug: ERROR: Script execution failed (use -d to debug)
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
3389/tcp  open  ms-wbt-server    Microsoft Terminal Services
|_sslv2-drown:
5357/tcp  open  http             Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-stored-xss: Couldn't find any stored XSS vulnerabilities.
8081/tcp  open  blackice-icecap?
38292/tcp open  landesk-cba?
Device type: general purpose|specialized
Running (JUST GUESSING): Microsoft Windows XP|2008|7 (92%), AVtech embedded (88%), FreeBSD 6.X|10.X (88%)
OS CPE: cpe:/o:microsoft:windows_xp::sp3 cpe:/o:freebsd:freebsd:6.2 cpe:/o:freebsd:freebsd:10.3 cpe:/o:microsoft:windows_server_2008 cpe:/o:microsoft:windows_7
Aggressive OS guesses: Microsoft Windows XP SP3 (92%), AVtech Room Alert 26W environmental monitor (88%), FreeBSD 6.2-RELEASE (88%), FreeBSD 10.3-STABLE (86%), Microsoft Windows XP SP2 (85%), Microsoft Windows Server 2008 (85%), Microsoft Windows Server 2008 SP1 or Windows Server 2008 R2 (85%), Microsoft Windows 7 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 19 hops
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
Host script results:
|_samba-vuln-cve-2012-1182: Could not negotiate a connection:SMB: Failed to receive bytes: ERROR
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: Could not negotiate a connection:SMB: Failed to receive bytes: ERROR
TRACEROUTE (using port 22/tcp)
HOP RTT       ADDRESS
1   7.91 ms   10.0.0.2
2   3.59 ms   10.255.254.254
3   3.09 ms   10.100.100.1
4   7.38 ms   100.65.31.34
5   119.16 ms 100.68.17.94
6   123.13 ms 100.64.10.12
7   ...
8   123.14 ms ae26-0.icr01.cpq02.ntwk.msn.net (104.44.238.157)
9   ... 18
19  17.04 ms  20.206.70.181
```

Looking at the result, I saw that there were HTTP ports on the target’s server, maybe it would be the command and control server (C2) that received the victims’ data. Currently the server is inactive, and therefore, it was not possible to proceed with the analysis.

There is an article about the malware that shows in detail how the C2 server works.

According to the Security Informatica website, this is the image of the panel where the victims’ data was stored:

![](https://miro.medium.com/max/720/1*AIWEWhEIENmuPpAqu-Yzrg.png)

[More details](https://seguranca-informatica.pt/latin-american-javali-trojan-weaponizing-avira-antivirus-legitimate-injector-to-implant-malware/#.Y0LgY3vMLZt)

## Final result
With everything that has been seen, we know that the Javali trojan is malware with the aim of stealing bank account data, with 18 types of screens. The malware uses DLL Side-Loading technique to bypass some antivirus using a legitimate Avira executable. The information obtained is sent to a C2 server with banking information and the geolocation of the victims. The author is a Brazilian whose first name is Pedro.

Mitre Att&ck Matrix
![](https://miro.medium.com/max/720/1*hwCowD8tDFHBoC4Gvh17gA.png)

![](https://miro.medium.com/max/720/1*_xXErqbd32SBZp7SqIRQFQ.png)

[More details](https://www.joesandbox.com/analysis/466251/0/html)
