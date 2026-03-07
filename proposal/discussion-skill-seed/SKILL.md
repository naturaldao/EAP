---
name: eap-discussion
description: Discuss the EAP / PoL research corpus by first locating the relevant chapter, then reading only the minimum necessary material, and then answering with clear separation between explicit text, inference, and extension proposals. Use when questions are about EAP, Reference texts, proposal drafts, future public AI governance, or version differences in this repository.
---

# EAP Discussion Skill

Treat this skill as a reading discipline, not as a style layer.

## Core Rule

Do not answer from memory if the question depends on repository text. Find the chapter first.

## Workflow

1. Classify the question: concept, protocol clause, implementation gap, transition path, version diff, or criticism.
2. Dispatch a locating subagent when available. The subagent only finds files, headings, and the smallest useful evidence package. It does not write the final answer.
3. Read the minimum set of relevant sections.
4. Answer in four blocks:
   - Source-backed reading
   - Inference from text
   - Extension proposal
   - Open uncertainty or missing anchor
5. If the material is insufficient, say so plainly.

## Source Precedence

1. Current `Reference/`
2. Current integrated draft `proposal/EAP协议讨论.md`
3. Current `docs/`
4. Older letters and review drafts in `proposal/`

If sources conflict, report the conflict. Do not quietly harmonize them.

## Hard Rules

- Do not quote the integrated draft as if it were the stable anchor text.
- Do not treat every criticism, refusal, or boundary expression as hate speech.
- Do not import outside theory unless the user asks for it. If you do, mark it as external.
- Keep context small. Prefer headings and short extracts over full files.
- When answering AI-boundary questions, distinguish what PAI may do directly, may assist with, and may not impersonate.
