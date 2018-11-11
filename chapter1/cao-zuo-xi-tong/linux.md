# 以CentOS 6.5示例

---

# 身份鉴别

### 1. 完善系统口令策略

* 打开 /etc/login.defs 配置文件，修改口令策略配置参数：

```bash
[root@oracledb /]# vi /etc/login.defs

# Password aging controls:
#
#	PASS_MAX_DAYS	Maximum number of days a password may be used.
#	PASS_MIN_DAYS	Minimum number of days allowed between password changes.
#	PASS_MIN_LEN	Minimum acceptable password length.
#	PASS_WARN_AGE	Number of days warning given before a password expires.
#
PASS_MAX_DAYS	90    # 口令最长有效期
PASS_MIN_DAYS	2     # 口令最短留存期       
PASS_MIN_LEN	8     # 口令长度最小值
PASS_WARN_AGE	7     # 口令有效期警告
```

> 该配置修改保存后立即生效，但只对修改后创建的账户生效。

### 2. 配置登录失败策略

* 


