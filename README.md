# claude-config

Personal [Claude Code](https://claude.com/claude-code) configuration and custom skills for [@mattoh-post](https://github.com/mattoh-post).

This repo is the source of truth for skills that run **autonomously as Claude Code cloud routines** — the routine clones this repo at fire time and follows the skill in `.claude/skills/`.

## Contents

```
.claude/skills/
└── daily-scrum-sync/        # Fills the Notion daily-scrum doc from Linear status
    ├── SKILL.md             # Workflow + config (target project, doc locations)
    └── references/
        ├── mapping.md       # Linear status → 진행 현황 buckets; WBS stage → Linear team
        └── templates.md     # Exact output format for the 진행 현황 / 어제 To do sections
```

## Skills

### `daily-scrum-sync`

Reads Linear issue status and writes it into the Notion daily-scrum document, then updates the project's WBS milestone status. Human-owned sections (`## 이슈`, `## 오늘 To do`) are never touched — the skill only fills `## 진행 현황` and `## 어제 To do`, and reconciles WBS stage rollups.

Default target is the **CoS Drive** project. To retarget, edit only the `## 설정 (Config)` block in `SKILL.md` (Linear project/team IDs, Notion project page, WBS data source, Meeting Notes DB).

Trigger phrases: `스크럼 채워줘`, `데일리 스크럼 정리`, `오늘 스크럼 문서 업데이트`, `리니어 진행현황 스크럼에 반영`, `스탠드업 문서`.

## Automation

`daily-scrum-sync` runs daily as a Claude Code cloud routine (claude.ai remote trigger):

| Field | Value |
|---|---|
| Routine | `Daily Scrum Sync — Project_Drive` |
| Schedule | `10 1 * * *` UTC → **10:10 KST daily** |
| Model | `claude-sonnet-4-6` |
| Connectors | Linear (`mcp.linear.app`), Notion (`mcp.notion.com`) |
| Source | this repo (`main`) |

The routine prompt instructs Claude to follow `daily-scrum-sync/SKILL.md` end to end and report what changed plus any items needing manual review.

**Note:** the skill operates entirely through the Linear and Notion MCP connectors, so the routine's allowed-tools / connector permissions must grant those MCP tools — local tools (Bash/Read/Write) alone are not enough for it to do real work.

### Verifying a run

A run "fires" (updates `last_fired_at`) independently of whether it produced output. To confirm it actually worked, check today's Notion scrum doc:

- `## 진행 현황 (Linear YYYY.MM.DD 기준)` is filled with 완료 / 이번주 마감 / 취소·정리 items
- `## 어제 To do` mirrors yesterday's `## 오늘 To do`

If those sections are empty after the scheduled time, the run fired but failed — most likely a connector/permission or doc-not-found issue (see `## 예외 처리` in `SKILL.md`).
