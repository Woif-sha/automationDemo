# GitHub Actions 入门与应用场景

本仓库是一个空的 Git 仓库，所以这里用一个不依赖具体编程语言的示例来说明 GitHub Actions。把这些文件推送到 GitHub 后，GitHub 会按 `.github/workflows/*.yml` 里的配置自动运行任务。

## GitHub Actions 适合做什么

GitHub Actions 的核心用途是：当仓库发生某些事件时，自动执行一组命令。

常见应用场景：

- 持续集成：每次 `push` 或 Pull Request 时自动运行测试、类型检查、格式检查。
- 自动构建：把前端、后端、桌面程序、Docker 镜像等构建出来。
- 自动发布：打 tag 后发布 npm 包、Docker 镜像、GitHub Release 或部署到服务器。
- 文档检查：检查 Markdown、链接、拼写，或者自动生成文档站点。
- 定时任务：每天定时同步数据、生成报告、扫描依赖漏洞。
- 仓库自动化：自动标记 issue、关闭过期 PR、根据模板检查 PR 内容。

## 核心概念

一个 GitHub Actions 工作流通常包含这些部分：

- `name`：工作流名称，显示在 GitHub 的 Actions 页面。
- `on`：触发条件，例如 `push`、`pull_request`、`workflow_dispatch`。
- `jobs`：要执行的任务集合。
- `runs-on`：运行环境，例如 `ubuntu-latest`、`windows-latest`。
- `steps`：任务里的具体步骤，每一步可以运行命令，也可以使用别人写好的 action。
- `uses`：复用现成 action，例如 `actions/checkout@v5` 用来拉取仓库代码。
- `run`：执行 shell 命令。

## 本仓库示例

示例文件在：

```text
.github/workflows/demo-ci.yml
```

它做了三件事：

1. 在 `push` 到 `main` 分支时运行。
2. 在面向 `main` 分支的 Pull Request 时运行。
3. 允许你在 GitHub 网页上手动点击运行。

这个示例不假设仓库使用任何语言，只检查本文档是否存在，并输出仓库上下文。这样即使当前仓库是空项目，也可以直接运行。

## 如何使用

1. 把本仓库推送到 GitHub。
2. 打开 GitHub 仓库页面。
3. 进入 `Actions` 标签页。
4. 选择 `Demo CI` 工作流。
5. 可以通过提交代码自动触发，也可以点击 `Run workflow` 手动触发。

## 如何扩展到真实项目

如果以后本仓库变成 Node.js 项目，可以把检查步骤换成：

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: 20
- run: npm ci
- run: npm test
```

如果是 Python 项目，可以换成：

```yaml
- uses: actions/setup-python@v5
  with:
    python-version: "3.12"
- run: pip install -r requirements.txt
- run: pytest
```

如果是 Docker 镜像构建，可以加入：

```yaml
- run: docker build -t demo-app .
```

## 常用触发方式

```yaml
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:
  schedule:
    - cron: "0 1 * * *"
```

说明：

- `push`：提交推送后运行。
- `pull_request`：创建或更新 PR 后运行。
- `workflow_dispatch`：允许手动运行。
- `schedule`：按 UTC 时间定时运行。

## 需要注意的点

- GitHub Actions 只会在 GitHub 远程仓库中运行，本地 Git 仓库不会自动运行它。
- workflow 文件必须放在 `.github/workflows/` 目录下。
- 免费额度、并发数、运行时间限制取决于仓库和账号类型。
- 涉及密码、token、服务器密钥时，不要写进 YAML 文件，应该放到 GitHub 仓库的 `Settings > Secrets and variables > Actions`。
- 如果 workflow 要部署生产环境，建议先让测试 job 通过，再执行部署 job。
