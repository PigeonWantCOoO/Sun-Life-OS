# 安全政策 / Security policy

## 支持版本 / Supported versions

当前支持 `0.1.x` 系列安全修复。报告时请注明准确版本、Python 版本和操作系统。其他版本请先用虚构数据在当前受支持版本复现。

Security fixes currently target `0.1.x`. Include the exact release, Python version, and operating system. For other versions, first try reproducing with fictional data on a supported release.

## 报告方法 / Reporting

不要把个人档案粘贴进公共 Issue，也不要附真实输入、导出 ZIP、令牌或机器私有路径。

**Do not paste personal records into a public Issue.** Do not attach real inputs, export ZIPs, credentials, or machine-private paths.

仓库上传到 GitHub 后，如果启用了 private vulnerability reporting，请从仓库的 Security 页面使用 “Report a vulnerability” 私密报告。此文档不声称该渠道已经启用。若未启用，只在公开渠道询问如何建立私密联系，不公开漏洞利用细节或敏感资料；建立私密渠道前先保留报告。

Once the repository is uploaded to GitHub, use “Report a vulnerability” on its Security page if private vulnerability reporting is enabled. This document does not claim it is already enabled. If unavailable, ask publicly only how to establish private contact, without exploit details or sensitive material, and hold the report until a private channel exists.

请提供最小复现、预期与实际行为、影响范围、精确版本和已脱敏的错误类别。全部样例与 fixtures 必须为虚构（fictional / synthetic）；删除或导出问题只描述必要 ID 结构和相对路径。不要为复现而处理真实私人档案。

Provide a minimal reproduction, expected and actual behavior, impact, exact versions, and sanitized error categories. All examples and fixtures must be fictional / synthetic. For deletion or export issues, include only necessary ID structure and relative paths. Do not reproduce against real private archives.

## 边界 / Boundaries

本地明文和未加密 ZIP 是当前明确选择的存储方式。可报告的问题包括越权读取、意外泄露、删除范围错误、排除失效或完整性保护绕过。模型请求的服务端处理不由本地存储器控制；隐私边界和删除限制见 [PRIVACY.md](PRIVACY.md)。此项目不承诺固定响应或修复期限。

Local plaintext and unencrypted ZIP files are the explicitly selected storage mode. Report issues such as unauthorized access, unintended disclosure, incorrect deletion scope, ineffective exclusions, or integrity-check bypasses. Server-side model request handling is outside the local store's control. See [PRIVACY.md](PRIVACY.md) for privacy and deletion limits. No fixed response or resolution deadline is promised.
