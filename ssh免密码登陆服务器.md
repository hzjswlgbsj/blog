首先，还是先介绍一下ssh吧(我也是自己查资料的)**

**SSH 的全称是Secure Shell，使用非对称加密方式，传输内容使用rsa 或者dsa 加密，可以有效避免网络窃听。有时候，需要能免密码登陆Linux系统，比如Hadoop操作，所以需要开启SSH免密码登陆。****

**注意，不用的用户生成的密钥是不同的，每个用户生成密钥都放在该用户主目录的”.ssh”目录中；比如：root生成的密钥存放在”/root/.ssh”，个人用户的存放在”/home/[username]/.ssh”目录中**

**ssh client有两个配置文件，**`/etc/ssh/ssh_config`** 和 **`~/.ssh/config`**,前者是对所有用户，后者是针对某个用户，两个文件的格式是一样的，待会密钥文件会生成在~/.ssh下

**<br />现在进入正题，假设我要**在A**(我这里是本地mac OS)免密码**登陆B**。<br />**第一步：在A生成密钥**<br />在A进入~/.ssh文件夹，执行以下命令
```bash
ssh-keygen -t rsa
```

一路回车后，在~/.ssh下会生成id_rsa，和id_rsa.pub，其中id_rsa是密钥，id_rsa.pub是公钥。<br />**第二步：把在A机生成的id_rsa.pub拷贝到B机上。**<br />怎么跨主机复制文件呢，下面的命令可以做到：(假设放在B的tmp)
```bash
scp id_rsa.pub root@B:/tmp  //scp -r是拷贝文件夹
# 举个例子：将服务器的/www/sixty-api/ 复制到本地的~/www（在本地执行） 
scp -r root@101.200.52.143:/www/sixty-api/ ~/www 
```

**第三步：将刚才拷贝的公钥拷贝到B的authorized_keys文件（没有就创建，注意权限不能是777，可以设置成700或者400）**<br />**切换到B的.ssh**
```bash
cd ~/.ssh
```
拷贝公钥到authorized_keys
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

然后去A上面测试，看是否还需要输入密码，如果直接进入了就表示成功了<br />另外，每次登陆都要输入ssh user@主机 -u -p太麻烦了，我们可以修改一下`ssh_config`<br />按照如下步骤即可<br /> 
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
其中service是别名 127.0.0.1 是你要登陆的服务器，zhangsan是用户, IdentifyFile是密钥，上面的配置在连接的时候就可以用如下命令，配合上面的免密码就会十分方便<br /> 
```bash
ssh service
```
你是不是觉得特爽！

