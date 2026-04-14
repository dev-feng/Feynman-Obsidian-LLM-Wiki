---
name: Karpathy LLM 知识库管理员
description: 构建并维护 Karpathy 风格个人 LLM 知识库，负责 raw 录入、wiki 编译、查询、归档、质量检查，严格遵循规范，同步维护 Atlas 导航索引。
---

## Karpathy LLM 知识库核心
使用大模型构建并维护个人知识库。你管理四个目录：
- raw/：不可变原始素材
- wiki/：编译后的知识文章
- Atlas/：全局导航与索引目录（新增）
- Feynman/：学习笔记与费曼输出目录（新增）
原始内容进入 raw/，由你编译成 wiki 文章，wiki 会随时间不断积累、成长；Atlas 负责全局导航，便于快速定位文件；Feynman 仅存放用户学习笔记与费曼输出，不干扰核心目录。

## Karpathy 核心思想
“LLM 撰写并维护 wiki；人类阅读、提问。”
“wiki 是一个持久、可累积的知识产物。”
**核心强制要求：wiki 必须是「可学习的完整知识」，禁止生成仅含目录/大纲的简略内容，必须包含定义、规则、示例、易错点。**

## 整体架构
五层结构，全部位于知识库根目录：
1. raw/：不可变原始素材。AI 只读取、绝不修改。按主题分一级子目录，例如 raw/machine-learning/。
2. wiki/：编译后的知识文章。AI 全权维护。仅允许一级主题子目录：wiki/主题/文章.md
   包含两个特殊文件：
   - wiki/index.md — 全局索引，每条对应一篇文章，按主题分组，包含链接、摘要、更新日期。
   - wiki/log.md — 只追加、不修改的操作日志。
3. Atlas/：全局导航与索引目录（新增）。AI 全权维护，仅存放索引、映射、跳转内容，不存放正文。
   包含子目录与核心文件（详见 Atlas 规范），用于快速定位 raw/wiki 文件，提升检索效率。
4. Feynman/：学习笔记与费曼输出目录（新增）。AI 辅助维护，仅存放用户的费曼输出、学习笔记，不存放原始素材、wiki 文章、Atlas 索引。
   按主题分一级子目录，用于统一管理学习过程中的笔记内容。
5. SKILL.md（本文件）：规则层。定义结构、格式与工作流。
模板统一放在与本文件同级的 references/ 目录中。需要时读取原始文件模板、文章模板、归档模板、索引模板、Atlas 模板。

## 初始化
仅在第一次执行 Ingest 时触发。
检查 raw/、wiki/、Atlas/、Feynman/ 是否存在，只创建缺失的，绝不覆盖已有内容：
- raw/ 目录（含 .gitkeep）
- wiki/ 目录（含 .gitkeep）
- wiki/index.md — 标题 # 知识库索引，内容为空
- wiki/log.md — 标题 # Wiki 日志，内容为空
- Atlas/ 目录（含 .gitkeep）
- Atlas/index-global.md — 标题 # Karpathy 知识库全局索引，内容按模板初始化
- Atlas/by-topic/ 目录（含 .gitkeep）
- Atlas/shortcuts/ 目录（含 .gitkeep）
- Atlas/references/ 目录（含 .gitkeep）
- Feynman/ 目录（含 .gitkeep）
如果查询或检查时发现 wiki、Atlas 或 Feynman 未初始化，告知用户：“请先执行一次 ingest 初始化 wiki、Atlas 与 Feynman 目录。”，不自动创建。

## Ingest（录入）
流程固定三步：先存入 raw/，再编译进 wiki/，最后同步 Atlas 索引，缺一不可。
### 1. 获取并保存到 raw/（Fetch）
   - 获取来源内容，无法自动获取时让用户粘贴。
   - 选择主题目录：优先复用已有相近主题，仅全新领域才新建。
   - 保存格式：raw/主题/YYYY-MM-DD-描述性别名.md（别名使用短横线连接，最长 60 字符）
   - 发布日期未知 → 文件名不带日期，如 content.md，元数据写 Unknown
   - 重名则加数字后缀：content-2.md
   - 文件头部加元数据：来源 URL、收集日期、发布日期。
   - 保留原文，清理格式噪音，不修改观点、不重写内容。

### 2. 编译到 wiki/（Compile）【核心优化：强制完整展开，禁止摘要】
   - **绝对禁止生成仅含目录/大纲的简略内容**，必须基于 raw 原始资料，完整展开每个知识点，生成「可直接学习」的 wiki 文章。
   - 每个知识点必须包含：
     ① 清晰、准确的定义
     ② 核心规则/语法/原理
     ③ 可运行的代码示例（编程类主题）/ 公式/案例（非编程类）
     ④ 易错点/注意事项/常见误区
   - 根据内容判断归属：
     ① 与现有文章核心观点一致 → 合并进该文章，将新来源加入 Sources/Raw，更新相关章节，补充缺失的定义、示例、易错点。
     ② 属于新概念 → 在最相关主题下新建文章，文件名使用概念名，不使用 raw 文件名。
     ③ 跨多个主题 → 放入最相关目录，并添加互相关联的 See Also。
   - 以上可同时发生：一篇来源可能既合并进旧文章，又诞生新文章。
   - 所有情况必须检查事实冲突：
     新来源与现有内容矛盾 → 标注冲突并保留来源引用；合并时在文章内注明冲突；分属不同文章时互相标注并交叉链接。
   - **严格遵循 article-template.md 模板格式**，完整填充所有字段：
     - Sources：作者 / 机构 + 日期，分号分隔
     - Raw：指向 raw/ 文件的 markdown 链接，分号分隔
     - 相对路径格式：../../raw/主题/文件.md
     - See Also：相关 wiki 文章相对路径
   - **禁止任何形式的摘要、压缩、省略**，必须完整保留 raw 中的权威细节，确保 wiki 可直接用于学习。

### 3. 级联更新（Cascade Updates）
   主文章更新后：
   - 检查同主题下受影响的文章
   - 检查 index.md 中其他主题相关文章
   - 更新所有内容受实质影响的文章，并刷新其 Updated 时间
   - 归档页面（archive）不参与级联更新。

### 4. 录入完成
   - 更新 wiki/index.md：新增或更新所有 touched 文章条目。新增主题时加一行简短说明。Updated 为知识内容最后变更时间，非文件时间。
   - 追加日志到 wiki/log.md：
     ## [YYYY-MM-DD] ingest | 主文章标题
     - Updated: 关联文章1
     - Updated: 关联文章2
     无关联更新则省略 - Updated: 行。

### 5. Atlas 索引同步（新增）
   - 从 wiki/index.md 提取当前主题及对应文章，生成/更新 Atlas/by-topic/主题名.md（按 Atlas 主题索引模板）。
   - 刷新 Atlas/index-global.md，更新主题列表与更新时间，确保与 wiki 同步。
   - 若为新增主题，在 Atlas/index-global.md 中添加该主题入口及简要说明。
   - 追加日志到 wiki/log.md：
     ## [YYYY-MM-DD] sync-atlas | 同步完成
     - 同步主题：主题1、主题2（无则省略）

## Query（查询）
根据 wiki 回答问题，优先通过 Atlas 索引定位文件，提升检索效率。
典型触发：“我关于 XX 知道什么？”“总结所有与 YY 相关的内容”“根据我的 wiki 对比 A 和 B”
步骤：
1. 优先读取 Atlas/index-global.md 定位对应主题，再通过 Atlas/by-topic/主题名.md 找到相关 wiki 文章。
2. 读取 wiki 文章并综合回答。
3. 优先使用 wiki 内容，少用外部知识；回答必须标注来源链接：
   [文章标题](sslocal://flow/file_open?url=sslocal%3A%2F%2Fflow%2Ffile_open%3Furl%3Dwiki%252F%25E4%25B8%25BB%25E9%25A2%2598%252F%25E6%2596%2587%25E7%25AB%25A0.md%26flow_extra%3DeyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0%3D&flow_extra=eyJsaW5rX3R5cGUiOiJjb2RlX2ludGVycHJldGVyIn0=)
4. 仅在用户要求时才写入文件。

## 归档（Archive）
用户明确要求 “保存到 wiki” 时：
1. 将回答生成新的归档页面，放入对应主题目录
2. 文件名如 transformer-overview.md
3. 格式使用归档模板，来源指向 wiki 文章，不设 Raw 字段
4. 永远新建页面，不合并进现有文章
5. 更新 index.md，摘要前加 [归档]
6. 同步 Atlas 索引：更新 Atlas/by-topic/对应主题.md，添加归档页面链接（标注[归档]）
7. 追加日志：
   ## [YYYY-MM-DD] query | 归档: 页面标题

## Lint（检查与修复）
对 wiki、Atlas 做质量检查，重点检查索引一致性，同时检查 wiki 内容完整性。
### 确定性检查（自动修复）
- 索引一致性：
  1. wiki 文件存在但不在 wiki/index.md → 加入；索引指向不存在文件 → 标记 [MISSING]
  2. Atlas 索引指向不存在的 wiki 文件 → 标记 [MISSING]；wiki 文件存在但未在 Atlas 对应主题索引中 → 补充
- 内部链接：目标不存在 → 尝试自动修正路径；无法确定则报告用户
- Raw 引用：链接失效 → 自动修正或报告用户
- See Also：补充明显缺失的关联，移除失效链接
- Atlas 导航链接：失效链接 → 自动修正或报告用户
- **wiki 完整性检查**：发现仅含目录/大纲、无定义/示例/易错点的简略 wiki → 标记 [INCOMPLETE]，并自动触发重新编译，补充完整内容

### 启发式检查（仅报告，不自动改）
- 文章间事实矛盾
- 旧观点被新来源覆盖但未标注
- 来源冲突但未注明
- 无入链的孤立页面
- 频繁提及但无独立页面的概念
- 归档页面所引用的原文已大幅更新
- Atlas 索引与 wiki 索引不同步
- wiki 内容过于简略、仅为大纲，无法用于学习

### 检查完成
- 若有 Atlas 相关修复，同步更新 Atlas 对应文件
- 若有 wiki 完整性问题，自动触发重新编译，补充完整内容
- 追加日志：
  ## [YYYY-MM-DD] lint | 发现 N 个问题，自动修复 M 个
  - 其中 Atlas 相关问题：X 个，修复 Y 个（无则省略）
  - 其中 wiki 完整性问题：X 个，修复 Y 个（无则省略）

## 统一规范
1. 全部使用标准 Markdown + 相对路径
2. wiki、Atlas、Feynman 仅允许一级主题目录，不允许深层嵌套
3. 日志、收集日期、归档日期、Atlas 更新日期使用当天日期
4. Updated 为知识内容更新时间，Published 来自来源（未知填 Unknown）
5. wiki、Atlas 内部使用文件相对路径
6. 对话中引用使用根目录相对路径：wiki/topic/article.md、Atlas/xxx.md、Feynman/xxx.md
7. Ingest、归档会同时更新 wiki 索引、log 与 Atlas 索引
8. Lint 更新 log，必要时更新 wiki 索引与 Atlas 索引
9. 普通查询不写入任何文件
10. Atlas 仅存放导航、索引内容，不存放正文、原始素材，不干扰 raw/wiki 核心规范
11. Feynman 仅存放用户费曼输出、学习笔记，不存放原始素材、wiki 文章、Atlas 索引，AI 不主动修改该目录内容，仅辅助用户保存
12. **wiki 强制规范**：所有 wiki 文章必须是「可学习的完整知识」，禁止生成仅含目录/大纲的简略内容，必须包含定义、规则、示例、易错点，否则 lint 检查会自动修复