## Redis快速入门

Redis是一个开源，高级的键值存储和一个适用的解决方案，用于构建高性能，可扩展的web应用程序。

Redis有如下三个主要特点。

* Redis将其数据库完全保存于内存中，仅适用磁盘进行持久化
* 与其他键值数据存储相比，Redis有一组相对丰富的数据类型
* Redis可以将数据复制到任意数量的从机中。



[Redis的官网](http://www.redis.io)

以下是Redis的一些优点:

* Redis非常快，每秒可执行大约`110000`次的设置(`SET`)操作，每秒大约可执行`81000`次的读取/获取(`GET`)操作。
* **支持丰富的数据类型** - Redis支持开发人员常用的大多数数据类型，例如列表，集合，排序集和散列等等。这使得Redis很容易被用来解决各种问题，因为我们知道哪些问题可以更好使用地哪些数据类型来处理解决。
* **操作具有原子性** - 所有Redis操作都是原子操作，这确保如果两个客户端并发访问，Redis服务器能接收更新的值。
* **多实用工具** - Redis是一个多实用工具，可用于多种用例，如：缓存，消息队列(Redis本地支持发布/订阅)，应用程序中的任何短期数据，例如，web应用程序中的会话，网页命中计数等。



## Redis与其他键值存储系统

* Redis是键值数据库系统的不同进化路线，它的值可以包含更复杂的数据类型，可在这些数据类型上定义原子操作
* Redis是一个内存数据库，但在磁盘数据库上是持久的,因此它代表了一个不同的权衡，在这种情况下，在不能大于存储器的数据集的限制下实现非常高的写和读速度
* 内存数据库的另一个优点是，它与磁盘上的相同数据结构相比，复杂数据结构在内存洪存储表示更容易操作。因此，Redis可以做很少的内部复杂性。



## 1. Redis环境安装配置



## 在Ubuntu上安装Redis



要在Ubuntu上安装Redis，打开终端并键入以下命令 -

```
[yiibai@ubuntu:~]$ sudo apt-get update 
[yiibai@ubuntu:~]$ sudo apt-get install redis-server
```

这将在Ubuntu机器上安装Redis。



**启动Redis**

```
[yiibai@ubuntu:~]$ redis-server
[2988] 07 Feb 17:09:42.485 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
[2988] 07 Feb 17:09:42.488 # Unable to set the max number of files limit to 10032 (Operation not permitted), setting the max clients configuration to 3984.
[2988] 07 Feb 17:09:42.490 # Warning: 32 bit instance detected but no memory lim
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 2.8.4 (00000000/0) 32 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in stand alone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 2988
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           http://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

[2988] 07 Feb 17:09:42.581 # Server started, Redis version 2.8.4
[2988] 07 Feb 17:09:42.582 # WARNING overcommit_memory is set to 0! Background s                                                                                        ' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_m
[2988] 07 Feb 17:09:42.582 * The server is now ready to accept connections on po
```

**检查Redis是否正在工作**

```
[yiibai@ubuntu:~]$ redis-cli


Shell
```

这将打开一个**redis**提示，如下所示 -

```
redis 127.0.0.1:6379>


Shell
```

在上面的提示中，`127.0.0.1`是计算机的IP地址，`6379`是运行**Redis**服务器的端口。 现在键入以下`PING`命令。

```
redis 127.0.0.1:6379> ping 
PONG


Shell
```

这表明**Redis**已成功在您的计算机上安装了。

在Ubuntu上安装**Redis桌面管理**

要在Ubuntu上安装**Redis**桌面管理器，可从 <http://redisdesktop.com/download> 下载该软件包，安装即可。

打开下载的软件包并安装。

**Redis桌面管理器**将提供用于管理Redis的键和数据的UI。

## 2. Redis配置

在Redis中，在Redis的根目录下有一个配置文件(`redis.conf`)。当然您可以通过Redis `CONFIG`命令获取和设置所有的**Redis**配置。

**语法**
以下是Redis中的`CONFIG`命令的基本语法。

```
redis 127.0.0.1:6379> CONFIG GET CONFIG_SETTING_NAME


Shell
```

**示例**

```
redis 127.0.0.1:6379> CONFIG GET loglevel  
1) "loglevel" 
2) "notice"


Shell
```

> 要获取所有配置设置，请使用`*`代替`CONFIG_SETTING_NAME`

**示例**

```
redis 127.0.0.1:6379> CONFIG GET *
  1) "dbfilename"
  2) "dump.rdb"
  3) "requirepass"
  4) ""
  5) "masterauth"
  6) ""
  7) "unixsocket"
  8) ""
  9) "logfile"
 10) "/var/log/redis/redis-server.log"
 11) "pidfile"
 12) "/var/run/redis/redis-server.pid"
 13) "maxmemory"
 14) "3221225472"
 15) "maxmemory-samples"
 16) "3"
 17) "timeout"
 18) "0"
 19) "tcp-keepalive"
 20) "0"
 21) "auto-aof-rewrite-percentage"
 22) "100"
 23) "auto-aof-rewrite-min-size"
 24) "67108864"
 25) "hash-max-ziplist-entries"
 26) "512"
 27) "hash-max-ziplist-value"
 28) "64"
 29) "list-max-ziplist-entries"
 30) "512"
 31) "list-max-ziplist-value"
 32) "64"
 33) "set-max-intset-entries"
 34) "512"
 35) "zset-max-ziplist-entries"
 36) "128"
 37) "zset-max-ziplist-value"
 38) "64"
 39) "lua-time-limit"
 40) "5000"
 41) "slowlog-log-slower-than"
 42) "10000"
 43) "slowlog-max-len"
 44) "128"
 45) "port"
 46) "6379"
 47) "databases"
 48) "16"
 49) "repl-ping-slave-period"
 50) "10"
 51) "repl-timeout"
 52) "60"
 53) "repl-backlog-size"
 54) "1048576"
 55) "repl-backlog-ttl"
 56) "3600"
 57) "maxclients"
 58) "3984"
 59) "watchdog-period"
 60) "0"
 61) "slave-priority"
 62) "100"
 63) "min-slaves-to-write"
 64) "0"
 65) "min-slaves-max-lag"
 66) "10"
 67) "hz"
 68) "10"
 69) "no-appendfsync-on-rewrite"
 70) "no"
 71) "slave-serve-stale-data"
 72) "yes"
 73) "slave-read-only"
 74) "yes"
 75) "stop-writes-on-bgsave-error"
 76) "yes"
 77) "daemonize"
 78) "yes"
 79) "rdbcompression"
 80) "yes"
 81) "rdbchecksum"
 82) "yes"
 83) "activerehashing"
 84) "yes"
 85) "repl-disable-tcp-nodelay"
 86) "no"
 87) "aof-rewrite-incremental-fsync"
 88) "yes"
 89) "appendonly"
 90) "no"
 91) "dir"
 92) "/var/lib/redis"
 93) "maxmemory-policy"
 94) "noeviction"
 95) "appendfsync"
 96) "everysec"
 97) "save"
 98) "900 1 300 10 60 10000"
 99) "loglevel"
100) "notice"
101) "client-output-buffer-limit"
102) "normal 0 0 0 slave 268435456 67108864 60 pubsub 33554432 8388608 60"
103) "unixsocketperm"
104) "0"
105) "slaveof"
106) ""
107) "notify-keyspace-events"
108) ""
109) "bind"
110) "127.0.0.1"


Shell
```

## 编辑配置

要更新配置，可以直接编辑`redis.conf`文件，也可以通过`CONFIG set`命令更新配置。

**语法**
以下是`CONFIG SET`命令的基本语法。

```
redis 127.0.0.1:6379> CONFIG SET CONFIG_SETTING_NAME NEW_CONFIG_VALUE


Shell
```

**示例**

```
redis 127.0.0.1:6379> CONFIG SET loglevel "notice" 
OK 
redis 127.0.0.1:6379> CONFIG GET loglevel  
1) "loglevel" 
2) "notice"


Shell
```

## 3. Redis数据类型

Redis支持`5`种数据类型。

## 字符串

Redis中的字符串是一个字节序列。Redis中的字符串是二进制安全的，这意味着它们的长度不由任何特殊的终止字符决定。因此，可以在一个字符串中存储高达`512`兆字节的任何内容。

**示例**

```
redis 127.0.0.1:6379> set name "yiibai.com" 
OK 
redis 127.0.0.1:6379> get name 
"yiibai.com"


Shell
```

在上面的示例中，`set`和`get`是Redis命令，`name`是Redis中使用的键，`yiibai.com`是存储在Redis中的字符串的值。

> 注 - Redis命令不区分大小写，如`SET`,`Set`和`set`都是同一个命令。字符串值的最大长度为 512MB。

## 散列/哈希

Redis散列/哈希(Hashes)是键值对的集合。Redis散列/哈希是字符串字段和字符串值之间的映射。因此，它们用于表示对象。

**示例**

```
redis 127.0.0.1:6379> HMSET ukey username "yiibai" password "passswd123" points 200


Shell
```

在上述示例中，散列/哈希数据类型用于存储包含用户的基本信息的用户对象。这里`HMSET`，`HGETALL`是Redis的命令，而`ukey`是键的名称。

每个散列/哈希可以存储多达`2^32 - 1`个健-值对(超过`40`亿个)。

## 列表

Redis列表只是字符串列表，按插入顺序排序。您可以向Redis列表的头部或尾部添加元素。

**示例**

```
redis 127.0.0.1:6379> lpush alist redis 
(integer) 1 
redis 127.0.0.1:6379> lpush alist mongodb 
(integer) 2 
redis 127.0.0.1:6379> lpush alist sqlite 
(integer) 3 
redis 127.0.0.1:6379> lrange alist 0 10  

1) "sqlite" 
2) "mongodb" 
3) "redis"


Shell
```

列表的最大长度为`2^32 - 1`个元素(`4294967295`，每个列表可容纳超过`40`亿个元素)。

## 集合

Redis集合是字符串的无序集合。在Redis中，您可以添加，删除和测试成员存在的时间O(1)复杂性。

**示例**

```
redis 127.0.0.1:6379> sadd yiibailist redis 
(integer) 1 
redis 127.0.0.1:6379> sadd yiibailist mongodb 
(integer) 1 
redis 127.0.0.1:6379> sadd yiibailist sqlite 
(integer) 1 
redis 127.0.0.1:6379> sadd yiibailist sqlite 
(integer) 0 
redis 127.0.0.1:6379> smembers yiibailist  

1) "sqlite" 
2) "mongodb" 
3) "redis"


Shell
```

> 注意 - 在上面的示例中，`sqlite`被添加了两次，但是由于集合的唯一属性，所以它只算添加一次。

一个集合中的最大成员数量为`2^32 - 1`(即`4294967295`，每个集合中元素数量可达`40`亿个)个。

## 可排序集合

Redis可排序集合类似于Redis集合，是不重复的字符集合。 不同之处在于，排序集合的每个成员都与分数相关联，这个分数用于按最小分数到最大分数来排序的排序集合。虽然成员是唯一的，但分数值可以重复。

**示例**

```
redis 127.0.0.1:6379> zadd yiibaiset 0 redis
(integer) 1 
redis 127.0.0.1:6379> zadd yiibaiset 0 mongodb
(integer) 1 
redis 127.0.0.1:6379> zadd yiibaiset 1 sqlite
(integer) 1 
redis 127.0.0.1:6379> zadd yiibaiset 1 sqlite
(integer) 0 
redis 127.0.0.1:6379> ZRANGEBYSCORE yiibaiset 0 1000  

1) "mongodb" 
2) "redis" 
3) "sqlite"


Shell
```

因为 ‘`sqlite`‘ 的排序值是 1 ，其它两个元素的排序值是 0 ，所以 ‘`sqlite`‘ 排在最后一个位置上。

## 4. Redis命令

Redis命令是用于在Redis服务器上执行一些操作。
要在Redis服务器上运行命令，需要一个Redis客户端。Redis客户端在Redis包中有提供，这个包在我们前面的安装教程中就有安装过了。

**语法**
以下是Redis客户端的基本语法。

```
[yiibai@ubuntu:~]$ redis-cli


Shell
```

**示例**
以下示例说明了如何启动Redis客户端。

要启动Redis客户端，请打开终端并键入命令`redis-cli`。 这将连接到您的本地Redis服务器，现在可以运行任何的Redis命令了。

```
[yiibai@ubuntu:~]$redis-cli 
redis 127.0.0.1:6379> 
redis 127.0.0.1:6379> PING  
PONG


Shell
```

在上面的示例中，连接到到在本地机器上运行的Redis服务器并执行`PING`命令，该命令检查服务器是否正在运行。

## 在远程服务器上运行命令

要在Redis远程服务器上运行命令，需要通过客户端`redis-cli`连接到服务器

**语法**

```
[yiibai@ubuntu:~]$ redis-cli -h host -p port -a password


Shell
```

**示例**
以下示例显示如何连接到Redis远程服务器，在主机(host)`127.0.0.1`，端口(port)`6379`上运行，并使用密码为 `mypass`。

```
[yiibai@ubuntu:~]$ redis-cli -h 127.0.0.1 -p 6379 -a "mypass" 
redis 127.0.0.1:6379> 
redis 127.0.0.1:6379> PING  
PONG


Shell
```

## 5. Redis键命令

Redis键命令用于管理**Redis**中的键。以下是使用redis键命令的语法。

**语法**

```
redis 127.0.0.1:6379> COMMAND KEY_NAME


Shell
```

**示例**

```
redis 127.0.0.1:6379> SET akey redis
OK 
redis 127.0.0.1:6379> DEL akey
(integer) 1
127.0.0.1:6379> GET akey
(nil)


Shell
```

在上面的例子中，`DEL`是Redis的命令，而`akey`是键的名称。如果键被删除，则命令的输出将为`(integer) 1`，否则为`(integer) 0`。



## Redis键命令

下表列出了与键相关的一些基本命令。

| 编号   | 命令                                       | 描述                                |
| ---- | ---------------------------------------- | --------------------------------- |
| 1    | [DEL key](http://www.yiibai.com/redis/keys_del.html) | 此命令删除一个指定键(如果存在)。                 |
| 2    | [DUMP key](http://www.yiibai.com/redis/keys_dump.html) | 此命令返回存储在指定键的值的序列化版本。              |
| 3    | [EXISTS key](http://www.yiibai.com/redis/keys_exists.html) | 此命令检查键是否存在。                       |
| 4    | [EXPIRE key seconds](http://www.yiibai.com/redis/keys_expire.html) | 设置键在指定时间秒数之后到期/过期。                |
| 5    | [EXPIREAT key timestamp](http://www.yiibai.com/redis/keys_expireat.html) | 设置在指定时间戳之后键到期/过期。这里的时间是Unix时间戳格式。 |
| 6    | [PEXPIRE key milliseconds](http://www.yiibai.com/redis/keys_pexpire.html) | 设置键的到期时间(以毫秒为单位)。                 |
| 7    | [PEXPIREAT key milliseconds-timestamp](http://www.yiibai.com/redis/keys_pexpireat.html) | 以Unix时间戳形式来设置键的到期时间(以毫秒为单位)。      |
| 8    | [KEYS pattern](http://www.yiibai.com/redis/keys_keys.html) | 查找与指定模式匹配的所有键。                    |
| 9    | [MOVE key db](http://www.yiibai.com/redis/keys_move.html) | 将键移动到另一个数据库。                      |
| 10   | [PERSIST key](http://www.yiibai.com/redis/keys_persist.html) | 删除指定键的过期时间，得永生。                   |
| 11   | [PTTL key](http://www.yiibai.com/redis/keys_pttl.html) | 获取键的剩余到期时间。                       |
| 12   | [RANDOMKEY](http://www.yiibai.com/redis/keys_randomkey.html) | 从Redis返回一个随机的键。                   |
| 13   | [RENAME key newkey](http://www.yiibai.com/redis/keys_rename.html) | 更改键的名称。                           |
| 14   | [PTTL key](http://www.yiibai.com/redis/keys_pttl.html) | 获取键到期的剩余时间(以毫秒为单位)。               |
| 15   | [RENAMENX key newkey](http://www.yiibai.com/redis/keys_renamenx.html) | 如果新键不存在，重命名键。                     |
| 16   | [TYPE key](http://www.yiibai.com/redis/keys_type.html) | 返回存储在键中的值的数据类型。                   |

## 6. Redis字符串

Redis字符串命令用于管理Redis中的字符串值。以下是使用Redis字符串命令的语法。

```
redis 127.0.0.1:6379> COMMAND KEY_NAME


Shell
```

**示例**

```
redis 127.0.0.1:6379> SET mykey "redis" 
OK 
redis 127.0.0.1:6379> GET mykey 
"redis"


Shell
```

在上面的例子中，`SET`和`GET`是redis中的命令，而`mykey`是键的名称。

## Redis字符串命令

下表列出了一些用于在**Redis**中管理字符串的基本命令。

| 编号   | 命令                                       | 描述说明                  |
| ---- | ---------------------------------------- | --------------------- |
| 1    | [SET key value](http://www.yiibai.com/redis/strings_set.html) | 此命令设置指定键的值。           |
| 2    | [GET key](http://www.yiibai.com/redis/strings_get.html) | 获取指定键的值。              |
| 3    | [GETRANGE key start end](http://www.yiibai.com/redis/strings_getrange.html) | 获取存储在键上的字符串的子字符串。     |
| 4    | [GETSET key value](http://www.yiibai.com/redis/strings_getset.html) | 设置键的字符串值并返回其旧值。       |
| 5    | [GETBIT key offset](http://www.yiibai.com/redis/strings_getbit.html) | 返回在键处存储的字符串值中偏移处的位值。  |
| 6    | [MGET key1 [key2..\]](http://www.yiibai.com/redis/strings_mget.html) | 获取所有给定键的值             |
| 7    | [SETBIT key offset value](http://www.yiibai.com/redis/strings_setbit.html) | 存储在键上的字符串值中设置或清除偏移处的位 |
| 8    | [SETEX key seconds value](http://www.yiibai.com/redis/strings_setex.html) | 使用键和到期时间来设置值          |
| 9    | [SETNX key value](http://www.yiibai.com/redis/strings_setnx.html) | 设置键的值，仅当键不存在时         |
| 10   | [SETRANGE key offset value](http://www.yiibai.com/redis/strings_setrange.html) | 在指定偏移处开始的键处覆盖字符串的一部分  |
| 11   | [STRLEN key](http://www.yiibai.com/redis/strings_strlen.html) | 获取存储在键中的值的长度          |
| 12   | [MSET key value [key value …\]](http://www.yiibai.com/redis/strings_mset.html) | 为多个键分别设置它们的值          |
| 13   | [MSETNX key value [key value …\]](http://www.yiibai.com/redis/strings_msetnx.html) | 为多个键分别设置它们的值，仅当键不存在时  |
| 14   | [PSETEX key milliseconds value](http://www.yiibai.com/redis/strings_psetex.html) | 设置键的值和到期时间(以毫秒为单位)    |
| 15   | [INCR key](http://www.yiibai.com/redis/strings_incr.html) | 将键的整数值增加`1`           |
| 16   | [INCRBY key increment](http://www.yiibai.com/redis/strings_incrby.html) | 将键的整数值按给定的数值增加        |
| 17   | [INCRBYFLOAT key increment](http://www.yiibai.com/redis/strings_incrbyfloat.html) | 将键的浮点值按给定的数值增加        |
| 18   | [DECR key](http://www.yiibai.com/redis/strings_decr.html) | 将键的整数值减`1`            |
| 19   | [DECRBY key decrement](http://www.yiibai.com/redis/strings_decrby.html) | 按给定数值减少键的整数值          |
| 20   | [APPEND key value](http://www.yiibai.com/redis/strings_append.html) | 将指定值附加到键              |

## 7. Redis哈希

Redis Hashes是字符串字段和字符串值之间的映射(类似于PHP中的数组类型)。 因此，它们是表示对象的完美数据类型。

在Redis中，每个哈希(散列)可以存储多达4亿个键-值对。

## 示例

```
redis 127.0.0.1:6379> HMSET myhash name "redis tutorial" 
description "redis basic commands for caching" likes 20 visitors 23000 
OK 
127.0.0.1:6379> HGETALL myhash
1) "field1"
2) "Hello"
3) "field2"
4) "World"
5) "name"
6) "redis tutorial"


Shell
```

在上面的例子中，在名称为’`myhash`‘的哈希中设置了Redis教程的详细信息(名称，描述，喜欢，访问者)。

## 8. Redis列表

Redis列表只是字符串列表，按插入顺序排序。可以在列表的头部或尾部添加Redis列表中的元素。

列表的最大长度为`2^32 - 1`个元素(即`4294967295`，每个列表可存储超过40亿个元素)。

## 示例

```
redis 127.0.0.1:6379> LPUSH mylist "redis" 
(integer) 1 
redis 127.0.0.1:6379> LPUSH mylist "mongodb"
(integer) 2 
redis 127.0.0.1:6379> LPUSH mylist "mysql"
(integer) 3 
redis 127.0.0.1:6379> LRANGE mylist 0 10  
1) "mysql" 
2) "mongodb" 
3) "redis"


Shell
```

在上面的示例中，通过命令`LPUSH`将三个值插入到名称为“`mylist`”的Redis列表中。

## 8. Redis集合

Redis集合是唯一字符串的无序集合。 唯一值表示集合中不允许键中有重复的数据。

在Redis中设置添加，删除和测试成员的存在(恒定时间O(1)，而不考虑集合中包含的元素数量)。列表的最大长度为`2^32 - 1`个元素(即4294967295，每组集合超过40亿个元素)。

## 示例

```
redis 127.0.0.1:6379> SADD myset "redis" 
(integer) 1 
redis 127.0.0.1:6379> SADD myset "mongodb" 
(integer) 1 
redis 127.0.0.1:6379> SADD myset "mysql" 
(integer) 1 
redis 127.0.0.1:6379> SADD myset "mysql" 
(integer) 0 
redis 127.0.0.1:6379> SMEMBERS "myset"  
1) "mysql" 
2) "mongodb" 
3) "redis"


Shell
```

在上面的示例中，通过命令`SADD`将三个值插入到名称为“`myset`”的Redis集合中。

## 9. Redis发送订阅

Redis发布订阅(pub/sub)是一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。
Redis 发布订阅(pub/sub)实现了消息系统，发送者(在redis术语中称为发布者)在接收者(订阅者)接收消息时发送消息。传送消息的链路称为信道。

在Redis中，客户端可以订阅任意数量的信道。

## 示例

以下示例说明了发布用户概念的工作原理。 在以下示例中，一个客户端订阅名为“`redisChat`”的信道。

```
redis 127.0.0.1:6379> SUBSCRIBE redisChat  
Reading messages... (press Ctrl-C to quit) 
1) "subscribe" 
2) "redisChat" 
3) (integer) 1


Shell
```

现在，两个客户端在名称为“`redisChat`”的相同信道上发布消息，并且上述订阅的客户端接收消息。

```
redis 127.0.0.1:6379> PUBLISH redisChat "Redis is a great caching technique"  
(integer) 1  
redis 127.0.0.1:6379> PUBLISH redisChat "Learn redis by yiibai"  
(integer) 1   
1) "message" 
2) "redisChat" 
3) "Redis is a great caching technique" 
1) "message" 
2) "redisChat" 
3) "Learn redis by yiibai"


Shell
```

## 10. Redis事务

Redis事务允许在单个步骤中执行一组命令。以下是事务的两个属性：

- 事务中的所有命令作为单个隔离操作并按顺序执行。不可以在执行Redis事务的中间向另一个客户端发出的请求。
- Redis事务也是原子的。原子意味着要么处理所有命令，要么都不处理。

## 语法示例

Redis事务由命令`MULTI`命令启动，然后需要传递一个应该在事务中执行的命令列表，然后整个事务由`EXEC`命令执行。

```
redis 127.0.0.1:6379> MULTI 
OK 
List of commands here 
redis 127.0.0.1:6379> EXEC


Shell
```

## 示例

以下示例说明了如何启动和执行Redis事务。

```
redis 127.0.0.1:6379> MULTI 
OK 
redis 127.0.0.1:6379> SET mykey "redis" 
QUEUED 
redis 127.0.0.1:6379> GET mykey 
QUEUED 
redis 127.0.0.1:6379> INCR visitors 
QUEUED 
redis 127.0.0.1:6379> EXEC  
1) OK 
2) "redis" 
3) (integer) 1


Shell
```

## 11. Redis脚本