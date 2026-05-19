---
type: task
title: Orphan 页面 Back-link 修复
date: '2026-05-19T00:00:00.000Z'
status: diagnosed
tags:
  - backlink
  - brain-health
  - orphan
  - todo
---

## 当前状态

- `gbrain orphans --json`：213 个无 inbound link 页面。
- 测试添加 `resolver -> inbox/orphan-backlink-repair` 后，orphan 数从 213 降到 212，确认：**当前 orphan 判定读取的是 GBrain link table / markdown-extracted link graph；用 `gbrain__add_link` 可以直接修复 orphan 状态。**
- `gbrain__get_health` 显示 orphan_pages=154，与 CLI/MCP find_orphans 的 213 不一致，说明 health 可能有不同过滤口径；后续以 `gbrain orphans --json` / `gbrain__find_orphans` 为修复口径。

## 结构分布（213）

| Domain / Cluster | Count | 说明 |
|---|---:|---|
| `originals/` | 111 | Henry 的原始想法、决策、调试记录；大量只向外链项目/实体，但没有 hub 反向连回来 |
| `wiki/` | 76 | Dream Cycle 生成的 ideas / patterns / reflections；多数没有被主题 hub 或源 transcript 反向引用 |
| `people/` | 9 | 人物实体页，无 incoming link 或存在重复人名实体 |
| `concepts/` | 5 | GBrain skill / search / ingest pipeline 等概念页 |
| `preferences/` | 4 | 搜索工具、Dream policy 等偏好页 |
| `dream-cycle-summaries/` | 3 | 每日 Dream summary，没有 summary index |
| others | 5 | inbox / decision / project / resolver 等 |

## 主题聚类（粗分类）

- GBrain / Dream / Signal / OpenClaw / Token：约 94
- GWC / Training / Manufacturing：约 21
- Feishu / Lark / Getnote / Meeting ingest：约 17
- Search tools：约 14
- People：9
- Telegram / topic：3
- Wiki personal patterns/reflections：约 29
- Unclassified / cross-domain：约 25

## 根因判断

1. **Signal Capture / MCP put_page 写入时自动链接被跳过**  
   MCP `put_page` 返回里常见：`auto_links.skipped = remote`，所以新页面不会自动抽取和补 link。

2. **很多页面只有 outbound，没有 inbound**  
   例如 GWC 相关 original 会链接到 `projects/gwc-training-project`，但 project hub 没有回链这些 original，因此 original 仍然是 orphan。

3. **Dream Cycle 生成的 wiki 页面没有进入 hub index**  
   Dream 的 ideas/patterns/reflections 通常互相引用少，也没有被主题页统一收录。

4. **命名空间存在不一致风险**  
   例如部分 wiki 页面引用 `entities/gwc`，而 canonical pages 同时存在 `entities/companies/gwc-suzhou`、`entities/companies/gwc`、`companies/gustav-wolf-steel-rope-suzhou` 等；这会削弱实体聚合。

## 已验证的小测试

- 新增 link：`resolver --tracks--> inbox/orphan-backlink-repair`
- 结果：该任务页不再出现在 orphan 列表中。
- 结论：批量修复可以用 `gbrain__add_link`，不必先改 markdown 文件。

## 推荐修复策略

### Batch 1：确定性 hub-to-leaf 回链（低风险）

用已有 hub 页面回链明确归属的 leaf pages：

- `projects/gwc-training-project -> GWC/training 相关 originals/wiki/pages`
- `concepts/search-tool-selection -> search/perplexity/exa/tavily 相关 pages`
- `concepts/unified-meeting-audio-ingestion-pipeline` 或对应 ingest hub -> Feishu/Getnote/meeting ingest pages
- `concepts/gbrain-skill-signal-detector` / GBrain system hub -> Signal Capture / Dream / GBrain debug pages
- `people/henry-lee -> originals/*` 仅作为兜底，不建议第一批全量用，避免 Henry 变成超级垃圾 hub

### Batch 2：Dream wiki cleanup

- 为 `wiki/personal/patterns/*` 建/使用 pattern index
- 为 `wiki/personal/reflections/*` 建/使用 reflection index
- 为 `wiki/originals/ideas/*` 建/使用 idea index

### Batch 3：实体去重 / namespace 归一

- GWC：统一到 `entities/companies/gwc-suzhou` 或一个 canonical slug
- Helen / 严建琴：统一到 `people/yan-jianqin-helen` 或 `people/helen-yan-严建琴`
- Henry：统一到 `people/henry-lee`

## 注意

不要粗暴把所有 orphan 都挂到一个 `orphan-index`；那会让 graph 看起来健康，但语义质量变差。

[Source: User, Telegram, 2026-05-19]
