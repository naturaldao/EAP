# Alignment Research Repository

本仓库是 EAP（Ethical Alignment Protocol）及其相邻研究材料的主研究仓。它不是成品软件仓，也不是已经冻结的正式规范仓。当前工作重点是把 `Reference/` 中的核心思想，推进为可讨论、可审计、可逐步实现的未来公共 AI / PAI 治理方案。

## 目录分工

- `Reference/`：核心锚点文本。相对稳定，用来约束术语和基本立场。
- `proposal/`：工作稿、整合稿、批注稿。这里允许更快迭代，但不能替代 `Reference/`。
- `docs/`：实现层文档。用于梳理治理栈、系统架构、过渡路线、失败模式、决策记录等。
- `.github/`：最小协作骨架，仅服务研究讨论与文本修订。

## 当前重点

1. 把 EAP 收紧为未来公共 AI / PAI 的治理协议，而不是聊天模型安全条款。
2. 明确仅有 EAP 还不够，未来社会还需要哪些制度层、系统层和过渡层。
3. 保持术语、文件分层、许可表述和版本边界可追溯。

## 建议阅读顺序

1. `Reference/伦理对齐性治理协议.md`
2. `Reference/爱的连接.md`
3. `Reference/爱语.md`
4. `proposal/EAP协议讨论.md`
5. `docs/` 下的实现层文档

## 相关仓库与外部资产

- `../SCC0-License`：SCC0 协议的独立研究仓。当前轮次只引用，不在此仓直接修改。
- `proposal/discussion-skill-seed/`：未来独立公开 discussion skill 的种子目录，用于沉淀章节映射、回答规则和主 agent / 定位 subagent 工作流。

## 许可状态

当前仓库内容以本仓库 `LICENSE` 为准。
`SCC0` 是相关协议与长期方向，但在当前轮次里，它还不是本仓库已经生效的许可证文本。任何许可证切换都必须在当前 EAP 回合稳定后，再与 `../SCC0-License` 仓同步处理。

## 协作

见 `CONTRIBUTING.md`。
