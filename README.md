# 定向画面与人物表演 Skill

一个可在 WorkBuddy 中使用的视频制作流程 Skill：按文字、人物/产品/声音素材和指定参考片段构建目标画面，再优化既定人物表演。内置 Hypit 与 facial-expression-prompting 的方法文件；默认视频目标为 **Seedance 2.0、720p**。只要提示词时不生成，只要照片时不生成视频。

## 在 WorkBuddy 联机安装

复制下面这段话，发送给 **WorkBuddy 桌面端**：

> 请从 https://github.com/Aiintalk/directed-video-performance-skill 下载完整仓库，将它作为一个 Skill 安装到当前用户的 WorkBuddy 技能目录，目录名为 `directed-video-performance`。如果同名目录已经存在，先核对来源并备份，再更新；不要覆盖无关文件。安装后检查 `SKILL.md`、`references/bundled/hypit/SKILL.md`、`references/bundled/facial-expression-prompting/SKILL.md` 都能读取，并告诉我实际安装路径。不要调用付费视频生成。

如果 WorkBuddy 当前不能直接从仓库安装，可下载 [Release 安装包](https://github.com/Aiintalk/directed-video-performance-skill/releases/latest/download/directed-video-performance.skill.zip)，再在 WorkBuddy 中打开 **技能 → 添加技能 → 上传技能**，选中该 ZIP。GitHub 自动生成的 Source code ZIP 与这里的 Skill 安装包不同，优先使用 Release 资产。

安装后新建任务，选中 `directed-video-performance`，上传素材并说明本次只要提示词、先要照片，或要生成视频。示例：

> 使用 directed-video-performance。参考视频只采用 8–15 秒的动作和节奏；人物按我上传的照片，产品按产品图，声音按声音样本。只改背景和台词。先给我画面清单与完整提示词，不提交生成。

## 生成视频还需配置

Skill 包内已包含两套**方法文件**，无需再安装两个独立 Skill。实际生成视频仍需 WorkBuddy 可调用的 Hypit 命令行程序、支持 Seedance 2.0 的服务渠道、账号权限、素材访问权和预算。聊天模型中转站的 Key 不等于视频生成 API Key。配置细节见 [WorkBuddy 使用与依赖](references/workbuddy-setup.md)。

工作流全文见 [SKILL.md](SKILL.md)。内置方法来源见 [SOURCES.md](references/bundled/SOURCES.md)。本仓库保留 [Hypit 附加许可条款](LICENSE)；内置的 facial-expression-prompting 保留其 [MIT 许可](references/bundled/facial-expression-prompting/LICENSE)。公开或商业再分发时请遵守这些条款。
