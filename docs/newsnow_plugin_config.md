# get_news_from_newsnow Plugin News Source Configuration Guide

## Overview

The `get_news_from_newsnow` plugin now supports dynamic configuration of news sources through the Web management interface, no longer requiring code modification. Users can configure different news sources for each agent in the Control Panel.

## Configuration Method

### 1. Configure via Web Management Interface (Recommended)

1. Log in to the Control Panel
2. Enter the "Role Configuration" page
3. Select the agent to configure
4. Click the "Edit Functions" button
5. Find the "newsnow News Aggregation" plugin in the parameter configuration area on the right
6. Enter semicolon-separated Chinese names in the "News Source Configuration" field

### 2. Configuration File Method

Configure in `config.yaml`:

```yaml
plugins:
  get_news_from_newsnow:
    url: "https://newsnow.busiyi.world/api/s?id="
    news_sources: "澎湃新闻;百度热搜;财联社;微博;抖音"
```

## News Source Configuration Format

News source configuration uses semicolon-separated Chinese names in the format:

```
Chinese Name 1;Chinese Name 2;Chinese Name 3
```

### Configuration Example

```
澎湃新闻;百度热搜;财联社;微博;抖音;知乎;36氪
```

## Supported News Sources

The plugin supports the following Chinese names of news sources:

- 澎湃新闻 (The Paper)
- 百度热搜 (Baidu Hot Search)
- 财联社 (Cailian Press)
- 微博 (Weibo)
- 抖音 (Douyin)
- 知乎 (Zhihu)
- 36氪 (36Kr)
- 华尔街见闻 (Wall Street Insights)
- IT之家 (IT Home)
- 今日头条 (Toutiao)
- 虎扑 (Hupu)
- 哔哩哔哩 (Bilibili)
- 快手 (Kuaishou)
- 雪球 (Xueqiu)
- 格隆汇 (Gelonghui)
- 法布财经 (Fab Finance)
- 金十数据 (Jin10 Data)
- 牛客 (Niuke)
- 少数派 (SSPAI)
- 稀土掘金 (Juejin)
- 凤凰网 (Phoenix News)
- 虫部落 (Chongbuluo)
- 联合早报 (Lianhe Zaobao)
- 酷安 (Coolapk)
- 远景论坛 (Pcbeta)
- 参考消息 (Cankaoxiaoxi)
- 卫星通讯社 (Sputnik)
- 百度贴吧 (Baidu Tieba)
- 靠谱新闻 (Kaopu News)
- And more...

## Default Configuration

If no news source is configured, the plugin will use the following default configuration:

```
澎湃新闻;百度热搜;财联社
```

## Usage Instructions

1. **Configure News Sources**: Set the Chinese names of news sources in the Web interface or configuration file, separated by semicolons
2. **Call Plugin**: Users can say "播报新闻" (broadcast news) or "获取新闻" (get news)
3. **Specify News Source**: Users can say "播报澎湃新闻" (broadcast The Paper) or "获取百度热搜" (get Baidu Hot Search)
4. **Get Details**: Users can say "详细介绍这条新闻" (introduce this news in detail)

## How It Works

1. The plugin accepts Chinese names as parameters (e.g., "澎湃新闻")
2. According to the configured news source list, converts Chinese names to corresponding English IDs (e.g., "thepaper")
3. Uses English IDs to call the API to get news data
4. Returns news content to users

## Notes

1. The configured Chinese names must exactly match the names defined in CHANNEL_MAP
2. After configuration changes, the service needs to be restarted or the configuration reloaded
3. If the configured news source is invalid, the plugin will automatically use the default news source
4. Multiple news sources are separated by English semicolons (;), do not use Chinese semicolons (；)
