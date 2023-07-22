# File types which can be exploited

# Jamovi OMV files
OMV files are used for Jamovi to display data.     
These files are basically zip archives so:      

- Run `unzip -l <file>`     
**If `xdata.json` is present**     
Run `unzip <file>` and open `xdata.json` and read data.

## Reference
- [https://docs.jamovi.org/_pages/info_file-format.html#xdata-json](https://docs.jamovi.org/_pages/info_file-format.html#xdata-json)