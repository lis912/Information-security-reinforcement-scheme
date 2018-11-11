# 以CentOS 6.5示例

---

# 身份鉴别

### 1. 完善系统口令策略

* 打开 /etc/login.defs 配置文件, 修改口令策略配置参数：

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

### 2. 配置系统口令复杂度策略,登录失败策略

* 打开 /etc/pam.d/system-auth 配置文件,  添加或配置 pam\_cracklib.so 模块下的口令复杂度配置参数, 及 pam\_tally2.so 模块下的登录失败处理配置参数.

```
[root@oracledb ~]# vi /etc/pam.d/system-auth

#%PAM-1.0
# This file is auto-generated.
# User changes will be destroyed the next time authconfig is run.
auth        required      pam_env.so
auth        sufficient    pam_fprintd.so
auth        sufficient    pam_unix.so nullok try_first_pass
auth        requisite     pam_succeed_if.so uid >= 500 quiet
auth        required      pam_tally2.so even_deny_root deny=5 unlock_time=60 root_unlock_time=1800
auth        required      pam_deny.so

account     required      pam_unix.so
account     sufficient    pam_localuser.so
account     sufficient    pam_succeed_if.so uid < 500 quiet
account     required      pam_permit.so

password    requisite     pam_cracklib.so try_first_pass retry=5 difok=3 ucredit=-1 lcredit=-3 dcredit=-3 ocredit=-1 remember=24
password    sufficient    pam_unix.so sha512 shadow nullok try_first_pass use_authtok
password    required      pam_deny.so

```



* 配置参数对照表:

```
password    requisite     pam_cracklib.so try_first_pass retry=5 difok=3 ucredit=-1 lcredit=-3 dcredit=-3 ocredit=-1 remember=24
```

|  |  |  |
| :--- | :--- | :--- |
|  |  |  |
|  |  |  |



