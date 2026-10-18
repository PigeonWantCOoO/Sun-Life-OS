# Sun Life OS

## Language

[中文](README.md) · English (this document)

## Overview

Sun Life OS is a Codex Skill for private life records, memory review, and action feedback. It stores the user's original words, structured facts, and tentative inferences separately, then uses the user's goals to suggest traceable, testable actions. Code and the Skill live in `skills/sun-life-os`; personal records are stored separately from the Skill package.

This is a reflection and action support tool, not medical advice or diagnosis, and does not replace medical, mental health, or other professional services. Models can misunderstand records or produce incorrect explanations. Check original facts and applicable professional advice before important decisions.

## Capabilities

- Quick record: save known information immediately, with at most one necessary follow-up question.
- Full daily check-in: compact collection across sleep, physical state, diet, exercise, sexuality and desire, emotions, work and study, relationships, resources, and major events. You can skip any item.
- Focused advice: retrieve relevant history for the current question, separating facts, theoretical explanations, predictions, and memory updates.
- Weekly review and monthly review: assess results within a date range, disclose gaps in coverage, and propose one main task and a minimal experiment.
- Export: create an unencrypted ZIP snapshot after explaining its full scope.
- Deletion: resolve a natural-language request to exact IDs, preview related effects, and execute after user confirmation.

Two-layer memory preserves provenance for the factual layer (original words and structured events) and the inference layer (hypotheses and profiles). Skipped, unknown, and unanswered responses are distinct from an explicit negative answer and cannot be inference evidence. A single event does not automatically establish a habit, personality trait, or cause.

## Four-layer framework

| Layer | Purpose |
| --- | --- |
| Sun theory (孙学) | Use goals, results, options, and resource allocation to design small actions that produce real feedback. |
| Lean-muscle foundation (薄肌) | Observe whether sleep, training, diet, and recovery support sustainable action. |
| Huangmao agency (黄毛) | Express needs and boundaries, respect other people's choices, and turn ideas into attempts. |
| AI feedback | Store and retrieve evidence, compare outcomes, and review explanations and memory. |

These are project-specific analytical layers rather than medical categories. They do not prescribe a fixed body type, identity, or lifestyle. Physical state, emotions, and sexual behavior are discussed neutrally in relation to the user's goals and actual effects, without moral labels or treating behavior as inherently pathological.

## Privacy notice

The selected storage mode is **local plaintext, without encryption**. Accounts, programs, backups, or sync services with directory access may read it; exported ZIP files are also unencrypted. Records loaded for analysis enter the current model context, so local storage does not mean fully offline processing. How a model service handles those requests also depends on the product, settings, and policies in use.

Retrieve only the minimum necessary records for the current question. Work advice does not load unrelated sexual history by default. `exclude` prevents analysis use while retaining files; it is not deletion. Deletion cannot guarantee erasure of disk remnants, existing exports, or content already sent to a model. Read [PRIVACY.md](PRIVACY.md) first and [SECURITY.md](SECURITY.md) for security concerns. Do not put personal records in the code repository or public Issues.

## Requirements

- A Codex environment supporting local Skills and file operations.
- Python 3.10 or later, available as `python`. Runtime code uses only the Python standard library; no pip dependencies are needed.
- A writable private data directory and an understanding of local plaintext and model context boundaries.

The default timezone is `Asia/Shanghai`. Reminder times in the package configuration do not create running scheduled tasks. Actual scheduling is configured separately only when requested by the user.

## Installation

Manually copy the entire `skills/sun-life-os` directory, including scripts, references, and templates. Run the following PowerShell commands from the repository root. Inspect and back up an existing installation rather than overwriting it. These are manual installation instructions; bundled plugin metadata does not itself imply a marketplace listing.

Project installation (the path validated for this project): copy the directory to `.codex/skills/sun-life-os` inside the target project. Replace the example project location with your actual target.

```powershell
$projectRoot = '../my-project'
$skillParent = Join-Path $projectRoot '.codex/skills'
if (Test-Path (Join-Path $skillParent 'sun-life-os')) { throw 'Skill destination already exists' }
New-Item -ItemType Directory -Force -Path $skillParent | Out-Null
Copy-Item -LiteralPath 'skills/sun-life-os' -Destination $skillParent -Recurse
```

User installation: copy to your Codex skills directory. The common configuration is `$HOME/.codex/skills`; when `CODEX_HOME` is configured, use its `skills` subdirectory. This example uses the Windows user directory and stops if the Skill is already installed.

```powershell
$codexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
$skillParent = Join-Path $codexRoot 'skills'
if (Test-Path (Join-Path $skillParent 'sun-life-os')) { throw 'Skill destination already exists' }
New-Item -ItemType Directory -Force -Path $skillParent | Out-Null
Copy-Item -LiteralPath 'skills/sun-life-os' -Destination $skillParent -Recurse
```

Current official path alternative: the [OpenAI Skills documentation](https://learn.chatgpt.com/docs/build-skills) lists project `.agents/skills` and user `$HOME/.agents/skills`. If your client discovers Skills there, copy to `.agents/skills/sun-life-os` in the project or your user's `.agents/skills/sun-life-os`, preserving every file. Choose one applicable installation to avoid duplicate names. The `.codex` method above corresponds to this project's validation environment and is not a universal discovery rule for every client.

After installation, start a new conversation in the target workspace and explicitly request `sun-life-os`. If it does not appear, check that client's discovery paths and restart Codex. On other platforms, a file manager can perform the same complete directory copy.

## First initialization

Tell the Skill where to keep personal data during the first conversation. The Skill chooses an explicit user path first, then `SUN_LIFE_DATA_DIR`, then `personal-life-data` under the current workspace, resolving it to an absolute path. Data must stay outside the Skill package. The CLI itself always requires `--data-dir` and does not automatically read that environment variable.

The commands below run the bundled script from the repository root and put example data in a neighboring directory outside the repository. After installation, replace the script path with `scripts/memory_store.py` inside the installed Skill. Run `init` only when the store does not exist. If an existing directory has missing configuration or corruption, stop and investigate rather than reinitializing it. Run `validate` before operating on an existing store.

```sh
python -B -X utf8 skills/sun-life-os/scripts/memory_store.py --data-dir ../personal-life-data init
python -B -X utf8 skills/sun-life-os/scripts/memory_store.py --data-dir ../personal-life-data validate
```

Only report success after checking exit code 0, outer JSON `status: ok`, and the actual `result`. Do not load all history by default. Do not use the code repository root as the personal data directory.

## Usage examples

You can ask in conversation:

- “Use sun-life-os for a quick record: I finished the proposal draft today and want to test one assumption tomorrow.”
- “Run a full daily check-in. Ask yes, no, or skip for sensitive items; I can skip at any time.”
- “Use only the past week's work records to help me understand why this task keeps getting delayed.”
- “Write this week's review, disclose missing dates, and give me one minimal experiment for next week.”
- “Export my records; first explain the full scope and destination.”
- “Delete a particular record; show exact IDs and related effects, then wait for my confirmation.”

For commands requiring JSON input, the Skill creates a unique UTF-8 file in `.state/inbox` under the data directory; record text never goes in shell arguments. See [daily-schema.md](skills/sun-life-os/references/daily-schema.md) for field contracts. Reuse `operation_id` when retrying the same write, and remove the input immediately after success. Retain a failed input as pending only when explicitly choosing recovery from that original file; otherwise clean it up.

The `add` invocation shape is below. `<unique-id>.json` is a placeholder for a prepared input file and cannot be run literally. After checking the result, the Skill removes that exact input file.

```text
python -B -X utf8 skills/sun-life-os/scripts/memory_store.py --data-dir ../personal-life-data add --input "../personal-life-data/.state/inbox/<unique-id>.json"
```

Before export, resolve leftover inbox inputs individually: finish the original operation and clean up, or explicitly discard and clean up. If pending inputs are explicitly retained, disclose their paths, purpose, and plaintext inclusion in the ZIP. The export command below requires a destination that does not already exist. It exports the whole data directory, with no filtered export or import command.

```sh
python -B -X utf8 skills/sun-life-os/scripts/memory_store.py --data-dir ../personal-life-data export --output ../sun-life-export.zip
```

Exact deletion uses `delete-preview`, then `delete` with the original manifest and `confirmation_token`. A token does not replace user confirmation. To remove original words, select `record_id`; deleting only an event or profile does not remove the original text. Resolve related inbox remnants first and rebuild the preview when needed. If the manifest changes or the result is `confirmation_required`, show it and obtain confirmation again; outer `status: ok` alone is insufficient. See [privacy-boundary.md](skills/sun-life-os/references/privacy-boundary.md) for the full procedure.

## Data directory

`personal-life-data` is the default directory name. Its actual location follows the current workspace and selection rules above. Prefer a private directory outside the repository, and review your sync, backup, and access settings. `.gitignore` reduces common accidental commits; it does not enforce access control.

| Relative location | Contents |
| --- | --- |
| `config.json` | Timezone, collection preferences, and profile promotion thresholds. |
| `daily/YYYY/MM/YYYY-MM-DD.md` | Original user words with IDs. |
| `events/YYYY-MM.jsonl` | Structured facts with provenance. |
| `memory/hypotheses.jsonl`, `memory/profile.json` | Hypothesis action stream and current profiles. |
| `memory/exclusions.json` | Analysis exclusion rules. |
| `advice/`, `reviews/` | Daily advice reports, weekly reviews, and monthly reviews. |
| `.state/` | Inputs, writer lock, and operation audit; may contain private plaintext. |
| `exports/` | Export directory created during initialization; actual ZIP location is specified by `--output`. |

Crashes may leave plaintext atomic-write temporary files. `validate` reports relative paths. Confirm that the writer process has exited before manually handling an orphaned lock. Do not delete blindly based on lock age or wildcards. See the privacy document for details.

## Tests

Run from the repository root; all checks use fictional data. In Windows PowerShell, first set an inherited environment variable so Python child processes started by tests also avoid bytecode generation. `-B` additionally protects the current process:

```powershell
$env:PYTHONDONTWRITEBYTECODE = '1'
python -B -X utf8 -m unittest discover -s tests -v
python -B -X utf8 tests/e2e_workflow.py
python -B -X utf8 tools/validate_release.py
```

For a POSIX shell on other platforms:

```sh
export PYTHONDONTWRITEBYTECODE=1
python -B -X utf8 -m unittest discover -s tests -v
python -B -X utf8 tests/e2e_workflow.py
python -B -X utf8 tools/validate_release.py
```

Unit tests cover storage, memory, privacy, and package contracts. The end-to-end check covers recording, review, reports, export, and temporary directory cleanup. Release validation checks required files, metadata, and private-content patterns.

## Contributing

Read [CONTRIBUTING.md](CONTRIBUTING.md). Keep both languages aligned, use fictional fixtures, link sources for external factual claims, and keep real records out of Issues, logs, and examples.

## License

Code and bundled materials use the [MIT License](LICENSE). The license does not change privacy boundaries for personal records. See [CHANGELOG.md](CHANGELOG.md) for release history.
