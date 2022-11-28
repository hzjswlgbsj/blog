# ssh 免密码登陆服务器

首先，还是先介绍一下 ssh。

**SSH 的全称是 Secure Shell，使用非对称加密方式，传输内容使用 rsa 或者 dsa 加密，可以有效避免网络窃听。有时候，需要能免密码登陆 Linux 系统，比如 Hadoop 操作，所以需要开启 SSH 免密码登陆。\*\***

**注意，不用的用户生成的密钥是不同的，每个用户生成密钥都放在该用户主目录的”.ssh”目录中；比如：root 生成的密钥存放在”/root/.ssh”，个人用户的存放在”/home/[username]/.ssh”目录中**

**ssh client 有两个配置文件，**`/etc/ssh/ssh_config`** 和 **`~/.ssh/config`\*\*,前者是对所有用户，后者是针对某个用户，两个文件的格式是一样的，待会密钥文件会生成在~/.ssh 下

**<br />现在进入正题，假设我要**在 A**(我这里是本地 mac OS)免密码**登陆 B**。<br />**第一步：在 A 生成密钥\*\*<br />在 A 进入~/.ssh 文件夹，执行以下命令

```bash
ssh-keygen -t rsa
```

一路回车后，在~/.ssh 下会生成 id_rsa，和 id_rsa.pub，其中 id_rsa 是密钥，id_rsa.pub 是公钥。<br />**第二步：把在 A 机生成的 id_rsa.pub 拷贝到 B 机上。**<br />怎么跨主机复制文件呢，下面的命令可以做到：(假设放在 B 的 tmp)

```bash
scp id_rsa.pub root@B:/tmp  //scp -r是拷贝文件夹
# 举个例子：将服务器的/www/sixty-api/ 复制到本地的~/www（在本地执行）
scp -r root@101.200.52.143:/www/sixty-api/ ~/www
```

**第三步：将刚才拷贝的公钥拷贝到 B 的 authorized_keys 文件（没有就创建，注意权限不能是 777，可以设置成 700 或者 400）**<br />**切换到 B 的.ssh**

```bash
cd ~/.ssh
```

拷贝公钥到 authorized_keys

```bash
cat /tmp/id_rsa.pub>>authorized_keys
```

当然你也可以这样拷贝：

```bash
cp /tmp/id_rsa.pub authorized_keys
```

如果需要修改权限，可以执行：

```bash
chmod 400 authorized_keys
```

然后去 A 上面测试，看是否还需要输入密码，如果直接进入了就表示成功了<br />另外，每次登陆都要输入 ssh user@主机 -u -p 太麻烦了，我们可以修改一下`ssh_config`<br />按照如下步骤即可<br />

```bash
cd /etc/ssh && sudo vim ssh_config
# 或者（这个两个目录的都会生效）
cd ~/.ssh && sudo vim config
```

然后加入如下代码：<br />

```bash
Host service
  HostName 127.0.0.1
  User zhangsan
  IdentifyFile xxx
```

其中 service 是别名 127.0.0.1 是你要登陆的服务器，zhangsan 是用户, IdentifyFile 是密钥，上面的配置在连接的时候就可以用如下命令，配合上面的免密码就会十分方便<br />

```bash
ssh service
```

你是不是觉得特爽！
