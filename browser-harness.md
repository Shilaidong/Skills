---
name: browser-harness
description: 通过 browser-harness 直接控制用户的 Chrome 浏览器，执行自动化、网页抓取、测试、交互等任务。通过 CDP 连接到用户已运行的 Chrome，需要提前开启远程调试。
---

# Browser Harness

通过 CDP 控制用户已运行的 Chrome 浏览器。

## Git 地址
```
https://github.com/browser-use/browser-harness
```

## 安装

### 首次安装

```powershell
# 1. 克隆仓库到稳定位置（不要用 /tmp）
git clone https://github.com/browser-use/browser-harness "$env:USERPROFILE\Developer\browser-harness"

# 2. 安装 uv（如果没有）
pip install uv

# 3. 全局安装为工具
cd $env:USERPROFILE\Developer\browser-harness
uv tool install -e .

# 4. 验证安装
& "$env:USERPROFILE\.local\bin\browser-harness.exe" --version
```

### 浏览器连接设置（用户操作）

1. 打开 Chrome，访问 `chrome://inspect/#remote-debugging`
2. 勾选 "Allow remote debugging for this browser instance"
3. 完成

### 验证连接

```powershell
$env:BU_CDP_URL = "http://127.0.0.1:9222"
& "$env:USERPROFILE\.local\bin\browser-harness.exe" --doctor
```

正常输出：
```
[ok  ] chrome running
[ok  ] daemon alive
```

## 更新

```powershell
# 进入目录
cd $env:USERPROFILE\Developer\browser-harness

# 拉取最新代码
git pull --ff-only

# 重启守护进程
& "$env:USERPROFILE\.local\bin\browser-harness.exe" -c "restart_daemon()"
```

或一键更新：
```powershell
& "$env:USERPROFILE\.local\bin\browser-harness.exe" --update -y
```

> 注意：有未提交更改时拒绝更新，需先处理。

## 使用方法

### 每次使用前设置环境变量

```powershell
$env:BU_CDP_URL = "http://127.0.0.1:9222"
$env:PATH = "C:\Users\shido\.local\bin;$env:PATH"
```

### 调用格式

```bash
browser-harness -c '
# 任何 Python 代码，helpers 已预导入
# 守护进程自动启动
'
```

### 核心原则

- **坐标点击优先**：使用 `click_at_xy(x, y)` 而非查找 DOM
- **截图驱动**：用 `capture_screenshot()` 了解页面、快速定位目标
- **首次导航用 `new_tab()`**：避免覆盖用户当前工作
- **用 `goto_url()`** 替换当前标签页内容（需小心使用）
- **验证操作**：每次重要操作后重新截图确认状态

## 常用命令

| 功能 | 命令 |
|------|------|
| 打开网址（新标签） | `new_tab('https://example.com')` |
| 当前标签跳转 | `goto_url('https://example.com')` |
| 获取页面信息 | `print(page_info())` |
| 点击坐标 | `click_at_xy(x, y)` |
| 滚动 | `scroll(0, y)` |
| 输入文本 | `type_text('css selector', 'text')` |
| 填写输入框 | `fill_input('css selector', 'text')` |
| 截图 | `capture_screenshot()` |
| 新标签页 | `new_tab('https://...')` |
| 切换标签 | `switch_tab(tab_index)` |
| 列出所有标签 | `print(list_tabs())` |
| 提取页面文本 | `print(js('document.body.innerText'))` |
| 执行JS | `js('your code')` |
| 等待 | `wait(seconds)` |
| 等待加载 | `wait_for_load()` |
| 等待网络空闲 | `wait_for_network_idle()` |
| 按键 | `press_key('Enter')` |
| 获取当前URL | `print(js('location.href'))` |

## 故障排除

| 问题 | 解决 |
|------|------|
| `daemon alive FAIL` | 重启守护进程：`browser-harness -c "restart_daemon()"` |
| `chrome FAIL` | 用户需开启 Chrome 远程调试 |
| 页面未加载 | 加 `wait_for_load()` 等待 |
| 点击无效 | 用 `capture_screenshot()` 确认页面状态 |
| 标签陈旧 | 用 `ensure_real_tab()` 重新附着 |

## 架构说明

```
Chrome / Browser Use cloud → CDP WS → browser_harness.daemon → IPC → browser_harness.run
```

- 协议：每方 JSON 行
- Windows IPC：TCP loopback + 端口文件
- `BU_NAME` 命名空间守护进程的 IPC、pid 和日志文件
- `BU_CDP_URL` 覆盖本地 Chrome 发现（Way 2）
- 首次导航用 `new_tab()` 而非 `goto_url()`，避免覆盖用户当前工作

## 云浏览器（可选）

需要 `BROWSER_USE_API_KEY`。

```bash
browser-harness -c '
start_remote_daemon("work")  # 默认 — 干净浏览器，无 profile
start_remote_daemon("work", profileName="my-work")  # 复用云 profile
start_remote_daemon("work", proxyCountryCode="de", timeout=120)  # DE 代理
'

BU_NAME=work browser-harness -c '
new_tab("https://example.com")
print(page_info())
'
```

## 交互技能（Interaction Skills）

遇到特定交互难题时，查看 `agent-workspace/interaction-skills/` 目录的辅助技能：

- `connection.md` — 连接管理
- `cookies.md` — Cookie 处理
- `dialogs.md` — 对话框
- `downloads.md` — 下载
- `dropdowns.md` — 下拉菜单
- `iframes.md` — 内联框架
- `tabs.md` — 标签页
- `uploads.md` — 文件上传
- `scrolling.md` — 滚动
- `shadow-dom.md` — Shadow DOM
- `screenshots.md` — 截图

## 域名技能（Domain Skills，可选）

设置 `BH_DOMAIN_SKILLS=1` 启用社区贡献的网站特定 playbook。

当启用时，`goto_url` 返回导航主机的最多 10 个技能文件名。如果学到任何非显而易见的内容（私有 API、稳定选择器、框架怪癖、URL 模式、隐藏等待或网站特定陷阱），提交 PR 到 `agent-workspace/domain-skills/<site>/`。

## 设计约束

- 坐标点击默认
- 连接到用户正在运行的 Chrome，不要启动自己的浏览器
- `run.py` 保持简洁
- 核心辅助函数保持简短，任务特定代码放在 `agent-workspace/agent_helpers.py`
- 不添加管理框架、无重试框架、无会话管理器、无守护进程监督、无配置系统

## 常见陷阱（field-tested）

- omnibox 弹出框是假页面目标，筛选 `chrome://omnibox-popup...`
- CDP target 顺序 != Chrome 可见标签顺序
- 默认守护进程会话可能陈旧，用 `ensure_real_tab()` 重新附着
- 每个有意义操作后重新截图验证
- 截图驱动探索，快速找到下一个点击目标
- 优先使用复合级操作而非框架 hack