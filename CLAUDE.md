# 微信公众号减脂文章自动化生成系统

## 项目简介
通过 6 步 AI 流水线自动生成减脂类公众号文章，每天产出 1 篇可直接发布的高质量内容（1500-2500 字）。

## 快速开始
在 Claude Code 中运行：
```
/wechat-gen
```
指定主题：
```
/wechat-gen --topic "平台期突破"
```

## 流水线架构
```
🎯 标题生成器 → ⭐ 标题甄选 → 📝 正文 → 👤 人设 → 💕 情绪 → 📐 排版 → 🧹 去味
                                                                    ↓
                                                     📎 拼接固定文案 → 📁 归档
```

固定文案（开头引导块 / 免责声明 / 结尾转化块）由脚本在流水线结束后拼接，
**不经过任何 agent**，避免被"润色""去味"步骤改花。

## 目录结构
```
team/
├── .claude/
│   ├── skills/wechat-gen.md    # Skill 入口
│   └── settings.json           # 项目配置
├── prompts/                    # Prompt 模板（脚本不读取，仅作参考）
│   ├── 01-title-generator.md   # 已同步到新版
│   ├── 02-body-generator.md    # 已同步到新版
│   ├── 03-persona-polisher.md  # ⚠️ 旧版，未同步
│   ├── 04-emotion-enhancer.md  # ⚠️ 旧版，未同步
│   ├── 05-layout-formatter.md  # ⚠️ 旧版，与现行排版规则冲突
│   └── 06-ai-deflavorer.md     # ⚠️ 旧版，未同步
├── scripts/
│   ├── wechat-gen-workflow.js       # 主工作流（prompt 内联，唯一事实来源）
│   └── wechat-gen-promo-workflow.js # 小程序推广单篇文
├── config/
│   ├── persona.md              # 账号人设 + 固定文案镜像
│   └── topics.md               # 主题池（8 大类，未指定 --topic 时从这里选）
└── output/
    └── YYYY-MM-DD/             # 按日期归档的文章
        ├── _summary.md         # 每日摘要
        └── article-01-xxx.md   # 当日成稿（1 篇）
```

## 人设定位
**「轻食小本」的作者小轻** — 靠自己摸索瘦了 32 斤的普通女生，不是专家。
第三人称自称"小轻"，口语化、有温度、不贩卖焦虑。
固定数字 **138 斤 → 106 斤、瘦了 32 斤**，每篇必带且全文一致。

## 正文结构（2026-09-21 起不固定）
```
① 标题重排区        ← 脚本拼接
② 正文              骨架由 agent 按选题自定，顺序/分段/小标题全自由
③ 免责声明 / ④ 结尾转化块   ← 脚本拼接
```
正文必须含 5 样保底要素：体重数字+否定式排比 / 一个有人的具体场景 / 带数字的道理（≥2 角度+机制名词）/
落到量的吃法（早中晚+加餐）/ 真心话。参考结构池：问答式 / 时间线式 / 自检式 / 对照式 /
误区破除式 / 采购视角式 / 算账式。

⚠️ **反雷同是硬规定**（原先写死 8 段顺序 + 清单 5 拍，导致 09-18~09-21 四篇 8 处句子逐字重复）：
套话黑名单在 `scripts/wechat-gen-workflow.js` 的 `STOCK_PHRASES`；
往期小标题/标题由 skill 读 `output/` 经 `args.avoidHeadings` / `args.avoidTitles` 传进 prompt。

详细写法见 `config/persona.md` 的「文章结构」。

## 自定义
- 改人设、固定数字、固定文案块：编辑 `config/persona.md`，**并同步到 `scripts/wechat-gen-workflow.js` 顶部**
- 加/改选题方向：编辑 `config/topics.md`（只被 Skill 读取，脚本不读）
- 改各步骤生成策略：编辑 `scripts/wechat-gen-workflow.js` 里的内联 prompt（脚本不读 `prompts/`）
- 配图：正文里的 `【配图：画面描述】` 占位符由使用者照描述自行找图
