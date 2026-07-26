# Contributing | 贡献指南

Thanks for helping improve `remotion-talking-head-director`.

感谢你帮助完善 `remotion-talking-head-director`。

## What to contribute | 可以贡献什么

- Improvements to phase gates, validation, and stop conditions.
- Remotion implementation practices that remain compatible with the current official APIs.
- Reusable templates for production records, storyboards, and review reports.
- Corrections and translations for the Chinese and English documentation.
- Reproducible examples of workflow failures and their fixes.

- 阶段门、验证方式和停止条件的改进。
- 与当前 Remotion 官方 API 兼容的实现实践。
- 可复用的生产记录、分镜和评审报告模板。
- 中英文文档的修正与翻译。
- 可复现的流程故障案例与修复方式。

## Before opening a pull request | 提交 PR 前

1. Keep the change focused. Explain which production phase or artifact it affects.
2. Preserve the hard safeguards: never overwrite source media, never silently rewrite factual captions, and never final-render without explicit approval.
3. Update both `README.md` and `README.en.md` when changing user-facing behavior.
4. Check Markdown links, directory trees, prompt examples, and template field names.
5. Do not commit private media, API keys, commercial fonts, watermarked samples, or assets without clear redistribution rights.

1. 保持改动聚焦，并说明它影响哪个生产阶段或产物。
2. 保留关键保护规则：不得覆盖原始素材、不得静默篡改事实字幕、不得未经明确确认导出最终视频。
3. 变更面向用户的行为时，同时更新 `README.md` 与 `README.en.md`。
4. 检查 Markdown 链接、目录树、提示词示例和模板字段名。
5. 不要提交私有媒体、API 密钥、商业字体、带水印样例或没有明确再分发权的资产。

## Pull request format | PR 格式

Use a short title and include:

- What changed and why.
- Files or phases affected.
- How you checked the change.
- Any remaining limitation or follow-up work.

使用简短标题，并说明：

- 改了什么，以及原因；
- 受影响的文件或阶段；
- 如何验证改动；
- 尚存的限制或后续工作。

## License | 许可证

By contributing, you agree that your contribution is licensed under this repository's [MIT License](LICENSE).

提交贡献即表示你同意按本仓库的 [MIT License](LICENSE) 授权你的贡献。
