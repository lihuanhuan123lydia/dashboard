# dmdmnow-dashboard

原封不动复刻 [dashboard.dmdmnow.com/new_products_viewer.html](https://dashboard.dmdmnow.com/new_products_viewer.html) 的竞品新品监控面板，数据自动同步，并在 Lasfit 上架新车型脚垫时自动推送到飞书群「车型匹配&新品下单」。

## 面板导航（2026-10-09 起全部走 GitHub Pages）

老域名（dashboard.dmdmnow.com / wolfbox.dmdmnow.com）已停用，面板按业务名迁移到以下路径：

| 业务名 | 新地址 | 原地址 |
|---|---|---|
| 3W-EU-UK客户车型需求 | [/3w-eu-uk-vehicle-needs/](https://lihuanhuan123lydia.github.io/dmdmnow-dashboard/3w-eu-uk-vehicle-needs/) | dashboard.dmdmnow.com/3w-eu/ |
| WB-US改装车型需求 | [/wb-us-gear-vehicle-needs/](https://lihuanhuan123lydia.github.io/dmdmnow-dashboard/wb-us-gear-vehicle-needs/) | dashboard.dmdmnow.com/analysis.html |
| 3W-US客户车型需求 | [/3w-us-vehicle-needs/](https://lihuanhuan123lydia.github.io/dmdmnow-dashboard/3w-us-vehicle-needs/) | dashboard.dmdmnow.com/3w/ |
| 3W-US脚垫竞品分析 | [/3w-us-mat-competitor/](https://lihuanhuan123lydia.github.io/dmdmnow-dashboard/3w-us-mat-competitor/) | dashboard.dmdmnow.com/（首页） |
| WB论坛监控面板 | [/wb-forum-monitor/](https://lihuanhuan123lydia.github.io/dmdmnow-dashboard/wb-forum-monitor/) | wolfbox.dmdmnow.com/ |

旧路径（`/3w-eu/`、`/analysis.html`、`/3w/`、`/`、`/wolfbox/`）保留并自动跳转到新地址，外部收藏不会 404。

## 文件

- `new_products_viewer.html` — 面板页面（与原站 1:1 一致）
- `new_products_data.js` — 数据文件，由 Actions 自动更新
- `scripts/sync_notify.py` — 同步 + 通知脚本
- `state/notified_ids.json` — 已通知条目去重状态（自动生成）

## 自动化（.github/workflows/sync-notify.yml）

- 每 5 分钟（GitHub Actions cron 最短间隔）从 `dashboard.dmdmnow.com/new_products_data.js` 拉取最新数据并 commit。
- 每次运行时检测：`b == "Lasfit"` 且标题含 `Floor Mat` / `Cargo Mat` / `Bed Mat` 且 `d == 当天（北京时间）` 的新品；发现即向飞书群发送通知卡片，同一产品只通知一次。
- 链接修正：数据里的 Shopify 数字 ID 会 404，发送前经 `lasfit.com/products.json` 解析为 handle（每周全店扫描缓存于 `_handles.json`，新品按需解析），且验证 handle URL 返回 200 才使用。
- 卡片格式 v5：标题日期 + 每产品一行「车型年份 · 价格」+ 每个变体一行中文配置（Sedan / 脚垫+尾箱垫），产品行不再重复日期。
- 支持手动触发（Actions → sync-and-notify → Run workflow）：`target_date` 指定检测日期（测试用），`dry_run=true` 只打印不发送。
