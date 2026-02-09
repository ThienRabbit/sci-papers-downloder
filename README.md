# 📚 Academic Paper Downloader Skill

[中文说明 (README.zh-CN.md)](./README.zh-CN.md)

A practical AI skill for **searching papers from Elsevier/Scopus** and then **downloading full text by DOI/title** with this strategy:

1. Use Scopus to get clean metadata (DOI, title, year, source, cited count).
2. Try download via Unpaywall first.
3. Optionally use Sci-Hub CLI as a fallback when Unpaywall is unavailable.

> Designed for conversational AI use ("download a batch", "download latest papers") and automated agent workflows.

## ✨ Features

- 🔎 Scopus search by keywords/title/raw query
- 🧾 Structured metadata output (DOI/title/year/source/cited_by)
- ⬇️ Auto download by DOI
- 🟢 Unpaywall first (open-access priority)
- 🛟 Optional Sci-Hub fallback
- 🧠 Intent mapping for natural language:
  - `few` / "some" / "a few"
  - `batch`
  - `as many as possible`
  - `latest` / recent-year mode

## 🧩 Repository Structure

- `SKILL.md` - skill behavior and policy mapping
- `agents/openai.yaml` - agent UI metadata
- `scripts/search_scopus.py` - Scopus search utility
- `scripts/download_open_access.py` - DOI downloader (Unpaywall + fallback)
- `scripts/topic_batch_download.py` - end-to-end topic search + quantity/latest-aware download

## 🚀 Quick Start

### 1) Get API access

#### Elsevier / Scopus API key

1. Create an account on Elsevier Developer Portal: <https://dev.elsevier.com/>
2. Create an API key in your profile.
3. Make sure your key/account has access to Scopus Search API (depends on institutional entitlement).

#### Unpaywall email

Unpaywall API requires an email parameter. A real or virtual email can be used.

### 2) Configure environment variables

```bash
export ELSEVIER_API_KEY="your_elsevier_api_key"
export UNPAYWALL_EMAIL="your_email_or_virtual_email@example.com"
```

### 3) Run via script or AI conversation

#### Option A: Direct script

```bash
# Download a latest batch in a topic
python3 scripts/topic_batch_download.py \
  --keywords "pedestrian simulation" \
  --quantity-mode batch \
  --latest \
  --outdir ./downloads
```

```bash
# Download exactly 5 latest papers
python3 scripts/topic_batch_download.py \
  --keywords "pedestrian simulation" \
  --latest \
  --target 5 \
  --outdir ./downloads_latest_5
```

#### Option B: Ask your AI agent (example prompts)

- "Download a batch of pedestrian simulation papers."
- "Download 5 latest papers in building evacuation simulation."
- "Download as many latest crowd simulation papers as possible."

The skill maps these words into concrete CLI behavior (`few`/`batch`/`max` + `latest` filters).

## 🤖 Automated Agent Workflow

Use JSON output for pipelines:

```bash
python3 scripts/topic_batch_download.py \
  --keywords "building pedestrian evacuation simulation" \
  --latest \
  --quantity-mode batch \
  --json --out ./summary.json \
  --outdir ./downloads
```

Then parse `summary.json` for downloaded paths, statuses, and DOI lists.

## 🧷 Optional: Install Sci-Hub CLI fallback

```bash
uv tool install git+https://github.com/Oxidane-bot/scihub-cli.git
```

`download_open_access.py` fallback resolution order:

1. custom `--scihub-cmd`
2. local `scihub-cli` in PATH
3. `uvx --from git+https://github.com/Oxidane-bot/scihub-cli.git scihub-cli`

## 🔌 Use as a Codex Skill

Install to your skill directory:

```bash
git clone https://github.com/wdc63/sci-papers-downloder.git ~/.codex/skills/sci-papers-downloder
```

Restart your AI CLI/session so skill discovery refreshes.

## ⚖️ Legal & Ethics

- Unpaywall is used as the primary legal OA source.
- If you enable fallback sources, ensure your usage complies with local laws, institutional policy, and publisher terms.
- This repository is provided for research workflow automation only.

## 📄 License

MIT - see [LICENSE](./LICENSE).
