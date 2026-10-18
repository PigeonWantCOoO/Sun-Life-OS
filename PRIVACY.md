# 隐私与数据控制 / Privacy and data controls

## 存储与模型边界 / Storage and model boundaries

本项目选择本地明文、未加密存储（local plaintext）。仓库中的代码、Skill、参考资料和虚构测试数据，与外部个人数据目录是两类内容。个人档案、输入 JSON、报告和导出不应混入仓库或 Skill 包；`.gitignore` 不是加密或访问控制。能读取目录的账户、程序、备份或同步服务可能读取内容。

The selected mode is local plaintext without encryption. Repository code, Skill files, references, and fictional tests are distinct from the external personal-data directory. Keep records, input JSON, reports, and exports out of the repository and Skill package. `.gitignore` is neither encryption nor access control. Accounts, programs, backups, and sync services with directory access may read the content.

记录被加载用于分析时，会进入当次模型上下文（model context）。Skill 不主动另行上传完整档案，但本地保存不代表完全离线分析。请求处理受所用模型产品、账户设置和服务政策约束；本项目不能保证从模型服务中撤回已发送内容。

Records retrieved for analysis enter the current model context. The Skill does not proactively upload the whole archive separately, but local storage does not imply fully offline analysis. Request handling depends on the model product, account settings, and service policies. This project cannot guarantee withdrawal of content already sent to a model service.

## 最少检索与回答状态 / Retrieval and response states

只加载当前问题所需的最少记录（minimum necessary retrieval），并按日期、领域或精确 ID 缩小范围。工作问题默认不读取无关性行为资料。`show` 用于档案查看；将结果用于建议前仍须执行排除规则。`review` 应用排除规则后，结果仍需按当前问题缩小。

Use minimum necessary retrieval, narrowing by date, domain, or exact IDs. Work questions do not retrieve unrelated sexual history by default. `show` is an archive-viewing tool; apply exclusions before using its results in advice. Even after `review` applies exclusions, narrow its output to the current question.

`declined`（跳过）、`unknown`（不确定）与 `unanswered`（未回答）都不等于明确回答“无”的 `false`，也不能作为推断证据。敏感问题允许跳过，只有回答“有”后才展开自愿细节，不作道德标签或默认病理化。完全无人回应的定时问询不创建记录。

`declined` (skip), `unknown`, and `unanswered` are distinct from an explicit negative answer represented by `false`. None is evidence for an inference. Sensitive questions can be skipped; optional detail follows only an affirmative answer, without moral labels or treating behavior as inherently pathological. A scheduled prompt with no response creates no record.

## 排除与删除 / Exclusion and deletion

`exclude` 是停止用于分析，不是物理删除。它支持领域、人物、记录、事件、画像和假设范围，保留底层文件；也不等于关闭敏感问询。用户可表达不再主动问询的偏好，但当前 CLI 没有专用模块开关命令。

`exclude` stops analysis use; it is not physical deletion. It supports domain, person, record, event, profile, and hypothesis scopes while retaining underlying files. It also does not switch off sensitive prompts. Users can request that prompting stop, but the current CLI has no dedicated module-toggle command.

精确删除先从语义请求检索候选，再核对真实 ID 并运行 `delete-preview`。向用户展示原始目标、扩展关联 ID、派生文件、临时文件与影响数量；取得对该清单的确认后，以原 `manifest` 和 `confirmation_token` 调用 `delete`。token 不是用户授权。要删除原话应选 `record_id`；只删事件、假设或画像不等于清除源文本。

Exact deletion first finds candidates from the semantic request, verifies real IDs, and runs `delete-preview`. Show original targets, expanded related IDs, derived files, temporary files, and impact counts. After confirmation of that manifest, call `delete` with its original `manifest` and `confirmation_token`. The token is not user authorization. Select `record_id` to remove original words; deleting only an event, hypothesis, or profile does not remove source text.

删除实际重写目标文件并移除相关派生报告，审计仅保留操作元数据，不保留被删正文的恢复备份。清除只覆盖已确认的数据目录范围，不保证擦除磁盘残留、备份、先前导出或模型上下文。多文件操作中断时可能部分完成；失败必须披露已知状态，不能称全部完成。返回 `result.status: confirmation_required` 或清单失效时，重新展示并确认；外层 `status: ok` 不足以证明删除完成。

Deletion rewrites target files and removes related derived reports. Audit entries retain operation metadata, without a recovery backup of deleted text. This covers only the confirmed scope in the data directory, not guaranteed erasure of disk remnants, backups, previous exports, or model context. Interrupted multi-file deletion may be partly complete; disclose known state rather than claiming completion. If `result.status` is `confirmation_required` or the manifest is stale, show and confirm the refreshed scope. Outer `status: ok` alone does not prove completed deletion.

## 输入与崩溃残留 / Inputs and crash remnants

输入放在数据目录 `.state/inbox` 的唯一 UTF-8 JSON 文件中，正文不放 shell 参数。成功后立即清理；失败输入仅在明确选择用原文件恢复时标记 pending 并保留，重试复用原文件和 `operation_id`。放弃、替换或没有明确恢复选择时清理确切旧输入。不要在错误、日志或公开报告中复制正文。

Inputs are unique UTF-8 JSON files in `.state/inbox` under the data directory, never record text in shell arguments. Clean up immediately after success. Keep failed inputs as pending only when explicitly choosing recovery from the original file, reusing it and `operation_id`. Clean up exact old inputs when abandoning, replacing, or not explicitly choosing recovery. Do not copy record text into errors, logs, or public reports.

导出或删除前枚举 inbox，逐项选择重试或放弃；明确保留 pending 时披露其路径、状态和用途。涉及待删内容的输入必须先完成重试并清理或放弃并清理，数据改变后重建删除预览；确认相关残留路径与影响，完成后再核对。清理失败时不宣称完整删除或继续导出。

Before export or deletion, enumerate inbox inputs and resolve each by retrying or discarding; disclose paths, states, and purposes for any explicitly retained pending inputs. Inputs containing deletion targets must be retried and cleaned up or discarded and cleaned up first. Rebuild the deletion preview after data changes, confirm related remnant paths and effects, and check again afterward. If cleanup fails, do not claim full deletion or continue export.

崩溃可能留下 `.sun-life-atomic.<target>.<32-hex>.tmp` 明文副本，`validate` 报告相对路径。确认写入进程已退出后才能人工处理遗留锁，不能按锁龄删锁。可验证归属的专用临时文件由删除预览展示整文件丢弃影响；旧版 `.<target>.<8-random>.tmp` 须人工核对，不自动清理任意 `*.tmp`。详见 [Skill 隐私操作参考](skills/sun-life-os/references/privacy-boundary.md)。

Crashes may leave plaintext `.sun-life-atomic.<target>.<32-hex>.tmp` copies; `validate` reports relative paths. Confirm that the writer has exited before manually handling an orphaned lock; lock age is insufficient. The deletion preview lists verified reserved temporaries and whole-file discard effects. Legacy `.<target>.<8-random>.tmp` files require manual inspection; do not automatically remove arbitrary `*.tmp`. See the [Skill privacy reference](skills/sun-life-os/references/privacy-boundary.md).

## 导出 / Exports

`export --output` 创建未加密的完整数据目录 ZIP 快照，保持相对路径，排除活动锁和临时文件，不覆盖已有目标。它不是 Skill 源码（Skill source）导出，不支持筛选或导入。当前 exporter 会收录 inbox JSON，因此导出前须清理或明确披露用户选择保留的 pending 输入；不能静默纳入。

`export --output` creates an unencrypted ZIP snapshot of the complete data directory, preserving relative paths, excluding active locks and temporary files, and refusing an existing destination. This exports personal data, not Skill source. Filtered export and import are unsupported. The exporter includes inbox JSON, so first clean it up or explicitly disclose pending inputs the user chooses to retain; never include them silently.

导出前展示完整范围及目标路径。导出文件是额外的私密副本，后续删除数据目录内容不会自动删除已有 ZIP；用户须分别管理分享、备份与删除。

Show the complete scope and destination before export. Each archive is another private copy. Later deletion from the data directory does not automatically delete existing ZIP files; manage their sharing, backup, and removal separately.
