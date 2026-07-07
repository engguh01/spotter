# spotter — agent notes

## Project type
OpenCode skill, not a user app. `SKILL.md` is the skill definition consumed by OpenCode. `scoper.py` is the single script it invokes.

## Key files
- `scripts/scoper.py` — single entrypoint, stdlib-only (Python 3.7+). No deps, no `pip install`.
- `SKILL.md` — OpenCode skill definition consumed by the agent runtime.
- `README.md` — full docs and install instructions.

## Commands
```bash
python3 scripts/scoper.py --scope "ubah button di header"
python3 scripts/scoper.py --build-index           # force cache rebuild
python3 scripts/scoper.py --check                 # report cache freshness
python3 scripts/scoper.py --root /path            # default: .
python3 scripts/scoper.py --max 10                # default: 5
python3 scripts/scoper.py --no-symbols            # disable symbol matching
python3 scripts/scoper.py --no-git-boost          # disable git-hot boost
python3 scripts/scoper.py --no-session-memory     # disable session memory
```

## Caching
- Cache dir: `.scoper_cache/tree_index.json` (gitignored)
- Invalidation: git HEAD hash + dirty file count, or 5-min mtime fallback

## Architecture notes
- `.gitignore`-aware via `git ls-files` (tracked + untracked-but-not-ignored)
- Non-git fallback uses `os.walk` with hardcoded ignore list (node_modules, dist, venv, etc.)
- Fuzzy matching via stdlib `difflib` on filenames/paths — no import graph or dependency traversal
- Symbol matching: regex-based extraction of function/class/component names inside files (JS/TS/Python/Go etc.)
- Git-hot recency boost: staged/unstaged changes + last 5 commits get ranking nudge
- Session memory: logs prompt→candidates in session_log.json for multi-turn continuity
- Output: JSON with `{cache_status, candidates, total_files_indexed}`

## Distinctions
- SKILL.md goes into `.opencode/skills/spotter/` or `~/.config/opencode/skills/spotter/`
- scoper.py goes into a `scripts/` subdir alongside SKILL.md
