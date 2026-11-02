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

构建由 GitHub Actions（`.github/workflows/pages.yml`）完成，产物推送到 **`gh-pages`** 分支根目录，
Pages 托管该分支。之所以不在 Pages 里直接选分支构建：站点用了 `jekyll-archives`，
**不在 Pages 老式（legacy）构建的插件白名单里**，且 Pages 内置的仍是 Jekyll 3.9.x。

工作流：

1. 监听 `master` 分支 push（也可手动 `workflow_dispatch`）
2. `ruby/setup-ruby` 按 `.ruby-version` 装 Ruby，`bundler-cache` 缓存 gem
3. `bundle exec jekyll build`（`JEKYLL_ENV=production`）
4. `peaceiris/actions-gh-pages` 把 `_site/` 推到 `gh-pages`，`:force_orphan` 保持单提交，
   并写入 `.nojekyll` 防止 Pages 对产物再跑一遍 Jekyll

仓库侧的对应设置（一次性，不随代码走）：

- Settings → Pages → Source 选 **Deploy from a branch** → `gh-pages` / `/ (root)`
- 自定义域名：仓库根目录放 `CNAME`（内容 `codinghunter.com`），Jekyll 会原样复制进 `_site`；
  DNS 侧按 Pages 要求配 A/AAAA 或 CNAME 记录
- `gh-pages` 分支不要设分支保护/PR 要求，否则 `GITHUB_TOKEN` 推不上去

> 另一种官方推荐做法是不落分支（`actions/upload-pages-artifact` + `actions/deploy-pages`，
> Settings → Pages → Source 选 **GitHub Actions**）。想保留可回溯的产物快照就用当前的 `gh-pages` 方式。

## 目录说明

| 路径 | 说明 |
| --- | --- |
| `.github/workflows/pages.yml` | 构建并推送产物到 `gh-pages` 分支 |
| `_config.yml` | 站点配置（`url` 必填，sitemap/feed/绝对链接依赖它） |
| `_data/i18n.yml` | 中文文案与日期格式覆盖 |
| `_data/navigation.yml` | 顶部导航 |
| `_includes/head_custom.html` | SEO 补充：og:type、JSON-LD、favicon、theme-color |
| `_includes/fonts.html` | 字体预加载（字体已自托管在 `assets/fonts/`） |
| `assets/fonts/` | Bitter 可变字体（OFL 1.1，见 `OFL.txt`） |
| `_posts/` | 文章 |

## 许可

文章内容采用 CC BY 4.0，详见 [LICENSE](LICENSE)。
