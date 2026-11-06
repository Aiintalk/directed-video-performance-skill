# WorkBuddy 使用与依赖

本技能是一份自包含流程包。WorkBuddy 可从“技能 → 添加技能 → 上传技能”导入 ZIP，或将技能目录放入当前设备的用户技能目录；在聊天中明确选中或提及 `directed-video-performance`。导入后检查技能处于启用状态。若组织限制自定义技能，需由组织管理员开放。

在线获取地址为 `https://github.com/Aiintalk/directed-video-performance-skill`。WorkBuddy 官方“上传技能”入口使用本地文件；如需从链接安装，可让 WorkBuddy 桌面端下载完整仓库并安装到当前用户技能目录，或先下载仓库 Release 页的 `directed-video-performance.skill.zip` 再通过界面上传。安装后核对本包内两份 `references/bundled/*/SKILL.md` 实际存在。

**不需要另外安装两个 Skill。** 它们的 `SKILL.md`、所需 `references/` 和许可文件位于本包 `references/bundled/`。WorkBuddy 的技能列表只需选中总技能；执行时由总技能按规定顺序读取两份内置方法文件。Hypit 方法只负责素材分析、画面基础稿和提示词，不执行生成。忽略其内置原始文档中的 Hypit CLI、Runtime、认证步骤；不要安装 Hypit 命令行程序、登录 Hypit 账号，或把火山方舟 Key 配进 Hypit。

要实际生成视频，使用本包的 `scripts/` 直接调用火山方舟 API，默认 `doubao-seedance-2-0-260128`、`720p`。首次在 Skill 根目录运行 `python3 scripts/setup.py` 安装 Python 运行依赖，再运行 `python3 scripts/run.py status` 检查已有凭据。缺少时才在本机交互运行 `python3 scripts/run.py configure api_key`；需要私域素材库管理或本地素材上传时，另需 `configure ak`、`configure sk` 及对应项目/TOS 权限。已存储的密钥可复用。完整命令与素材流程见 [火山方舟 API 执行](ark-api-workflow.md)。WorkBuddy 的中转聊天模型 Key 不等同于火山方舟 API Key。不要把密钥写进聊天、提示词、技能包或项目文件。只做提示词无需这些配置。

图像生成、Seedance、Clipcat、剪映工程编辑都是按需能力；在 WorkBuddy 中应使用实际可用的工具或等效渠道，不假定 Codex 专有的 `imagegen` 工具存在。

使用示例：

> 使用 directed-video-performance。参考视频只看 8–15 秒，人物用我给的照片，产品按产品图，改背景和这段台词。先只给我完整提示词，不生成。

> 使用 directed-video-performance。按这段参考动作生成新画面，人物和声音用我提供的素材。先做一张关键场景照片给我看。

> 使用 directed-video-performance。根据已确认的画面方案，用 Seedance 2.0、720p 生成视频，按镜头验收后交付成片。沿用本项目已确认的渠道和预算。
