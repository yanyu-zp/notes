## 一、配置用户名和邮箱

```
git config --global user.name "用户名"
git config --global user.email "邮箱"
```

## 二、拿到git公钥

```
ssh-keygen -t rsa -C "邮箱"
cat ~/.ssh/id_rsa.pub
```

密钥文件自动放到`~/.ssh/id_rsa.pub`下

## 三、github设置

github -> setting -> SSH and GPG keys -> New SSH key

## 四、测试是否绑定成功

```
ssh -T git@github.com
```

