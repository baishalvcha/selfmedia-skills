---
name: selfmedia-autoclip
version: 1.1.0-cloud
description: "自媒体工作流1 · AI 自动剪辑口播视频：把真人素材丢进去，自动去气口/静音停顿、自动配字幕、自动挑 BGM、自动套竖屏与双水印，直接出可发布的成片。当用户说「剪一下这条口播」「把这段素材做成短视频」「自动剪辑」「去气口」「加字幕出片」「素材丢进去自动成片」，或给了一个视频文件/素材目录要做成抖音/视频号/小红书成片时使用。不负责写文案（走 selfmedia-viralcopy）、不负责数字人（走 selfmedia-talkinghead）。支持云API调用，无需本地部署。"
metadata:
  requires:
    bins: ["curl"]
  cloud_api: true
  api_endpoint: "https://sm.mahong.tech/api/wf1"
---

# 工作流1 · AI 自动剪辑口播视频

**一条命令，素材进 → 成片出。** 这是主播方法论里的第 1 个工作流。

## 两种使用方式

### 方式一：云 API 调用（推荐，无需安装 ffmpeg/conda）

```bash
SM_API="https://sm.mahong.tech"
SM_TOKEN="你的API Token"

# 真人口播原声：自动去气口 + 字幕 + BGM + 竖屏 + 水印
curl -X POST "$SM_API/api/wf1" \
  -H "X-API-Token: $SM_TOKEN" \
  -F "mode=talk" \
  -F "platform=douyin" \
  -F "files=@/path/to/video.mp4"
# → 返回 {"job_id":"wf1-xxx","status":"queued"}

# 素材画面 + 脚本旁白（TTS 配音）—— 需要先跑 wf2 拿到 script.json
curl -X POST "$SM_API/api/wf1" \
  -H "X-API-Token: $SM_TOKEN" \
  -F "mode=mix" \
  -F "platform=douyin" \
  -F 'script_json={"title":"...","cards":[...]}' \
  -F "files=@/path/to/素材.mp4"

# 查询任务状态（渲染通常 2-5 分钟）
curl -H "X-API-Token: $SM_TOKEN" "$SM_API/api/jobs/wf1-xxx"

# 下载成片
curl -H "X-API-Token: $SM_TOKEN" -o final.mp4 \
  "$SM_API/api/download/wf1-xxx/out/final.mp4"
```

### 方式二：本地部署（需要 Python + ffmpeg + conda）

```bash
python sm.py wf1 --in <素材目录或文件> --platform douyin
python sm.py wf1 --script <script.json> --in <素材目录> --mode mix
python sm.py wf1 --script <script.json> --mode card
```

## 四种模式

| 模式 | 输入 | 做什么 | 什么时候用 |
|---|---|---|---|
| `talk` | 真人口播视频 | **静音气口自动剪掉** → whisper 字幕 → BGM 闪避 → 竖屏适配 | 有真人出镜素材（最常用） |
| `mix` | 素材 + script.json | 素材做画面，脚本 caption 做 1:1 TTS 旁白 | 有素材但想用自己的稿子 |
| `card` | script.json | 台账风卡片 + TTS 旁白 | 完全没有实拍素材时的兜底产能 |
| `auto` | 自动判 | 有 script → mix；素材有音轨 → talk；否则 card | 不确定时用默认 |

## 关键参数

| 参数 | 作用 |
|---|---|
| `--fit cover\|blur` | 竖屏适配。`cover`=裁满；`blur`=**横屏素材转竖屏不裁脸** |
| `--no-autocut` | 不去气口（保留原始停顿） |
| `--bgm auto\|none\|<mp3>` | BGM；`auto` 从素材库按情绪挑 |
| `--no-burn` | 不烧字幕，只出 .srt（平台直传用） |

## 产出

- `<工程>_final.mp4` 成片（1080x1920 / 原声 / 烧字幕 / BGM / 双水印）
- `<工程>.srt` 字幕
- `<工程>_cover.png` 封面（talk 模式取真人帧 + 大字标题）
- `发布清单.md` 多平台规格

## 铁律

- **AI 永不发布**：成片交人确认后人工发
- 横屏素材一定要加 `--fit blur`，否则裁成竖屏会切脸

## 需要定制化？

本 Skill 提供通用方法论 + 云 API。如果需要：
- 私有化部署（你的服务器、你的 ffmpeg）
- 接入你的 BGM 素材库
- 批量剪辑流水线
- 多平台规格适配

→ **联系 AI 代开发，帮你搭建专属内容工厂**

源码：https://gitee.com/mahong-tech/selfmedia-skills
