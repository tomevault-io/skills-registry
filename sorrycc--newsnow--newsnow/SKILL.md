---
name: newsnow
description: | Use when this capability is needed.
metadata:
  author: sorrycc
---

# newsnow CLI

Fetch trending news and hot topics from 66 sources across 44 platforms. Returns news items with title, URL, and optional metadata.

Run `newsnow --help` for usage details.

## Workflow

Follow this pattern:

1. **List** - Don't know what sources are available? List them first.
2. **Fetch** - Know the source? Fetch news directly.
3. **JSON** - Need structured data? Add `--json` for machine-readable output.

| Need | Command | When |
|---|---|---|
| See all sources | `newsnow list` | Don't know source names |
| See sources as JSON | `newsnow list --json` | Need source list programmatically |
| Get news | `newsnow <source>` | Know the source, want readable output |
| Get news as JSON | `newsnow <source> --json` | Need structured data for processing |

## Commands

### list

List all available sources.

```bash
newsnow list
newsnow list --json
```

### Fetch a source

```bash
newsnow hackernews
newsnow hackernews --json
```

Output fields (JSON mode):
- `id` - Unique item identifier
- `title` - News headline
- `url` - Link to the article (optional)
- `pubDate` - Publication date (optional)
- `extra` - Additional metadata like view counts, comments (optional)

## Sources

66 source endpoints across 44 platforms:

| Platform | Sources |
|---|---|
| 36kr | `36kr`, `36kr-quick`, `36kr-renqi` |
| Baidu | `baidu` |
| Bilibili | `bilibili`, `bilibili-hot-search`, `bilibili-hot-video`, `bilibili-ranking` |
| Cankaoxiaoxi | `cankaoxiaoxi` |
| Chongbuluo | `chongbuluo`, `chongbuluo-hot`, `chongbuluo-latest` |
| CLS | `cls`, `cls-telegraph`, `cls-depth`, `cls-hot` |
| Coolapk | `coolapk` |
| Douban | `douban` |
| Douyin | `douyin` |
| Fastbull | `fastbull`, `fastbull-express`, `fastbull-news` |
| FreeBuf | `freebuf` |
| Gelonghui | `gelonghui` |
| Ghxi | `ghxi` |
| GitHub | `github`, `github-trending-today` |
| Hacker News | `hackernews` |
| Hupu | `hupu` |
| iFeng | `ifeng` |
| iQIYI | `iqiyi-hot-ranklist` |
| ITHome | `ithome` |
| Jin10 | `jin10` |
| Juejin | `juejin` |
| Kaopu | `kaopu` |
| Kuaishou | `kuaishou` |
| LinuxDo | `linuxdo`, `linuxdo-latest`, `linuxdo-hot` |
| MktNews | `mktnews`, `mktnews-flash` |
| Nowcoder | `nowcoder` |
| PCBeta | `pcbeta-windows`, `pcbeta-windows11` |
| Product Hunt | `producthunt` |
| QQ Video | `qqvideo-tv-hotsearch` |
| SMZDM | `smzdm` |
| Solidot | `solidot` |
| Sputnik News CN | `sputniknewscn` |
| SSPai | `sspai` |
| Steam | `steam` |
| Tencent | `tencent-hot` |
| The Paper | `thepaper` |
| Tieba | `tieba` |
| Toutiao | `toutiao` |
| V2EX | `v2ex`, `v2ex-share` |
| Wall Street CN | `wallstreetcn`, `wallstreetcn-quick`, `wallstreetcn-news`, `wallstreetcn-hot` |
| Weibo | `weibo` |
| Xueqiu | `xueqiu`, `xueqiu-hotstock` |
| Zaobao | `zaobao` |
| Zhihu | `zhihu` |

## Source Selection Guide

| Category | Recommended Sources |
|---|---|
| Tech | `hackernews`, `github`, `v2ex`, `juejin`, `ithome`, `linuxdo` |
| Finance | `xueqiu`, `wallstreetcn`, `cls`, `jin10`, `gelonghui`, `fastbull` |
| General News | `toutiao`, `baidu`, `thepaper`, `ifeng`, `zaobao`, `cankaoxiaoxi` |
| Social/Trending | `weibo`, `douyin`, `bilibili`, `zhihu`, `tieba`, `douban` |
| Security | `freebuf` |
| Product/Design | `producthunt`, `sspai` |

## Environment Variables

- `PRODUCTHUNT_API_TOKEN` - Required for `producthunt` source

## Known Limitations

- `linuxdo`, `linuxdo-latest`, `linuxdo-hot` may return 403 Forbidden (Cloudflare)
- Some Chinese sources may be inaccessible from outside mainland China

## Working with Results

```bash
newsnow hackernews --json | jq '.[].title'
newsnow hackernews --json | jq '.[:5]'
newsnow weibo --json | jq '.[] | "\(.title) \(.url)"'
```

---
> Source: [sorrycc/newsnow](https://github.com/sorrycc/newsnow) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
