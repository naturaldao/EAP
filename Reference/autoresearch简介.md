# 极简介绍

根据仓库的README和相关信息，这个项目名为“autoresearch”，是由Andrej Karpathy（@karpathy）创建的。它是一个自主研究系统，旨在让AI代理（基于LLM）在单个GPU上自动迭代训练小型语言模型（基于nanochat框架）。 具体来说，代理会反复修改训练代码、运行短时实验（约5分钟一次）、评估结果（如验证bits-per-byte指标，越低越好），并据此优化模型，从而实现“一夜之间”的自动化研究进展，而无需人工干预。项目强调自包含性，只修改一个文件（train.py），固定时间预算，确保在不同硬件上实验可比。

### 主要特点：

- **单GPU优化**：适合个人或小型设置（如NVIDIA H100），无分布式训练需求。
- **最小依赖**：主要依赖PyTorch和uv包管理器。
- **指导机制**：人类可以通过编辑program.md文件来引导AI代理的实验方向。
- **评估标准**：使用val_bpb作为核心指标，实验结果记录在results.tsv中（不提交到Git）。
- **平台兼容**：主版本针对Linux GPU，但有fork版本支持macOS和Windows。

### 使用步骤（简要）：

1. 安装uv：运行curl -LsSf https://astral.sh/uv/install.sh | sh。
2. 安装依赖：uv sync。
3. 准备数据和分词器（一次性）：uv run prepare.py。
4. 手动测试实验：uv run train.py。
5. 启动AI代理：用program.md的内容提示一个LLM（如“Hi have a look at program.md and let's kick off a new experiment!”），然后让它自动迭代。

# Autoresearch的简介

https://github.com/karpathy/autoresearch

<img src="https://github.com/karpathy/autoresearch/raw/master/progress.png" alt="Autoresearch" title="Autoresearch">

曾经有一段时间，前沿人工智能研究是由人类研究员在吃饭、睡觉、享受其他乐趣之余进行的，他们偶尔会通过“组会”这一使用声波互联的仪式来同步工作。那个时代早已一去不返。如今，研究工作完全由运行在空中的计算集群超级结构上的人工智能代理自主群体所主导。代理们声称代码库现在已经到了第10205代无论如何，没有人能判断这是否正确，因为现在的“代码”是一个自我修改的二进制文件，已经超越了人类理解的范围。这个Github仓库是一个关于这一切如何开始的故事。——@karpathy，2026年3月。

核心思想：给一个人工智能代理一个小型但真实的LLM训练环境，让它自主实验一整夜。它修改代码，训练5分钟，检查结果是否有所改善，保留或丢弃，然后重复。你早上醒来时会看到实验日志和一个（希望）更好的模型。这里的训练代码是一个简化版的单GPU实现版[Nanochat](https://github.com/karpathy/nanochat)。核心思想是，你不会像常规研究员那样触碰任何Python文件。相反，你正在编写program.md Markdown文件，这些文件为人工智能代理提供上下文，并帮助你建立自主研究组织。这个仓库中默认的program.md被有意保持为最简基线，尽管显而易见的是，随着时间的推移，人们会如何迭代它来找到能够实现最快研究进度的“研究组织代码”，以及如何向组合中添加更多代理等。关于这个项目的更多背景信息可以在这条推文中找到。

## 工作原理

该仓库被刻意保持精简，实际上只有三个重要的文件：

- **`prepare.py`** — 固定常量、一次性数据准备（下载训练数据、训练 BPE 分词器）和运行时工具（数据加载器、评估）。不修改。
- **`train.py`** — 代理修改的唯一文件。包含完整的 GPT 模型、优化器（Muon + AdamW）和训练循环。所有内容都可以修改：架构、超参数、优化器、批次大小等。**此文件由代理编辑和迭代**。
- **`program.md`** — 一个代理的基线指令。将你的代理指向这里然后让它运行。**此文件由人类编辑和迭代**。

根据设计，训练运行**固定的 5 分钟时间预算**（墙上时间，不包括启动/编译），无论你的计算细节如何。指标是 **val_bpb**（验证每字节位数）——越低越好，且与词汇大小无关，以便公平比较架构变化。

## 快速开始

**要求：** 单个 NVIDIA GPU（已在 H100 上测试）、Python 3.10+、[uv](https://docs.astral.sh/uv/)。

shell

```shell
# 1. 安装 uv 项目管理器（如果你还没有）
curl -LsSf https://astral.sh/uv/install.sh | sh

# 2. 安装依赖
uv sync

# 3. 下载数据并训练分词器（一次性，约 2 分钟）
uv run prepare.py

# 4. 手动运行单个训练实验（约 5 分钟）
uv run train.py
```

如果上述命令都能正常运行，你的设置就工作了，可以进入自主研究模式。

**平台支持**。此代码目前要求你拥有单个 NVIDIA GPU。原则上支持 CPU、MPS 和其他平台是完全可以的，但这也会使代码膨胀。我现在 100% 不确定是否要亲自承担这个。这个代码只是一个演示，我不知道以后会支持多少。人们可以参考（或让他们的代理参考）完整的/父 nanochat 仓库，它有更广泛的平台支持并展示了各种解决方案（例如 Flash Attention 3 内核回退实现、通用设备支持、自动检测等），欢迎为其他平台创建分支或讨论，我很乐意将它们链接到 README 的新显著分支部分等。

## 运行代理

只需在此仓库中启动你的 Claude/Codex 或任何你想要的（并禁用所有权限），然后你可以提示类似：

```
你好，看看 program.md，让我们开始一个新实验！先做设置。
```

`program.md` 文件本质上是一个超轻量级的"技能"。

## 项目结构

```
prepare.py      — 常量、数据准备 + 运行时工具（不修改）
train.py        — 模型、优化器、训练循环（代理修改此文件）
program.md      — 代理指令
pyproject.toml  — 依赖项
```

## 设计选择

- **单一修改文件。** 代理只接触 `train.py`。这使范围可控，使差异可审查。
- **固定时间预算。** 训练始终 exactly 运行 5 分钟，无论你的具体平台如何。这意味着你可以期望大约每小时 12 个实验，睡眠时大约 100 个实验。这个设计决定有两个好处。首先，无论代理改变什么（模型大小、批次大小、架构等），实验都可以直接比较。其次，这意味着自主研究将在该时间预算内为你的平台找到最优模型。缺点是你的运行（和结果）无法与在其他计算平台上运行的其他人的结果进行比较。
- **自包含。** 除 PyTorch 和几个小包外没有外部依赖。没有分布式训练，没有复杂的配置。一个 GPU，一个文件，一个指标。

## 分支

- [miolini/autoresearch-macos](https://github.com/miolini/autoresearch-macos)


# 以下是Nanochat的简介

nanochat 是用于训练 LLM（大语言模型）的最简单的实验性框架。它设计为在单个 GPU 节点上运行，代码极简且易于修改，涵盖了所有主要的 LLM 阶段，包括分词、预训练、微调、评估、推理以及聊天界面。例如，你只需花费约 48 美元（约 2 小时的 8XH100 GPU 节点时间）就可以训练一个具有 GPT-2 能力的 LLM（2019 年训练成本约 43,000 美元），然后在一个熟悉的类似 ChatGPT 的 Web UI 中与它对话。如果是竞价型实例，总成本可能低至约 15 美元。更一般地说，nanochat 开箱即配，只需设置一个单一的复杂度旋钮：`--depth`（即 GPT transformer 模型中的层数），即可训练整个“微型系列”的计算最优模型（GPT-2 的能力恰好大约在深度 26）。所有其他超参数（transformer 的宽度、头数、学习率调整、训练范围、权重衰减等）都会以最优方式自动计算。

关于代码库的问题，我建议使用来自 Devin/Cognition 的 [DeepWiki](https://deepwiki.com/karpathy/nanochat) 提问，或者使用 [Discussions 标签页](https://github.com/karpathy/nanochat/discussions)，也可以加入 Discord 上的 [#nanochat](https://discord.com/channels/1020383067459821711/1427295580895314031) 频道。

## 达成 GPT-2 时间排行榜

[](https://github.com/karpathy/nanochat#time-to-gpt-2-leaderboard)

目前，开发的主要重点是调整预训练阶段，这一阶段消耗的计算量最大。受 modded-nanogpt 代码库的启发，为了激励进步和社区协作，nanochat 维护着一个“GPT-2 速通”排行榜，即训练一个 nanochat 模型达到 GPT-2 级别能力所需的实际时间，由 DCLM CORE 分数衡量。[runs/speedrun.sh](https://github.com/karpathy/nanochat/blob/master/runs/speedrun.sh) 脚本始终反映了训练 GPT-2 级别模型并与其对话的参考方法。目前的排行榜如下：

|#|时间|val_bpb|CORE|描述|日期|Commit|贡献者|
|---|---|---|---|---|---|---|---|
|0|168 小时|-|0.2565|原始 OpenAI GPT-2 检查点|2019|-|OpenAI|
|1|3.04|0.74833|0.2585|d24 基线，略微过拟合|Jan 29 2026|348fbb3|@karpathy|
|2|2.91|0.74504|0.2578|d26 训练不足 **+fp8**|Feb 2 2026|a67eba3|@karpathy|
|3|2.76|0.74645|0.2602|将总批次大小增加到 1M tokens|Feb 5 2026|2c062aa|@karpathy|
|4|2.02|0.71854|0.2571|将数据集更改为 NVIDIA ClimbMix|Mar 4 2026|324e69c|@ddudek @karpathy|

我们关注的主要指标是“达成 GPT-2 的时间”——在 8XH100 GPU 节点上超越 GPT-2 (1.6B) CORE 指标所需的实际时间。GPT-2 的 CORE 分数是 0.256525。2019 年，GPT-2 的训练成本约为 43,000 美元，由于 7 年来全栈领域的许多进步，我们现在可以以快得多的速度、远低于 100 美元的成本做到这一点（例如，按目前的 ~$3/GPU/小时计算，一个 8XH100 节点约为 ~$24/小时，所以 2 小时约为 ~$48），这简直不可思议。

关于如何解读排行榜及如何贡献，请参阅 [dev/LEADERBOARD.md](https://github.com/karpathy/nanochat/blob/master/dev/LEADERBOARD.md) 获取更多文档。

## 入门指南

[](https://github.com/karpathy/nanochat#getting-started)

### 复现并与 GPT-2 对话

[](https://github.com/karpathy/nanochat#reproduce-and-talk-to-gpt-2)

最有趣的事情莫过于训练你自己的 GPT-2 并与它对话。执行此操作的整个流程包含在单个文件 [runs/speedrun.sh](https://github.com/karpathy/nanochat/blob/master/runs/speedrun.sh) 中，该文件设计为在 8XH100 GPU 节点上运行。在你喜欢的提供商那里启动一个新的 8XH100 GPU 机器（例如，我使用并喜欢 [Lambda](https://lambda.ai/service/gpu-cloud)），然后启动训练脚本：

```shell
bash runs/speedrun.sh
```

你可能希望在一个 screen 会话中执行此操作，因为运行大约需要 3 小时。完成后，你可以通过类似 ChatGPT 的 Web UI 与它对话。再次确保你的本地 uv 虚拟环境处于活动状态（运行 `source .venv/bin/activate`），然后启动服务：

```shell
python -m scripts.chat_web
```

然后访问显示的 URL。确保正确访问，例如在 Lambda 上使用你所在节点的公共 IP，后跟端口，例如 [http://209.20.xxx.xxx:8000/](http://209.20.xxx.xxx:8000/) 等。然后像平时使用 ChatGPT 一样与你的 LLM 对话！让它写故事或诗歌。问问它是谁，看看它如何产生幻觉。问问它天空为什么是蓝色的。或者为什么是绿色的。这个速通模型是一个 4e19 FLOPs 能力的模型，所以跟它聊天有点像在跟一个幼儿园小朋友说话 :).

---

[![image](https://private-user-images.githubusercontent.com/241138/500544377-ed39ddf8-2370-437a-bedc-0f39781e76b5.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NzMwNTM4NjEsIm5iZiI6MTc3MzA1MzU2MSwicGF0aCI6Ii8yNDExMzgvNTAwNTQ0Mzc3LWVkMzlkZGY4LTIzNzAtNDM3YS1iZWRjLTBmMzk3ODFlNzZiNS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwMzA5JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDMwOVQxMDUyNDFaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1jM2NjMmZhZjkwNDEyNDJjNGUyMDM4MGJlYTJlNTkxZWY2MGVhNWE5YmJmMTlhOWYyNDE3YzI2N2E5MjUwNTI0JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.UQ3VVKUs1a1Qs1Ca_WR1DckujV3t7eY7Am_Qz_4Ek5I)](https://private-user-images.githubusercontent.com/241138/500544377-ed39ddf8-2370-437a-bedc-0f39781e76b5.png?jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3NzMwNTM4NjEsIm5iZiI6MTc3MzA1MzU2MSwicGF0aCI6Ii8yNDExMzgvNTAwNTQ0Mzc3LWVkMzlkZGY4LTIzNzAtNDM3YS1iZWRjLTBmMzk3ODFlNzZiNS5wbmc_WC1BbXotQWxnb3JpdGhtPUFXUzQtSE1BQy1TSEEyNTYmWC1BbXotQ3JlZGVudGlhbD1BS0lBVkNPRFlMU0E1M1BRSzRaQSUyRjIwMjYwMzA5JTJGdXMtZWFzdC0xJTJGczMlMkZhd3M0X3JlcXVlc3QmWC1BbXotRGF0ZT0yMDI2MDMwOVQxMDUyNDFaJlgtQW16LUV4cGlyZXM9MzAwJlgtQW16LVNpZ25hdHVyZT1jM2NjMmZhZjkwNDEyNDJjNGUyMDM4MGJlYTJlNTkxZWY2MGVhNWE5YmJmMTlhOWYyNDE3YzI2N2E5MjUwNTI0JlgtQW16LVNpZ25lZEhlYWRlcnM9aG9zdCJ9.UQ3VVKUs1a1Qs1Ca_WR1DckujV3t7eY7Am_Qz_4Ek5I)

---

还有几点注意事项：

- 代码在 Ampere 8XA100 GPU 节点上也能正常运行，只是稍慢一些。
- 即使在单个 GPU 上，所有代码也能正常运行，只需省略 `torchrun`，并产生基本相同的结果（代码会自动切换到梯度累积），但你必须等待 8 倍的时间。
- 如果你的 GPU 显存小于 80GB，你需要调整一些超参数，否则会 OOM（显存不足）。在脚本中找到 `--device_batch_size` 并减小它直到适合为止。例如，从 32（默认值）减至 16、8、4、2，甚至 1。如果更低，你需要更清楚自己在做什么，并且要更有创造力。
- 大部分代码是相当原生的 PyTorch，所以它应该能在任何支持 PyTorch的平台上运行——xpu、mps 等，但我个人没有测试过所有这些代码路径，所以可能存在一些粗糙的边缘情况。

## 研究

[](https://github.com/karpathy/nanochat#research)

如果你是一名研究人员并希望帮助改进 nanochat，有两个值得关注的脚本：[runs/scaling_laws.sh](https://github.com/karpathy/nanochat/blob/master/runs/scaling_laws.sh) 和 [runs/miniseries.sh](https://github.com/karpathy/nanochat/blob/master/runs/miniseries.sh)。相关文档请参阅 [Jan 7 miniseries v1](https://github.com/karpathy/nanochat/discussions/420)。对于快速实验（约 5 分钟的预训练运行），我最喜欢的规模是训练一个 12 层模型（GPT-1 大小），例如像这样：

```
OMP_NUM_THREADS=1 torchrun --standalone --nproc_per_node=8 -m scripts.base_train -- \
    --depth=12 \
    --run="d12" \
    --model-tag="d12" \
    --core-metric-every=999999 \
    --sample-every=-1 \
    --save-every=-1 \
```

这会使用 wandb（运行名称“d12”），仅在最后一步运行 CORE 指标，并且不采样和保存中间检查点。我喜欢在代码中修改一些内容，重新运行 d12（或 d16 等），看看是否有帮助，如此循环迭代。要查看运行是否有帮助，我喜欢监控 wandb 图表中的：

1. `val_bpb`（以字节为单位的验证损失，与词表大小无关）作为 `step`、`total_training_time` 和 `total_training_flops` 的函数。
2. `core_metric`（DCLM CORE 分数）
3. VRAM 利用率、`train/mfu`（模型 FLOPS 利用率）、`train/tok_per_sec`（训练吞吐量）

请参阅此处的示例 [here](https://github.com/karpathy/nanochat/pull/498#issuecomment-3850720044)。

需要注意的是，nanochat 的编写和配置围绕着一个单一的复杂度旋钮——transformer 的深度。这个整数会自动确定所有其他超参数（transformer 的宽度、头数、学习率调整、训练范围、权重衰减等），以便训练出的模型是计算最优的。其理念是用户不必考虑或设置任何这些参数，他们只需使用 `--depth` 请求一个更小或更大的模型，一切就会“正常工作”。通过扫描深度，你可以获得各种尺寸的 nanochat 计算最优模型微型系列。GPT-2 能力模型（目前最令人感兴趣的）恰好处于当前代码的 d24-d26 范围内。但是，对代码库的任何候选更改都必须有足够的原则性，以便它们适用于所有深度设置。

## 在 CPU / MPS 上运行

[](https://github.com/karpathy/nanochat#running-on-cpu--mps)

脚本 [runs/runcpu.sh](https://github.com/karpathy/nanochat/blob/master/runs/runcpu.sh) 展示了在 CPU 或 Apple Silicon 上运行的一个非常简单的示例。它大幅缩小了正在训练的 LLM，以便将训练控制在几十分钟的合理时间范围内。你不会通过这种方式获得很强的结果。

## 精度 / dtype

[](https://github.com/karpathy/nanochat#precision--dtype)

nanochat 不使用 `torch.amp.autocast`。相反，精度通过单个全局 `COMPUTE_DTYPE`（在 `nanochat/common.py` 中定义）显式管理。默认情况下，这是根据你的硬件自动检测的：

|硬件|默认 dtype|原因|
|---|---|---|
|CUDA SM 80+ (A100, H100, ...)|`bfloat16`|原生 bf16 tensor cores|
|CUDA SM < 80 (V100, T4, ...)|`float32`|无 bf16；可通过 `NANOCHAT_DTYPE=float16` 使用 fp16（使用 GradScaler）|
|CPU / MPS|`float32`|无低精度 tensor cores|

你可以使用 `NANOCHAT_DTYPE` 环境变量覆盖默认值：

```shell
NANOCHAT_DTYPE=float32 python -m scripts.chat_cli -p "hello"   # 强制使用 fp32
NANOCHAT_DTYPE=bfloat16 torchrun --nproc_per_node=8 -m scripts.base_train  # 强制使用 bf16
```

工作原理：模型权重以 fp32 存储（为了优化器精度），但我们自定义的 `Linear` 层在前向传播期间将其转换为 `COMPUTE_DTYPE`。Embedding 直接以 `COMPUTE_DTYPE` 存储以节省内存。这为我们提供了与 autocast 相同的混合精度优势，但对以何种精度运行什么内容拥有完全的显式控制权。

注意：`float16` 训练会在 `base_train.py` 中自动启用 `GradScaler` 以防止梯度下溢。SFT 也支持这一点，但 RL 目前不支持。fp16 推理在任何地方都可以正常工作。

## 指南

[](https://github.com/karpathy/nanochat#guides)

我发布了一些指南，其中可能包含有用的信息，按时间从近到远排列：

- [2026 年 2 月 1 日：以远低于 100 美元的成本击败 GPT-2：nanochat 之旅](https://github.com/karpathy/nanochat/discussions/481)
- [1 月 7 日 miniseries v1](https://github.com/karpathy/nanochat/discussions/420) 记录了第一个 nanochat 模型微型系列。
- 要为 nanochat 添加新能力，请参阅 [指南：数出 strawberry 中的 r（以及通常如何添加能力）](https://github.com/karpathy/nanochat/discussions/164)。
- 要自定义你的 nanochat，请参阅讨论中的 [指南：为你的 nanochat 注入身份](https://github.com/karpathy/nanochat/discussions/139)，其中描述了如何通过合成数据生成调整 nanochat 的个性，并将该数据混合到 SFT 阶段。
- [2025 年 10 月 13 日：原始 nanochat 帖子](https://github.com/karpathy/nanochat/discussions/1) 介绍了 nanochat，但现在它包含一些已弃用的信息，并且模型比当前 master 分支旧得多（结果也较差）。

## 文件结构

[](https://github.com/karpathy/nanochat#file-structure)

```
.
├── LICENSE
├── README.md
├── dev
│   ├── gen_synthetic_data.py       # 用于身份的示例合成数据
│   ├── generate_logo.html
│   ├── nanochat.png
│   └── repackage_data_reference.py # 预训练数据分片生成
├── nanochat
│   ├── __init__.py                 # 空文件
│   ├── checkpoint_manager.py       # 保存/加载模型检查点
│   ├── common.py                   # 杂项小工具，提升生活质量
│   ├── core_eval.py                # 评估基础模型 CORE 分数 (DCLM 论文)
│   ├── dataloader.py               # 分词分布式数据加载器
│   ├── dataset.py                  # 预训练数据的下载/读取工具
│   ├── engine.py                   # 带有 KV Cache 的高效模型推理
│   ├── execution.py                # 允许 LLM 执行 Python 代码作为工具
│   ├── gpt.py                      # GPT nn.Module Transformer
│   ├── logo.svg
│   ├── loss_eval.py                # 评估每字节的位数（而非损失）
│   ├── optim.py                    # AdamW + Muon 优化器，单 GPU 和分布式
│   ├── report.py                   # 编写 nanochat 报告的工具
│   ├── tokenizer.py                # GPT-4 风格的 BPE Tokenizer 包装器
│   └── ui.html                     # nanochat 前端的 HTML/CSS/JS
├── pyproject.toml
├── runs
│   ├── miniseries.sh               # Miniseries 训练脚本
│   ├── runcpu.sh                   # 如何在 CPU/MPS 上运行的小示例
│   ├── scaling_laws.sh             # 缩放定律实验
│   └── speedrun.sh                 # 训练 ~$100 的 nanochat d20
├── scripts
│   ├── base_eval.py                # 基础模型：CORE 分数，每字节位数，样本
│   ├── base_train.py               # 基础模型：训练
│   ├── chat_cli.py                 # 聊天模型：通过 CLI 对话
│   ├── chat_eval.py                # 聊天模型：评估任务
│   ├── chat_rl.py                  # 聊天模型：强化学习
│   ├── chat_sft.py                 # 聊天模型：训练 SFT
│   ├── chat_web.py                 # 聊天模型：通过 WebUI 对话
│   ├── tok_eval.py                 # 分词器：评估压缩率
│   └── tok_train.py                # 分词器：训练
├── tasks
│   ├── arc.py                      # 多项选择科学问题
│   ├── common.py                   # TaskMixture | TaskSequence
│   ├── customjson.py               # 从任意 jsonl 对话生成任务
│   ├── gsm8k.py                    # 8K 小学数学问题
│   ├── humaneval.py                # 名称误导；简单的 Python 编码任务
│   ├── mmlu.py                     # 多项选择题，广泛的主题
│   ├── smoltalk.py                 # 来自 HF 的 SmolTalk 聚合数据集
│   └── spellingbee.py              # 教模型拼写/数字母的任务
├── tests
│   └── test_engine.py
└── uv.lock
```

## 贡献

[](https://github.com/karpathy/nanochat#contributing)

nanochat 的目标是改进微型模型的最新技术水平，使其可以在低于 1000 美元的预算下端到端地使用。可及性不仅关乎总体成本，还关乎认知复杂性——nanochat 不是一个配置繁琐的 LLM“框架”；代码库中没有巨大的配置对象、模型工厂或 if-then-else 怪物。它是一个单一、连贯、极简、可读、可修改、最大化可分支的“强基线”代码库，旨在从头到尾运行并生成一个你可以与之对话的 ChatGPT 模型。目前，个人最感兴趣的部分是缩短达成 GPT-2 的时间（即获得高于 0.256525 的 CORE 分数）。目前这大约需要 3 小时，但通过改进预训练阶段，我们可以进一步改善这一点。

当前 AI 政策：披露。提交 PR 时，请声明任何由 LLM 实质性编写且你未编写或未完全理解的部分。

## 致谢

[](https://github.com/karpathy/nanochat#acknowledgements)

- 名称源于我之前的项目 [nanoGPT](https://github.com/karpathy/nanoGPT)，该项目仅涵盖预训练。
- nanochat 也受到了 [modded-nanoGPT](https://github.com/KellerJordan/modded-nanogpt) 的启发，该项目通过明确的指标和排行榜将 nanoGPT 代码库游戏化，并借鉴了其许多想法和部分预训练实现。
- 感谢 [HuggingFace](https://huggingface.co/) 提供 fineweb 和 smoltalk。
- 感谢 [Lambda](https://lambda.ai/service/gpu-cloud) 提供用于开发此项目的计算资源。
- 感谢首席 LLM 催化师 🧙‍♂️ Alec Radford 提供的建议/指导。
- 感谢代码库掌管人 Sofie [@svlandeg](https://github.com/svlandeg) 协助管理 nanochat 的问题、拉取请求和讨论。

## 引用

[](https://github.com/karpathy/nanochat#cite)

如果你发现 nanochat 对你的研究有帮助，请简单引用为：

```bibtex
@misc{nanochat,
  author = {Andrej Karpathy},
  title = {nanochat: The best ChatGPT that \$100 can buy},
  year = {2025},
  publisher = {GitHub},
  url = {https://github.com/karpathy/nanochat}
}
```
