# keepGV

GoogleVoice 全自动批量保号
通过 GitHub Actions 定期向群内发送短信触发关键词，然后配置各gv号自动回复短信实现全自动保号。

## 使用方法

1. 注册Github Fork本项目
2. 在[Groupme官网](https://groupme.com)注册一个账户，并激活主手机号。（建议用谷歌邮箱和对应gv号）
3. 登录后新建一个群组，群名和介绍都用英文。手机打开 "Voice"或者"环聊Hangouts" App 就能看到这个群组（系统默认会发来一条消息，也可以手动发一条）（群建好后把所有的gv号加进去，就能实现批量给gv号群发短信）
4. 在[GroupMe Dev](https://dev.groupme.com/bots)建立 bot 机器人。**group** 选刚建的群名； **name** 取英文；**callback URL** 输入`http://www.baidu.com`；其他留空。获得 Bot_ID 记下来。
5. 在你刚新建的 GitHub 项目里面点 **Settings**（设置）然后点 **Secrets**（隐私）新建`GM_BOT_ID`对应前面的Bot_ID
6. 在你刚建的 GitHub 项目里点 **Actions** 然后点左侧 **Auto Send Message To Groupme By Bot** 切换，然后点 **Run workflow** 开始第一次运行。运行成功你应该会收到 bot 的短信。 (看不到 Autosend 的话，点开 .yml 文件随便加一空行保存)
7. 设置 Gmail 自动回复 GoogleVoice 短信，可以设定触发关键词为`SKGVLM`。

### gmail 自动回复GV短信

1. 在GV中设置中，把“将消息转发到电子邮件”打开

   ![](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209220219525.png)

2. 登陆Gmail，在设置里面选择“过滤器和屏蔽的地址” 

   ![](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209220317396.png)

3. 创建新的过滤器

4. 在发件人处填写“@txt.voice.google.com”，在包含字词中填写“Send Keep GV Live Message![SKGVLM]”，如下图所示：

   ![image-20221209222528076](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209222528076.png)

5. 点击“创建过滤器”，在弹出的对话框中点击“选择标签” –> “新建标签”，标签名"GV-auto-reply"，创建即可。

   ![](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209220655775.png)

   ![](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209220736399.png)

6. 登陆[GoogleDrive](https://drive.google.com/drive/)，点击左上角“新建”

   ![](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209221113800.png)

7. 复制下面的代码替换内容

   ```js
   function autoReplier() {
     var labelObj = GmailApp.getUserLabelByName('GV-auto-reply');
     var gmailThreads;
     var messages;
     var messagecount;
     var sender;
     var num = 9;  //设置连续自动回复邮件的次数（为防止两人都是自动回复，当发送次数达到 9 时将不自动回复）。
     var hours = 12;  //过了多少小时后又可以自动回复。
       
     try {
       for (var gg = 0; gg < labelObj.getUnreadCount(); gg++) {
         gmailThreads = labelObj.getThreads()[gg];
         messages = gmailThreads.getMessages();
         messagecount = gmailThreads.getMessageCount();
         //console.log(messages[messagecount - 9].getDate() + "  time");
         for (var ii = 0; ii < messages.length; ii++) {
         
           if (messages[ii].isUnread()) {
           
             msg = messages[ii].getPlainBody();
             sender = messages[ii].getFrom(); 
           
             array = [["最灵繁的人也看不见自己的背脊。——非洲"],["最困难的事情就是认识自己。——希腊"],["有勇气承担命运这才是英雄好汉。——黑塞"],["阅读使人充实，会谈使人敏捷，写作使人精确。——培根"],["自知之明是最难得的知识。——西班牙"],["有时候读书是一种巧妙地避开思考的方法。——赫尔普斯"],["越是无能的人，越喜欢挑剔别人的错儿。——爱尔兰"],["一个人即使已登上顶峰，也仍要自强不息。——罗素·贝克"],["最大的挑战和突破在于用人，而用人最大的突破在于信任人。——马云"]];
             var j = Math.floor(Math.random() * (array.length));
             var temp = array[j];
           
             if (messagecount < num){
               MailApp.sendEmail(sender, "Auto Reply", temp);
             }else if( (messages[messagecount - 1].getDate().getTime() - messages[messagecount - num].getDate().getTime()) > hours * 60 * 60 * 1000 ){
               MailApp.sendEmail(sender, "Auto Reply", "Hi, 您好！我们已经发了好几条信息了，可以停下来休息休息一下了！本短信由 Google Apps Script 自动发出。");
             }
             messages[ii].markRead();
             messages[ii].moveToTrash();
           }
         }
       }
     } catch (err) {
         console.error('for loop error: ' + e);
     }
   }
   ```

8. 点击保存，在弹出的对话框中输出你要显示的名称，例如：autoReplier。再单击“运行”会提示你授权，你按提示授权即可。授权完后会提示没有找到文件之类的，不用管。

   ![](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209221717449.png)

9. 再次点击运行，如果没有任何提示说明脚本没有错误。你也可以在“脚本执行”中查看脚本运行状态。如果显示状态为已完成则表示脚本没有错误。

   ![image-20221209221924701](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209221924701.png)

10. 设置触发器，在右下角添加触发器

    ![](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209222021496.png)

11. 触发器设置如图

    ![](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209222100206.png)

12. 好了，现在可以试试会不会自动回复了

## 其他保号方法

![](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209223322414.png)

![](https://cdn.jsdelivr.net/gh/HalfCoke/blog_img@master/img/image-20221209223350451.png)

# 参考

https://github.com/ssapym/Action-keepGV

https://www.moeelf.com/archives/4.html

https://www.youtube.com/watch?v=wyjPplc8bf8