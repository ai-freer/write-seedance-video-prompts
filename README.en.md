[简体中文](README.md) | [English](README.en.md)

# Seedance 2.5 Video Prompt Skill

A workflow-routing skill for Seedance 2.5 video creation. Rather than using a fixed prompt template, it selects a workflow based on production cost, video length, operation type, character assets, and the level of control required, then compiles a ready-to-use generation package.

> Current status: v0.3.0 creative-direction and iteration update. Adds shot purpose, space and performance, reference responsibilities, selective test shots, and rough-cut feedback on top of the existing official capability rules. Third-party production lessons remain separate from official capabilities. Offline validation is not a test of actual video quality; paid generation is not automatic.

## What It Helps With

- Decide between a quick demo and a long-form or precision-production workflow.
- Select the appropriate protocol for generation, extension, editing, and seamless transitions.
- Choose a storyline, narrative beats, shot-by-shot planning, or a precise timeline based on risk.
- Anchor main-character identity with approved reference images while allowing scene-specific changes of look.
- Manage project assets: characters, outfits, locations, props, audio, and reference videos.
- Assemble only the relevant asset mappings, story, camera directions, sound, continuity rules, and negative constraints.
- Identify conflicting assets, input-limit violations, and rework risks before costly generation.

## Two Creative Paths

| Path | Best for | Default workflow |
| --- | --- | --- |
| Quick short video | Short demos, concept validation, low asset complexity | Minimal brief → essential assets → prompt → preflight |
| Long-form / precision production | Videos over 30 seconds, multiple scenes or looks, product showcases, or costly retries | Four approval gates: brief → assets → storyboard → final generation package |

For new projects, choose the path first, then confirm the project directory and asset records. Reuse answers the user has already provided. If no records exist, suggest the default templates; users may also choose to continue without saving project files yet.

## Core Design

### Multi-Axis Routing

For each task, decide independently on:

1. Creative workflow.
2. Operation type.
3. Generation strategy.
4. Planning granularity.
5. Control anchors.
6. Constraint modules.

Timelines, storyboard grids, blockout models, and creative references are not mutually exclusive modes on a single axis. Combine them as the task requires.

### Main-Character Asset Hierarchy

```text
Character visual specification
  → Approved canonical character reference assets
  → Scene-specific looks
  → Reference bundle for this generation
  → Seedance @图片/@视频/@音频 mappings
```

Text specifications guide the creation and review of character sheets; production videos use approved visual reference assets to anchor identity. Supporting characters default to descriptive prompts across relevant visual dimensions and become managed assets when consistency across shots matters.

## Usage

### From Model Controls to a Creative Feedback Loop

The new [creative-direction guide](references/creative-direction.md) translates audience experience into visible actions, shot reveals, and entry/exit states, with two original prompt walkthroughs. Precision projects may test representative risks first, then use actual viewing to choose between trimming, improving assets, revising shots, or regenerating—not default retries.

Use the skill independently or with a director workbench. An existing workbench remains the single source of truth, with no parallel YAML/TXT master. The workbench owns records, versions, media, and editorial relationships; this skill supplies generation settings, reference mappings, prompts, and creative preflight. No new workbench API is required.

Once a client that supports skills has loaded this skill, start with:

```text
Use $write-seedance-video-prompts to help me plan and write a Seedance 2.5 video prompt.
```

The skill first asks you to choose:

1. A quick short video.
2. Long-form / precision production.

It then asks context-dependent follow-up questions and delivers:

- Generation settings.
- Upload order and reference mappings.
- A copy-ready Seedance 2.5 prompt.
- Pre-generation risks and decisions still requiring confirmation.

This repository contains the skill source, with [`SKILL.md`](SKILL.md) as its entry point. It does not depend on a particular username, workspace, publishing tool, or signed-in account.

### Installation and Updates

1. Download or clone this repository and select the version you want to use.
2. Follow your agent client's current documentation to locate a supported user-level or project-level skills directory.
3. Create `write-seedance-video-prompts/` there and copy `SKILL.md`, `LICENSE`, `agents/`, `references/`, and `assets/` into it, preserving their relative structure and license notices. The README files and `docs/` are for reading and maintenance, not required at runtime.
4. Refresh the skill list or start a new session as your client requires, then try the example request above.

If your client supports installing skills from Git repositories, you can use its installer and select the repository root. Directory locations, discovery mechanisms, and symbolic-link support vary by client; do not assume one path works for every agent.

Before updating, preserve your custom changes, then replace this skill's runtime files with the selected version. Do not overwrite other skills or video-project assets. The version is recorded on a standalone `version:` line in the body of `SKILL.md`.

Optional: for multiple agents, choose a shared directory and reference it using each client's supported mechanism. No private publishing script outside this repository is required; shared-directory and link management are up to your environment.

### Keep Projects and Tools Separate

- Store project records, character assets, and generated outputs in a separate creative-project directory of your choice—not in the skill installation directory or back in this repository.
- Copy `assets/project-template/` before filling it in for a new project. Repository templates remain blank; example characters and asset IDs only illustrate the fields.
- Writing prompts alone requires no API key, Dreamina login, or generation tool. Actual image or video generation requires your own tools, accounts, permissions, and authorization for costs.
- Web, CLI, and API capabilities may differ. Check the actual interface before execution; do not assume every tool exposes every capability described in the manual.

## Repository Structure

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

## Documentation Guide

The Chinese README is linked at the top. The skill instructions and supporting documents are currently written in Chinese.

| Document | Purpose |
| --- | --- |
| [`SKILL.md`](SKILL.md) | Agent runtime entry point, required ordering, and conditional routing |
| [`docs/design/system-design.md`](docs/design/system-design.md) | Overall architecture, design rationale, state model, and roadmap |
| [`docs/creative-upgrade-validation.md`](docs/creative-upgrade-validation.md) | v0.3 scope, scenario review, and validation limits |
| [`docs/plans/2026-08-03-v0.1-implementation.md`](docs/plans/2026-08-03-v0.1-implementation.md) | v0.1 implementation and validation record |
| [`docs/plans/2026-09-06-v0.2-update.md`](docs/plans/2026-09-06-v0.2-update.md) | v0.2 manual coverage, update scope, and validation limits |
| [`references/workflow-router.md`](references/workflow-router.md) | Multi-axis routing and the two creative paths |
| [`references/project-assets.md`](references/project-assets.md) | Characters, looks, and project asset management |
| [`references/prompt-compiler.md`](references/prompt-compiler.md) | Prompt compilation for generation, extension, editing, and transitions |
| [`references/seedance-2.5-reference.md`](references/seedance-2.5-reference.md) | Quick reference for official Seedance 2.5 capabilities and limits |
| [`references/case-patterns.md`](references/case-patterns.md) | Selecting official example patterns by goal, with example conflicts and failure risks |
| [`references/creative-direction.md`](references/creative-direction.md) | Third-party inspirations, original walkthroughs, shot purpose, and iteration |
| [`references/image-asset-generation.md`](references/image-asset-generation.md) | Image-prompt adaptation for GPT, Gemini, Seedream, and Grok Image |

## Document Responsibilities and Sources of Truth

- The README is the repository entry point, not a duplicate of the full system rules.
- The system design explains why the system is structured this way.
- `SKILL.md` defines how the agent must operate.
- `references/` contains detailed rules for each module.
- `assets/project-template/` provides state and record templates for new video projects.

If documents conflict, runtime behavior follows `SKILL.md` and the references it directly cites. Changes to the overall design should first be recorded in the system design document.

## Current Limitations

- Seedance 2.5 is the only target video model.
- GPT, Gemini, Seedream, and Grok Image are used only for upstream image assets.
- The skill does not automatically execute paid image generation, video generation, or retries.
- Timelines provide strong guidance, not frame-accurate guarantees.
- The example index distills the manual's text; it is not a record of videos generated and tested by this repository. Image-model adaptations have not been functionally verified for every model.
- Manual revision 8839 was reviewed, but the previous full text was inaccessible. A difference in revision numbers is not a count of specific content changes.

## Next Steps

Validate video quality, asset reuse, and rework costs with one real quick-short project and one long-form project before deciding whether automation is worthwhile. For now, keep a single router, on-demand references, and lightweight YAML records rather than turning a growing example collection into a more complex workflow.

The Seedance 2.5 capability baseline comes from the [official usage manual](https://bytedance.larkoffice.com/wiki/RXh5ww6EqighMdkVTMccm2d4n7e).

## Sharing and Publication Boundaries

This is an independent community skill, not an official product of Dreamina or any model provider. Manual links are provided for source traceability and may require separate access permissions; installing and using this skill does not require signing in to that document. The repository does not distribute the complete official manual, example videos, account credentials, or authorization sessions.

Before publishing:

- Check current files, all branches and tags to be published, and commit history for personal paths, private email addresses, secrets, or real-project assets. Removing content from the latest files does not remove it from Git history.
- Use author and committer identities that maintainers are comfortable making public, including a GitHub-provided privacy email address where appropriate.
- `.gitignore` only helps prevent some new files from being accidentally committed. It does not remove tracked content or history; review changes before publishing.
- Real-world examples require permission and anonymization. Prefer reproducible fictional examples; do not submit real portraits, voice references, client materials, or links containing access tokens.
- Preserve copyright and license notices in shared copies. Making this repository public does not grant redistribution rights to third-party manuals, trademarks, or assets.

## License

The original skill, documentation, and templates in this repository are licensed under the [MIT License](LICENSE), with copyright attributed to `ai-freer`. Preserve the copyright and license notices when redistributing. Third-party manuals, trademarks, and referenced assets are not covered by this repository's MIT license.
