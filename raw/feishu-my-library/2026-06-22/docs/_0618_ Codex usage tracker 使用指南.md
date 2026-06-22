# [0618] Codex usage tracker 使用指南

## 这个工具是做什么的

Codex usage tracker 是一个本地用量分析工具。它会读取你电脑上的 Codex 本地日志，生成用量统计，并提供 Dashboard、命令行查询和 CSV 导出。

常用来看：

- 哪些 Codex 对话或任务消耗最多
- 不同模型用了多少 tokens 和 credits
- 哪些长线程因为上下文太大导致每轮都变贵
- 导出 CSV 后，进一步生成个人报告和团队报告



## 安装前准备

先确认电脑里有没有 Python。

macOS 打开 Terminal，复制执行：

```Bash
python3 --version
```

Windows 打开 PowerShell，复制执行：

```PowerShell
py --version
```

如果能看到类似 `Python 3.10.x`、`Python 3.11.x`、`Python 3.12.x` 的版本号，就可以继续。

如果没有 Python：

- macOS 可以先安装 Homebrew，再执行 `brew install python`。
- Windows 去 Python 官网安装 Python，安装时勾选 `Add python.exe to PATH`，装完后重新打开 PowerShell。



## macOS 安装

如果电脑有 Homebrew，复制执行：

```Bash
brew install pipx
pipx ensurepath
pipx install codex-usage-tracking
codex-usage-tracker setup
codex-usage-tracker serve-dashboard --open
```

如果没有 Homebrew，复制执行：

```Bash
python3 -m pip install --user pipx
python3 -m pipx ensurepath
pipx install codex-usage-tracking
codex-usage-tracker setup
codex-usage-tracker serve-dashboard --open
```

如果提示 `pipx: command not found` 或 `codex-usage-tracker: command not found`，关闭 Terminal，重新打开后再试一次。



## Windows 安装

打开 PowerShell，复制执行：

```PowerShell
py -m pip install --user pipx
py -m pipx ensurepath
```

关闭 PowerShell，重新打开一个新的 PowerShell，然后复制执行：

```PowerShell
pipx install codex-usage-tracking
codex-usage-tracker setup
codex-usage-tracker serve-dashboard --open
```

如果提示 `pipx` 找不到，改用这一条：

```PowerShell
py -m pipx install codex-usage-tracking
```

然后继续执行：

```PowerShell
codex-usage-tracker setup
codex-usage-tracker serve-dashboard --open
```

注意：安装包名是 `codex-usage-tracking`，命令名是 `codex-usage-tracker`。



## 安装后检查

复制执行：

```Bash
codex-usage-tracker doctor
```

如果没有明显报错，再打开 Dashboard：

```Bash
codex-usage-tracker serve-dashboard --open
```

正常情况下浏览器会自动打开本地 Dashboard。



## Dashboard 怎么看

打开 Dashboard 后，主要看这几个页面：

- `Insights`：快速看高消耗、高风险、缓存复用差的项目。
- `Calls`：每一次模型调用的明细，可以导出 CSV。
- `Threads`：按对话线程汇总，适合看长对话是不是越来越贵。

推荐先看 `Insights`，再看 `Threads`，最后到 `Calls` 导出明细。



## 常用命令

查看最近 7 天汇总：

```Bash
codex-usage-tracker summary --preset last-7-days
```

打开 Dashboard：

```Bash
codex-usage-tracker serve-dashboard --open
```

检查环境：

```Bash
codex-usage-tracker doctor
```



## 脚本是做什么的

当前有两个辅助脚本：

- `codex_usage_task_report.py`：把导出的 CSV 变成个人任务归因报告。
- `codex_usage_multi_report.py`：把多个人的个人报告合并成团队报告。

整体流程是：

```Plaintext
导出 CSV -> 生成个人报告 -> 收集多人报告 -> 生成团队报告
```



## 第一步：导出 CSV

先进入你放脚本的目录。

macOS 示例：

```Bash
cd ~/Desktop/codex_usage_tracker
codex-usage-tracker export --output codex-usage-insights-calls.csv
```

Windows 示例：

```PowerShell
cd C:\Users\你的用户名\Desktop\codex_usage_tracker
codex-usage-tracker export --output codex-usage-insights-calls.csv
```

如果你是从 Dashboard 里导出的 CSV，也把 CSV 放到这个脚本目录里，并命名为：

```Plaintext
codex-usage-insights-calls.csv
```



## **第二步：生成个人报告**

macOS 复制执行，把 `Alice` 改成自己的名字：

```Bash
python3 codex_usage_task_report.py codex-usage-insights-calls.csv --owner "Alice" -o Alice_task_report.md
```

Windows 复制执行，把 `Alice` 改成自己的名字：

```PowerShell
py codex_usage_task_report.py codex-usage-insights-calls.csv --owner "Alice" -o Alice_task_report.md
```

生成后会得到类似：

```Plain Text
Alice_task_report.md
```

每个人都这样生成自己的 `姓名_task_report.md`。



## **第三步：生成团队报告**

把所有人的 `*_task_report.md` 放到同一个目录。

macOS 复制执行：

```Bash
python3 codex_usage_multi_report.py *_task_report.md -o codex-usage-team-report.md
```

Windows PowerShell 复制执行：

```PowerShell
py codex_usage_multi_report.py *_task_report.md -o codex-usage-team-report.md
```

生成后会得到：

```Plain Text
codex-usage-team-report.md
```



## 常见问题

如果 Dashboard 没有数据，先确认这台电脑已经使用过 Codex，并且本地有 Codex 日志。

如果个人报告里显示“未匹配到本地日志”，通常是因为 CSV 不是这台电脑导出的，或者本地 Codex session 日志已经不存在。

如果 `python3` 找不到，macOS 先执行：

```Bash
python3 --version
```

如果 `py` 找不到，Windows 先执行：

```PowerShell
py --version
```

如果 `codex-usage-tracker` 找不到，先关闭并重新打开终端，再执行：

```Bash
codex-usage-tracker doctor
```
