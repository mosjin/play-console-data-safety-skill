[English](README.en.md) | **简体中文**

# play-console-data-safety

Claude Code skill：填写Google Play Console的Data safety问卷（5步）。
这份声明不属于Store listing，却会卡住Publishing overview页的"Submit
changes for review"入口。通用skill，不绑定任何具体项目目录。内容默认
简体中文；代码、CLI指令、文件路径、技术术语保留原文。

## 安装

```
/plugin marketplace add mosjin/play-console-data-safety-skill
/plugin install datapub@play-console-data-safety
```

实际调用用短名`datapub`（如`/datapub:version`、`/datapub:upgrade`），
沿用`mosjin/android-voice-test-kit`的约定——描述性的marketplace/repo名下
配一个简短的插件命令名。

## 覆盖内容

- Data safety问卷5步流程，尤其是"Data collection and security"这步两个
  容易看串的URL字段——"Delete account URL"（删账号）和"Delete data
  URL"（只删数据不删账号）——漏填前者会让Next一直灰着，很容易只顾着填
  后者。
- Data types步骤的填写原则：据实申报，不是自动化任务，填之前先核实
  项目真实的数据收集/第三方SDK情况（尤其语音类App的音频上传大多数不
  符合"ephemeral processing"豁免）。
- 常见项目数据类型 → Google分类的映射参考表（账号邮箱/姓名/语音录音/
  用户自建内容/购买凭证/设备定位）。
- 跟`assetpub`/`signinpub`共享的通病：表单是"未走完问卷就等于没填"，
  重新打开页面URL会静默清空未保存的输入。

## 相关

- [`play-console-signin-skill`](https://github.com/mosjin/play-console-signin-skill)（signinpub）——
  同一个Publishing overview送审前置声明的另一半，Sign in details声明。
- [`play-console-assets-skill`](https://github.com/mosjin/play-console-assets-skill)（assetpub）——
  同一个Play Console Publishing流程的另一段，商店列表图形资源上传。
- [`play-console-app-publish`](https://github.com/mosjin/play-console-app-publish-skill)（playpub）——
  Chrome CDP连接方式、App创建、Internal testing上传、订阅商品创建。
