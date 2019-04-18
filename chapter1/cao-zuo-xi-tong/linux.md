# 本方案以CentOS 6.5为示例:

---

# 身份鉴别

### 1. 完善系统口令策略

* 打开 /etc/login.defs 配置文件, 修改口令策略配置参数,如下：

```bash
[root@oracledb /]# vi /etc/login.defs

# Password aging controls:
#
#    PASS_MAX_DAYS    Maximum number of days a password may be used.
#    PASS_MIN_DAYS    Minimum number of days allowed between password changes.
#    PASS_MIN_LEN    Minimum acceptable password length.
#    PASS_WARN_AGE    Number of days warning given before a password expires.
#
PASS_MAX_DAYS    90    # 口令最长有效期
PASS_MIN_DAYS    2     # 口令最短留存期       
PASS_MIN_LEN     8     # 口令长度最小值
PASS_WARN_AGE    7     # 口令有效期警告
```

> 该配置修改保存后立即生效, 但只对修改后创建的账户生效。

### 

### 2. 配置系统口令复杂度策略,登录失败策略

* 打开 /etc/pam.d/system-auth 配置文件,  添加或配置 pam\_cracklib.so 模块下的口令复杂度配置参数, 及 pam\_tally2.so 模块下的登录失败处理配置参数.修改如下:

```
[root@oracledb ~]# vi /etc/pam.d/system-auth

#%PAM-1.0
# This file is auto-generated.
# User changes will be destroyed the next time authconfig is run.
auth        required      pam_env.so
auth        sufficient    pam_fprintd.so
auth        sufficient    pam_unix.so nullok try_first_pass
auth        requisite     pam_succeed_if.so uid >= 500 quiet
# 登录失败策略
auth        required      pam_tally2.so even_deny_root deny=5 unlock_time=600 root_unlock_time=1800
auth        required      pam_deny.so

account     required      pam_unix.so
account     sufficient    pam_localuser.so
account     sufficient    pam_succeed_if.so uid < 500 quiet
account     required      pam_permit.so

# 口令复杂度策略
password    requisite     pam_cracklib.so try_first_pass difok=3 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1 remember=8
password    sufficient    pam_unix.so sha512 shadow nullok try_first_pass use_authtok
password    required      pam_deny.so
```

* 配置参数对照表:

```
# 口令复杂度策略
password    requisite     pam_cracklib.so try_first_pass difok=3 ucredit=-1 lcredit=-1 dcredit=-1 ocredit=-1 remember=8
```

| 参数 | 配置参考值 | 含义 |
| :---: | :---: | :---: |
| difok | 3 | 允许新密码中有3个字符与旧密码相同 |
| ucredit | -1 | 密码中最少有1位大写字母 |
| lcredit | -1 | 密码中最少有1位小写字母 |
| dcredit | -3 | 密码中至少有1位数字 |
| ocredit | -1 | 密码中至少有1位特殊字符 |
| remember | 8 | 最近8次使用过的旧密码无法重用 |

```
# 登录失败策略
auth        required      pam_tally2.so even_deny_root deny=5 unlock_time=600 root_unlock_time=1800
```

| 参数 | 配置参考值 | 含义 |
| :---: | :---: | :---: |
| even\_deny\_root |  | 同时限制 root 账户 |
| deny | 5 | 登录失败5次锁定账户 |
| unlock\_time | 600 | 普通账户600秒解锁 |
| root\_unlock\_time | 1800 | root 账户 1800秒解锁 |

### 

### 3.清除多余的系统账户

* linux系统下lp、adm、sync、shutdown、halt、news、operator、gopher等为多余的虚拟系统账户，在不影响业务运行的情况下应将其删除。实际情况下打开/etc/passwd文件将这些账户注释即可，如下：



```
[root@localhost ~]# vi /etc/passwd

root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
#adm:x:3:4:adm:/var/adm:/sbin/nologin
#lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
#sync:x:5:0:sync:/sbin:/bin/sync
#shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
#halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
#operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
chrony:x:998:996::/var/lib/chrony:/sbin/nologin
```

# 安全审计



