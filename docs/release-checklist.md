# 发行说明检查清单

正式版和预发布版均须更新根目录 `FNMUSIC_COMPAT.json`：扩展版本与 `VERSION` 一致，
音乐版本依据实机 `trim.music` manifest 核对，填写实际核对日期和说明。即使官方版本
未变化也要重新核对记录。发布和打包前运行 `python scripts/check_music_compat.py`；
校验失败不得继续发布。适配记录随 FPK 分发，仅作记录，不限制运行时行为。

发布流程及版本规则以本项目 `fnmusic-release` skill 为准。本清单补充发布前的完整性检查，不替代发布授权及唯一预发布约束。

1. 明确实际已发布的对比基线：正式版对比上一正式版；首个预发布对比上一正式版；后续预发布对比上一预发布或正式版。
2. 逐条阅读 `git log --oneline <基线tag>..HEAD`，同时检查 `git diff --stat <基线tag>..HEAD`。不能只看本次任务提交。
3. 对照 CHANGELOG 检查新增功能、错误修复、架构兼容、配置和默认行为、文档及维护变更；内部维护可以合并概括，用户可见变更不能省略。
4. 安装版本统一使用数字 `x.y.z-N`，正式版也必须保留修订号，不得降为 `x.y.z`。根目录 `RELEASE_CHANNEL` 显式取 `stable` 或 `prerelease`，禁止根据 `-N` 判断发布类型。`proxy/version.py` 为唯一转换：`2.11.0-4` + `stable` → tag/UI `v2.11.0`、CHANGELOG `[2.11.0]`；同一安装版本 + `prerelease` → tag/UI `v2.11.0e`、CHANGELOG `[2.11.0e]`（N=0..25 对应 a..z）。缺少/错误通道时新发布必须失败；旧版只在显式兼容读取时允许推断。
5. 写明对比基线，核对 VERSION、manifest、包内 VERSION、附件文件名和 moo 索引保留同一数字安装版本，且包内包含 RELEASE_CHANNEL。WebUI 共用转换代码并带 v 前缀，禁止手写显示版本。是否已发布同时检查通道和目标 tag，不能因相同安装版本曾作预发布就自动升号。CI 通过不能证明发行说明完整。
6. 正式发行从干净 dev 开始，完成测试后打无字母 tag，将 dev 合入 main 并推送两分支与 tag；Release CI 校验正式 tag 的提交已合入 main，只由 CI 构建并创建 Release。核对公开正文、prerelease=false、FPK/SHA256、moo 数字安装版本及 Gitee 镜像；成功后才清理被替代的同基础版本旧预发布，删除前复核身份，不碰其他正式版。

## 仅修订已发布说明

若只遗漏发行说明，修正 dev 的对应 CHANGELOG 段，由 `Correct release notes` 工作流同步已有 GitHub Release 正文。工作流不创建 Release、不改 tag、不重建或上传附件；执行前要求 tag 是当前提交的祖先，且相对 tag 仅包含 CHANGELOG、该纠错工作流、相关回归测试或检查清单的变更。

同步前后校验 Release ID、tag、预发布状态和附件 ID、大小、摘要保持不变，最后核对公开正文。若有其他变更则拒绝同步，按正常发布流程处理。

项目 skill 位于被版本控制忽略的本地目录；此检查清单和工作流纳入仓库，避免仅在本机补规则。
