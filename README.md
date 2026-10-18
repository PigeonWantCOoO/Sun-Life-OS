# Sun Life OS

## 语言

中文（本文） · [English](README.en.md)

## 项目简介

Sun Life OS 是一个用于私人生活记录、记忆复查和行动反馈的 Codex Skill。它将用户原话、结构化事实与待验证推断分开保存，再依据用户目标给出有来源、可核对的行动建议。代码和 Skill 位于 `skills/sun-life-os`；个人档案独立于 Skill 包保存。

这是个人反思与行动辅助工具，不是医疗诊断，也不替代专业医疗、心理或其他专业服务。模型可能误解记录或给出错误解释，重要决定应核对原始事实和适用的专业意见。

## 核心能力

- 快速记录：立即保存已知信息，最多提出一个必要补充问题。
- 完整日报：紧凑采集睡眠、身体、饮食、运动、性行为与欲望、情绪、工作学习、关系、资源和重大事件十域，允许跳过任何项目。
- 专项咨询：按当前问题检索必要历史，区分事实、理论解释、预测和记忆更新。
- 周报与月报：按日期复盘执行结果，标明记录覆盖缺口，提出一个主任务和最小实验。
- 导出：在说明完整范围后生成未加密 ZIP 快照。
- 删除：从语义请求定位精确 ID，预览关联影响，经用户确认后执行。

双层记忆保留事实层（原话与结构化事件）和推断层（假设与画像）的来源链。跳过、未知、未回答不等于明确回答“无”，不成为推断证据；单次事件不自动升级为习惯、人格或因果。

## 四层框架

| 层 | 用途 |
| --- | --- |
| 孙学（Sun theory） | 根据目标、结果、选择权和资源配置，设计能获得真实反馈的小行动。 |
| 薄肌（Lean-muscle foundation） | 观察睡眠、训练、饮食和恢复是否支持稳定行动。 |
| 黄毛（Huangmao agency） | 主动表达需求与边界，尊重他人选择，将想法变成尝试。 |
| AI 反馈（AI feedback） | 保存与检索证据，比较前后结果，复查解释与记忆。 |

这些是本项目特定的分析层，并非医学分类，也不要求用户追求固定体型、身份或生活方式。身体、情绪和性行为按用户目标与实际影响中性讨论，不作道德标签或默认病理化。

## 隐私提示

本项目选择的存储模式是**本地明文、未加密**。有权限访问目录的账户、程序、备份或同步服务可能读取它；导出 ZIP 也未加密。为分析加载的记录会进入当次模型上下文，因此本地存储不代表完全离线。模型服务如何处理这些请求还受所用产品、设置和政策约束。

只检索当前问题所需的最少记录；工作咨询默认不读取无关性行为历史。`exclude` 排除分析用途但保留文件，不能称为删除。删除不保证擦除磁盘残留、已有导出或已发送给模型的内容。请先阅读 [PRIVACY.md](PRIVACY.md)；安全问题见 [SECURITY.md](SECURITY.md)。不要将个人档案放进代码仓库或公共 Issue。

## 要求

- 支持本地 Skill 和文件操作的 Codex 环境。
- Python 3.10 或更新版本，命令 `python` 可用；运行时仅使用 Python 标准库，无需 pip 依赖。
- 可写的私有数据目录，以及对本地明文和模型上下文边界的理解。

默认时区为 `Asia/Shanghai`。包内的提醒时间配置不是已运行的定时任务；仅在用户请求时另行设置实际调度。

## 安装

手动复制完整 `skills/sun-life-os` 目录，包含脚本、参考资料和模板。下列 PowerShell 命令从本仓库根目录运行；不要覆盖已有同名安装，先检查并备份。这里提供手动安装方式，附带的插件元数据本身不代表市场上架。

项目级安装（本项目已验证的路径）：将整个目录复制到目标项目的 `.codex/skills/sun-life-os`。把示例中的目标项目替换为实际路径。

```powershell
$projectRoot = '../my-project'
$skillParent = Join-Path $projectRoot '.codex/skills'
if (Test-Path (Join-Path $skillParent 'sun-life-os')) { throw 'Skill destination already exists' }
New-Item -ItemType Directory -Force -Path $skillParent | Out-Null
Copy-Item -LiteralPath 'skills/sun-life-os' -Destination $skillParent -Recurse
```

用户级安装：复制到自己的 Codex skills 目录。常见配置为 `$HOME/.codex/skills`；若设置了 `CODEX_HOME`，使用其 `skills` 子目录。示例适用于 Windows 用户目录，已有安装时会停止。

```powershell
$codexRoot = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE '.codex' }
$skillParent = Join-Path $codexRoot 'skills'
if (Test-Path (Join-Path $skillParent 'sun-life-os')) { throw 'Skill destination already exists' }
New-Item -ItemType Directory -Force -Path $skillParent | Out-Null
Copy-Item -LiteralPath 'skills/sun-life-os' -Destination $skillParent -Recurse
```

当前官方路径的可选方式：[OpenAI Skills 文档](https://learn.chatgpt.com/docs/build-skills)列出项目 `.agents/skills` 和用户 `$HOME/.agents/skills`。若当前客户端按这些位置发现 Skill，可分别复制到 `.agents/skills/sun-life-os` 或用户的 `.agents/skills/sun-life-os`；保留目录内全部文件，只选择一种适用安装，避免同名重复。上述 `.codex` 方式对应本项目验证环境，不代表所有客户端的通用发现规则。

安装后在目标工作区开始新会话，明确要求使用 `sun-life-os`；若未出现，检查该客户端的发现路径并重启 Codex。其他平台可用文件管理器完成相同的完整目录复制。

## 首次初始化

首次对话先告诉 Skill 个人数据目录。Skill 按“用户明确提供的路径 → `SUN_LIFE_DATA_DIR` → 当前工作区的 `personal-life-data`”选取目录，并解析为绝对路径；数据必须位于 Skill 包之外。CLI 本身始终要求 `--data-dir`，不自动读取该环境变量。

下列命令在仓库根目录使用随包脚本，并把示例数据放在仓库外的相邻目录。已安装后，将脚本路径换成安装目录内的 `scripts/memory_store.py`。仅在 store 不存在时运行 `init`；已有目录缺配置或损坏时停止检查，不重新初始化覆盖。对已有 store 操作前运行 `validate`。

```sh
python -B -X utf8 skills/sun-life-os/scripts/memory_store.py --data-dir ../personal-life-data init
python -B -X utf8 skills/sun-life-os/scripts/memory_store.py --data-dir ../personal-life-data validate
```

确认命令退出码为 0、返回 JSON 外层 `status` 为 `ok`，并核对 `result` 后，才称操作成功。默认不批量读取历史。不要用本仓库根目录作为个人数据目录。

## 使用示例

在对话中可直接说：

- “用 sun-life-os 快速记录：今天完成了方案初稿，明天想先验证一个假设。”
- “做完整日报；敏感项先问有、无或跳过，我可以随时跳过。”
- “只看最近一周的工作记录，帮我分析为什么这个任务一直拖延。”
- “生成本周周报；注明缺失日期，给我一个下周最小实验。”
- “导出我的档案；先说明完整范围和目标路径。”
- “删除某次记录；先列出精确 ID 和关联影响，等我确认。”

需要 JSON 输入的命令由 Skill 在数据目录 `.state/inbox` 中创建唯一 UTF-8 文件；正文不放在 shell 参数里。字段契约见 [daily-schema.md](skills/sun-life-os/references/daily-schema.md)。同一写入重试沿用 `operation_id`；成功后立即清理输入。失败只在明确选择原文件恢复时保留并标记 pending，否则清理。

例如 `add` 的调用形状如下；`<unique-id>.json` 是已准备输入文件的占位符，不能原样运行。检查结果后由 Skill 删除该确切输入文件。

```text
python -B -X utf8 skills/sun-life-os/scripts/memory_store.py --data-dir ../personal-life-data add --input "../personal-life-data/.state/inbox/<unique-id>.json"
```

导出前逐项处理遗留 inbox 输入：完成原操作并清理，或明确放弃后清理；若明确保留 pending，必须披露路径、用途与将进入 ZIP 的明文范围。导出命令如下，目标不得已存在；导出完整数据目录，不支持筛选，也没有导入命令。

```sh
python -B -X utf8 skills/sun-life-os/scripts/memory_store.py --data-dir ../personal-life-data export --output ../sun-life-export.zip
```

精确删除使用 `delete-preview` 和随后携带原 manifest、`confirmation_token` 的 `delete`；token 不能代替用户确认。清除原话应选择 `record_id`，只删事件或画像不等于清除原始正文。关联 inbox 残留先处理并在必要时重建预览；清单变化或返回 `confirmation_required` 时重新展示并确认，不能只看外层 `status: ok`。详细流程见 [privacy-boundary.md](skills/sun-life-os/references/privacy-boundary.md)。

## 数据目录

`personal-life-data` 是默认目录名，实际位置由当前工作区和上述选择规则决定。建议选择仓库外的私有目录，注意自己的同步、备份和访问权限。`.gitignore` 只是防止常见误提交，并非访问控制。

| 相对位置 | 内容 |
| --- | --- |
| `config.json` | 时区、采集偏好和画像升级阈值。 |
| `daily/YYYY/MM/YYYY-MM-DD.md` | 带 ID 的用户原话。 |
| `events/YYYY-MM.jsonl` | 来源可追溯的结构化事实。 |
| `memory/hypotheses.jsonl`、`memory/profile.json` | 假设动作流与当前画像。 |
| `memory/exclusions.json` | 分析排除规则。 |
| `advice/`、`reviews/` | 日报建议、周报和月报。 |
| `.state/` | 输入、写锁和操作审计；可能含私密明文。 |
| `exports/` | 初始化创建的导出目录；实际 ZIP 位置由 `--output` 指定。 |

崩溃后可能留下明文原子写入临时文件；`validate` 会报告相对路径。先确认写入进程已退出，再人工处理遗留锁，不能按锁龄或通配符盲目删除。详情见隐私文档。

## 测试

在仓库根目录运行，全部仅使用虚构数据。Windows PowerShell 中先设置可继承的环境变量，让测试启动的 Python 子进程也不生成字节码；`-B` 同时保护当前进程：

```powershell
$env:PYTHONDONTWRITEBYTECODE = '1'
python -B -X utf8 -m unittest discover -s tests -v
python -B -X utf8 tests/e2e_workflow.py
python -B -X utf8 tools/validate_release.py
```

其他平台的 POSIX shell：

```sh
export PYTHONDONTWRITEBYTECODE=1
python -B -X utf8 -m unittest discover -s tests -v
python -B -X utf8 tests/e2e_workflow.py
python -B -X utf8 tools/validate_release.py
```

单元测试检查存储、记忆、隐私和包契约；端到端检查记录、复盘、报告、导出与临时目录清理；发布验证检查必要文件、元数据及私密内容模式。

## 贡献

请阅读 [CONTRIBUTING.md](CONTRIBUTING.md)。保持中英文信息一致，使用虚构测试资料，为外部事实提供来源链接，避免在 Issue、日志和样例中带入真实档案。

## 许可证

本项目代码及随包资料使用 [MIT License](LICENSE)。许可证不改变个人档案的隐私边界；发布版本记录见 [CHANGELOG.md](CHANGELOG.md)。
