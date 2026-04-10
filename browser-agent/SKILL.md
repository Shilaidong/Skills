---
name: browser-agent
description: 浏览器自动化助手。用自然语言描述你想对浏览器做的事情，自动帮你完成。
allowed-tools: Bash(npx agent-browser:*), Bash(agent-browser:*)
---

# 浏览器自动化助手

当用户用自然语言描述浏览器操作时，使用 agent-browser 自动完成。

## 使用方式

用户只需要用自然语言描述想要做的事情，例如：
- "帮我打开谷歌"
- "搜索 AI 新闻"
- "打开百度并搜索天气"
- "截图"
- "打开youtube"

## 自动处理流程

1. 如果浏览器未打开，先用 `agent-browser --headed open <url>` 打开网页（加 --headed 可以看到浏览器窗口）
2. 用 `agent-browser snapshot -i` 获取页面元素
3. 根据用户需求自动选择操作：
   - 搜索 → fill + click 搜索按钮
   - 点击链接/按钮 → click @eN
   - 输入文字 → fill @eN "内容"
   - 截图 → screenshot
   - 滚动 → scroll down/up
   - 获取信息 → get text @eN
4. 等待页面加载后返回结果

## 示例

用户说 "打开谷歌搜索AI新闻"：
```bash
agent-browser open "https://www.google.com"
agent-browser wait --load networkidle
agent-browser snapshot -i
# 找到搜索框，输入内容，点击搜索
```

用户说 "截个图"：
```bash
agent-browser screenshot
```

用户说 "点击第一个链接"：
```bash
agent-browser click @e1
```

## 注意事项

- 每次点击或导航后都需要重新 snapshot 获取新元素
- 使用 --session-name 参数可以保存登录状态
- 有问题随时截图查看页面状态
