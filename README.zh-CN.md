# 📚 学术论文下载 Skill

[English README](./README.md)

这是一个实用的 AI Skill，用于**通过 Elsevier/Scopus 检索论文**，并根据 DOI/标题**自动下载全文**，流程如下：

1. 先用 Scopus 获取结构化元数据（DOI、标题、年份、来源、被引次数）。
2. 优先走 Unpaywall（合法 OA）下载。
3. OA 不可用时，可选使用 Sci-Hub CLI 作为 fallback。

> 适用于 AI 对话场景（如“下载一批”“下载最新论文”）以及自动化 Agent 流程。

## ✨ 功能特点

- 🔎 按关键词/标题/原始查询语句检索 Scopus
- 🧾 返回结构化条目（DOI/title/year/source/cited_by）
- ⬇️ 基于 DOI 自动下载
- 🟢 Unpaywall 优先（开放获取优先）
- 🛟 可选 Sci-Hub fallback
- 🧠 支持自然语言意图映射：
  - `few`（几篇/一些）
  - `batch`（一批）
  - `max`（尽可能多）
  - `latest`（最新/近几年）

## 🧩 仓库结构

- `SKILL.md` - Skill 行为与策略映射
- `agents/openai.yaml` - Agent 界面元信息
- `scripts/search_scopus.py` - Scopus 检索脚本
- `scripts/download_open_access.py` - DOI 下载脚本（Unpaywall + fallback）
- `scripts/topic_batch_download.py` - 主题检索 + 数量/最新策略一体化下载

## 🚀 快速开始

### 1）获取 API 访问能力

#### Elsevier / Scopus API Key

1. 注册 Elsevier 开发者账号：<https://dev.elsevier.com/>
2. 在账号中创建 API Key。
3. 确认该账号/API Key 具备 Scopus Search API 权限（通常依赖机构订阅授权）。

#### Unpaywall 邮箱

Unpaywall API 需要 email 参数，真实邮箱或虚拟邮箱均可。

### 2）配置环境变量

```bash
export ELSEVIER_API_KEY="你的_elsevier_api_key"
export UNPAYWALL_EMAIL="你的真实或虚拟邮箱@example.com"
```

### 3）通过脚本或 AI 对话使用

#### 方式 A：直接运行脚本

```bash
# 下载某方向“最新一批”论文
python3 scripts/topic_batch_download.py \
  --keywords "pedestrian simulation" \
  --quantity-mode batch \
  --latest \
  --outdir ./downloads
```

```bash
# 下载“最新 5 篇”
python3 scripts/topic_batch_download.py \
  --keywords "pedestrian simulation" \
  --latest \
  --target 5 \
  --outdir ./downloads_latest_5
```

#### 方式 B：与 AI 对话（示例）

- “帮我下载一批行人仿真论文”
- “帮我下载 5 篇建筑疏散仿真最新论文”
- “帮我尽可能多下载最新人群仿真论文”

Skill 会把这些词自动映射成可执行策略（`few`/`batch`/`max` + `latest` 年份过滤）。

## 🤖 自动化 Agent 流程

建议使用 JSON 输出接入流水线：

```bash
python3 scripts/topic_batch_download.py \
  --keywords "building pedestrian evacuation simulation" \
  --latest \
  --quantity-mode batch \
  --json --out ./summary.json \
  --outdir ./downloads
```

后续可解析 `summary.json` 获取下载路径、状态和 DOI 列表。

## 🧷 可选：安装 Sci-Hub fallback 工具

```bash
uv tool install git+https://github.com/Oxidane-bot/scihub-cli.git
```

`download_open_access.py` 的 fallback 解析顺序：

1. 自定义 `--scihub-cmd`
2. PATH 中本地 `scihub-cli`
3. `uvx --from git+https://github.com/Oxidane-bot/scihub-cli.git scihub-cli`

## 🔌 作为 Codex Skill 使用

将仓库安装到本地 skill 目录：

```bash
git clone https://github.com/wdc63/sci-papers-downloder.git ~/.codex/skills/sci-papers-downloder
```

然后重启 AI CLI/会话，使 skill 发现机制刷新。

## ⚖️ 合规与伦理说明

- 默认优先使用 Unpaywall 合法开放资源。
- 如启用 fallback，请确保符合本地法律、机构政策与出版商条款。
- 本仓库仅用于科研流程自动化。

## 📄 许可证

MIT，详见 [LICENSE](./LICENSE)。
