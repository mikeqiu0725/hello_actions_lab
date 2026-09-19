# GitHub Actions：运行和更新 Python

第一课已上传 GitHub 并手动运行成功。当前进入第二课：推送到 main 自动运行；仍没有创建 AWS 资源。

## 第二课：当前要做的操作

当前脚本输出已改为 `Hello World v2`，workflow 已加入 `push` 到 `main` 的触发规则。下面第一课部分保留为学习记录，其手动触发说明描述的是旧版本。

关键提醒：这次 push 本身就会触发更新后的 workflow，无需再点 Run workflow。先检查差异，再执行：

```sh
git --no-pager diff
git add hello_world.py .github/workflows/hello.yml README.md
git diff --cached --stat
git commit -m "Run Hello World v2 automatically on push"
git push
```

去 GitHub Actions 查看自动出现的新运行，确认事件为 push、提交对应本次 commit，并在步骤 3 看到 `Hello World v2`。只做本地 commit 不会触发。

公司项目中，push 后是否部署 QA / Prod，由 jobs、if 条件、复用 workflow 和 environment 等共同决定；不能仅凭 `on: push` 判断。这个练习只执行 Python，没有部署步骤或 AWS 身份。

---

以下为第一课操作记录（当时脚本输出为 `Hello World`，仅手动触发）：
打开 VS Code 后，先读本文件，再打开 `.github/workflows/hello.yml`，按中文注释理解每一步。

## 1. 在本机运行

在 VS Code 菜单选择“终端 → 新建终端”，确认终端当前目录是 `hello-actions-lab`，然后输入：

```sh
python3 hello_world.py
```

预期输出 `Hello World`。这一步在你的 Mac 上执行，不涉及 GitHub 或 AWS。

## 2. 放入 GitHub 练习仓库

关键提醒：选公开仓库并使用此 workflow 的标准 runner；练习内容公开可见，不放个人资料、工作代码或密钥。私有仓库有不同的免费额度规则。

在 GitHub 创建一个空的公开仓库，例如 `hello-actions-lab`。不要勾选初始化 README、许可证或 .gitignore，因为本地已有文件。

本练习文件夹位于另一个工作区里面。以下命令必须在 `hello-actions-lab` 内运行，让它成为独立仓库；不要在上一级目录执行。当前还没有替你创建 GitHub 仓库或提交代码。

```sh
git init -b main
git add README.md hello_world.py .gitignore .github/workflows/hello.yml
git diff --cached
```

关键检查：暂存内容应只有这四个学习文件，没有密钥。确认后：

```sh
git commit -m "Add annotated Hello World workflow"
```

随后按照 GitHub 新仓库页面的提示执行 `git remote add origin ...` 和 `git push -u origin main`。地址必须使用你自己的仓库地址。若 GitHub 要求登录，使用其正常登录流程，不把 token 写进文件或发到聊天里。

注意：GitHub 仓库根目录应直接包含 `hello_world.py` 和 `.github`，不能再包一层 `hello-actions-lab`。

## 3. 手动运行并观察

1. 打开仓库的 Actions 页面。
2. 选择 `01 - Hello World 运行练习`。
3. 点击 Run workflow，选择 main，再确认运行。
4. 打开运行记录和任务，展开步骤 3，确认输出 `Hello World`。
5. 回到运行摘要页，查看步骤 4 写入的学习提醒。

这一阶段的流程：手动触发 → GitHub 分配临时 Linux runner → 拉取代码 → 准备 Python → 执行脚本 → 结束任务。
YAML 的 `uses` 调用现成 Action，`run` 执行命令，`with` 传参数；缩进决定层级，使用空格而不是 Tab。

## 4. 练习一次更新

将脚本输出改为 `Hello World v2`，保存，然后：

```sh
git add hello_world.py
git commit -m "Update greeting to v2"
git push
```

再次手动运行 workflow，预期输出 `Hello World v2`。旧运行记录仍保留原来的输出，因为每次运行对应具体的代码版本。

关键区别：保存只修改本机文件；commit 记录本地版本；push 上传到 GitHub；Run workflow 执行云端流程。这里还没有部署到 AWS。

## 后续两课：Terraform 和 AWS

等第一课跑通后，再逐步添加，避免同时学习太多概念：

1. Terraform 管理一个 Lambda：先解释配置，再查看 `terraform plan` 的新增/修改/删除清单，最后部署并手动调用。
2. GitHub Actions 自动部署：配置 OIDC 临时身份、限定仓库和分支权限；先明确 Terraform 状态保存方式，再加入部署步骤。runner 每次都是新的，不能假定上次的本地状态还在。

费用提醒：保持 AWS Free plan，不升级 Paid plan，不加入可能改变账户计划的组织或服务。部署前再次核对账户计划、免费期和剩余额度；这里不执行任何 AWS 命令。Free plan 的期限和额度不是永久免费。

后续不需要 EC2、公网 HTTP 入口、定时任务或一直运行的程序。部署、调用、更新、删除会分别解释。清理前查看 Terraform 删除计划，只删除本练习创建的资源。

## 官方参考

- [GitHub Actions 计费](https://docs.github.com/en/billing/concepts/product-billing/github-actions)
- [GitHub Python 工作流示例](https://docs.github.com/en/actions/tutorials/build-and-test-code/python)
- [AWS Free Tier 说明](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/free-tier.html)
