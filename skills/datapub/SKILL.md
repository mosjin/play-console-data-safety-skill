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

步骤3勾完"收集哪些数据类型"之后，这一步给每个勾中的数据类型生成一行
"Not started"状态的表格项，点"Open <数据类型> questions"弹出一个独立
dialog，依次问：

1. **Is this data collected, shared, or both?** —— 两个checkbox
   （Collected / Shared），可以都勾。
2. 只有勾了Collected，才会展开"Is this data processed ephemerally?"
   （Yes/No单选）——不确定就选No（"有疑问就不算ephemeral"）。
3. "Is this data required for your app, or can users choose whether
   it's collected?" —— 必选单选（required / optional）。姓名/邮箱这类
   账号必填信息通常选required；类似"购买记录"这种是否算"用户可选"要
   结合产品实际付费墙判断，不确定就跟产品负责人确认，不要凭直觉选。
4. "Why is this user data collected? Select all that apply" —— 一组
   用途checkbox（App functionality/Analytics/Developer communications/
   Advertising or marketing/Fraud prevention.../Personalization/
   Account management）。
5. **如果同时勾了Shared，还会再出现一组独立的"Why is this user data
   shared?"** ——跟第4点结构一样但是单独一组checkbox，容易漏勾（勾了
   "收集用途"却忘了"分享用途"）。

填完点dialog内的"Save"按钮（不是页面底部的"Next"），dialog关闭后该行
状态从"Not started"变成"Completed"。**所有行都变成Completed，页面底部
的"Next"才会从disabled变可点**。

### 步骤5 Preview

预览生成的Data safety卡片文案（Data shared/Data collected/Data
deletion/Security practices/Privacy policy几个板块），确认无误后保存/
提交，页面提示"Change saved"即为成功。

## 送审前置依赖：Target audience and content声明必须一起填完

**这份问卷内容填完保存 ≠ Data safety彻底闭环。** `App content`总览页
的"declarations need attention"计数是**跟Target audience and content
（目标受众与内容分级）声明联动的**——如果那份声明从没开始填过（按钮
写着"Start declaration"），即使Data safety这份问卷5步全部Completed、
Preview页也生成了完整摘要，Preview页仍会出现红色提示："To submit your
Data safety questionnaire, you must let us know the target age group of
your app..."，问卷仍不算真正可送审。

Target audience and content是一份独立的法律声明（涉及是否面向儿童、
应用商店年龄分级等问题），**不属于这个skill的范围**，需要产品负责人
自己拍板填写，不要替他们自动化决定。但在判断"Data safety是不是真的
完成了"时，务必先检查`App content`总览页里这份声明的状态，不要只看
Data safety自己的5个圆点是不是打勾——两份声明有依赖关系，容易漏查。

## 用agent-browser+CDP自动化填这份问卷时的额外坑

以下是实测GoldenPen（金笔头）项目Data safety问卷全流程时踩出来的坑，
跟`assetpub`/`signinpub`skill记录的"未Save即丢""重复文案元素"是同一类
问题在这份问卷里的具体表现。

### 陷阱1：多个tab共存时，`eval`/`get url`可能悄悄操作错tab

如果同一个CDP连接的Chrome里还开着其它无关tab，`agent-browser --cdp
<port> eval "..."`和`get url`默认作用的"当前活动tab"，**不一定是`open`
命令刚打开、`click`/`snapshot`实际在操作的那个tab**——实测出现过`click`
和`snapshot`稳定操作Data safety页面，但紧接着的`eval`却读到了完全无关
的另一个网站标题。

**排查信号**：`eval "document.querySelectorAll('button').length"`返回
`0`或明显偏小的数字，而`snapshot`里同一时刻看到几十个按钮——说明`eval`
打到了别的tab。

**修复**：先`agent-browser --cdp <port> tab list`看所有tab，确认目标
页面的tab id（形如`t4`），再显式`agent-browser --cdp <port> tab t4`
切换过去，之后的`eval`/`get`才会跟`click`/`snapshot`保持在同一个tab
上。每次怀疑状态不对时，先tab list+get url核对，不要假设"上一条命令
用的哪个tab这条也一定用哪个"。

### 陷阱2：snapshot里的`ref`一过期就可能点到完全不相关的元素，且不报错

`ref=eNN`这种引用只在**它所属的那一次snapshot**内有效。页面任何DOM
变化（点了一次Next翻页、点了一次手风琴展开/收起、甚至只是自动保存
触发的局部重渲染）之后，旧snapshot里的`ref`编号会被**新的、完全不同
的元素**复用——继续拿旧ref去点击，`click "@eNN"`会正常返回`✓ Done`
（不报错），但实际点中的可能是页面上另一个disabled的tab、或者别的
按钮，造成"看起来点了但什么也没发生"甚至"点到了错误的地方"这种沉默
故障。

**规则：每次点击前，如果不确定当前DOM是不是跟上一次snapshot时完全
一致（尤其是刚发生过页面跳转/翻页/展开折叠/自动保存提示出现），就
先重新`snapshot`拿新鲜的ref，不要复用超过一步操作之前的ref。** 用
`grep -A5 'tab "Step'`这类小范围grep确认当前处于第几步，比每次读全量
snapshot省token。

### 陷阱3：步骤3 Data types页面的分类是手风琴——同一时间只能展开一个

Location/Personal info/Financial info/.../Device or other IDs这十几个
分类，点"Show content: X"展开一个的同时，**之前展开的那个会自动收起**
（不是并列可展开多个）。如果按"依次点开A、B、C、D四个分类"的顺序连续
调用而不在每次点击后重新snapshot确认，最终只有最后点的D是展开状态，
A/B/C早就被自动折叠了——此时如果拿着"snapshot前记录的A/B/C里的checkbox
ref"去check，命中的是已经不在DOM可见路径里的过期ref，行为同陷阱2。

**正确顺序**：展开一个分类 → 立刻snapshot确认已展开 → 在这次新
snapshot里勾选这个分类需要的checkbox → 立刻snapshot确认勾上了 → 换
下一个分类，重复。不要指望能一次性展开多个分类批量操作。

步骤3本身只是"勾选收集/分享了哪些数据类型"的粗粒度多选，**不含**
"是否分享""用途"这些细节问题——那些细节问题在步骤4才出现，不要在
步骤3的checkbox旁边找这些字段。

### 陷阱4：checkbox的`click`经常静默不生效，`check`命令更可靠

Play Console的checkbox是Material Design风格的自定义组件（勾选框本身
是`checkbox` role，外层包一层`clickable`的`generic`容器）。实测：
- 直接对checkbox的accessibility ref用`click "@eNN"`，经常报`✓ Done`
  但`checked`状态纹丝不动。
- 对checkbox外层的`clickable`容器ref用`click`，同样经常不生效。
- 改用`agent-browser --cdp <port> check "@eNN"`（专门的check命令，
  语义是"确保这个checkbox处于选中状态"）**稳定生效**。

结论：**Play Console里所有checkbox一律用`check`命令，不要用`click`**。
"Show content: X"/"Open X questions"这类**按钮**（不是checkbox）则
相反，`click`一般够用，但如果`click`报`✓ Done`后状态没变化，改用
`eval "(() => { const b = Array.from(document.querySelectorAll('button'))
.find(b => (b.getAttribute('aria-label')||'') === '<精确文案>'); b.click();
return b.getAttribute('aria-expanded'); })()"`这种基于`aria-label`精确
匹配再调用原生`.click()`的写法兜底，比反复重试ref click更快收敛。

### 陷阱5：偶发整页"Loading Google Play Console"重载，不会丢已保存的
步骤，只会把wizard的可见步骤重置到第1步

操作过程中可能出现一次页面突然整个重载成"Loading Google Play Console"
过渡态，几秒后恢复。**实测重载后：**
- URL、tab、登录态都还在，不是掉线。
- Wizard顶部5个步骤圆点会回到"Step 1 of 5"选中状态（看起来像是回到了
  第1步）。
- **但已经点过Next/dialog Save保存过的数据完全没丢**——重新一路点Next
  跳回之前的进度（比如跳到Step 4），会看到之前填过的行仍然是
  "Completed"状态，未处理的行仍是"Not started"，跟重载前一致。

结论：这次重载只是**wizard当前高亮到第几步的前端状态**被重置，不是
"未保存数据被丢弃"（未点Save之前的草稿状态才会真的丢，见开头"通用
陷阱"）。遇到这种整页重载，先按陷阱1的规则确认tab、URL没变，然后不慌
不忙地把Next点回之前的进度，不用重新填已经Completed的项。

### 陷阱6："Other user-generated content"这类数据类型，藏在看似无关的
大类底下

"Other user-generated content"（App存的自由文本内容，比如物品名称/
存放位置描述这类用户自己输入的非结构化内容）**归在"App activity"大类
下面**，不是一个独立大类，也不在"Personal info"或"Files and docs"里
——展开"App activity"能看到`App interactions / In-app search history /
Installed apps / Other user-generated content / Other actions`这5个
子项。判断"用户自由输入的业务数据该勾哪里"时，先展开App activity看一眼
，比瞎猜要快。

### 陷阱7：怎么确认问卷真的"完成"了，而不是被"Declaration overdue"这种
无关横幅唬住

Data safety问卷全部5步填完保存后，**判断"是否真的完整"不要只看**
`App content`概览页里"Data safety"卡片上的"Declaration overdue"字样
——这行字是Google给2022年8月23日合规截止日之后所有App的**通用提示**，
意思是"这份声明还没有作为一次正式发布的一部分被送审"，跟问卷内容
填没填完全无关，只要App还没提交审核，这行字永远会显示，属于正常现象。

**真正可靠的完成判据（任选其一/组合验证）：**
1. Data safety页面wizard顶部5个圆点全部变成✓打勾图标（不再是数字）。
2. 走到Step 5 Preview，页面能生成完整的"Store listing preview"卡片
   （几个板块都有实际内容，不是空白），且底部出现"Change saved. Send
   for review in Publishing overview."提示。
3. 去Publishing overview页的"Changes not yet submitted for review"
   表格里确认**没有**"Data safety"相关的行。

**但以上三条都只验证"Data safety这份问卷自己填完了"，验证不出上面
"送审前置依赖"一节说的Target audience未填问题**——这是本skill在
GoldenPen项目实战中亲身踩过的坑：三条判据全部满足，Preview页仍然弹出
Target audience相关的红色提示。所以完整的判断顺序是：先用上面三条确认
问卷自身填完，再去`App content`总览页确认Target audience and content
声明状态，两者都通过才算真正闭环。

**"送审"（Publishing overview页点"Submit changes for review"）是完全
独立于"填完问卷"的下一步，且通常属于用户自己拍板的范围，不要因为想
"彻底完成"就顺手点了它。**

## 相关

- [`play-console-signin-skill`](https://github.com/mosjin/play-console-signin-skill)（signinpub）——
  同一个Publishing overview送审前置声明的另一半，Sign in details声明。
- [`play-console-assets-skill`](https://github.com/mosjin/play-console-assets-skill)（assetpub）——
  同一个Play Console Publishing流程的另一段，商店列表图形资源上传。
- [`play-console-app-publish`](https://github.com/mosjin/play-console-app-publish-skill)（playpub）——
  Chrome CDP连接方式、App创建、Internal testing上传、订阅商品创建。
