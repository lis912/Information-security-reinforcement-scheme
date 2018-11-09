# 本方案以 Windows Server 2008 R2 操作系统例：

---

# 身份鉴别

### 1. 勾选“要使用本机，用户必须输入同户名和密码”

> 加固方法：

```
Win + r                                         # 调出运行
rundll32 netplwiz.dll UsersRunDll               # 输入命令，调出如下对话框，勾选
```

![](/assets/勾选“用户名+密码”.png)

