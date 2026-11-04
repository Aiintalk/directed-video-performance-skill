# WorkBuddy 使用与依赖

本技能是一份自包含流程包。WorkBuddy 可从“技能 → 添加技能 → 上传技能”导入 ZIP，或将技能目录放入当前设备的用户技能目录；在聊天中明确选中或提及 `directed-video-performance`。导入后检查技能处于启用状态。若组织限制自定义技能，需由组织管理员开放。

在线获取地址为 `https://github.com/Aiintalk/directed-video-performance-skill`。WorkBuddy 官方“上传技能”入口使用本地文件；如需从链接安装，可让 WorkBuddy 桌面端下载完整仓库并安装到当前用户技能目录，或先下载仓库 Release 页的 `directed-video-performance.skill.zip` 再通过界面上传。安装后核对本包内两份 `references/bundled/*/SKILL.md` 实际存在。

**不需要另外安装两个 Skill。** 它们的 `SKILL.md`、所需 `references/` 和许可文件位于本包 `references/bundled/`。WorkBuddy 的技能列表只需选中总技能；执行时由总技能按规定顺序读取两份内置方法文件。这不等于把 Hypit 命令行程序也打包进来了。

要实际生成视频，默认目标是 Seedance 2.0、720p。还须在 WorkBuddy 可访问的环境中安装 Hypit 运行工具（其上游文档给出的 npm 安装方式为 `npm install --global @hypit/hypit`），配置真正支持 Seedance 2.0 的 Provider/渠道，并由使用者在本机安全配置对应凭据、素材访问权和额度。`hypit --version` 只验证可执行程序存在，不能证明视频渠道已可调用。WorkBuddy 的中转聊天模型 Key 不等同于视频生成 API、虚拟素材库或本地处理工具的权限。不要把 Key 写进聊天、提示词、技能包或项目文件。只做提示词无需视频生成凭据。

图像生成、Seedance、Clipcat、剪映工程编辑都是按需能力；在 WorkBuddy 中应使用实际可用的工具或等效渠道，不假定 Codex 专有的 `imagegen` 工具存在。

使用示例：

> 使用 directed-video-performance。参考视频只看 8–15 秒，人物用我给的照片，产品按产品图，改背景和这段台词。先只给我完整提示词，不生成。

> 使用 directed-video-performance。按这段参考动作生成新画面，人物和声音用我提供的素材。先做一张关键场景照片给我看。

> 使用 directed-video-performance。根据已确认的画面方案，用 Seedance 2.0、720p 生成视频，按镜头验收后交付成片。沿用本项目已确认的渠道和预算。
