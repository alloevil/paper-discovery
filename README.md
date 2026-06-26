<div align="center">

# 📄 Paper Discovery

**每日自动发现 AI 领域最新论文，智能筛选后推送到飞书/邮件**

[![GitHub Actions](https://img.shields.io/github/actions/workflow/status/alloevil/paper-discovery/daily.yml?label=daily%20discovery&logo=github-actions&logoColor=white)](https://github.com/alloevil/paper-discovery/actions)
[![License](https://img.shields.io/github/license/alloevil/paper-discovery)](LICENSE)
[![Python](https://img.shields.io/badge/python-3.11+-blue?logo=python&logoColor=white)](https://www.python.org/)
[![Stars](https://img.shields.io/github/stars/alloevil/paper-discovery?style=social)](https://github.com/alloevil/paper-discovery/stargazers)

[快速开始](#-快速开始) · [工作原理](#-工作原理) · [配置说明](#-配置说明) · [自定义数据源](#-自定义数据源)

</div>

---

每天花 10 分钟刷论文？太累了。**Paper Discovery** 帮你自动完成：采集 → 筛选 → 推送，你只看精选结果。

## ✨ 为什么选 Paper Discovery

| 特性 | 说明 |
|:---|:---|
| 🔍 **三源采集** | arXiv API + HuggingFace Daily Papers + Papers With Code，覆盖面广 |
| 🤖 **AI 筛选** | LLM 语义过滤，只推高价值论文，不是简单关键词匹配 |
| 📬 **多渠道推送** | 飞书群消息 / 邮件订阅 / GitHub Pages，随你选 |
| 🏷️ **智能分类** | Agent、RAG、Knowledge Graph、LLM 等标签自动归类 |
| 📊 **代码优先** | 优先推送有开源代码的论文，方便复现 |
| 🆓 **零成本运行** | 基于 GitHub Actions，无需服务器，Fork 即用 |

## 📦 效果展示

<details>
<summary>📱 飞书推送效果（点击展开）</summary>

```
📄 论文日报 | 2026.06.26（周四）

1. AgentBench: Evaluating LLMs as Agents 📦代码 👍128
   提出统一的 Agent 能力评测框架，覆盖 8 种不同任务环境
   [论文] | [PDF] | [代码]

2. Self-RAG: Learning to Retrieve, Generate, and Critique 📦代码 👍95
   通过自我反思机制提升 RAG 质量，无需额外训练数据
   [论文] | [PDF] | [代码]

...

共 10 篇 | 由 Paper Discovery 自动推送
```

</details>

<details>
<summary>📧 邮件推送效果（点击展开）</summary>

邮件采用响应式 HTML 设计，支持暗色模式，包含论文标题、推荐理由和直达链接。

</details>

## 🚀 快速开始

### Step 1：Fork 仓库

点击右上角 **Fork** 按钮，将仓库复制到你的 GitHub 账号下。

### Step 2：配置 Secrets

进入 `Settings → Secrets and variables → Actions`，添加以下 Secrets：

| Secret | 必填 | 说明 |
|:---|:---:|:---|
| `LLM_API_KEY` | ✅ | LLM API Key（用于论文筛选和摘要生成） |
| `LLM_BASE_URL` | ⬚ | LLM API 地址（默认 `https://api.openai.com/v1`） |
| `FEISHU_WEBHOOK` | ⬚ | 飞书群机器人 Webhook 地址 |
| `SMTP_HOST` | ⬚ | 邮件服务器地址 |
| `SMTP_USER` | ⬚ | 邮箱账号 |
| `SMTP_PASS` | ⬚ | 邮箱密码/应用密码 |

> 💡 **最低配置**：只需 `LLM_API_KEY` 即可运行，论文会保存到数据库并生成 GitHub Pages 报告。

### Step 3：启用 Actions

进入 `Actions` 页面，点击 **"I understand my workflows, go ahead and enable them"**。

### Step 4：自定义关键词（可选）

编辑 `config.yaml`，修改你关注的研究方向：

```yaml
keywords:
  - "LLM agent"
  - "knowledge graph"
  - "RAG retrieval augmented generation"
  - "multi-agent system"
```

### Step 5：手动触发测试

进入 `Actions → Daily Paper Discovery → Run workflow`，点击运行按钮测试一次。

## 🔧 工作原理

```
┌─────────────────────────────────────────────────────────┐
│                    GitHub Actions                        │
│                  (每天北京时间 12:00)                      │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐              │
│  │  arXiv   │  │ Hugging  │  │ Papers   │   多源采集    │
│  │   API    │  │  Face    │  │ With Code│              │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘              │
│       │             │             │                     │
│       └──────┬──────┴──────┬──────┘                     │
│              ▼             ▼                            │
│         ┌────────┐   ┌──────────┐                      │
│         │  去重   │   │ AI 筛选  │   智能过滤            │
│         │        │   │ & 摘要   │                      │
│         └────┬───┘   └────┬─────┘                      │
│              └──────┬─────┘                             │
│                     ▼                                   │
│         ┌──────────────────┐                            │
│         │   SQLite 存储     │   持久化                   │
│         └────────┬─────────┘                            │
│                  ▼                                      │
│    ┌─────────────┼─────────────┐                        │
│    ▼             ▼             ▼                        │
│ ┌──────┐   ┌──────────┐   ┌────────┐                   │
│ │ 飞书  │   │   邮件    │   │ GitHub │   多渠道推送      │
│ │ Webhook│  │  订阅    │   │ Pages  │                   │
│ └──────┘   └──────────┘   └────────┘                   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 采集阶段

- **arXiv**：通过 API 搜索关键词 + 分类组合，获取最近 2 天的新论文
- **HuggingFace**：抓取 Daily Papers 热门列表，自带社区投票数
- **Papers With Code**：搜索有代码仓库的论文，按星标排序

### 筛选阶段

将候选论文列表发送给 LLM，按以下标准打分：
1. 与关键词的相关性
2. 创新性和实用价值
3. 是否有开源代码（优先）
4. 社区投票/星标数

LLM 不可用时，自动降级为按投票 + 星标 + 代码排序。

### 推送阶段

- **飞书**：通过 Webhook 发送 Interactive Card，格式美观
- **邮件**：响应式 HTML 邮件，支持暗色模式
- **GitHub Pages**：生成 Markdown 报告，可配置自定义域名

## 📋 配置说明

编辑 `config.yaml`：

```yaml
# ── 关键词和分类 ──────────────────────────
keywords:
  - "LLM agent"
  - "knowledge graph"
  - "RAG retrieval augmented generation"
  - "multi-agent system"
  - "tool use language model"
  - "agentic AI"

categories:        # arXiv 分类（与关键词组合搜索）
  - "cs.AI"        # Artificial Intelligence
  - "cs.CL"        # Computation and Language
  - "cs.IR"        # Information Retrieval
  - "cs.MA"        # Multiagent Systems

# ── 推送设置 ──────────────────────────────
max_papers: 10     # 每日推送数量上限
schedule_cron: "0 4 * * *"  # UTC 时间，北京时间 12:00
language: "zh"     # zh=中文摘要, en=英文摘要

# ── 数据源开关 ──────────────────────────────
sources:
  arxiv: true
  huggingface: true
  paperswithcode: true

# ── 推送渠道 ──────────────────────────────
notify:
  feishu: true     # 需配置 FEISHU_WEBHOOK Secret
  email: false     # 需配置 SMTP_* Secrets
```

## 📁 项目结构

```
paper-discovery/
├── scripts/
│   ├── sources/              # 数据源采集器
│   │   ├── arxiv_source.py   # arXiv API
│   │   ├── huggingface_source.py  # HuggingFace Daily Papers
│   │   └── paperswithcode_source.py  # Papers With Code
│   ├── filter.py             # AI 筛选和摘要生成
│   ├── notifier.py           # 推送（飞书卡片 / HTML 邮件）
│   ├── storage.py            # SQLite 存储 + 推送日志
│   └── main.py               # 主入口
├── config.yaml               # 配置文件
├── data/                     # 数据目录（自动创建）
├── docs/                     # GitHub Pages 报告
└── .github/workflows/
    └── daily.yml             # CI/CD 配置
```

## 🔌 自定义数据源

想添加新的论文数据源？只需实现一个采集函数：

```python
# scripts/sources/my_source.py

def fetch_my_source(keywords: list[str], days: int = 2) -> list[dict]:
    """从自定义数据源获取论文"""
    papers = []
    # ... 你的采集逻辑 ...

    for item in data:
        papers.append({
            "id": "唯一标识",
            "title": "论文标题",
            "abstract": "摘要",
            "authors": ["作者列表"],
            "url": "论文链接",
            "pdf_url": "PDF 链接",
            "published": "发表日期",
            "source": "my_source",
            "categories": [],
            "has_code": True,
            "code_url": "代码链接",
            "votes": 0,
        })

    return papers
```

然后在 `scripts/main.py` 中注册：

```python
from sources.my_source import fetch_my_source

# 在采集阶段添加
all_papers.extend(fetch_my_source(keywords, days=2))
```

欢迎提交 PR 添加新数据源！见 [CONTRIBUTING.md](CONTRIBUTING.md)。

## ❓ FAQ

<details>
<summary><b>Q: LLM API 费用大概多少？</b></summary>

每天一次调用，筛选 50 篇论文大约消耗 2000-4000 tokens，按 GPT-4o-mini 计算约 $0.001/天，基本免费。

</details>

<details>
<summary><b>Q: 支持哪些 LLM？</b></summary>

任何兼容 OpenAI API 格式的服务都可以：OpenAI、Azure OpenAI、DeepSeek、Moonshot、本地 Ollama 等。只需修改 `LLM_BASE_URL`。

</details>

<details>
<summary><b>Q: 飞书 Webhook 怎么获取？</b></summary>

飞书群 → 设置 → 群机器人 → 添加机器人 → 自定义机器人 → 复制 Webhook 地址。

</details>

<details>
<summary><b>Q: 可以修改推送时间吗？</b></summary>

编辑 `config.yaml` 的 `schedule_cron` 字段。格式为标准 cron 表达式（UTC 时间）。例如 `0 6 * * *` = 北京时间 14:00。

</details>

<details>
<summary><b>Q: 数据存在哪里？</b></summary>

SQLite 数据库在 `data/papers.db`，自动创建。推送日志也在同一个数据库中。

</details>

## 🤝 Contributing

欢迎贡献！请先阅读 [CONTRIBUTING.md](CONTRIBUTING.md)。

- 🐛 [报告 Bug](https://github.com/alloevil/paper-discovery/issues/new?template=bug_report.md)
- 💡 [提出建议](https://github.com/alloevil/paper-discovery/issues/new?template=feature_request.md)
- 🔧 [提交 PR](https://github.com/alloevil/paper-discovery/pulls)

## 📄 License

[MIT](LICENSE) © [alloevil](https://github.com/alloevil)

---

<div align="center">

**觉得有用？给个 ⭐ Star 支持一下！**

[![Star History Chart](https://api.star-history.com/svg?repos=alloevil/paper-discovery&type=Date)](https://star-history.com/#alloevil/paper-discovery&Date)

</div>
