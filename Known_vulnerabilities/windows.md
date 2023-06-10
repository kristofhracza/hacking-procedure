# Azure admin right on user account
Modify and use the POC script from [here](https://blog.xpnsec.com/azuread-connect-for-redteam/).     

## Edit the script
Change the `$client` variable to this line, where the IP is the address of the victim.
```ps1
$client = new-object System.Data.SqlClient.SqlConnection -ArgumentList "Server = <ip>; Data Source=(localdb)\.\ADSync;Initial Catalog=ADSync"
```

*Next, download and execute the script*

### References
- [https://blog.xpnsec.com/azuread-connect-for-redteam/](https://blog.xpnsec.com/azuread-connect-for-redteam/)
