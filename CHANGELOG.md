# Changelog

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
