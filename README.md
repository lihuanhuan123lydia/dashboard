# dmdmnow-dashboard

原封不动复刻 [dashboard.dmdmnow.com/new_products_viewer.html](https://dashboard.dmdmnow.com/new_products_viewer.html) 的竞品新品监控面板，数据自动同步，并在 Lasfit 上架新车型脚垫时自动推送到飞书群「车型匹配&新品下单」。

## 文件

- `new_products_viewer.html` — 面板页面（与原站 1:1 一致）
- `new_products_data.js` — 数据文件，由 Actions 自动更新
- `scripts/sync_notify.py` — 同步 + 通知脚本
- `state/notified_ids.json` — 已通知条目去重状态（自动生成）

## 自动化（.github/workflows/sync-notify.yml）

- 每 5 分钟（GitHub Actions cron 最短间隔）从 `dashboard.dmdmnow.com/new_products_data.js` 拉取最新数据并 commit。
- 每次运行时检测：`b == "Lasfit"` 且标题含 `Floor Mat` / `Cargo Mat` / `Bed Mat` 且 `d == 当天（北京时间）` 的新品；发现即向飞书群发送与布鲁斯卡片同款格式的通知卡片，且同一条目只通知一次。
- 支持手动触发（Actions → sync-and-notify → Run workflow）：`target_date` 指定检测日期（测试用），`dry_run=true` 只打印不发送。
