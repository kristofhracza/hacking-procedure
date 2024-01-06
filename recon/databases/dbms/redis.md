# Redis

## Data types and retrieve commands
```
String      GET <key>
HASH        HGETALL <key>
LIST        lrange <key> <start> <end>
SET         smembers <key>
SORTED SETS ZRANGEBYSCORE <key> <min> <max>
```

## Connection
```sh
# After Redis 6.0
redis-cli -h <ip> -a <password> 
```

## References
- [https://redis.io/docs/](https://redis.io/docs/)