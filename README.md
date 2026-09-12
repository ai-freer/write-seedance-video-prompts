[简体中文](README.md) | [English](README.en.md)

# Seedance 2.5 视频 Prompt Skill

一个面向 Seedance 2.5 的路由型视频创作 Skill。它不是固定 Prompt 模板，而是根据创作成本、视频长度、操作类型、人物资产和控制精度，选择合适的工作流并编译出可执行生成包。

> 当前状态：v0.3.0 创作与迭代升级。在既有官方能力规则上增加镜头任务、空间与表演、参考职责、试拍和粗剪反馈；第三方案例经验与官方能力分开。离线验证不等于视频效果实测，不自动调用付费生成。

## 解决什么问题

- 先判断应该走快速 demo，还是长视频 / 精细制作流程；
- 在生成、续写、编辑和无缝拼接之间选择正确协议；
- 根据风险选择故事线、节拍、逐镜头或精确时间轴；
- 用已批准参考图锁定主要人物，并允许同一人物按场景更换 Look；
- 管理角色、服装、场景、道具、音频和参考视频等项目资产；
- 按条件组装素材映射、故事、镜头、声音、连续性和负向约束；
- 在高成本生成前发现资产冲突、输入超限和返工风险。

## 两条创作路径

| 路径 | 适用场景 | 默认流程 |
| --- | --- | --- |
| 快速短片 | 简短 demo、创意验证、低资产复杂度 | 最小 Brief → 必要资产 → Prompt → 预检 |
| 长视频 / 精细制作 | 超过 30 秒、多场景、多 Look、产品展示或高重抽成本 | Brief → 资产 → 分镜 → 最终生成包四个 Gate |

新创作先确定路径，再确认项目目录和资产档案；用户已提供的答案直接沿用。没有档案时推荐默认模板，也可按用户意愿暂不落盘。

## 核心设计

### 多轴路由

每次任务分别决定：

1. 创作流程；
2. 操作类型；
3. 生成策略；
4. 规划粒度；
5. 控制锚点；
6. 约束模块。

时间轴、多宫格、白模和创意参考不是同一个维度的互斥模式，可以按任务组合。

### 主要人物资产层级

```text
角色视觉规格
  → 已批准标准角色参考资产
  → 不同场景 Look
  → 本次生成引用包
  → Seedance @图片/@视频/@音频 映射
```

文字规格用于生成和验收角色设定图；正式视频通过已批准参考图资产锁定身份。次要人物默认使用 Prompt 多维描述，需要跨镜头稳定时再升级为受管资产。

## 使用方式

### 从能力控制到创作闭环

新增的[创作与迭代指南](references/creative-direction.md)把观众体验转成可见动作、镜头揭示和进出状态，包含两个原创 Prompt 推演示例。精细项目按风险选择代表镜头试拍，并根据实际看片决定裁切、补资产、改镜头或重制，而非默认重抽。

可独立使用，也可配合导演工作台：已有工作台档案时沿用其唯一主档，不再创建平行 YAML/TXT。工作台负责对象、版本、媒体与剪辑关系，本 Skill 负责生成设置、引用映射、Prompt 和创作预检；无需新增工作台 API。

当该 Skill 已被支持 Skills 的客户端加载后，可直接发起：

```text
使用 $write-seedance-video-prompts 帮我规划并生成一支 Seedance 2.5 视频 Prompt。
```

Skill 会先让用户选择：

1. 快速短片；
2. 长视频 / 精细制作。

随后自适应追问，并在最终交付中提供：

- 生成设置；
- 上传顺序与素材引用映射；
- 可复制的 Seedance 2.5 Prompt；
- 生成前风险和待确认项。

当前仓库是 Skill 源码，入口为 [`SKILL.md`](SKILL.md)。不依赖特定用户名、工作区、发布工具或已登录账号。

### 安装与更新

1. 下载或克隆本仓库，选择要使用的版本。
2. 按目标 Agent 客户端的当前说明，找到其支持的用户级或项目级 Skills 目录。
3. 在该目录下创建 `write-seedance-video-prompts/`，放入 `SKILL.md`、`LICENSE`、`agents/`、`references/` 和 `assets/`，保留相对结构及许可声明。`README.md` 与 `docs/` 用于阅读和维护，不是运行时必需文件。
4. 按客户端要求刷新技能列表或开启新会话，再使用上面的示例请求。

如果客户端支持从 Git 仓库安装 Skill，也可以使用其安装器，选择仓库根目录。各客户端的目录、发现机制和符号链接支持可能不同，不假定一个路径适用于所有 Agent。

更新时先保留自定义修改，再用选定的新版本替换本 Skill 的运行时文件；不要覆盖其他 Skill 或视频项目资产。版本标记位于 `SKILL.md` 正文的独立 `version:` 行。

可选：多 Agent 共用时，可以自行选择一个共享目录，再按各客户端支持的方式引用。不需要本仓库之外的私有发布脚本；共享目录与链接管理由使用者的环境决定。

### 项目与工具隔离

- 在使用者选择的独立创作目录中保存项目档案、人物素材和生成结果，不写入 Skill 安装目录，也不回传到本仓库。
- 新项目复制 `assets/project-template/` 后再填写；仓库内模板保持空白，示例人物与资产 ID 仅作字段演示。
- 只编写 Prompt 不需要 API Key、即梦登录或生成工具。实际生图/视频生成需要使用者自己的工具、账号、权限和成本授权。
- 网页、CLI、API 的开放能力可能不同；执行前核对实际入口，不把手册能力视为所有工具都已支持。

## 仓库结构

```text
write-seedance-video-prompts/
├── README.md
├── README.en.md
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
│   ├── workflow-router.md
│   ├── project-assets.md
│   ├── prompt-compiler.md
│   ├── seedance-2.5-reference.md
│   ├── case-patterns.md
│   ├── creative-direction.md
│   └── image-asset-generation.md
├── assets/
│   └── project-template/
│       ├── project.yaml
│       ├── asset-registry.yaml
│       └── brief.md
└── docs/
    ├── design/
    │   └── system-design.md
    └── plans/
        ├── 2026-08-03-v0.1-implementation.md
        └── 2026-09-06-v0.2-update.md
```

## 文档导航

英文版 README 见顶部链接；其余 Skill 与参考文档目前以中文编写。

| 文档 | 职责 |
| --- | --- |
| [`SKILL.md`](SKILL.md) | Agent 运行时入口、强制顺序和条件路由 |
| [`docs/design/system-design.md`](docs/design/system-design.md) | 总体架构、设计依据、状态体系和演进路线 |
| [`docs/creative-upgrade-validation.md`](docs/creative-upgrade-validation.md) | v0.3 升级范围、场景检查与验证边界 |
| [`docs/plans/2026-08-03-v0.1-implementation.md`](docs/plans/2026-08-03-v0.1-implementation.md) | v0.1 实施过程与验证记录 |
| [`docs/plans/2026-09-06-v0.2-update.md`](docs/plans/2026-09-06-v0.2-update.md) | v0.2 手册覆盖、更新范围与验证边界 |
| [`references/workflow-router.md`](references/workflow-router.md) | 多轴路由与两条创作路径 |
| [`references/project-assets.md`](references/project-assets.md) | 角色、Look 和项目资产管理 |
| [`references/prompt-compiler.md`](references/prompt-compiler.md) | 生成、续写、编辑和拼接 Prompt 编译 |
| [`references/seedance-2.5-reference.md`](references/seedance-2.5-reference.md) | Seedance 2.5 官方能力与边界速查 |
| [`references/case-patterns.md`](references/case-patterns.md) | 按目标选择官方案例模式，以及示例冲突与失败风险 |
| [`references/creative-direction.md`](references/creative-direction.md) | 第三方案例启发、原创推演、镜头任务与迭代闭环 |
| [`references/image-asset-generation.md`](references/image-asset-generation.md) | GPT、Gemini、Seedream、Grok Image 生图适配 |

## 文档职责与事实来源

- README 只做仓库入口，不复制完整系统规则；
- 系统设计解释为什么这样设计；
- `SKILL.md` 定义 Agent 必须如何执行；
- `references/` 保存各模块的详细操作规则；
- `assets/project-template/` 是新视频项目的状态与档案模板。

当文件内容出现冲突时，运行时行为以 `SKILL.md` 和其直接引用的 `references/` 为准；总体设计的改变应先记录在系统设计文档中。

## 当前边界

- 视频目标模型只支持 Seedance 2.5；
- GPT、Gemini、Seedream、Grok Image 仅用于生成上游图片资产；
- 不自动执行付费生图、视频生成或重抽；
- 时间轴是强引导，不是帧级保证；
- 案例索引是对手册文字的提炼，不是本仓库生成实测；图片模型适配尚未完成逐模型功能验证；
- 手册修订 8839 已复核，旧正文无法读取，不能把修订计数差当成具体更新数量。

## 下一阶段

用一个真实快速短片和一个长视频项目验证成片质量、资产复用和返工成本，再决定是否需要自动化工具。当前保留单一 Router、按需参考文档和轻量 YAML 档案，避免把案例数量变成流程复杂度。

Seedance 2.5 能力基线来自[官方使用手册](https://bytedance.larkoffice.com/wiki/RXh5ww6EqighMdkVTMccm2d4n7e)。

## 共享与发布边界

本项目是独立的社区 Skill，不是即梦或模型厂商的官方产品。手册链接仅用于来源追溯，可能需要单独访问权限；安装和使用本 Skill 不要求登录该文档。不随仓库分发官方完整手册、示例成片、账号凭据或授权会话。

公开前检查：

- 当前文件、所有待公开分支/标签及提交历史中不含个人路径、邮箱、密钥或真实项目素材；删除最新文件中的内容不会清除 Git 历史。
- 提交作者/提交者身份使用维护者愿意公开的信息；必要时使用 GitHub 提供的隐私邮箱。
- `.gitignore` 只防止部分新文件被意外提交，不清除已跟踪内容或历史；发布前仍需检查差异。
- 真实使用案例须经授权和脱敏；优先贡献可复现的虚构案例，不提交真实肖像、音色、客户资料或带访问令牌的链接。
- 检查共享副本保留版权及许可声明；第三方手册、商标及素材不因本仓库公开而获得再分发授权。

## 许可证

本仓库原创 Skill、文档和模板采用 [MIT License](LICENSE)，版权署名为 `ai-freer`。再分发时请保留版权及许可声明。第三方手册、商标及引用素材不包含在本仓库的 MIT 授权范围内。
