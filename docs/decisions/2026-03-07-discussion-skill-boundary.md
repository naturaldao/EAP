# ADR 2026-03-07：discussion skill 作为独立公开项目处理

## Status

Accepted

## Context

讨论类技能需要频繁读取仓库文本，但如果直接把所有材料塞进上下文，成本高且容易混淆不同稳定度的内容。

## Decision

- discussion skill 未来作为独立公开 GitHub 项目处理。
- 当前仓库只保留 `proposal/discussion-skill-seed/` 作为种子目录。
- 该 skill 采用“主 agent + 定位 subagent”架构：subagent 只做章节定位和最小证据包抽取，主 agent 负责最终回答。
- 回答时必须区分文本已有结论、从文本推出的推论、超出文本的扩展提案。

## Consequences

- 主仓保持研究资料与实现文档的清晰边界。
- 讨论流程更可控，且不会因为长文导致上下文污染。
- 外部公开 skill 可以独立迭代，而不绑死在主仓发布节奏上。
