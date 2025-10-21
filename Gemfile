source "https://rubygems.org"

# 明确指定 Jekyll 与主题、插件
# 选择与 Jekyll 4.4.x 兼容的版本以匹配 vendored 依赖和 minima 主题

gem "jekyll", "~> 4.4.0"

group :jekyll_plugins do
  gem "jekyll-feed", "~> 0.17"
  gem "jekyll-seo-tag", "~> 2.8"
  gem "jekyll-sitemap", "~> 1.4"
end

# 站点主题（与 _config.yml 中 theme: minima 一致）
# minima 2.5.x 与 Jekyll 4 兼容

gem "minima", "~> 2.5"

# 可选：本地开发需要的 Ruby 标准库 gems（通常随 Ruby 提供，此处不显式列出）

# 使用 Bundler 的 vendor 路径（项目已包含 vendor/bundle 结构）
# 在 CLI 使用：bundle config set path 'vendor/bundle'
