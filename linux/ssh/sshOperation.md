## 1. 生成 私钥 和 公钥

A 服务器远程登陆 B 服务器，双方需要生成公钥和私钥，然后将A的公钥copy到B的 ~/.ssh/authorized_keys文件中。

<p class='tip'>
<strong>A、B 服务器都需生成 公钥 私钥文件</strong><br><br>
如果 A、B 服务器都已经生成了公钥和私钥文件可以跳过这一步。
</p>

```bash
# 生成 公钥 私钥 文件，提示输入信息，可以选择什么也不输入，一直回车。
$ ssh-keygen -t rsa

# 查看生成的文件。
$ ls ~/.ssh/
id_rsa      id_rsa.pub  known_hosts
```

## 2. ssh 配置免密登陆

修改 ssh 配置文件，启用密钥登陆，指定密钥文件。

<p class='tip'>
<strong>使用vi匹配搜索功能，查找配置项。</strong><br><br>
如果文件中没有下面的配置，可以添加，如果有就取消注释或者将no修改为yes
</p>

```bash
$ vi /etc/ssh/sshd_config
    #启用密钥验证
    RSAAuthentication yes
    PubkeyAuthentication yes
    #指定公钥数据库文件
    AuthorsizedKeysFile.ssh/authorized_keys
$ service sshd reload
```

## 3. 禁止 用户名/密码 远程登陆

<p class='warning'>
<strong>注意，禁止用户名/免密 远程登陆前请先确保第二步操作成功。</strong><br><br>
<strong>注意，提前配置多个服务器可以登陆服务器B。</strong><br>
</p>


```bash
$ vi /etc/ssh/sshd_config
    #PasswordAuthentication yes 改为
    PasswordAuthentication no
$ service sshd reload
```

## 其他

### 更换了A服务器，如何登陆B服务器

如果B服务器只允许秘钥登陆，而且只允许A通过密钥登陆。此次A服务器重装了，如何登陆服务器B？

#### 1. 可以物理登陆B服务器

如果可以通过显示屏和键盘直接连接B服务器，替换或添加B服务器 ~/.ssh/authorized_keys 文件中 关于A服务器的公钥字符串。

#### 2. 不可以直接物理登陆服务器B，B是云服务器，通过VPN登陆

<p class='tip'>
<strong>如果没有配置过VPN，会提示输入登陆密码，配置过后可以正常登陆。</strong><br>
</p>

云服务器提供商提供的后台有VPN登陆功能，可以使用 用户名/密码 直接登陆。替换或添加B服务器 ~/.ssh/authorized_keys 文件中 关于A服务器的公钥字符串。

#### 3. 待定

    

