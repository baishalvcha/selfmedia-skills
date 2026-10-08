---
name: selfmedia-talkinghead
version: 1.1.0-cloud
description: "自媒体工作流3 · AI 数字人出镜口播全自动：形象图 + AI 配音 + 运镜 + 呼吸 + 音频包络驱动的口型微动 + 字幕 + BGM，零成本出「一个人对着镜头讲完一条口播」的成片。当用户说「数字人」「AI 出镜」「口播视频」「不露脸但要有人讲」「用 IP 形象讲一条」「虚拟主播」「做个数字人视频」时使用。零 GPU、零云端数字人服务费；需要真人级口型时可替换渲染层的嘴部生成模块。不负责写稿（走 selfmedia-viralcopy）。支持云API调用，无需本地部署。"
metadata:
  requires:
    bins: ["curl"]
  cloud_api: true
  api_endpoint: "https://sm.mahong.tech/api/wf3"
---

# 工作流3 · AI 数字人出镜口播

**零成本数字人**：不需要 GPU、不需要买云端数字人服务，用 `ffmpeg + Pillow + edge-tts`
就能出「一个人对着镜头讲完一条口播」的成片。

## 两种使用方式

### 方式一：云 API 调用（推荐，无需安装环境）

```bash
SM_API="https://sm.mahong.tech"
SM_TOKEN="你的API Token"

# 用 script.json（来自 wf2）出片（标准链路）
SCRIPT=$(cat script.json)
curl -X POST "$SM_API/api/wf3" \
  -H "X-API-Token: $SM_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"script_json\":\"$SCRIPT\",\"platform\":\"douyin\"}"
# → 返回 {"job_id":"wf3-xxx","status":"queued"}

# 直接给口播稿，自动切卡
curl -X POST "$SM_API/api/wf3" \
  -H "X-API-Token: $SM_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"text":"很多人以为做短视频最难的是剪辑。其实最难的是选题。","platform":"douyin"}'

# 查询任务状态（渲染通常 2-5 分钟）
curl -H "X-API-Token: $SM_TOKEN" "$SM_API/api/jobs/wf3-xxx"

# 下载成片
curl -H "X-API-Token: $SM_TOKEN" -o final.mp4 \
  "$SM_API/api/download/wf3-xxx/out/final.mp4"
```

### 方式二：本地部署（需要 Python + ffmpeg + edge-tts）

```bash
python sm.py wf3 --script "workspace/工程/script.json"
python sm.py wf3 --text "口播稿全文..."
python sm.py wf3 --script s.json --avatar "你的形象图.png"
```

## 镜头四层（从下往上）

1. **基准帧**：形象图归一化到目标画幅。两种构图自动选：
   - `cover`：源图已是竖构图 → 直接裁满，适合真人半身照
   - `compose`：方图/横图 → 自身虚化做背景 + 主体居中框景，自动抠掉平色背景
2. **口型微动**：按配音的**音频包络**逐帧纵向缩放嘴部条带（下颌开合感）
3. **运镜 + 呼吸**：`zoompan` 缓推（1→1.06，余弦缓动）+ 正弦呼吸浮动，死图变活人
4. **字幕 + 双水印**：底部旁白字幕 + 顶部要点 + 品牌/AI 标识

## 产出

- `<工程>_final.mp4` 成片（1080x1920 / 数字人口播 / 烧字幕 / BGM 闪避 / 双水印）
- `<工程>.srt` 字幕
- `<工程>_cover.png` 封面（形象图 + 大字标题 + 双标）

## 需要真人级口型时怎么升级

渲染层已经把接口收敛成一个函数：**输出一段与整体等长的嘴部条带视频**。
把 `render_avatar.py` 的 `_build_mouth_video` 换成 MuseTalk / Wav2Lip 的产物即可，上层完全不用动。

## 常见坑

- **形象图嘴部位置**：不同形象图嘴部位置差别很大，换图后要核对口型区
- **编码器报 "width not divisible by 2"**：口型条带宽高必须为偶数（代码已强制）
- **配音失败**：edge-tts 需要能访问微软语音接口

## 需要定制化？

本 Skill 提供通用方法论 + 云 API。如果需要：
- 私有化部署（你的服务器、你的 GPU）
- 自定义数字人形象 + 口型调参
- 批量出片流水线
- 真人级口型（接入 MuseTalk/Wav2Lip）

→ **联系 AI 代开发，帮你搭建专属内容工厂**

源码：https://gitee.com/mahong-tech/selfmedia-skills
