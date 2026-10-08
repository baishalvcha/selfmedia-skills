---
name: selfmedia-viralcopy
version: 1.1.0-cloud
description: "自媒体工作流2 · AI 爆款文案：选题 → 写稿 → 改稿一条龙，产出结构化脚本可直接进渲染出片。当用户说「想个选题」「今天讲什么」「写条口播稿」「写短视频文案」「爆款文案」「改稿去AI味」「选题池还有啥」时使用。内置违禁词双表与选题红线门禁。不负责剪辑出片（走 selfmedia-autoclip / selfmedia-talkinghead）。支持云API调用，无需本地部署。"
metadata:
  requires:
    bins: ["curl"]
  cloud_api: true
  api_endpoint: "https://sm.mahong.tech/api/wf2"
---

# 工作流2 · AI 爆款文案（选题 → 写稿 → 改稿）

**一条龙：选题打分 → 写稿 → 去 AI 味改稿 → 合规自检 → 可渲染的结构化脚本。**

## 两种使用方式

### 方式一：云 API 调用（推荐，无需安装环境）

```bash
# 设置 API 地址和 Token
SM_API="https://sm.mahong.tech"
SM_TOKEN="你的API Token"

# 从选题池自动选题并写稿
curl -X POST "$SM_API/api/wf2" \
  -H "X-API-Token: $SM_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"from_pool":true,"platform":"douyin"}'
# → 返回 {"job_id":"wf2-xxx","status":"queued"}

# 指定选题直接写稿
curl -X POST "$SM_API/api/wf2" \
  -H "X-API-Token: $SM_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"topic":"一条差评，AI帮你回成二次获客的机会","platform":"douyin"}'

# 查询任务状态
curl -H "X-API-Token: $SM_TOKEN" "$SM_API/api/jobs/wf2-xxx"

# 下载产出的 script.json
curl -H "X-API-Token: $SM_TOKEN" -o script.json \
  "$SM_API/api/download/wf2-xxx/script.json"
```

### 方式二：本地部署（需要 Python + LLM）

```bash
python sm.py wf2 --from-pool --platform douyin
python sm.py wf2 --topic "你的选题" --platform douyin
python sm.py wf2 --product "产品名" --audience "目标人群" --core "核心卖点"
```

## 核心能力

| 环节 | 功能 |
|------|------|
| 选题 | 8维打分（情绪/传播/独家/身份/时效/锚点/可视化/门槛）+ 红线过滤 |
| 写稿 | 知识卡注入 + 60s口播模板 → 结构化脚本（钩子+3要点+收尾） |
| 改稿 | 去AI味重写（删套话、长短句交错、抽象词换具体动作）+ 合规审计 |

## 产出

| 文件 | 内容 |
|---|---|
| `script.json` | 可直接进渲染的结构化脚本 |
| `选题提案.md` | 候选 + 8维打分表 + 钩子/承接 |
| `口播稿.md` | 人类可读版（卡面 + 口播 + 时长） |
| `自检报告.md` | 合规审计（结构 + 红线） |

## 硬约束

- 选题红线：根据你的业务定制黑名单
- 口播禁技术词：受众是小老板不是程序员，不说「渲染/质检/JSON/管线/API/部署」
- 不编造：不用绝对化用语、不承诺效果、不编数据
- 钩子卡禁问候语开场：前3秒直接抛钩子
- 卡面可读性：headline ≤10字、bullet ≤14字

## 闸门：选题必须人拍板

先出候选 → 人拍板 → 再写稿 → 再出片。
文案方向错了，渲染10版都是错的。

## 需要 API Token？

云 API 需要授权 Token。Token 在 `config.yaml` 的 `api.tokens` 配。
未配置 Token 时本地调试开放，生产环境必须配置。

## 需要定制化？

本 Skill 提供通用方法论 + 云 API。如果需要：
- 私有化部署（你的服务器、你的 LLM）
- 接入你的选题池/知识库/违禁词表
- 批量选题+写稿流水线
- 你的品牌声音训练（让文案更像你）
- 多平台合规适配

→ **联系 AI 代开发，帮你搭建专属内容工厂**

源码：https://gitee.com/mahong-tech/selfmedia-skills
