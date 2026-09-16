# Changelog

## 1.2.0 (2026-09-16)

- **血泪教训新增（陷阱0）**：Data types步骤实测发生过真实数据丢失——
  上次会话已填完并显示"Change saved"的4个数据类型，重新打开页面后
  全部清零，而同一份问卷步骤2的内容完好，说明不是整份重置，是步骤3
  单独的持久化问题；疑似与CDP连接下同时存在多个指向同一URL的重复tab
  有关。新增可靠验证法：填完关键步骤后强制`location.reload()`，走到
  目标步骤展开checkbox直接读`checked`属性核实，不要只信页面提示或
  Preview摘要文案。确认"More options"菜单的"Save draft"选项能真正
  持久化（已用强制刷新验证）。
- **新增陷阱8**：打开过一次"More options"菜单后，旧的"Open X
  questions"/"Show content: X"按钮ref会被菜单项顶替，导致点击命中
  "Save draft"而不是目标按钮；修复法是先点空白处关闭残留菜单，再用
  `aria-label`精确匹配+`offsetParent !== null`过滤重新定位（这个页面
  同一`aria-label`经常匹配到2个DOM节点，跟`assetpub`记录的响应式布局
  重复渲染是同一类问题）。

## 1.1.0 (2026-09-16)

- 补充GoldenPen（金笔头）项目实战填写Data safety问卷全流程时踩的7个新坑：
  多tab场景下`eval`/`get url`可能操作错tab、snapshot里的`ref`过期后
  静默点错元素、步骤3 Data types的分类手风琴（同时只能展开一个）、
  checkbox要用`check`命令而非`click`、步骤4 Data usage and handling
  每个数据类型的详细子问卷结构（collected/shared/ephemeral/required/
  purpose，Shared额外有一组独立用途checkbox）、偶发整页重载不会丢
  已保存进度只会重置wizard可见步骤、"Other user-generated content"
  归在"App activity"大类下。
- 新增关键发现：**这份问卷内容填完保存不等于Data safety彻底闭环**——
  `App content`总览页的Target audience and content声明如果没填过，
  Preview页仍会弹出红色提示阻止真正送审，两份声明有依赖关系。
- 修正"怎么判断问卷真的完成"的判据：原来只看5步圆点/Preview摘要/
  Publishing overview变更表这三条不够，必须额外核对Target audience
  声明状态。

## 1.0.0 (2026-09-16)

- 首个版本。从`play-console-declarations-skill`（declarepub）拆分出来，
  专注Data safety问卷5步流程：Delete account URL/Delete data URL易混淆
  坑、Data types据实申报原则和常见分类映射表。
