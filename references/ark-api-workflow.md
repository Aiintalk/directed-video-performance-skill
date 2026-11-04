# 火山方舟素材与 Seedance 2.0 API 执行

本包内的 `scripts/ark.py` 直接调用火山方舟视频生成 API 和私域素材库 API；`scripts/run.py` 在本包独立的 Python 环境中运行它。**Hypit 只用于前面的分析和画面基础稿，不参与素材上传、任务提交、轮询或导出；不要求安装 Hypit CLI 或登录 Hypit 账号。**以下命令在本 Skill 根目录执行。

## 首次环境检查与密钥

1. `python3 scripts/setup.py`：在用户目录建立独立 Python 环境，安装 `keyring`、官方 TOS Python SDK `tos` 和 FFmpeg 执行器 `imageio-ffmpeg`。已可用的环境不重复安装。
2. `python3 scripts/run.py status`：只报告凭据是否存在，不输出值，也不访问网络。
3. 仅缺少本次确实需要的凭据时，让使用者在本机交互输入：
   - `python3 scripts/run.py configure api_key`：火山方舟视频生成 API Key；创建、查询视频任务需要。
   - `python3 scripts/run.py configure ak` 与 `python3 scripts/run.py configure sk`：素材库管理及本地 TOS 上传需要的 Access Key ID / Secret Access Key。

密钥保存在 macOS Keychain、Windows Credential Locker 等系统凭据库中；也可由使用者在运行环境提供 `ARK_API_KEY`、`VOLC_ACCESS_KEY_ID`、`VOLC_SECRET_ACCESS_KEY`。不要把密钥写进聊天、提示词、JSON、日志或 GitHub。若本次只写提示词，不运行上述配置；若已有可用密钥，不让使用者重复输入。WorkBuddy 聊天模型的中转站 Key 不能自动代替方舟 API Key 或 AK/SK。视频 API Key、AK/SK、素材库权限、项目与 TOS 桶是不同条件，须分别核对实际可用性。本包在提交含 `asset://` 素材的任务前会用 AK/SK 查询其 `Active` 状态与项目归属，因此即使素材已入库，也需要可用的 AK/SK 做这项核对。

## 只准备当前镜头真正用到的素材

先核对已有 `asset://` ID、素材组与 HTTPS 链接，复用可用素材。虚拟人像和用于该形象的音视频按火山方舟私域素材库要求入库；产品、普通背景等非虚拟人像素材可直接用可访问的 HTTPS 链接。本地普通图片也可在生成 JSON 的 `references` 中写 `{"type":"image","file":"/绝对路径/产品.png"}`，工具会按官方支持的 Base64 图片输入提交，不强制创建私域人像资产。**本地 `file` 不用于虚拟人像，也不支持视频和音频**。若本地虚拟人像文件需入库，工具先上传至使用者已有的北京 TOS 桶，再将临时签名 URL 传给 `CreateAsset`。这一步除 AK/SK 外，还需要对应 TOS 桶及上传/签名权限；工具不会创建或公开存储桶。已有可访问的 HTTPS 源链接则不需要本地 TOS 上传。

查询或创建私域素材组：

```bash
python3 scripts/run.py groups --project default
python3 scripts/run.py group-create --name 我的虚拟人物 --project default
```

上传素材（按实际情况二选一）：

```bash
python3 scripts/run.py asset-create --group-id group-实际ID --type Image --url https://可访问的素材地址 --project default
python3 scripts/run.py asset-create --group-id group-实际ID --type Video --file /本地/人物.mp4 --bucket 已有的北京TOS桶 --project default
```

音频使用 `--type Audio`。`CreateAsset` 是异步的：拿到 `asset-...` 后运行 `python3 scripts/run.py asset-wait asset-实际ID --project default`，只有 `Status=Active` 才能在生成请求中写 `asset://asset-实际ID`。上传与素材库操作失败时报告具体权限/状态，不把失败 ID 写成已可用素材。素材项目与视频 API Key 所属项目需一致。

## 生成请求与任务回收

按通过验收的提示词和**实际已上传**素材，为每个需要生成的短镜头写 JSON。示意如下，真实执行前替换所有示意值：

```json
{
  "prompt": "图片1中的人物保持指定身份与服装，按视频1的动作时间线完成既定动作，准确说出已确认台词；其余要求见本镜头画面基础稿。",
  "duration": 8,
  "resolution": "720p",
  "project": "default",
  "ratio": "9:16",
  "generate_audio": true,
  "references": [
    {"type": "image", "url": "asset://asset-实际人物ID"},
    {"type": "image", "file": "/绝对路径/普通产品.png"},
    {"type": "video", "url": "https://可访问的参考视频地址"}
  ]
}
```

`scripts/ark.py` 的实际 API 模型固定为 `doubao-seedance-2-0-260128`，未写分辨率时默认 `720p`；用户指定其他模型而本客户端不支持时须说明，不静默替换。Seedance 2.0 单次输出为 4–15 秒，参考素材还受数量、时长和格式限制。提示词用“图片1、视频1、音频1”按同类型输入次序指代，不能把 `asset://` ID 写进自然语言提示词来代替真正的 `references` 输入。画面要求严格编辑原镜头时写“严格编辑视频1”；仅借用动作生成新画面时写“参考视频1的动作”。

提交与回收：

```bash
python3 scripts/run.py generate --spec /绝对路径/shot01.json --dry-run
python3 scripts/run.py generate --spec /绝对路径/shot01.json
python3 scripts/run.py task-wait cgt-实际任务ID --output /绝对路径/shot01.mp4
```

`--dry-run` 不提交生成。正式提交会创建 `.receipt.json`，包含任务 ID，超时或中断时先查询原任务，不重复付费提交。任务成功后立即下载结果；火山方舟返回的视频链接有时效。多镜头或长视频按接口限制切分、只生成需变化片段，质检通过后用 FFmpeg 拼接。用户若要求保留生成片段的原始音轨，不额外换配音。

## 官方接口依据

- [Seedance 2.0 系列教程](https://docs.volcengine.com/docs/ark/seedance-2-0?lang=zh)
- [创建视频生成任务 API](https://docs.volcengine.com/docs/ark/create-video-generation-task-api?lang=zh)
- [私域虚拟人像素材库指南](https://docs.volcengine.com/docs/ark/private-virtual-avatar-library-guide-preview?lang=zh)
- [火山 TOS Python SDK](https://github.com/volcengine/ve-tos-python-sdk)
