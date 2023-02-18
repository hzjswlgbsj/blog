我使用的是 163 邮箱服务器，首先你需要开启 POP3/SMTP/IMAP 服务
![image.png](https://cdn.nlark.com/yuque/0/2023/png/160765/1676519064530-29b2668b-8824-4a7e-8963-c9630658c683.png#averageHue=%23f5f5f5&clientId=uea5be4ee-38a0-4&from=paste&height=354&id=u4ab29f4b&name=image.png&originHeight=707&originWidth=926&originalType=binary&ratio=2&rotation=0&showTitle=false&size=188676&status=done&style=none&taskId=u0b83f53d-8d8e-4cd1-86c2-2ab3c54b5d9&title=&width=463)
然后设置授权码，注意不是邮箱登录密码，这个授权码待会要用
![image.png](https://cdn.nlark.com/yuque/0/2023/png/160765/1676519078803-b459aedf-6625-455a-b3cc-3feccea7e46c.png#averageHue=%23d2b8a8&clientId=uea5be4ee-38a0-4&from=paste&height=198&id=ue7dfa882&name=image.png&originHeight=395&originWidth=990&originalType=binary&ratio=2&rotation=0&showTitle=false&size=143941&status=done&style=none&taskId=u1717c218-0d05-448a-868e-d45292ef67c&title=&width=495)
ok，这就好啦，我们开始代码部分

使用 composer 来安装 PHPMailer

```php
composer require phpmailer/phpmailer
```

然后去 PHPMailer 的 github 地址，[这里](https://github.com/PHPMailer/PHPMailer)，鼠标滑到一半左右可以看到实例代码，这我就不讲那么多了，今天主要讲这中间的坑！
双手奉上我封装的工具类（我很久不碰 php 代码了，写的很垃圾，你自己再改改）
**MailUtil.php**

```php
// MailUtil.php
<?php
/**
 * Created by PhpStorm.
 * User: liulin
 * Date: 2018/8/8
 * Time: 下午5:48
 */

namespace app\mail;
// Import PHPMailer classes into the global namespace
// These must be at the top of your script, not inside a function
use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\Exception;

//Load Composer's autoloader
require '../vendor/autoload.php';

class MailUtil {
  // Server settings(邮件服务器设置)
  public $SMTPDebug = 2;
  public $host = '';
  public $SMTPAuth = true;
  public $fromMailAddress = '';
  public $fromMailPass = '';
  public $charSet = 'UTF-8';
  public $SMTPSecure = 'ssl';
  public $port = 465;

  // Sender(发件人)
  public $senderName = '';

  // Recipients(收件人)
  public $recipientsAddress = '';
  public $recipientsName = '';

  // Responder(回复者)
  public $responderAddress = '';
  public $responderName = '';

  // Cc(抄送人)
  public $CcAddress = '';
  public $CcName = '';

  // Secret copy(秘密抄送人)
  public $secretCcAddress = '';
  public $secretCcName = '';

  // Attachments(附件)
  public $attachmentsPath = '';
  public $attachmentsName = '';

  // Content(内容)
  public $subject = '';
  public $body = '';

  function __construct(){}

  function send () {
    $mail = new PHPMailer(true);
    try {
      // Server settings
      // $mail->SMTPDebug = $this->SMTPDebug;      // Enable verbose debug output(启用详细调试输出)
      $mail->isSMTP();                          // Set mailer to use SMTP(使用SMTP服务)
      $mail->Host = $this->host;                // Specify main and backup SMTP servers(指定主要和备用SMTP服务器)
      $mail->SMTPAuth = $this->SMTPAuth;        // Enable SMTP authentication(启用SMTP身份验证)
      $mail->Username = $this->fromMailAddress; // SMTP username(发件人邮箱)
      $mail->Password = $this->fromMailPass;    // SMTP username(发件人密码)
      $mail->CharSet = $this->charSet;          // Set charSet(设置邮件的字符编码，这很重要，不然中文乱码)
      $mail->SMTPSecure = $this->SMTPSecure;          // Enable TLS encryption, `ssl` also accepted(启用TLS加密，`ssl`也被接受)
      $mail->Port = $this->port;                         // TCP port to connect to(要连接的TCP端口) 常用邮箱的 SMTP 地址和端口参见：https://blog.wpjam.com/m/gmail-qmail-163mail-imap-smtp-pop3/

      // Recipients(收件人)
      $mail->setFrom($this->fromMailAddress, $this->senderName);           // 设置发件人信息，如邮件格式说明中的发件人，这里会显示为Mailer(xxxx@163.com），Mailer是当做名字显示
      $mail->addAddress($this->recipientsAddress, $this->recipientsName);  // 设置收件人信息，如邮件格式说明中的收件人，这里会显示为Liang(yyyy@163.com)
      // $mail->addReplyTo($this->responderAddress, $this->responderName);    // 设置回复人信息，指的是收件人收到邮件后，如果要回复，回复邮件将发送到的邮箱地址
      $mail->addCC($this->CcAddress, $this->CcName);                       // 设置邮件抄送人，可以只写地址，上述的设置也可以只写地址
      // $mail->addBCC($this->secretCcAddress, $this->secretCcName);          // 设置秘密抄送人

      // Attachments(附件)
      // $mail->addAttachment($this->attachmentsPath, $this->attachmentsName); // 上传附件，第二参数为name，可以不传

      // Content(内容)
      $mail->isHTML(true);       // Set email format to HTML
      $mail->Subject = $this->subject;  // 邮件标题
      $mail->Body = $this->body;        // 邮件正文,可以写html，如： 'This is the html body <b>very stronge非常强壮</b>'
      //$mail->AltBody = "";            // 这个是设置纯文本方式显示的正文内容，如果不支持Html方式，就会用到这个，基本无用
      $mail->send();
    } catch (Exception $e) {
      var_dump($mail->ErrorInfo);
    }
  }
}
```

**Mail.php（model 层）**

```php
// Mail.php（model层）
<?php
/**
 * Created by PhpStorm.
 * User: liulin
 * Date: 2018/8/9
 * Time: 上午12:26
 */
namespace app\models;

use app\mail\MailUtil;
use yii\base\Model;

class Mail extends Model {
  public $imageFile;

  /**
   * @param string $recipientsAddress 表示收件人地址
   * @param string $subject 表示邮件标题
   * @param string $body 表示邮件正文
   */
  public function sendMail($recipientsAddress, $subject = "", $body = "") {
    $mail = new MailUtil();
    $mail->host = 'smtp.163.com';
    $mail->fromMailAddress = 'xxx@163.com';
    $mail->fromMailPass = '*******'; // 注意，这里是授权码，不是你邮箱登录密码
    $mail->senderName = 'xxx';
    $mail->CcAddress = 'xxx@163.com'; // 抄送人邮箱
    $mail->recipientsAddress = $recipientsAddress; // 收件人邮箱
    $mail->subject = $subject; // 邮件标题
    $mail->body = $body;  // 邮件内容
    $mail->send();
  }
}
```

**MailControl.php**

```php
// MailControl.php
<?php
/**
 * Created by PhpStorm.
 * User: liulin
 * Date: 2018/8/9
 * Time: 上午12:39
 */
namespace app\controllers;
use app\models\Mail;
use Yii;
class MailController extends BaseController {
  function actionSendmail () {
    $request = Yii::$app->request;
    $mailModel = new Mail();
    $recipientsAddress = $request->post('recipientsAddress');
    $subject = $request->post('subject');
    $body = $request->post('body');
    return $mailModel->sendMail($recipientsAddress, $subject, $body);
  }
}
```

**注意：**

1. 遇到无法连接的错误，你可能在网上搜到了把小写的 `smtp` 改成大写的，其实这只是因为源码里面判断是否等于小写的 `smtp`，你改成大写的就不相等了，走了另一个逻辑，弄巧成拙而已。
2. 设置那里有个密码，这个要换成授权码不是你的邮箱登录密码，qq、163 等都可以在相应的管理界面生成授权码
3. 下面这两行是你不成功最大的原因：

```php
public $SMTPSecure = 'ssl';
public $port = 465;
```

在官网的示例代码里面你可以看到他是这么写的

```php
$mail->SMTPSecure = 'tls';  // Enable TLS encryption, `ssl` also accepted
$mail->Port = 587;          // TCP port to connect to
```

他推荐使用 **857 **端口和 **tls **协议，实际上你们肯定大多数都用的 **25 **端口，重点来了！！

我在本地 macOS 使用 **25 **端口 **tls **协议没问题，可以发邮件，上线到 **阿里云 **服务器后就崩了！**注意！阿里云服务器关闭了 25 端口！**

所以你再也不发使用 **25** 端口了，走 **465 **或者 **587 **吧，注意这两个端口协议不一样，看上面两份代码

**456 **端口走的是 **ssl** 协议，**587 **和 **25 **走的是 **tls** 协议！
关于阿里云关闭** 25 **端口你自己可以用 telnet 来测试一下，注意不要用 ping，没啥用。
先安装吧：（centos 7）

```shell
yum install telnet -y
```

来，干一把

```shell
telnet smtp.163.com 25 // 我测试的163的，你可以测试其他的
```

结果如下
![image.png](https://cdn.nlark.com/yuque/0/2023/png/160765/1676519758913-f641c626-17f0-4adb-9485-3ace4d3b4a12.png#averageHue=%231c1b24&clientId=uea5be4ee-38a0-4&from=paste&height=294&id=uf0b829ee&name=image.png&originHeight=588&originWidth=980&originalType=binary&ratio=2&rotation=0&showTitle=false&size=381991&status=done&style=none&taskId=u1c522a82-4506-4170-ad3b-713ee51bc3c&title=&width=490)
不通，哈哈哈。。。，同样的方法，456 和 587 端口是通的，so，你懂了吧。

还有一些问题的话一般就是你代码里面的设置问题了，比如邮箱格式错误、抄送人错误、附件没传倒是又打开了等等，
这是 163 的所有 [错误提示](http://help.163.com/09/1224/17/5RAJ4LMH00753VB8.html)，你对比着来。
其中 554 MI:SPB UserReject 0,smtp3,DdGowED...... ，这个是因为你的邮箱账户被冻结了，你需要去网易把它给解封，去如下地址操作即可。

```shell
http://feedback.mail.126.com/antispam/complain.php?user=****@163.com // 把***改成你自己的
```

这样一遍流程的话你的还是不通的话，俺就帮不了你了。
