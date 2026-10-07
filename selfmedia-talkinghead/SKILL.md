---
name: selfmedia-talkinghead
version: 1.0.0-public
description: "自媒体工作流3 · AI 数字人出镜口播全自动：形象图 + AI 配音 + 运镜 + 呼吸 + 音频包络驱动的口型微动 + 字幕 + BGM，零成本出「一个人对着镜头讲完一条口播」的成片。当用户说「数字人」「AI 出镜」「口播视频」「不露脸但要有人讲」「用 IP 形象讲一条」「虚拟主播」「做个数字人视频」时使用。零 GPU、零云端数字人服务费。不负责写稿（走 selfmedia-viralcopy）。"
metadata:
  requires:
    bins: ["python", "ffmpeg"]
---

# 工作流3 · AI 数字人出镜口播

**零成本数字人**：不需要 GPU、不需要买云端数字人服务，用 `ffmpeg + Pillow + edge-tts` 就能出「一个人对着镜头讲完一条口播」的成片。

## 核心能力

| 功能 | 说明 |
|------|------|
| 零GPU零服务费 | 纯CPU渲染，不需要GPU/云端数字人 |
| 音频包络口型 | 按配音音频包络逐帧缩放嘴部，便宜可复现 |
| 运镜+呼吸 | zoompan缓推 + 正弦呼吸浮动，死图变活人 |
| 自动抠图 | 平色背景自动抠除（四角泛洪+形态学） |
| 字幕+水印 | 底部旁白字幕 + 顶部要点 + 双水印 |

## 使用方式

```
# 用脚本出片（标准链路）
wf3 --script script.json

# 直接给口播稿，自动切卡
wf3 --text "你的口播稿..."

# 换数字人形象图
wf3 --script s.json --avatar /path/to/avatar.png

# 核对口型框位置（换新形象图必做）
wf3 --script s.json --debug-mouth

# 关掉口型，只留运镜+呼吸
wf3 --script s.json --no-lipsync
```

## 镜头四层（从下往上）

1. **基准帧**：形象图归一化，竖图裁满/方图虚化背景+主体居中
2. **口型微动**：音频包络驱动嘴部条带纵向缩放
3. **运镜+呼吸**：zoompan缓推 + 正弦呼吸浮动
4. **字幕+水印**：底部旁白 + 顶部要点 + 品牌/AI标识

## 产出

- `_final.mp4` 成片（1080x1920 / 数字人口播 / 字幕 / BGM / 双水印）
- `.srt` 字幕
- `_cover.png` 封面（形象图 + 大字标题）
- `_mouth_debug.png` 口型框调试图

## 换形象图的调参流程

不同形象图嘴部位置差别很大，换图后必须核对：

```
wf3 --script s.json --debug-mouth --no-render
```

看 `_mouth_debug.png`：绿框没套住嘴就调 `mouth_region`（x,y,w,h 相对形象图）。

## 升级真人级口型

渲染层已收敛成一个函数：输出与整体等长的嘴部条带视频。
把 `_build_mouth_video` 换成 MuseTalk / Wav2Lip 的产物即可，上层完全不用动。

## 依赖

- Python 3.10+
- ffmpeg
- Pillow
- edge-tts（AI配音，需能访问微软语音接口）

## 需要定制化？

本 Skill 提供通用方法论。如果需要：
- 你的品牌IP形象定制
- 真人级口型（MuseTalk/Wav2Lip接入）
- 批量数字人视频生产线
- 私有化部署到你的服务器

→ **联系 AI 代开发，帮你搭建专属数字人内容工厂**（从写稿→数字人→剪辑→发布全流程自动化）
