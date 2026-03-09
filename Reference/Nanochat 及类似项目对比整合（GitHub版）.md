# Autoresearch / Nanochat 及类似项目对比整合（GitHub版）

更新时间：2026-03-09  
范围：在 GitHub 上挑选与“极简可改的训练脚本 + 指标驱动迭代 +（可选）代理自动化外循环”相近的项目进行对比整合。

---

## 1. 项目清单（本次纳入对比）

1. **karpathy/autoresearch**（核心对比对象：代理跑“外循环”自动实验）([github.com](https://github.com/karpathy/autoresearch))  
2. **karpathy/nanochat**（全栈极简：从训练到Chat UI，强调可hack与端到端复现）([github.com](https://github.com/karpathy/nanochat?utm_source=openai))  
3. **karpathy/nanoGPT**（经典极简训练脚本：`train.py`+`model.py`，项目本身已提示更推荐 nanochat）([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai))  
4. **KellerJordan/modded-nanogpt**（“speedrun”竞速：用明确目标指标驱动协作优化训练速度）([github.com](https://github.com/KellerJordan/modded-nanogpt?utm_source=openai))  
5. **miolini/autoresearch-macos**（autoresearch 的 macOS 适配分支/移植）([github.com](https://github.com/miolini/autoresearch-macos))  

---

## 2. 先给结论：这些项目的“共同模式”是什么？

它们在不同层面上都在做同一件事：

- **把研究/工程的“反馈回路”缩短**：更快试错、更可比较
- **将优化目标显式化为单一（或少量）关键指标**
- **刻意压缩复杂度**（依赖少、文件少、脚本直接可读可改）
- （在 autoresearch 中）进一步把“外循环”也自动化：改代码 → 训练 → 评估 → 保留/丢弃 → 重复

---

## 3. 核心对比表（重点维度）

| 维度 | karpathy/autoresearch | karpathy/nanochat | karpathy/nanoGPT | KellerJordan/modded-nanogpt | miolini/autoresearch-macos |
|---|---|---|---|---|---|
| 关注点 | **自动研究外循环**（agent）([github.com](https://github.com/karpathy/autoresearch)) | **全栈ChatGPT最小实现**（端到端训练/评估/推理/UI）([github.com](https://github.com/karpathy/nanochat?utm_source=openai)) | **训练/微调中型GPT最小代码**；并提示“更可能你需要 nanochat”([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai)) | **速度赛/记录**：最快达成目标loss([github.com](https://github.com/KellerJordan/modded-nanogpt?utm_source=openai)) | **平台适配**（macOS）([github.com](https://github.com/miolini/autoresearch-macos)) |
| “外循环”谁负责 | AI代理自动：修改→跑5分钟→看指标→保留/丢弃([github.com](https://github.com/karpathy/autoresearch)) | 人/脚本/流程（repo提供脚本，但不是“自我改代码”）([github.com](https://github.com/karpathy/nanochat?utm_source=openai)) | 人工实验（经典训练脚本+配置）([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai)) | 社区协作+脚本（记录/规则/提交）([github.com](https://github.com/KellerJordan/modded-nanogpt?utm_source=openai)) | 同 autoresearch 思路，但针对 macOS 环境([github.com](https://github.com/miolini/autoresearch-macos)) |
| “修改面”设计 | **三文件结构**；agent 主要改 `train.py`，人改 `program.md`([github.com](https://github.com/karpathy/autoresearch)) | 多目录多脚本，但强调“single clean minimal hackable codebase”([github.com](https://github.com/karpathy/nanochat?utm_source=openai)) | 主要 `train.py`/`model.py` 简洁可hack([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai)) | 主要训练脚本/运行脚本围绕 speedrun 目标不断优化([github.com](https://github.com/KellerJordan/modded-nanogpt?utm_source=openai)) | 基于 autoresearch 的文件结构/目标，但解决 macOS 运行问题([github.com](https://github.com/miolini/autoresearch-macos)) |
| 时间预算策略 | **固定5分钟 wall-clock**，提升可比性与迭代频率([github.com](https://github.com/karpathy/autoresearch)) | 提供 speedrun（数小时级）端到端体验；也强调 wall clock 汇总报告([github.com](https://github.com/karpathy/nanochat?utm_source=openai)) | 传统训练时长（例：复现 GPT-2 需要数天/更久），更偏“训练脚本基线”([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai)) | 以“分钟级”刷新记录（示例：目标在 8xH100 上几分钟达成）([github.com](https://github.com/KellerJordan/modded-nanogpt?utm_source=openai)) | 仍遵循 autoresearch 的短预算迭代思路([github.com](https://github.com/miolini/autoresearch-macos)) |
| 主指标 | `val_bpb`（bits per byte）([github.com](https://github.com/karpathy/autoresearch)) | README 展示多指标（含 CORE 等）与报告表格([github.com](https://github.com/karpathy/nanochat?utm_source=openai)) | 传统 loss/perplexity 等（README示例为val loss）([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai)) | 明确目标：FineWeb val loss ≤ 3.28（speedrun）([github.com](https://github.com/KellerJordan/modded-nanogpt?utm_source=openai)) | 同 autoresearch 指标/流程（以移植为主）([github.com](https://github.com/miolini/autoresearch-macos)) |

---

## 4. 各项目要点（带原文引用）

### 4.1 karpathy/autoresearch（“自动研究外循环”样板）
**原文关键句：**
- “give an AI agent a small but real LLM training setup and let it experiment autonomously overnight”([github.com](https://github.com/karpathy/autoresearch))  
- “trains for 5 minutes, checks if the result improved, keeps or discards, and repeats”([github.com](https://github.com/karpathy/autoresearch))  
- “three files that matter: `prepare.py` … `train.py` … `program.md`”([github.com](https://github.com/karpathy/autoresearch))  
- “metric is val_bpb … lower is better, and vocab-size-independent”([github.com](https://github.com/karpathy/autoresearch))  

**整合解读：**
- autoresearch 的创新点不在于“又一个训练框架”，而在于把研究流程变成**可编排的元流程**：你主要写 `program.md`，把“研究策略”交给 agent；agent 则只在受控范围内改 `train.py` 并自动产生可审查的差异与实验记录。([github.com](https://github.com/karpathy/autoresearch))  

---

### 4.2 karpathy/nanochat（“$100 级全栈 ChatGPT”）
**原文关键句：**
- “full-stack implementation of an LLM like ChatGPT in a single, clean, minimal, hackable, dependency-lite codebase”([github.com](https://github.com/karpathy/nanochat?utm_source=openai))  
- “includes tokenization, pretraining, finetuning, evaluation, inference, and web serving … so that you can talk to your own LLM just like ChatGPT”([github.com](https://github.com/karpathy/nanochat?utm_source=openai))  
- quick start 提到运行 `speedrun.sh` 端到端体验([github.com](https://github.com/karpathy/nanochat?utm_source=openai))  

**整合解读：**
- nanochat 更像“可复现的端到端产品化训练流水线最小实现”，autoresearch 则是“在其基础上，把改代码与试验选择也自动化”的外循环。  
- 如果你要写对外文档，建议把 nanochat 定位为“底座（训练/评估/推理全链路）”，autoresearch 定位为“在底座上跑自动研究”。([github.com](https://github.com/karpathy/autoresearch))  

---

### 4.3 karpathy/nanoGPT（经典极简训练脚本；README已提示“更推荐 nanochat”）
**原文关键句：**
- “Update Nov 2025 … nanoGPT has a new and improved cousin called nanochat. It is very likely you meant to use/find nanochat instead.”([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai))  
- “`train.py` is a ~300-line … training loop and `model.py` a ~300-line GPT model definition”([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai))  

**整合解读：**
- nanoGPT 更适合当“极简训练 loop 教材/模板”；nanochat 是“更现代、更全栈”；autoresearch 则是“自动化研究组织”。三者是一条演进链。([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai))  

---

### 4.4 KellerJordan/modded-nanogpt（“明确指标 + 速度赛”驱动的工程化��限优化）
**原文关键句：**
- “search for the fastest algorithm … train a language model that attains … validation set”([github.com](https://github.com/KellerJordan/modded-nanogpt?utm_source=openai))  
- 目标与规则（不改数据管线、达到指定 val loss、同硬件对比计时等）([github.com](https://github.com/KellerJordan/modded-nanogpt?utm_source=openai))  

**整合解读：**
- 这是“用竞赛机制把训练工程优化游戏化”的代表；和 autoresearch 的相似点是：**目标指标清晰、可比较性强、鼓励高频迭代**。  
- 主要差异：modded-nanogpt 的“外循环”是社区协作（PR/记录/规则），autoresearch 的“外循环”是 agent 自动跑。([github.com](https://github.com/KellerJordan/modded-nanogpt?utm_source=openai))  

---

### 4.5 miolini/autoresearch-macos（生态分支：平台兼容）
**定位：**
- autoresearch README 里提到“支持 CPU/MPS 会让代码膨胀，欢迎 forks”，而 macOS 分支正是这种生态延展的一种体现。([github.com](https://github.com/karpathy/autoresearch))  

---

## 5. 建议你在 naturaldao/EAP 的整合写法（可直接替换/新增章节）

你现有文档（`Reference/autoresearch简介.md`）已经覆盖了 autoresearch 与 nanochat 的大量信息（包含“三文件结构/5分钟预算/val_bpb”等）。建议补上下面三块，让“对比整合”更完整：

1. **演进链视角**  
   - nanoGPT（训练脚本极简）→ nanochat（全栈极简）→ autoresearch（外循环自动化）([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai))  

2. **“指标驱动”项目横向对比**  
   - autoresearch：`val_bpb`、固定5分钟  
   - modded-nanogpt：FineWeb val loss 目标 + 计时规则（speedrun）([github.com](https://github.com/karpathy/autoresearch))  

3. **生态与移植**  
   - 在“分支/社区”章节里列出 `miolini/autoresearch-macos`，强调平台适配路线。([github.com](https://github.com/miolini/autoresearch-macos))  

---

## 6. 引用（原文来源链接）

- karpathy/autoresearch ([github.com](https://github.com/karpathy/autoresearch))  
- karpathy/nanochat ([github.com](https://github.com/karpathy/nanochat?utm_source=openai))  
- karpathy/nanoGPT ([github.com](https://github.com/karpathy/nanoGPT?utm_source=openai))  
- KellerJordan/modded-nanogpt ([github.com](https://github.com/KellerJordan/modded-nanogpt?utm_source=openai))  
- miolini/autoresearch-macos ([github.com](https://github.com/miolini/autoresearch-macos))  

（你仓库内参考）naturaldao/EAP: `Reference/autoresearch简介.md`（commit 167cbc842a...，你提供的版本内容与 autoresearch README 相互印证）([github.com](https://github.com/karpathy/autoresearch))
