# datapub

填写Google Play Console的**Data safety**问卷（数据安全声明）。这份声明
不属于Store listing，却会卡住Publishing overview页的"Submit changes for
review"入口——即使Store listing本身已经全部填完，这份问卷只要
"Incomplete"，送审按钮就是禁用的。

## 何时用这个skill

- Publishing overview页出现"X issues found"红色横幅，点开"View issues"看到
  "Incomplete data safety declaration"字样。
- 路径：`App content` → `Data safety`，或Publishing overview的"View
  issues"直接跳转。

## 通用陷阱：未走完5步就等于没填

跟Play Console其它声明表单同一个坑（见`assetpub`/`signinpub`skill）：
Data safety问卷所有勾选/输入，在走完5步问卷到底之前，都是本地未持久化
状态。**重新打开这个页面的URL会回到第1步/清空输入**，不会有任何警告。
填到一半要临时切走，先确认已经保存过。

## 5步流程

Overview → Data collection and security → Data types → Data usage and
handling → Preview。

### 步骤1 Overview

纯说明页，直接点Next。

### 步骤2 Data collection and security

这一步最容易漏字段导致"Next"变灰不可点——**遇到Next禁用，往下滚动**，
常见的漏填点：

- "Does your app collect or share any of the required user data types?" ——
  选Yes通常必然（几乎所有App都至少收集账号邮箱）。
- "Which of the following methods of account creation does your app
  support?" —— 按App实际登录方式勾（如OAuth/Username and password）。
  **如果没有勾"My app does not allow users to create an account"，会出现
  两个容易混淆的URL字段，必须都填**：
  - **"Delete account URL"**（在"Add a link that users can use to request
    that their account **and associated data** is deleted"下面）——
    删除整个账号。
  - **"Delete data URL"**（在"Do you provide a way for users to request
    that some or all of their data is deleted, without requiring them to
    delete their account?"选Yes之后出现）—— 只删部分数据，不删账号。

  这两个字段视觉上隔得比较远（中间夹着一堆说明文字），很容易只填了
  后者、漏了前者——`aria-label`/`placeholder`都叫"Delete account URL"
  和"Delete data URL"，字面很像，扫一眼容易看串。如果项目只有一个
  "删除账号"页面（没有单独的"只删数据不删账号"功能），**两个字段填
  同一个URL就够了**，不用为了这一格另外做一个页面。

### 步骤3 Data types

按App**实际**收集/分享的数据据实勾选，不要图省事全不选或全选。参考
分类："Location"（是否用GPS/网络定位）、"Personal info"（姓名/邮箱/
用户ID等）、"Financial info"（支付信息）、"Health and fitness"、
"Audio files"（语音录音，即使只是"处理后立刻丢弃"也要勾，除非严格符合
Google的"ephemeral processing"豁免定义——语音转文字类App大多数情况下
不豁免，因为音频会真的传到第三方ASR服务）、"App activity"、
"Web browsing"、"App info and performance"、"Device or other IDs"。
每类还要进一步说明是否"Collected"/"Shared"、是否用户可选、用途
（App functionality/Analytics/Advertising等）。

**这是法律声明，不是自动化任务**——不要在不确认项目真实数据流的情况下
自动勾选。先读项目自己的隐私政策/后端models（看用户表、日志、第三方SDK
调用）确认真实收集情况，再据实填写。常见分类映射参考：

| 项目实际数据 | Data types分类 | 备注 |
|---|---|---|
| 账号邮箱/用户名 | Personal info → Email address | 通常Collected=Yes，仅App functionality |
| 用户昵称/姓名 | Personal info → Name | 同上 |
| 语音录音（发第三方ASR转写，不落盘） | Audio files → Voice or sound recordings | Collected=Yes，Shared=Yes（第三方处理），仅App functionality |
| 用户输入的自由文本内容 | Other user-generated content | Collected=Yes，通常不对外分享 |
| Google Play购买凭证/到期时间（不是信用卡号） | Financial info → Purchase history | Collected=Yes，不分享 |
| 设备GPS/网络定位 | Location | 如果App不读取设备定位，选未收集 |

### 步骤4 Data usage and handling

补充说明数据是否加密传输、是否遵循Google的"Families Policy"等，跟随
步骤3的答案自动展开对应问题。

### 步骤5 Preview

预览生成的Data safety卡片文案，确认无误后保存/提交。

## 相关

- [`play-console-signin-skill`](https://github.com/mosjin/play-console-signin-skill)（signinpub）——
  同一个Publishing overview送审前置声明的另一半，Sign in details声明。
- [`play-console-assets-skill`](https://github.com/mosjin/play-console-assets-skill)（assetpub）——
  同一个Play Console Publishing流程的另一段，商店列表图形资源上传。
- [`play-console-app-publish`](https://github.com/mosjin/play-console-app-publish-skill)（playpub）——
  Chrome CDP连接方式、App创建、Internal testing上传、订阅商品创建。
