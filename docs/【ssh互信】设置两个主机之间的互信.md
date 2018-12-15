
## 设置两个主机的互信
主机host1到host2的ssh互信设置

### host1-ssh-host2.sh
```
#!/bin/sh

user=root
host1=192.168.35.137
host2=192.168.35.138
passwd=qaz

echo "1. 登录$host1,并在该node上生成ssh密钥"
expect -f ssh-keygen.expect $user $host1 $passwd
retval=$?
if [ $retval -ne 0 ] && [ $retval -ne 1 ]; then 
  echo "在$host1上生成ssh密钥失败";
  return -1;
fi

echo "2. 将$host1的公钥拷贝到$host2上"
expect -f ssh-copy-id.expect $user $host1 $host2 $passwd
retval=$?
if [ $retval -ne 0 ] && [ $retval -ne 1 ];then 
  echo "将$host1的公钥拷贝到$host2失败"
  return -1;
fi

echo '$host1和$host2互信SUCCESS'

```

### ssh-keygen.expect
```
#!/usr/bin/expect -f

# 把命令行第一个参数赋值给 user
set user [lindex $argv 0]

# 把命令行第2个参数赋值给 host
set host [lindex $argv 1]
set pass [lindex $argv 2]

# 设置超时时间为30秒
set timeout 30 

# spawn启动一个进程，进程执行ssh命令，程序后面可以通过expect/send和新启的进程进行交互
spawn ssh ${user}@${host} ssh-keygen
expect {
    "yes/no" { send "yes\r";exp_continue}
    "password:" {send "${pass}\r";exp_continue}
    "All accesses logged" {exp_continue}
    "Enter file in which to save the key (/root/.ssh/id_rsa)" {send "\r";exp_continue}
    "Enter passphrase (empty for no passphrase):" {send "\r";exp_continue}
    "Enter same passphrase again:" {send "\r";exp_continue}
    "Overwrite (y/n)?" {send "n\r";exp_continue}
    timeout { exit -1 }
}

# 获取退出码
catch wait result;
exit [lindex $result 3]

```

### ssh-copy-id.expect
```
#!/usr/bin/expect -f
set user [lindex $argv 0]
set host1 [lindex $argv 1]
set host2 [lindex $argv 2]
set pass [lindex $argv 3]
set timeout 10 

#登录到host1
spawn ssh ${user}@${host1}
expect {
    "yes/no" { send "yes\r";exp_continue}
    "password:" { send "${pass}\r";exp_continue}
    "All accesses logged" {exp_continue}
    "]#" {}
    timeout { }
}

send "ssh-copy-id -i /root/.ssh/id_rsa.pub ${user}@${host2}\r"
expect {
    "yes/no" { send "yes\r";exp_continue}
    "password:" { send "${pass}\r";exp_continue}
    "All accesses logged" {exp_continue}
    "]#" {}
    timeout { }
}
exit

```

### 执行结果
```
[root@rmq-node3 liulin]# ./host1-ssh-host2.sh 
1. 登录192.168.35.137,并在该node上生成ssh密钥
spawn ssh root@192.168.35.137 ssh-keygen
root@192.168.35.137's password: 
Enter file in which to save the key (/root/.ssh/id_rsa): 
Generating public/private rsa key pair.
/root/.ssh/id_rsa already exists.
Overwrite (y/n)? n
2. 将192.168.35.137的公钥拷贝到192.168.35.138上
spawn ssh root@192.168.35.137
root@192.168.35.137's password: 
Last login: Mon Dec 17 01:37:05 2018 from rmq-node3
[root@rmq-node3 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub root@192.168.35.138
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed

/usr/bin/ssh-copy-id: WARNING: All keys were skipped because they already exist on the remote system.

[root@rmq-node3 ~]# 192.168.35.137和192.168.35.138互信SUCCESS
```

