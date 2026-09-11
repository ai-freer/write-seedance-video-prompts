# 项目资产与状态管理

资产管理的目标不是收集尽可能多的图片，而是为每次生成提供少量、明确、已批准且互不冲突的引用素材。

## 默认项目结构

从 `assets/project-template/` 创建项目档案后，建议采用：

```text
project-root/
├── project.yaml
├── asset-registry.yaml
├── brief.md
├── assets/
│   ├── characters/
│   ├── looks/
│   ├── locations/
│   ├── props/
│   ├── audio/
│   └── source-videos/
├── storyboards/
├── prompts/
└── outputs/
```

若用户已有目录，优先适配现有结构，不强制搬迁。只要能明确定位项目状态、资产登记、Brief、分镜、Prompt 和输出即可。

项目目录由使用者选择，与 Skill 安装目录及源码仓库分开。只复制空白模板到项目中填写，不把真实资产、个人路径、审批人信息或账号数据回写到共享 Skill。新档案优先使用相对项目根目录的路径；分享项目副本前检查身份资料、素材许可和私有链接，不能把“批准用于生成”理解为“批准公开发布”。

## 状态来源

- `project.yaml`：项目级工作流与当前决策；
- `asset-registry.yaml`：资产 ID、类型、版本、状态、关联角色/Look、来源和使用限制；
- `brief.md`：人可读创作意图、确认项和假设；
- 分镜与 Prompt 文件：每次生成的具体编排和可追溯输入。

不要把聊天记录当作唯一状态来源。用户确认后，把稳定决策写回项目档案。

模板是可选字段的轻量档案，不是要求填写的数据库。保持 `schema_version: 1`，本轮只增添兼容字段；旧项目缺少这些字段时按任务补齐，不重建目录、不删除未知字段。图片模型选择记录在 `image_assets.target_model`，不混入视频目标模型。

## 主要人物资产层级

主要人物按以下层级管理：

1. **角色视觉规格（Character Visual Specification）**
   文字版源定义，用于生成和验收图片资产。包括年龄/族裔、面部结构、肤质、眼神、发型、体态、标志特征和不可漂移项。
2. **标准角色参考资产（Approved Canonical Character Reference Assets）**
   按路径准备并确认的近景、全身及必要视图；长/精细项目可建立完整多视图包。这一层才是视频一致性的主要视觉依据。
3. **场景 Look 资产**
   从同一标准角色派生的服装、妆发、配饰、状态和场景适配版本。身份不变，Look 可变。
4. **本次生成引用包（Per-generation Reference Bundle）**
   为某一个视频段选择的最小素材集合，并映射到 `@图片N`、`@视频N`、`@音频N`。

“身份核”只能作为图片资产生产和验收规则，不能替代视频中的已批准参考图。

## 主要人物资产包

### 快速短片最低包

- 一张身份清晰的近景或四分之三视角图；
- 一张当前 Look 的独立全身图；
- 镜头确实需要时，再增加背面、侧面、手部或特定表情/姿势参考。

### 长视频 / 精细制作标准包

- 多视图角色设定图；
- 独立近景；
- 独立全身；
- 分镜要求的独立侧面、背面、手部、表情或动作角度；
- 每个重要 Look 至少一张独立全身图，重要近景 Look 再补近景。

多视图设定图适合建立整体认知，但不应是唯一输入。关键镜头优先引用清晰的独立视图，避免模型从拥挤拼图中误取服装或角度。

制图需求从粗略故事/镜头中来，后续逐镜头完善，不预先批量生成不会使用的角度。多人任务按本次生成包的主体数选择视图，具体建议见 `seedance-2.5-reference.md`；不把整个项目角色库都上传。仅修改源视频声音、背景等且保留原角色时，可将用户确认的源视频登记为身份依据，不重做图包。

## Look 与服装变化

- 每个 Look 都关联到一个 `character_id`，并拥有独立 `look_id`；
- 记录服装、鞋、配饰、妆发、脏污/湿润/受伤等状态；
- 明确 Look 适用的场景或镜头范围；
- 同一镜头不要同时引用互相冲突的两个 Look；
- 服装在镜头间变化时，分镜中写明变化发生在何处，不能让模型自行猜测；
- 若变化本身是画面事件，给出起始 Look、目标 Look 和变化触发方式。

## 次要人物

默认使用文字描述，不强制生成参考图。至少选择真正有辨识度的维度：

- 年龄段与族裔/地域特征；
- 肤色、肤质与面部结构；
- 眼睛、眼神和情绪；
- 发型、发色和整洁程度；
- 服装廓形、材质、颜色和使用痕迹；
- 体态、动作习惯和整体气质。

出现以下任一情况，建议升级为受管资产：

- 跨多个镜头或视频段重复出现；
- 承担特写、中近景或对白；
- 与主角发生肢体接触、交换物品等复杂交互；
- 服装或身份误差会破坏叙事；
- 后续需要复用或客户审批。

## 资产状态

建议使用以下状态：

- `planned`：已识别但尚未制作；
- `candidate`：已有候选，尚未确认；
- `approved`：可作为标准或正式输入；
- `superseded`：被新版本替代，保留追溯；
- `blocked`：存在版权、质量、身份冲突或文件缺失等问题。

只有 `approved` 资产可默认进入正式生成引用包。快速 demo 可在用户知情后使用 `candidate`，并在交付中标注风险。

单次 demo 使用许可属于引用包，不把资产状态改为 `approved`。只有候选、尚无使用许可时先交付草稿并询问；不将“用户允许快速制作”推断为所有候选都已批准。

## 最小登记字段

- `assets[]` 管实际文件：`asset_id`、`media_type`（image/video/audio）、`file`（相对项目根目录或现有绝对路径）、`version`、`status`、`source`。`planned` 可无文件；候选/批准素材应可定位真实文件。音视频用于限额/选段时补 `duration_seconds`。
- 按需增加 `character_id`、`look_id`、`derived_from`（来源资产 ID 列表）、`usage_limits`、`approval`（确认人/记录依据，时间已知才写）。不猜来源、许可或批准时间。
- `characters[]` 管逻辑人物：`character_id`、`name`、`visual_spec`（简述或文件路径）、`canonical_asset_ids`；需要固定音色再加 `voice_asset_id`。
- `looks[]` 管场景造型：`look_id`、`character_id`、`description`、`asset_ids`。场景/产品/道具/音频/源视频/分镜集合可按需用 `id`、`description`、`asset_ids` 组织；实体文件只在 `assets[]` 登记一份。
- `generation_bundles[]` 管本次选择：`bundle_id`、`target_clip_id`、`purpose`（demo/production）、`status`（draft/ready/needs_review）、`references`。每个引用至少有 `asset_id`、`mention`、`use`；按需补 `exclude`、`source_range_seconds`、`target_range_seconds`。

旧档案若已有内联文件记录，先沿用其已有规则，确需统一登记时逐条保留来源并迁入，不能静默把旧 ID 改掉。状态 `ready` 仅表示生成包已预检，不代表付费执行已授权。

## 本次生成引用包

每个生成包记录：

- `bundle_id` 和目标镜头/片段；
- 选用的角色标准资产和 Look 资产；
- 场景、产品、道具、音频和参考视频；
- 上传顺序与 `@` 映射；
- 每个素材“参考什么 / 不参考什么”；
- 只有局部参考时，记录源素材选段与目标片段生效范围，二者不能互相代替；
- 资产冲突检查结果。

以下是字段联动示例，不是真实已批准素材；实际使用必须替换文件、审批依据和引用范围。仅示范字段，不要求每个包都填写所有字段：

```yaml
assets:
  - asset_id: lin-face-v1
    media_type: image
    file: assets/characters/lin-face-v1.png
    version: 1
    status: approved
    source: "示例：用户提供的身份母版"
    character_id: lin
    approval: {by: user, note: "示例：用户确认身份"}
  - asset_id: lin-rain-v1
    media_type: image
    file: assets/looks/lin-rain-v1.png
    version: 1
    status: candidate
    source: "示例：基于身份母版生成的全身Look"
    character_id: lin
    look_id: lin-rain
    derived_from: [lin-face-v1]
  - asset_id: run-v1
    media_type: video
    file: assets/source-videos/run-v1.mp4
    version: 1
    status: approved
    source: "示例：用户提供并确认可用的动作参考"
    duration_seconds: 8
    approval: {by: user, note: "示例：确认仅用于动作参考"}
characters:
  - character_id: lin
    name: 林
    visual_spec: "示例：稳定面部与体型规格"
    canonical_asset_ids: [lin-face-v1]
looks:
  - look_id: lin-rain
    character_id: lin
    description: "黄色雨衣、深色长裤与鞋；用于雨夜场景"
    asset_ids: [lin-rain-v1]
generation_bundles:
  - bundle_id: gen-sc03-v1
    target_clip_id: sc03
    purpose: demo
    status: draft
    candidate_use_permission:
      asset_ids: [lin-rain-v1]
      by: user
      note: "示例：已知尚未批准Look，只允许这次方向性demo"
    references:
      - asset_id: lin-face-v1
        mention: "@图片1"
        use: "面部身份与发型"
      - asset_id: lin-rain-v1
        mention: "@图片2"
        use: "本场雨衣Look"
      - asset_id: run-v1
        mention: "@视频1"
        use: "奔跑节奏"
        exclude: "人物、服装、场景及原视频机位"
        source_range_seconds: [2, 6]
        target_range_seconds: [4, 8]
```

`source_range_seconds` 相对**实际上传文件**计时；若将原文件裁切成新文件，登记派生关系并重置选段时间。`target_range_seconds` 相对 `target_clip_id` 的本次生成段；续写时相对新增段 0 秒。无区间意味着参考整份素材/作用于整个目标段，不必填空区间。多镜头跨段引用建立各段自己的最小包，不建立通用引用图谱。

## 片段状态与确认回退

`video.duration_seconds` 表示项目目标总时长。需要分段、续写或关联 Look 时才填写 `video.clips[]`，例如：

```yaml
clips:
  - clip_id: sc03
    operation: extension
    source_asset_id: source-sc02-v1
    source_duration_seconds: 20
    extension_seconds: 10
    duration_seconds: 30  # 该次返回的完整视频；20 + 10，不是项目总时长
    time_basis: added_clip
    connection: cut
    look_assignments: {lin: lin-rain}
    entry_state: "原片末尾林关上门；新增段以关门声连接雨夜街道"
    exit_state: "林停在站牌下，左手仍持纸袋，雨声持续"
    bundle_id: gen-sc03-v1
    prompt_file: prompts/sc03-v1.md
    status: draft
```

此段源资产 ID 也需登记后才可执行。普通生成的片段时长就是本次生成时长，`time_basis: clip`；无需填写源片/新增字段。多个续写产物包含重复源片，计算项目成片时不能把它们的完整时长简单相加。

`workflow.gate_decisions[]` 按需记 `gate`、`decision`、`scope`、`by`、`note`（如涉及资产版本列入 scope）。最终状态以 `workflow.gates` 为准，决策列表保留历史。只在决策/资产变化时追加，不记录每句闲聊。

变更时做最小影响检查：换 Look → 相关资产/镜头/终包待确认；改局部对白 → 相关台词时序、音色映射和终包待确认；改核心 Brief → 复核受影响的方案。受影响 bundle 标 `needs_review`，相应 Gate 回 `pending`；旧资产仍保留，不自动重新生成、不把无关 Gate 全部清空。没有相关状态的快速任务无需补建整套历史系统。

## 阻断条件

遇到以下情况，不直接交付正式生成包：

- 正式新生成需要锁定主角色，却没有足够可识别的批准视觉资产；只保留源片人物的编辑可用已确认源片，候选 demo 按前述单次许可处理；
- 角色标准资产之间脸型、发型或体态明显冲突；
- 同一镜头的 Look 互相矛盾，且并非已明确的换装事件；
- 文件存在但来源、版本或对应人物不明；
- 上传映射中的素材编号重复或 Prompt 引用了不存在的素材；
- 用户要求保留的产品/人物特征在现有素材中不可见。

给出最小补救方案，例如补一张独立全身图、确认一个 Look、重命名冲突素材或拆分镜头，而不是把所有资产推倒重来。

超出稳定性建议、人物交互复杂等只作风险提示和方案选择，不与文件缺失/硬性超限等阻断条件混淆。无法交正式包时仍可给注明缺口的 Prompt 草稿。
