# Jude Research Portfolio (Jekyll)

本仓库是基于 Jekyll 的研究者作品集，已采用单页分区与数据驱动的结构，并完成 Sass 现代化（@use/@forward 与 color.adjust/color.scale）。

## 开发环境

- Ruby 3.2+/3.3（Homebrew）
- Bundler（最新版）

## 本地运行

### 安装 Homebrew Ruby 并运行
```bash
# 安装/升级 Ruby（arm64 下通常为 /opt/homebrew）
brew update
brew upgrade ruby || brew install ruby

# 放到 PATH 前（zsh）
echo 'export PATH="$(brew --prefix)/opt/ruby/bin:$PATH"' >> ~/.zshrc
echo 'export GEM_HOME="$HOME/.gem"' >> ~/.zshrc
echo 'export PATH="$GEM_HOME/bin:$PATH"' >> ~/.zshrc
exec zsh

# 安装依赖并启动
cd /Volumes/macOSexternal/Documents/proj/jude-portfolio
gem install bundler
bundle config set path 'vendor/bundle'
bundle install
bundle exec jekyll serve --livereload
```

访问 http://localhost:4000 查看站点。

可选：为静默 Ruby 3.5 相关提示，可在 Gemfile 中增加 `gem "logger", "~> 1.6"` 后 `bundle install`。

注意：不建议卸载系统自带 Ruby（macOS 组件依赖）。仅通过 PATH 使用 Homebrew Ruby 即可。

## 站点架构（单页 + 数据驱动）

- `index.md`：主页，包含四个分区锚点（About、Experience、Projects、Publications）。侧边导航通过锚点跳转。
- `_data/`：结构化数据源
  - `profile.yml`：侧边栏个人信息与社交等
  - `experience.yml`：时间线（工作/实习/研究）
  - `publications.yml`：出版物条目
- `_projects/`：项目集合（Collection）。每个 Markdown 生成独立详情页，布局见 `_layouts/project.html`。
- `_layouts/`：
  - `default.html`：全局布局（侧边栏 + 主内容区）
  - `project.html`：项目详情布局
- `assets/`：静态资源
  - `css/main.css`：当前站点生效的样式文件（自定义样式）
  - `js/main.js`：交互脚本
  - `main.scss` 与 `_sass/`：已引入现代 Sass 模块化（详见下文），用于迁移与扩展

说明：历史的独立页面 `about.md` 与 `education.md` 已移除（内容均由首页分区承载），避免重复路由与 SEO 冲突。

## 内容维护指南

- 新增/编辑项目：在 `_projects/` 新建 `.md` 文件，前置字段至少包含 `title`，可选 `date`、`summary` 等。
- 编辑经历：在 `_data/experience.yml` 中增删条目，字段包含 `period`、`role`、`company`、`summary`、`stack`。
- 编辑出版物：在 `_data/publications.yml` 中增删条目，字段包含 `title`、`venue`、`year`、`url`。
- 主页列表数量：`index.md` 中通过 `limit: 9` 控制项目/出版物展示数量。

## 样式与 Sass 现代化

- 站点当前使用的样式文件为 `assets/css/main.css`。（保留以保证现有 UI 样式稳定）
- 已新增可扩展的 Sass 结构（可选使用）：
  - `assets/main.scss`：入口文件，使用 `@use` 组织模块
  - `assets/_sass/minima/`：本地化的主题样式片段与变量
  - 用 `@use` 取代 `@import`，并以 `color.adjust/color.scale` 替代旧的 `lighten/darken`
- 若需要迁移到 Sass 版本的样式：将布局中的 CSS 引用从 `assets/css/main.css` 切换为构建产物 `assets/main.css` 即可（建议在分支中验证 UI 一致性）。

## 部署

常用方式：GitHub Pages、Netlify、Vercel（静态导出）。


