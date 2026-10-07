# AI自媒体内容工厂 · Skill 三件套

> 从选题 → 写稿 → 剪辑/数字人 → 出片，全流程AI自动化。

## 包含的 Skill

| Skill | 功能 | 触发词 |
|-------|------|--------|
| **selfmedia-viralcopy** | AI爆款文案（选题→写稿→改稿→合规） | 想个选题、写口播稿、爆款文案、改稿去AI味 |
| **selfmedia-autoclip** | AI自动剪辑口播视频（去气口+字幕+BGM+竖屏） | 剪一下这条口播、自动剪辑、去气口、加字幕出片 |
| **selfmedia-talkinghead** | AI数字人出镜口播（零GPU零服务费） | 数字人、AI出镜、虚拟主播、做个数字人视频 |

## 安装方法

### TRAE SOLO / Agents

```bash
mkdir -p ~/.agents/skills && cd ~/.agents/skills
git clone https://gitee.com/你的用户名/selfmedia-skills.git
```

安装后重启 TRAE，输入「写条口播稿」或「剪一下这条口播」即可使用。

## 工作流串联

```
selfmedia-viralcopy（选题+写稿）
    ↓ script.json
    ├→ selfmedia-autoclip（真人素材剪辑）
    └→ selfmedia-talkinghead（数字人口播）
```

## 需要定制化？

本 Skill 提供通用方法论。如果需要：

- 接入你的素材库/BGM库/选题池
- 批量出片流水线
- 品牌IP形象定制
- 私有化部署到你的服务器
- 与你的内容管理系统对接

→ **联系 AI 代开发，帮你搭建专属自媒体内容工厂**

## License

MIT
