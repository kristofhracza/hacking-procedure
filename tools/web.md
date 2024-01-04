
# Resources
## Hashcat hash examples
[https://hashcat.net/wiki/doku.php?id=example_hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)


## Requestly
[Requestly](https://chromewebstore.google.com/detail/requestly-open-source-htt/mdnleldcmiljblolnjhpnblkcekpdkpa?pli=1) is a chrome extension used for debugging sites.     
However I mainly use it for editing headers.    


# Tools
## WasmFiddle for WASM
[https://wasdk.github.io/WasmFiddle/](https://wasdk.github.io/WasmFiddle/)     
Allows for C code to be turned into Web Assembly.

## Cewl
CeWL (Custom Word List generator) is a ruby app which spiders a given URL, up to a specified depth, and returns a list of words which can then be used for password crackers such as John the Ripper. Optionally, CeWL can follow external links.      

CeWL can also create a list of email addresses found in mailto links. These email addresses can be used as usernames in brute force actions. 

```bash
cewl -w <wordlist> -d 10 -m 1 <url>
```
