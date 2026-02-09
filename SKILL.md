---
name: sci-papers-downloder
description: Search papers from Scopus and then download PDFs by DOI using Unpaywall first, with optional scihub-cli fallback. Includes quantity-aware and latest-aware handling.
---

# sci papers downloder

## Overview

Use this pipeline:

1. Search Scopus to get metadata (title, DOI, year, source, cited-by).
2. Download by DOI via Unpaywall first.
3. If needed, fallback to `scihub-cli`.

## One-time setup (persistent env, no per-command params)

Add credentials to `~/.bashrc`:

```bash
# >>> sci papers downloder env >>>
export ELSEVIER_API_KEY="<your_elsevier_key>"
export UNPAYWALL_EMAIL="<your_unpaywall_email>"
# <<< sci papers downloder env <<<
```

For login/non-interactive shells (e.g. `bash -lc`), keep the same block in `~/.profile`.

Apply now:

```bash
source ~/.bashrc
source ~/.profile
```

Optional but recommended:

```bash
uv tool install git+https://github.com/Oxidane-bot/scihub-cli.git
```

## Intent mapping: quantity + freshness

This section is the **no-context deterministic policy** for other agents.

### Quantity mapping (Chinese wording)

- "几篇" / "一些" / "几篇就行" -> `--quantity-mode few` (target 5)
- "一批" / "批量" -> `--quantity-mode batch` (target 20)
- "尽可能多" / "越多越好" -> `--quantity-mode max` (high caps, bounded runtime)
- explicit number (e.g. "12 篇") -> `--target 12` (overrides quantity mode)
- if quantity is not mentioned -> default `--quantity-mode batch`

### Freshness mapping (latest papers)

- "最新" / "近几年" / "最近" -> add `--latest`
  - auto adds year filter: last 3 years by default
  - auto switches sort to `-coverDate`
- "最近 N 年" -> `--latest --years-back N`
- explicit lower year (e.g. "2023年以来") -> `--from-year 2023`

### Combination rules

- "最新一批" -> `--quantity-mode batch --latest`
- "最新一些" -> `--quantity-mode few --latest`
- "最新尽可能多" -> `--quantity-mode max --latest`
- explicit number + latest (e.g. "最新 8 篇") -> `--target 8 --latest`

### Priority rules (must follow)

1. explicit number (`--target`) > quantity keywords
2. explicit year (`--from-year`) > years-back
3. latest keyword implies date-first ranking (`-coverDate`)
4. if latest is requested and no year is given, use 3-year window

## Recommended command (end-to-end)

Use `scripts/topic_batch_download.py` for search + download in one step.

### Standard batch

```bash
python3 scripts/topic_batch_download.py --keywords "pedestrian simulation" --quantity-mode batch --outdir ./downloads
```

### Latest batch (recommended for "最新")

```bash
python3 scripts/topic_batch_download.py --keywords "pedestrian simulation" --quantity-mode batch --latest --outdir ./downloads
```

### Latest with explicit window

```bash
python3 scripts/topic_batch_download.py --keywords "pedestrian simulation" --quantity-mode batch --latest --years-back 2 --outdir ./downloads
python3 scripts/topic_batch_download.py --keywords "pedestrian simulation" --quantity-mode batch --from-year 2023 --outdir ./downloads
```

### Explicit count

```bash
python3 scripts/topic_batch_download.py --keywords "pedestrian simulation" --target 12 --latest --outdir ./downloads
```

## Alternative split workflow

### Search only

```bash
python3 scripts/search_scopus.py --keywords "pedestrian evacuation simulation" --count 20 --sort=-citedby-count
python3 scripts/search_scopus.py --query 'TITLE-ABS-KEY("pedestrian simulation") AND PUBYEAR > 2022' --count 20 --sort=-coverDate
```

### Download by DOI only

```bash
python3 scripts/download_open_access.py --doi "10.2307/2392994" --outdir ./downloads --scihub-fallback auto
python3 scripts/download_open_access.py --doi-file ./dois.txt --outdir ./downloads --scihub-fallback auto
```

## Fallback command resolution

`download_open_access.py` chooses fallback command in order:

1. `--scihub-cmd`
2. local `scihub-cli` in `PATH`
3. `uvx --from git+https://github.com/Oxidane-bot/scihub-cli.git scihub-cli`

## Output contract

Include:

- query + sort + year filter (`from_year`)
- total hits + scanned entries + candidate DOI count
- attempted DOI count + downloaded count
- per DOI status/method/path/error

## Resources

- `scripts/search_scopus.py`: Scopus query + metadata extraction
- `scripts/download_open_access.py`: Unpaywall + fallback downloader
- `scripts/topic_batch_download.py`: quantity-aware and latest-aware end-to-end runner
