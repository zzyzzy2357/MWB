# 度量衡 · 公共中转站库（GitHub 版）

> 本目录是一个**独立仓库**的模板：把它里面的内容单独建成一个仓库（例如 `mwb-sites`），
> 而不是和主程序仓库混在一起。主程序仓库里的 `community/` 只是留档，方便随版本分发。

用 GitHub 仓库当「云端站点库」，不需要任何服务器：所有人从 raw 地址拉取，
别人通过 Issue 提交（机器人自动校验合并）。

## 一、怎么建

1. 新建一个 **Public** 仓库（例如 `mwb-sites`），把本目录内容整体上传。
2. 仓库 → Settings → Actions → General → Workflow permissions 选 **Read and write permissions**（否则机器人不能提交）。
3. 仓库 → Issues → 新建一个标签 `site-submission`。
4. 打开 Actions 页签，启用 `accept-site` 工作流。

## 二、别人怎么用

把下面任一地址填到软件 **设置 → 站点与数据 → 云端站点库地址**：

```
https://raw.githubusercontent.com/<你的用户名>/mwb-sites/main/sites.json
```

国内访问 raw 不稳时可以换成 jsDelivr（自动带缓存）：

```
https://cdn.jsdelivr.net/gh/<你的用户名>/mwb-sites@main/sites.json
```

支持填多个地址（换行分隔），软件会依次合并、按域名去重。

## 三、别人怎么提交

- **在软件里**：站点卡片 → 铅笔（编辑）→ 左下角「分享到公共站点库」。
  在设置里把「分享提交仓库」填成本仓库地址后，会直接打开预填好的提交页。
- **在 GitHub 上**：Issues → New issue → 选「提交一个中转站」表单。

机器人（`scripts/merge_site.py`）会做三件事：

1. 只取白名单字段（name / baseUrl / kind / rechargeRate / note），**账号密码永远不写进来**；
2. 地址必须是 http(s) 且 host 合法，按域名归一化去重；
3. 收录成功自动回帖并关闭 issue；重复的直接提示跳过。

## 四、维护建议

- **审核**：自动合并适合信任社区的场景；如果要人工把关，把工作流里的 `git push` 改成只开 PR。
- **下架**：加一个 `site-report` 标签的 issue，或手动从 `sites.json` 删掉对应条目。
- **健康检查**：可以再加一个定时工作流，每天探测各站 `/api/status`，连续失败 N 次的自动剔除并开 issue。
- **免责**：README 里写清楚「清单由社区提交，软件不保证站点质量与资金安全」，提示用户自行小额测试。

## 五、还能怎么升级（都不需要自己买服务器）

| 方案 | 上传体验 | 成本 | 说明 |
| --- | --- | --- | --- |
| Issue + Actions（本模板） | 点提交即可，需 GitHub 账号 | 免费 | 最接近「一键上传」，全自动收录 |
| Pull Request | 要会 fork/PR | 免费 | 审核最严，适合高质量库 |
| Gist 订阅 | 各自维护自己的库 | 免费 | 适合小圈子互推，软件支持多源 |
| Cloudflare Workers / Pages Functions | 真正的 POST 接口 | 免费额度足够 | 需要写几十行代码，但仍是零服务器运维 |
| Supabase / Firebase | 表单直接写库 | 免费额度 | 想做投票、评分、举报时更合适 |

> 说明：GitHub 本身没有「匿名写入」接口，所以「用户直接上传到你的仓库」必须有账号 + Issue/PR 或 Actions 这条链路；
> 想让完全匿名的人也能提交，就得引入 Cloudflare Workers 这类免费后端做中转。
