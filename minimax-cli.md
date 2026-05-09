---
name: minimax-cli
description: MiniMax 多模态 AI 能力集成。通过 mmx-cli 提供文本对话、图像生成、视频生成、音乐生成、语音合成、图像理解、网络搜索等功能。当用户请求涉及 MiniMax 相关能力时自动调用：生成图片、文生图、画图、生成视频、文生视频、生成音乐、歌曲、语音合成、朗读、文字转语音、看图、理解图片、分析图片、搜索、查询、查一下。
---

# MiniMax CLI

通过 `mmx-cli` 调用 MiniMax 多模态 AI 能力。

## 命令速查

| 能力 | 命令 | 触发关键词 |
|------|------|------------|
| 文本对话 | `mmx text chat --message "<内容>"` | 对话、聊天、问问 |
| 图像生成 | `mmx image "<描述>"` | 生成图片、文生图、画图 |
| 视频生成 | `mmx video generate --prompt "<描述>"` | 生成视频、文生视频 |
| 音乐生成 | `mmx music generate --prompt "<描述>" --out <文件>` | 生成音乐、歌曲 |
| 语音合成 | `mmx speech synthesize --text "<文本>" --out <文件>` | 语音合成、朗读、文字转语音 |
| 图像理解 | `mmx vision describe --image <路径或URL> --prompt "<问题>"` | 看图、理解图片、分析图片 |
| 网络搜索 | `mmx search "<查询>"` | 搜索、查询、查一下 |

## 语音合成

```bash
mmx speech synthesize --text "你的文本" --voice <音色ID> --out <输出文件>
```

**常用音色：**
- 中文：`Chinese (Mandarin)_Warm_Girl`、`female-tianmei`
- 英文：`English_Graceful_Lady`、`English_Trustworthy_Man`

**自定义音色（用户克隆声音）：**
| 音色ID | 说明 |
|--------|------|
| `lazz_voice_004` | 用户本人克隆声音 |

```bash
# 示例：使用用户克隆声音配音
mmx speech synthesize --text "文本内容" --voice lazz_voice_004 --out output.mp3
```

## 配额查询

```bash
mmx quota show
```

## 输出目录

所有生成文件保存在 `minimax-output/` 目录下。