# 贡献指南 / Contributing

欢迎提交清晰的问题说明和小范围修正。先说明触发条件、预期行为与实际行为；使用虚构数据给出最小复现。不要提交真实个人档案、输入文件、导出、凭据或私有机器路径。安全漏洞请遵循 [SECURITY.md](SECURITY.md)。

Describe the trigger, expected behavior, and actual behavior, using a minimal reproduction with fictional data. Keep changes focused. Never submit real personal records, inputs, exports, credentials, or private machine paths. Follow [SECURITY.md](SECURITY.md) for vulnerabilities.

## 实现与内容要求 / Code and content requirements

- 运行时只用 Python 标准库，保持 Python 3.10+ 兼容、UTF-8 与跨平台路径处理。命令必须与真实 CLI 一致，不编造筛选器、导入能力或 MCP 依赖。
- 保持确定性存储、来源链、事实与推断分离、排除规则、精确删除二次确认和完整导出边界。模型建议不能冒充已成功写入。
- 敏感主题使用中性、非评判语言，允许跳过；不以跳过推断行为，不把性行为默认病理化，不作诊断。用户目标及他人边界优先。
- 外部事实或研究断言附可核对的来源链接（source-linked claims），区分证据、项目理论与假设；不宣称未经验证的医学或因果效果。
- 所有 fixtures 必须是 fictional / synthetic（完全虚构），不得使用真实档案，即使做过改名或脱敏。测试不得在仓库内留下个人数据、字节码或导出。
- 改变行为时增加能观察到真实结果的回归验证；中英文 README 的信息类别和命令同步更新，并更新变更记录。

- Runtime code must use only the Python standard library, retain Python 3.10+ compatibility, use UTF-8, and handle paths portably. Commands must match the real CLI; do not invent filters, import capabilities, or MCP dependencies.
- Preserve deterministic storage, provenance, separation of facts and inferences, exclusions, confirmation of exact deletion, and full export boundaries. Generated advice is not proof of a successful write.
- Handle sensitive topics neutrally, allow skipping, never infer behavior from a skipped answer, avoid treating sexual behavior as inherently pathological, and do not diagnose. Respect user goals and others' boundaries.
- Provide source-linked claims for external factual or research assertions. Distinguish evidence, project theory, and hypotheses; do not claim unverified medical or causal effects.
- Fixtures must be fictional / synthetic, never derived from real archives, even after renaming or sanitizing. Tests must not leave personal data, bytecode, or exports in the repository.
- For behavior changes, add regression checks of observable outcomes. Keep information categories and commands aligned in both READMEs and update the changelog.

## 本地验证 / Local verification

`.gitattributes` 为一般仓库文本选择 LF，并用 `skills/** -text` 保留提取的 Skill 原始字节（包括已有的混合换行）。`tests/release_manifest.json` 验证原始 SHA-256；不要为了匹配本机换行设置而重写 Skill 或更新摘要。广泛搜索内部目录名称可能匹配校验器、测试和忽略规则中的通用检测字符串；应结合实际路径和发布验证结果判断。

`.gitattributes` selects LF for authored repository text and uses `skills/** -text` to preserve extracted Skill bytes, including existing mixed line endings. `tests/release_manifest.json` checks raw SHA-256 values; do not rewrite the Skill or update hashes to match local newline settings. Broad searches for internal directory names can match generic detector strings in the validator, tests, and ignore rules; assess actual paths together with release-validation findings.

从仓库根目录运行以下全部三项检查。Windows PowerShell 命令如下；环境变量让测试启动的 Python 子进程也不生成字节码，`-B` 同时保护当前进程。无需安装第三方运行时依赖。

Run all three checks from the repository root. The following works in Windows PowerShell. The environment variable also disables bytecode in Python child processes started by tests; `-B` additionally protects the current process. No third-party runtime packages are required.

```powershell
$env:PYTHONDONTWRITEBYTECODE = '1'
python -B -X utf8 -m unittest discover -s tests -v
python -B -X utf8 tests/e2e_workflow.py
python -B -X utf8 tools/validate_release.py
```

相同的可移植 Python 命令也适用于其他平台（若 Python 可执行文件名是 `python3`，替换命令名）：

The same portable Python commands work on other platforms; substitute `python3` if that is your Python executable:

```sh
export PYTHONDONTWRITEBYTECODE=1
python -B -X utf8 -m unittest discover -s tests -v
python -B -X utf8 tests/e2e_workflow.py
python -B -X utf8 tools/validate_release.py
```

单元测试包含公共文档契约；端到端检查使用临时目录和虚构输入，检查报告、ZIP 与清理；发布验证检查文件、元数据和隐私模式。检查输出后再报告通过，失败时描述必要类别，不复制私密正文。

Unit tests include public-document contracts. The end-to-end check uses temporary directories and fictional inputs to verify reports, ZIP contents, and cleanup. Release validation checks files, metadata, and privacy patterns. Read output before claiming a pass; describe necessary error categories without private text.

提交的代码和随包资料按项目 [MIT License](LICENSE) 提供；许可证不授权公开他人的个人档案。

Contributed code and bundled materials are provided under the project's [MIT License](LICENSE), which does not authorize publishing someone else's personal records.
