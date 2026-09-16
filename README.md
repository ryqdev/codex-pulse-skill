# Codex Pulse Skill

让 Codex 调用 Pelican CLI，分析“鹈鹕骑自行车”HTML/SVG、运行生成评测、解释评分并比较历史报告。
这是从 Pelican Test 项目独立出来的 Skill，调用名称保持为 `$codex-pulse`。

## 安装

通过 [Skills CLI](https://github.com/vercel-labs/skills#options) 安装到 Codex：

```bash
npx skills add ryqdev/codex-pulse-skill --skill codex-pulse --agent codex -g
```

`-g` 表示全局安装，去掉则只安装到当前项目。已从旧仓库安装 `codex-pulse` 的用户，
重新执行上面的命令并确认覆盖，即可切换安装来源。若仍安装着旧名 `pelican-test`，
先移除旧 Skill，再安装 `codex-pulse`。安装后如果 Codex 未发现 Skill，重启后再试。

Skill 文件位于 [skills/codex-pulse](skills/codex-pulse/SKILL.md)，包含运行说明和 Codex UI 元数据。

## 运行环境

- 分析 HTML/SVG 或运行新评测：需要 Node.js 和 npm/npx。以所选 CLI 包的 `engines` 为准，
  可用 `npm view @ryqdev/pelican-test@latest engines --json` 查看已发布版本要求。
- 运行新评测：还需要本机已安装并配置好 Codex，使用当前账号或 provider 的额度。
- 读取或比较已有 JSON 报告：直接读取文件，无需启动 CLI 或调用模型。

Skill 默认通过 `npx --yes @ryqdev/pelican-test@latest` 调用已发布的 CLI，
无需克隆 Pelican Test 源码、安装 pnpm 或手动构建。

## 使用

在 Codex 对话中输入：

```text
$codex-pulse 分析 result.html，并解释主要扣分项。
$codex-pulse 用当前 Codex 配置跑一次评测，返回评分和报告路径。
$codex-pulse 解读 .pelican/runs/<run-id>/report.json。
$codex-pulse 比较 report-a.json 和 report-b.json，说明评分差异。
$codex-pulse 跑一次评测，并把结果带回 Codex Pulse 发布。
```

文件路径和输出目录均相对于当前项目。新评测默认生成一次，使用本机 Codex 配置，
测试的是新启动的 Codex 会话。报告和生成产物默认保存在 `.pelican/runs/<run-id>/`。

也可以直接在终端使用 CLI：

```bash
npx --yes @ryqdev/pelican-test@latest analyze result.html --json
npx --yes @ryqdev/pelican-test@latest doctor --json
npx --yes @ryqdev/pelican-test@latest run --json
npx --yes @ryqdev/pelican-test@latest --help
```

开发者如需测试已有的本地 CLI 源码，可在请求中提供源码仓库的绝对路径；
Skill 会按该仓库的 `package.json` 准备和调用本地 CLI。

## 发布到网站

```bash
npx --yes @ryqdev/pelican-test@latest connect --site https://codex-pulse.com --port 0
```

连接器打开配对页面；连接电脑、开始测试，然后将 HTML、评分和预览图带回发布草稿。
登录并确认后提交，审核通过才会公开。该流程需要网站的连接器集成版本上线。
已有文件或使用其他 agent 时，先 `analyze`，再到网站上传截图和可选 HTML；
`run` / `connect` 当前只调用本地 Codex，不代表运行 Skill 的客户端模型。

遇到镜像 registry 的 E404，可在 npx 命令加上 `--registry=https://registry.npmjs.org` 重试。
无需改变全局 npm 配置或关闭 Node 版本校验。

## 评分范围

总分 100：完整性 25、SVG 丰富度 35、动画 25、工程质量 15。
评分来自静态 HTML/SVG 规则，不能单独证明模型降智，也不能确认画面正确或动画实际运行。

## 许可证

沿用原项目的 LGPL-3.0-only 许可证，见 [LICENSE](LICENSE) 和 [COPYING](COPYING)。
本仓库分发 Skill 指令和元数据；Pelican CLI 作为独立 npm 包在使用时下载。
