![](https://miro.medium.com/max/640/1*k3Nmb_CTNFtYVAlkbDVjOA.png)

Local Security Authority Subsystem Service, or better known as LSASS, is one of the core software of a Windows server. It is responsible for enforcing the security policy on the system. It verifies users connecting to a Windows computer or server, handles password changes, and creates access tokens.

Because it is important software, it is always a target for hackers who try to extract your information in order to escalate privileges within an Active Directory, through NTLM hash dumps for example.

## 1st Method: Using Procump

ProcDump is a command-line application used to monitor an application for CPU spikes and create memory dumps during a spike. If a malicious user has administrative access to the machine, he is able to dump data from Lsass’s memory, and could gain sensitive information.

```
.\procdump64.exe -accepteula -ma lsass.exe lsass.txt
```

![](https://miro.medium.com/max/720/1*dW652XTaxK0DY2LldELVVA.png)

```
sekurlsa::minidump lsass.txt.dump
sekurlsa::logonpasswords
```

![](https://miro.medium.com/max/720/1*_e2KRWNQlFAdkfc8tIz-dQ.png)

## 2nd Method: Task Manager

Task Manager is a task manager, system monitor, and boot manager included with Microsoft Windows systems. It allows users to dump information from processes including Lsass. If you have access to a machine’s Remote Desktop Protocol (RDP), it is a great choice, especially if you have strong protection solutions.

![](https://miro.medium.com/max/720/1*fiYCmfqV38vW6ISZWmEYjQ.png)

![](https://miro.medium.com/max/720/1*ZMU_Btt7kq6JN9D6Pcklmg.png)

![](https://miro.medium.com/max/720/1*pk16dsZdcZjfktnatH42Xg.png)

## Conclusion

The methods presented are great to use when there is a protection solution in Active Directory, as the executables are legitimate, so most antiviruses will not detect it as a malicious entry.
