# Changelog

All notable changes to this project will be documented in this file.

## [0.1.0] - 2026-02-09

### Added
- Initial public release of `sci-papers-downloder` as an academic paper download Skill.
- Scopus search workflow (`scripts/search_scopus.py`) for DOI/title metadata retrieval.
- DOI download workflow (`scripts/download_open_access.py`) with Unpaywall-first strategy.
- Optional Sci-Hub fallback integration via `scihub-cli`.
- End-to-end quantity and freshness runner (`scripts/topic_batch_download.py`).
- Intent mapping policy for terms like "some", "batch", "as many as possible", and "latest".
- Bilingual documentation: English `README.md` and Chinese `README.zh-CN.md`.
- MIT license and public GitHub repository packaging.
