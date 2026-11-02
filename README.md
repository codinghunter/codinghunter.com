# Coding Hunter

[codinghunter.com](https://codinghunter.com/) — 基于 Jekyll + [whiteglass](https://github.com/yous/whiteglass) 主题的个人博客。

## 本地开发

```bash
# Ruby 版本见 .ruby-version
gem install bundler
bundle install

bundle exec jekyll serve      # http://127.0.0.1:4000
bundle exec jekyll build      # 产物输出到 _site/
```

## 部署

站点使用了 `jekyll-archives`，它**不在 GitHub Pages 老式（legacy）构建的白名单里**，
且 Pages 内置的仍是 Jekyll 3.9.x。因此必须用 GitHub Actions 构建后再发布到 Pages：

1. 仓库 Settings → Pages → Source 选择 **GitHub Actions**
2. 添加工作流（示例）：

```yaml
name: Deploy Jekyll site

on:
  push:
    branches: [master]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ruby/setup-ruby@v1
        with:
          bundler-cache: true
      - run: bundle exec jekyll build
      - uses: actions/upload-pages-artifact@v3
        with:
          path: _site
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

3. 自定义域名：仓库根目录放 `CNAME` 文件（内容为 `codinghunter.com`），
   并在域名 DNS 侧按 Pages 要求配置 A/AAAA 或 CNAME 记录。

## 目录说明

| 路径 | 说明 |
| --- | --- |
| `_config.yml` | 站点配置（`url` 必填，sitemap/feed/绝对链接依赖它） |
| `_data/i18n.yml` | 中文文案与日期格式覆盖 |
| `_data/navigation.yml` | 顶部导航 |
| `_includes/head_custom.html` | SEO 补充：og:type、JSON-LD、favicon、theme-color |
| `_includes/fonts.html` | 字体预加载（字体已自托管在 `assets/fonts/`） |
| `assets/fonts/` | Bitter 可变字体（OFL 1.1，见 `OFL.txt`） |
| `_posts/` | 文章 |

## 许可

文章内容采用 CC BY 4.0，详见 [LICENSE](LICENSE)。
