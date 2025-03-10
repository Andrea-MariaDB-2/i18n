# 合并请求

* [设置您的本地环境](#setting-up-your-local-environment)
  * [步骤1: 复制](#step-1-fork)
  * [步骤2: 构建](#step-2-build)
  * [步骤3: 分支](#step-3-branch)
* [进行更改](#making-changes)
  * [步骤4: 编写代码](#step-4-code)
  * [步骤5: 提交更改](#step-5-commit)
    * [提交代码说明的指导](#commit-message-guidelines)
  * [步骤6：变基](#step-6-rebase)
  * [步骤7: 测试](#step-7-test)
  * [步骤8: 推送代码](#step-8-push)
  * [步骤9: 新建一个合并代码请求](#step-9-opening-the-pull-request)
  * [步骤10: 讨论和更新](#step-10-discuss-and-update)
    * [批准和请求更改工作流程](#approval-and-request-changes-workflow)
  * [步骤11: 执行合并](#step-11-landing)
  * [持续集成测试](#continuous-integration-testing)

## 设置您的本地环境

### 步骤1: 复制

在 [GitHub](https://github.com/electron/electron) 上复制项目到你的账号并把项目克隆到本地。

```sh
$ git clone git@github.com:username/electron.git
$ cd electron
$ git remote add upstream https://github.com/electron/electron.git
$ git fetch upstream
```

### 步骤2: 构建

根据您的操作系统, 项目构建步骤和依赖项稍有不同。 请参阅这些关于构建 Electron 项目的详细指南:

* [在 macOS 上构建](build-instructions-macos.md)
* [在 Linux 上构建](build-instructions-linux.md)
* [在 Windows 上构建](build-instructions-windows.md)

一旦您在本地构建了项目, 就可以开始进行更改了!

### 步骤3: 分支

为了保持您的开发环境的组织, 创建本地分支来保存您的工作。 应该直接从 ` master ` 分支上创建您的分支。

```sh
$ git checkout -b my-branch -t upstream/master
```

## 进行更改

### 步骤4: 编写代码

Most pull requests opened against the `electron/electron` repository include changes to either the C/C++ code in the `shell/` folder, the JavaScript code in the `lib/` folder, the documentation in `docs/api/` or tests in the `spec/` folder.

请确保都运行 ` npm run lint ` 在任何代码更改后, 以确保它们遵循项目的代码样式。

在项目的不同部分修改代码时, 有关最佳实践的更多信息, 请参见 [ 编码样式 ](coding-style.md)。

### 步骤5: 提交更改

建议将更改按逻辑分组在每个独立提交中。 其他贡献者可以从拆分多个提交中更容易的检查代码的改变。 合并请求没有限制提交的数量。

```sh
$ git add my/changed/files
$ git commit
```

请注意, 在最后执行合并代码时多个提交通常会被合并。

#### 提交代码说明的指导

好的提交说明应描述更改的内容和原因。 Electron项目使用[语义化提交信息](https://conventionalcommits.org/)以精简发布过程。

Before a pull request can be merged, it **must** have a pull request title with a semantic prefix.

带有语义化前缀的提交信息的样例：

* `fix: don't overwrite prevent_default if default wasn't prevented`
* `feat: add app.isPackaged() method`
* `docs: app.isDefaultProtocolClient 现在在Linux可用`

常见前缀：

* fix: A bug fix
* feat: A new feature
* docs: Documentation changes
* 测试：添加缺失测试或纠正现有测试
* build: Changes that affect the build system
* ci: Changes to our CI configuration files and scripts
* perf: A code change that improves performance
* refactor: A code change that neither fixes a bug nor adds a feature
* style: Changes that do not affect the meaning of the code (linting)

其他在写提交信息时需要留意的事情：

1. 第一行应该:
   * contain a short description of the change (preferably 50 characters or less, and no more than 72 characters)
   * 完全使用小写, 除了适当的名词, 缩写, 和引用代码的单词, 如函数/变量名
2. 将第二行留空。
3. 每行文字在72列处换行。

#### 重大更改

A commit that has the text `BREAKING CHANGE:` at the beginning of its optional body or footer section introduces a breaking API change (correlating with Major in semantic versioning). 一个重大更改可以是任何类型提交的一部分。 e.g., a `fix:`, `feat:` & `chore:` types would all be valid, in addition to any other type.

参阅 [conventionalcommits.org](https://conventionalcommits.org) 以获得更多细节。

### 步骤6：变基

提交更改后, 最好使用 ` git rebase ` (而不是 ` git merge `) 将您的工作与主代码库同步。

```sh
$ git fetch upstream
$ git rebase upstream/master
```

这可确保您的工作分支具有来自 ` electron/electron ` master 分支的最新更改。

### 步骤7: 测试

Bug 修复和功能应始终与测试一起进行。 提供了 [ 测试指南 ](testing.md) 使流程更容易。 看其他测试, 看看它们应该如何结构化也会有帮助。

当提交您的改变到合并代码请求前, 请始终运行完整的测试套件。 运行测试:

```sh
$ npm run test
```

请确保 linter 不报告任何问题, 并且所有测试都通过。 请不要提交失败的修补程序或检查。

If you are updating tests and want to run a single spec to check it:

```sh
$ npm run test -match=menu
```

上述操作只运行与 ` menu ` 匹配的规范模块, 这对于那些在测试周期的末尾进行测试的任何人都很有用。

### 步骤8: 推送代码

一旦您的提交准备就绪--通过测试和 linting--将您的工作分支推送到您在 GitHub 上复制的分支, 然后开始开启合并代码请求的过程。

```sh
$ git push origin my-branch
```

### 步骤9: 新建一个合并代码请求

从 GitHub 中, 开启一个新的合并代码请求将为您呈现一个模板，这应填写如下:

```markdown
<!--
Thank you for your pull request. Please provide a description above and review
the requirements below.

Bug fixes and new features should include tests and possibly benchmarks.

Contributors guide: https://github.com/electron/electron/blob/master/CONTRIBUTING.md
-->
```

### 步骤10: 讨论和更新

您可能会得到反馈或请求更改您的请求。 这是提交过程的重要部分, 所以不要气馁! 有些贡献者可能会立刻签署请求。 其他人可能有详细的评论或反馈。 这是过程的必要部分, 以便评估更改是否正确和必要。

要更改现有的请求, 请对本地分支进行更改, 添加新的提交, 并将这些更改推送到您的分支。 GitHub 将自动更新请求。

```sh
$ git add my/changed/files
$ git commit
$ git push origin my-branch
```

一些更高级的方法比如使用 ` git rebase ` 来管理提交是很有效的, 但这超出本指南的范围。

如果您正在等待某事的答案, 请随时添加评论 ping 向审阅者。 如果您遇到的单词或缩略词似乎不熟悉, 请参阅此 [ 词汇表 ](https://sites.google.com/a/chromium.org/dev/glossary)。

#### 批准和请求更改工作流程

All pull requests require approval from a [Code Owner](https://github.com/electron/electron/blob/master/.github/CODEOWNERS) of the area you modified in order to land. Whenever a maintainer reviews a pull request they may request changes. These may be small, such as fixing a typo, or may involve substantive changes. Such requests are intended to be helpful, but at times may come across as abrupt or unhelpful, especially if they do not include concrete suggestions on *how* to change them.

尽量不要气馁。 如果你觉得审查是不公平的, 那么说或者寻求另一个项目参与者的投入。 通常, 这种评论是由于审阅者没有足够的时间来审查和无意的。 这样的困难往往可以用一点耐心来解决。 That said, reviewers should be expected to provide helpful feedback.

### 步骤11: 执行合并

为了合并代码, 代码合并请求要求必须由至少一个 Electron 代码所有者审查和批准并且通过 CI。 之后, 如果没有其他参与者的异议, 请求可以合并。

恭喜您, 感谢您的贡献!

### 持续集成测试

每个请求都在连续集成 (CI) 系统上进行测试, 以确认它在 Electron 支持的平台上工作。

理想情况下, 代码合并请求将在 CI 的所有平台上通过测试 ("变成绿色")。 这意味着所有测试都通过, 并且没有 linting 错误。 然而，CI 自身的基础设施在特定的平台上或者在"不可靠"的测试下会失败("变红") 的情况并不少见。 必须手动检查每个 CI 故障以确定原因。

CI starts automatically when you open a pull request, but only core maintainers can restart a CI run. If you believe CI is giving a false negative, ask a maintainer to restart the tests.
