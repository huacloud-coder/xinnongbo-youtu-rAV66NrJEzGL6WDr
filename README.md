
大家好，我是Edison。


[上一篇](https://github.com)我们写了一个运维小助手的prompt，它帮我们写一些我们开发者不太愿意做的一些运维小任务。这一篇，给大家分享下我在日常的系统开发中遇到的多语言/全球化的任务中是如何通过GPT来帮忙完成的。


# **背景：基于Blazor的Web应用支持多语言**


在Web应用开发中，我们可能会有一些需要**多语言\+本地化**的场景，特别在一些国际化的外资企业当中特别常见。例如，Edison所在的IT开发团队，就需要**英语，中文和德语三种语言**的支持，用户可以通过“切换页面语言”这个功能来切换到适合他的语言来浏览页面的内容。因此，为Web应用提供多语言，页面内容可以本地化，会扩展我们的IT系统受众范围，提升一点用户体验。我们使用的是Blazor来开发WebUI类应用，通过创建不同的资源文件（.resx或.json）来填充页面的本地化内容，换句话说就是翻译。然后，在代码中使用IStringLocalizer来实现本地化资源的读取。


![](https://img2024.cnblogs.com/blog/381412/202408/381412-20240828183732768-1077820862.png)


我们一般的做法是先完成一种语言的资源文件，再通过翻译到其他类型的资源文件。但是，这个过程在之前比较繁琐，需要一个个的替换，**纯纯的体力活重复劳动**。


这里我们基于之前的提示词模板，稍作修改，形成我们的系统全球化翻译助手的提示词，如下所示，经过一些测试在GPT4\-o模型下效果真的不错，**建议收藏！**




```
你是一位专业的系统多语言翻译助手 @System Globalization Assistant，我会输入相关的请求，你将根据`preferences`、`instruction`和`rules`来协助我高效完成系统语言的全球化工作。  
   
初始回复：“**Hi I'm System Globalization Assistant，Created by Edison Zhou，V1.0，20230823**
### ⚙️ Preferences：
- 🌍 src_lang: <> else English
- 🌍 dest_lang: <> else German
- 📄 output: <> else .resx
### 🤖 Menu：
请使用表格输出支持的`instructions`和对应名称，不需要解释具体含义，也不需要显示这句话：
---
请设置翻译的源语言、目标语言和输出文件格式，E.g.：/src_lang English。  
回复1保持默认。”  
   
`preferences`  
- /src_lang:<源语言偏好，默认为英语>  
- /dest_lang:<目标语言偏好，默认为德语>  
- /headers:<源文件headers偏好，默认为key value comment>
- /output:<输出文件格式偏好，默认为.resx格式>  
   
`instructions`  
- /translate：请根据用户输入的数据直接转换成用户指定的文件格式的输出翻译数据 
- /fix: 用户补充的额外需求和完善需求，非必要步骤请根据用户补充的需求完善之前输出的翻译数据 
- /help：输出支持的指令指引  
- /reset : 重新开始一轮新的对话，用户会再次输入翻译的相关信息比如translate指令，请牢记我们的rules进行输出哈  
   
`rules`  
- 请使用用户设置的偏好的源语言和目标语言进行翻译  
- 每个翻译后的文本请保持原文格式  
- 翻译文本时，提示用户翻译已完成即可，不需要输出翻译结果
- 若文本已翻译，当用户切换dest_lang时，请直接输出结果，无须再次输入需要翻译的数据
- 请一步一步思考，不需要解释过程，如果有错误，用户会纠正你  
- 若将结果转换成 .resx 文件时，格式如下：
xml version=<span style="color: rgba(128, 0, 0, 1)""1.0" encoding="utf-8"?>  
  
  "resmimetype">  
    text/microsoft-resx  
    
  "version">  
    2.0  
    
  "reader">  
    System.Resources.ResXResourceReader, System.Windows.Forms, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089  
    
  "writer">  
    System.Resources.ResXResourceWriter, System.Windows.Forms, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089  
    
    
  "Key1" xml:space="preserve">  
    Value1  
    
  "Key2" xml:space="preserve">  
    Value2  
    
  "Key3" xml:space="preserve">  
    Value3  
    
 
- 若将结果转换成 .json 文件时，格式如下：
{
  "Key1": "Value1",
  "Key2": "Value2",
  "Key3": "Value3"
}
```


# **对话示例**


假设我们有一个基于.NET 6开发的Blazor项目，其使用了.resx做为资源文件（其实它本质上是一个xml文件），并首先完成了英文的资源，我们希望能迅速地把德语资源文件填充。这时，基于上面的模板，这是一个默认配置，因此直接给到GPT需要翻译的资源，这里我直接从.resx文件中拷贝出来即可：




```
/translate  
ActionTextLabel    Action    
AppTokenModule_CreateAppTokenDialog_Title    Create Token    
AppTokenModule_EmailReceiversHelperText    eg. aaa@siemens.com;bbb@siemens.com;    
AppTokenModule_EmailReceiversLabel    Email Receivers    
AppTokenModule_ExpireTimeLabel    Expire Time    
AppTokenModule_NamespaceLabel    Namespace    
AppTokenModule_ReceiverEmailInvalidError    Mail Receivers is invalid    
AppTokenModule_ReceiverListRequiredError    Mail Receivers is required    
AppTokenModule_ReceiverListTooLongError    Mail Receivers is too long    
AppTokenModule_TableLabel    App Token Management    
AppTokenModule_TokenExpireOptionLabel    Expire Option    
AppTokenModule_TokenNameLabel    Name    
AppTokenModule_TokenRemarkLabel    Remark    
AppTokenModule_TokenTypeLabel    Type    
AppTokenModule_UpdateAppTokenDialog_Title    Update Token    
AuthenticationModule_AuthFailedTip    Sorry, you don't have enough permission or not log in.
```


GPT的一个输出示例：




```
</spanxml version="1.0" encoding="utf-8"?>  
<root>  
  <resheader name="resmimetype">  
    <value>text/microsoft-resxvalue>  
  resheader>  
  <resheader name="version">  
    <value>2.0value>  
  resheader>  
  <resheader name="reader">  
    <value>System.Resources.ResXResourceReader, System.Windows.Forms, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089value>  
  resheader>  
  <resheader name="writer">  
    <value>System.Resources.ResXResourceWriter, System.Windows.Forms, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089value>  
  resheader>  
    
  <data name="ActionTextLabel" xml:space="preserve">  
    <value>Aktionvalue>  
  data>  
  <data name="AppTokenModule_CreateAppTokenDialog_Title" xml:space="preserve">  
    <value>Token erstellenvalue>  
  data>  
  <data name="AppTokenModule_EmailReceiversHelperText" xml:space="preserve">  
    <value>z.B. aaa@siemens.com;bbb@siemens.com;value>  
  data>  
  <data name="AppTokenModule_EmailReceiversLabel" xml:space="preserve">  
    <value>Email-Empfängervalue>  
  data>  
  <data name="AppTokenModule_ExpireTimeLabel" xml:space="preserve">  
    <value>Ablaufzeitvalue>  
  data>  
  <data name="AppTokenModule_NamespaceLabel" xml:space="preserve">  
    <value>Namespacevalue>  
  data>  
  <data name="AppTokenModule_ReceiverEmailInvalidError" xml:space="preserve">  
    <value>Mail-Empfänger ist ungültigvalue>  
  data>  
  <data name="AppTokenModule_ReceiverListRequiredError" xml:space="preserve">  
    <value>Mail-Empfänger ist erforderlichvalue>  
  data>  
  <data name="AppTokenModule_ReceiverListTooLongError" xml:space="preserve">  
    <value>Mail-Empfänger ist zu langvalue>  
  data>  
  <data name="AppTokenModule_TableLabel" xml:space="preserve">  
    <value>App-Token-Verwaltungvalue>  
  data>  
  <data name="AppTokenModule_TokenExpireOptionLabel" xml:space="preserve">  
    <value>Ablaufoptionvalue>  
  data>  
  <data name="AppTokenModule_TokenNameLabel" xml:space="preserve">  
    <value>Namevalue>  
  data>  
  <data name="AppTokenModule_TokenRemarkLabel" xml:space="preserve">  
    <value>Bemerkungvalue>  
  data>  
  <data name="AppTokenModule_TokenTypeLabel" xml:space="preserve">  
    <value>Typvalue>  
  data>  
  <data name="AppTokenModule_UpdateAppTokenDialog_Title" xml:space="preserve">  
    <value>Token aktualisierenvalue>  
  data>  
  <data name="AuthenticationModule_AuthFailedTip" xml:space="preserve">  
    <value>Entschuldigung, Sie haben nicht genügend Berechtigungen oder sind nicht eingeloggt.value>  
  data>  
root>  
```


这时，我们可以直接将其贴到.resx文件中（比如用VS Code打开）进行覆盖，当你在Visual Studio再次打开时就能看到所有的资源项（翻译）了。


完成德语的翻译后，我们还想再填充一下中文的资源，并且让它改为输出json格式的资源文件，我们只需要执行以下指令：




```
/dest_lang Chinese
/output .json
```


GPT会直接基于上一次的需要翻译的资源，给到新的输出：




```
{  
  "ActionTextLabel": "操作",  
  "AppTokenModule_CreateAppTokenDialog_Title": "创建令牌",  
  "AppTokenModule_EmailReceiversHelperText": "例如 aaa@sdc.com;bbb@sdc.com;",  
  "AppTokenModule_EmailReceiversLabel": "邮件接收者",  
  "AppTokenModule_ExpireTimeLabel": "过期时间",  
  "AppTokenModule_NamespaceLabel": "命名空间",  
  "AppTokenModule_ReceiverEmailInvalidError": "邮件接收者无效",  
  "AppTokenModule_ReceiverListRequiredError": "邮件接收者是必需的",  
  "AppTokenModule_ReceiverListTooLongError": "邮件接收者太长",  
  "AppTokenModule_TableLabel": "应用令牌管理",  
  "AppTokenModule_TokenExpireOptionLabel": "过期选项",  
  "AppTokenModule_TokenNameLabel": "名称",  
  "AppTokenModule_TokenRemarkLabel": "备注",  
  "AppTokenModule_TokenTypeLabel": "类型",  
  "AppTokenModule_UpdateAppTokenDialog_Title": "更新令牌",  
  "AuthenticationModule_AuthFailedTip": "对不起，您没有足够的权限或未登录。"  
}  
```


如果你用的json格式资源文件，那么直接用这个json内容替换你原有的，就能整体添加了，是不是很快？


# **小结**


本篇，我们了解了如何基于ChatGPT中来帮助我们解决一些系统开发中的多语言/全球化/本地化的任务，通过给到清晰的提示词，让这些任务的解决时间缩短一半以上。


近期，我们也在使用GitHub Copilot作为代码的副驾驶来给我们的Dev开发过程中探索提效体验，后续有机会也希望和大家分享一下GitHub Copilot的使用体会。


# **本文工具**


本文使用的工作：[Azure OpenAI Studio](https://github.com)


本文示例大模型版本：*gpt\-4o*


![](https://images.cnblogs.com/cnblogs_com/edisonchou/1647700/o_200902144330EdisonTalk-Footer.jpg)



作者：[周旭龙](https://edisonchou.cnblogs.com "author"):[豆荚加速器](https://yirou.org)


出处：[https://edisonchou.cnblogs.com](https://edisonchou.cnblogs.com "from")


本文版权归作者和博客园共有，欢迎转载，但未经作者同意必须保留此段声明，且在文章页面明显位置给出原文链接。



