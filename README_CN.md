# Claude Code（非官方源码提取）

[English](README.md) | [中文](README_CN.md)

> **这不是 Anthropic 的官方仓库。**

本仓库包含从 [Anthropic 的 Claude Code](https://www.anthropic.com/) CLI 工具中提取的 TypeScript 源代码 —— Anthropic 的官方命令行工具，让你可以直接在终端中与 Claude 交互，执行软件工程任务，如编辑文件、运行命令、搜索代码库、管理 git 工作流等。

源代码是通过解包官方发布的 npm 包中打包的 source map（`cli.js.map`）获得的。

- **npm 包：** [@anthropic-ai/claude-code v2.1.88](https://www.npmjs.com/package/@anthropic-ai/claude-code/v/2.1.88)
- **官方主页：** [github.com/anthropics/claude-code](https://github.com/anthropics/claude-code)

## 源码泄露过程

源代码泄露由 [Chaofan Shou (@Fried_rice)](https://x.com/Fried_rice) 于 2026 年 3 月 31 日发现并公开发布：

> *"Claude code source code has been leaked via a map file in their npm registry!"*
>
> — [@Fried_rice](https://x.com/Fried_rice)，2026 年 3 月 31 日

发布的 npm 包（`@anthropic-ai/claude-code`）包含一个 source map 文件（`cli.js.map`），其中包含完整、未混淆的 TypeScript 源代码。source map 的 `sourcesContent` 字件包含了被打包进 `cli.js` 的所有原始 `.ts`/`.tsx` 文件，使得整个代码库可以轻松提取。

## 为什么会有这个仓库？

Anthropic 在 npm 上发布打包后的 JavaScript CLI。发布的包包含一个 source map 文件（`cli.js.map`），其中包含原始 TypeScript 源代码。本仓库只是提取并保存这些源代码，以便更方便阅读和参考。

## 如何获取源码

### 克隆本仓库

```bash
git clone https://github.com/RadishoNeo/Claude-Code.git
cd Claude-Code
```


### 或者自行从 npm 提取

1. **安装包：**

```bash
mkdir claude-code-extract && cd claude-code-extract
npm pack @anthropic-ai/claude-code@2.1.88
tar -xzf anthropic-ai-claude-code-2.1.88.tgz
cd package
```

2. **运行解包脚本：**

创建一个名为 `unpack.mjs` 的文件：

```js
import { readFileSync, writeFileSync, mkdirSync } from "fs";
import { dirname, join } from "path";

const mapFile = join(import.meta.dirname, "cli.js.map");
const outDir = join(import.meta.dirname, "unpacked");

console.log("Reading source map...");
const map = JSON.parse(readFileSync(mapFile, "utf-8"));

const sources = map.sources || [];
const contents = map.sourcesContent || [];

console.log(`Found ${sources.length} source files.`);

let written = 0;
let skipped = 0;

for (let i = 0; i < sources.length; i++) {
  const src = sources[i];
  const content = contents[i];

  if (content == null) {
    skipped++;
    continue;
  }

  const outPath = join(outDir, src.replace(/^\.\.\//g, ""));
  mkdirSync(dirname(outPath), { recursive: true });
  writeFileSync(outPath, content);
  written++;
}

console.log(`Done! Wrote ${written} files to ${outDir}`);
if (skipped > 0) console.log(`Skipped ${skipped} files with no content.`);
```

3. **运行脚本：**

```bash
node unpack.mjs
```

提取的源代码将位于 `unpacked/` 目录中。

## 项目结构

```
src/
├── cli/           # CLI 入口和参数解析
├── commands/      # 命令实现
├── components/    # UI 组件（Ink/React）
├── constants/     # 应用常量和配置
├── context/       # 上下文管理
├── hooks/         # React hooks
├── ink/           # 终端 UI（Ink 框架）
├── services/      # 核心服务
├── skills/        # Skill 定义
├── tools/         # 工具实现（文件编辑、搜索等）
├── types/         # TypeScript 类型定义
├── utils/         # 工具函数
├── main.tsx       # 主应用入口
├── query.ts       # 查询处理
└── ...
```

## 免责声明

本仓库中的所有代码均为 [Anthropic](https://www.anthropic.com/) 的知识产权。本仓库仅用于**教育和参考目的**。请参考 Anthropic 的[许可条款](https://www.npmjs.com/package/@anthropic-ai/claude-code/v/2.1.88)了解使用限制。

本仓库**不**隶属于 Anthropic，也未经 Anthropic 认可或支持。