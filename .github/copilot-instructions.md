# Copilot 指南：快速让 AI 代理在此仓库中立即有用

目的：为 AI 编码代理提供可执行、仓库具体的上下文，帮助快速定位架构、关键工作流、约定和修改点。

- **大体概览**
  - 这是一个静态网站发布产物仓库（预渲染 HTML/JS/CSS）。仓库包含多语言站点目录（例如 `zh-cn/`, `zh-tw/`, `ja/`, `en/`），并含有 `themes/`、`post/`、`page/` 等静态内容目录。
  - 资产经常是编译/打包后的产物：`ts/` 下是已编译的 JS（例如 `ts/main.<hash>.js`, `ts/search.js`），`scss/` 下有已压缩的 CSS（例如 `scss/style.min.<hash>.css`）。仓库看起来像 Hugo 或类似静态站点生成器的输出（`themes/`、`sitemap.xml`、`index.xml`、`about-hugo/` 等提示）。

- **关键文件 / 关注路径（优先级）**
  - `search/index.json` — 客户端搜索索引，前端 `ts/search.js` 会读取它。修改搜索数据需更新此文件。
  - `ts/` — 前端脚本（已编译）。若修改行为优先查看这里；若想从源头修改需找到上游 TypeScript 源码（不在此仓库）。
  - `scss/style.min.*.css` — 压缩的样式文件，通常由 SCSS 源文件构建生成。直接编辑此文件会生效，但更推荐找到源构建链。
  - 多语言内容目录（`zh-cn/`, `ja/`, `en/` 等）— 每种语言为独立预渲染目录，注意路径/文件名含空格或非 ASCII（例如 `关于/`）。

- **可执行的开发/调试步骤（发现式、先验证）**
  - 若只是本地查看/调试静态输出，快速启动静态服务器：

    ```bash
    python -m http.server 8000
    # or
    npx http-server -p 8000
    ```

  - 若仓库为 Hugo 输出（需要你确认）：尝试在项目源仓库或上层目录查找 `config.toml` / `config.yaml` / `content/` 源码并使用：

    ```bash
    hugo server -D
    hugo --minify
    ```

  - 修改前端交互（搜索、UI 脚本）：编辑 `ts/search.js` 和 `ts/main*.js`（注意这些是编译产物）。如果你需要长期维护，要求用户提供 TypeScript 源或构建步骤。

- **约定与陷阱（项目特有）**
  - 预渲染输出：多处 HTML 已包含对带哈希资产的引用（example: `scss/style.min.833d6e...css`、`ts/main.c922af6...js`）。直接替换文件内容通常安全，但若更改文件名或引入新构建产物，需要同时更新所有 HTML 引用。
  - 路径與編碼：部分目录使用空格或非拉丁字符（`test-with-whitespaces/`, `关于/`），修改或生成新的路径时要保持与 URL 编码一致。
  - 搜索索引与客户端耦合：`search/index.json` 与 `ts/search.js` 是单点耦合，更新索引格式会破坏客户端检索，变更前请先检查 `ts/search.js` 的解析逻辑。

- **集成点 / 外部依赖（可见）**
  - 没有在仓库中发现 CI/workflow 或构建脚本（例如 `.github/workflows` 或 `package.json`）。因此：
    - 若要做可重复构建，请咨询是否存在上游源码仓库或构建流水线。
    - 目前可用的集成点以静态文件为主，浏览器端通过 `search/index.json`、直接引用 `ts/` 和 `scss/` 资产实现交互。

- **示例场景与建议操作**
  - 修复搜索缺陷：先查看 `ts/search.js` 中解析索引的实现，再在 `search/index.json` 做最小改动并在浏览器中验证。
  - 更新样式的快速方法：临时编辑 `scss/style.min.*.css` 并用静态服务器验证；长期应要求提供 SCSS 源与构建步骤。
  - 增加新语言页面：在仓库中新建相同目录结构（例如 `fr/`）并添加预渲染 HTML，确保 `sitemap.xml` 与索引页一致。

- **当你不确定时应如何询问/记录**
  - 如果找不到构建源或 CI：询问“哪里保存了 Hugo（或其它 SSG）的源文件及构建脚本？”。
  - 如果要改动哈希命名的静态资产：先询问是否存在自动化构建，会不会覆盖手动更改。

请检查上述要点是否覆盖了你期望 AI 代理掌握的关键信息；我可以根据你的反馈迭代这份文件（例如加入确切的构建命令、CI 路径或 TypeScript 源位置）。
