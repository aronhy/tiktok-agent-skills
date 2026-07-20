# KSS MCP Tool Reference

本文档是 Skill 内唯一的 KSS MCP 工具事实来源。工具实际返回与本文件冲突时，以 MCP 实时工具 schema 为准，并记录差异。

- 官方来源：[KSS Open API — MCP 文档](https://o.kolsprite.com/doc/api.html)
- 核验日期：2026-07-20

## 目录

- [字幕 MCP](#字幕-mcp)
- [通用 MCP](#通用-mcp)
- [通用参数类型](#通用参数类型)
- [达人查询](#creator_search达人查询)
- [视频查询](#video_search视频查询)
- [商品查询](#product_search商品查询)
- [店铺查询](#shop_search店铺查询)
- [限流说明](#限流说明)
- [常见问题](#常见问题)
- [跨工具关联](#跨工具关联)
- [数据完整性规则](#数据完整性规则)

## 字幕 MCP

Endpoint：`https://mcp.kolsprite.com/caption/mcp`

| 工具名 | 说明 | 对应 API |
| --- | --- | --- |
| `caption_extract` | 通过 URL 提取视频字幕 | `/api/caption/extract/url` |

## 通用 MCP

Endpoint：`https://mcp.kolsprite.com/universal/mcp`

| 工具名 | 说明 | 对应 API |
| --- | --- | --- |
| `creator_search` | 多维度搜索 TikTok 达人 | `/api/creator/search` |
| `video_search` | 视频搜索与筛选 | `/api/video/search` |
| `product_search` | 商品搜索与销量分析 | `/api/product/search` |
| `shop_search` | 店铺搜索与评分查询 | `/api/shop/search` |

### 通用参数类型

文档中的范围参数使用对象传值：

```json
{
  "from": 0,
  "to": 100000
}
```

日期范围示例：

```json
{
  "from": "2024-01-01",
  "to": "2024-12-31"
}
```

### `creator_search`：达人查询

#### 工具参数

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `region` | string | 可选 | 国家/区域，默认 `US` |
| `searchType` | string | 可选 | `N`=昵称，`K`=关键词（默认 `K`） |
| `keyword` | string | 可选 | 搜索词 |
| `fansCnt` | IntRange | 可选 | 粉丝数范围 |
| `playCnt` | IntRange | 可选 | 平均播放量范围 |
| `interact` | DoubleRange | 可选 | 互动率范围 |
| `videoCnt` | IntRange | 可选 | 视频数量范围 |
| `fansGr` | DoubleRange | 可选 | 粉丝增长率范围 |
| `avgLike` | DoubleRange | 可选 | 平均点赞量范围 |
| `categoryList` | string[] | 可选 | 达人分类列表 |
| `productCategoryList` | string[] | 可选 | 带货分类列表 |
| `productCnt` | IntRange | 可选 | 商品数范围 |
| `salesVolume` | IntRange | 可选 | 带货销量范围 |
| `pageNum` | integer | 可选 | 页码，默认 `1` |
| `pageSize` | integer | 可选 | 每页数量，默认 `20`，最大 `100` |
| `orderField` | string | 可选 | 排序字段，默认带货销量 |
| `orderType` | integer | 可选 | `0` 降序（默认），`1` 升序 |

#### 返回字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `id` | string | 达人 ID |
| `name` | string | 达人昵称 |
| `uniqueId` | string | Handle 名称 |
| `cover` | string | 头像地址 |
| `region` | string | 面向区域 |
| `fansCnt` | integer | 粉丝数量 |
| `likeCnt` | long | 点赞数 |
| `fansGr` | double | 粉丝增长率 |
| `videoCnt` | integer | 视频数量 |
| `avgViewsPerVideo` | integer | 平均播放量 |
| `videoCompletionRate` | double | 完播率 |
| `interactionRate` | double | 互动率 |
| `avgLikeCnt` | integer | 平均点赞量 |
| `likeFansRate` | double | 赞粉比 |
| `gpm` | decimal | 每千次播放销售额 |
| `productCnt` | integer | 带货数 |
| `salesVolume` | integer | 带货销量 |
| `currency` | string | 货币 |
| `mailAddress` | string | 邮箱 |
| `lastVideoDate` | datetime | 最后视频发布时间 |
| `category` | string[] | 达人分类 |
| `enCategory` | string[] | 达人分类（英文） |
| `exposure` | double | 视频曝光率 |
| `contactList[].type` | string | 联系方式类型 |
| `contactList[].contactAddress` | string | 联系方式地址 |
| `goodsCategory[].catId` | string | 带货分类 ID |
| `goodsCategory[].catName` | string | 带货分类名称（英文） |
| `goodsCategory[].cnCatName` | string | 带货分类名称（中文） |

### `video_search`：视频查询

#### 工具参数

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `region` | string | 可选 | 国家/区域，默认 `US` |
| `keyword` | string | 可选 | 搜索词 |
| `playCnt` | IntRange | 可选 | 播放量范围 |
| `interact` | DoubleRange | 可选 | 互动率范围 |
| `likeCnt` | IntRange | 可选 | 点赞量范围 |
| `videoType` | string | 可选 | 视频类型 |
| `videoIds` | string[] | 可选 | 视频 ID 列表，最多 50 个 |
| `pubDate` | DateRange | 可选 | 发布时间范围 |
| `productCategoryList` | string[] | 可选 | 带货分类 |
| `salesVolume` | IntRange | 可选 | 总销量范围 |
| `salesVolumeLst30d` | IntRange | 可选 | 近 30 天销量范围 |
| `salesLst30d` | DoubleRange | 可选 | 近 30 天销售额范围 |
| `price` | DoubleRange | 可选 | 价格范围 |
| `seconds` | IntRange | 可选 | 时长（秒）范围 |
| `product` | boolean | 可选 | 是否带货视频 |
| `shopId` | string | 可选 | 店铺 ID |
| `productId` | string | 可选 | 商品 ID |
| `pageNum` | integer | 可选 | 页码，默认 `1` |
| `pageSize` | integer | 可选 | 每页数量，默认 `20`，最大 `100` |
| `orderField` | string | 可选 | 排序字段，默认近 30 天销量 |
| `orderType` | integer | 可选 | `0` 降序（默认），`1` 升序 |

#### 返回字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `id` | string | 视频 ID |
| `title` | string | 视频标题 |
| `cover` | string | 视频封面 |
| `region` | string | 区域 |
| `seconds` | integer | 视频时长（秒） |
| `playCnt` | long | 播放量 |
| `likeCnt` | long | 点赞数 |
| `commentCnt` | integer | 评论数 |
| `collectCnt` | integer | 收藏数 |
| `forwardCnt` | integer | 转发数 |
| `interactionRate` | double | 互动率 |
| `pubTime` | datetime | 发布时间 |
| `salesVolumeLst30d` | integer | 近 30 天销量 |
| `totalSalesVolume` | integer | 总销量 |
| `salesLst30d` | double | 近 30 天销售额 |
| `currency` | string | 货币 |
| `creator.id` | string | 达人 ID |
| `creator.name` | string | 达人昵称 |
| `creator.handleName` | string | 达人 Handle |
| `creator.fansCnt` | integer | 粉丝数 |
| `creator.region` | string | 达人区域 |
| `creator.categoryList` | string[] | 达人分类 |
| `creator.cover` | string | 达人头像 |
| `creator.enCategoryList` | string[] | 达人英文分类 |
| `product.id` | string | 商品 ID |
| `product.title` | string | 商品标题 |
| `product.imageUrl` | string | 商品图片 |
| `product.price` | double | 现价 |
| `product.originalPrice` | double | 原价 |
| `product.currency` | string | 货币 |
| `product.region` | string | 商品区域 |
| `product.catId` | string | 分类 ID |
| `product.catName` | string | 分类名称 |
| `product.cnCatName` | string | 中文分类名称 |
| `product.salesVolumeLst30d` | integer | 商品近 30 天销量 |
| `product.totalSales` | double | 总销售额 |

### `product_search`：商品查询

#### 工具参数

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `region` | string | 可选 | 国家/区域，默认 `US` |
| `searchType` | string | 可选 | `N`=商品 ID，`K`=关键词 |
| `keyword` | string | 可选 | 关键词或产品标题 |
| `excludeKeyword` | string | 可选 | 排除关键词 |
| `shopId` | string | 可选 | 店铺 ID |
| `catIds` | string[] | 可选 | 类目 ID 列表 |
| `price` | DoubleRange | 可选 | 价格范围 |
| `salesVolume` | IntRange | 可选 | 总销量范围 |
| `salesVolumeLst7d` | IntRange | 可选 | 近 7 天销量范围 |
| `salesVolumeGr7d` | DoubleRange | 可选 | 7 天增长率范围 |
| `salesVolumeLst30d` | IntRange | 可选 | 近 30 天销量范围 |
| `salesLst30d` | DoubleRange | 可选 | 近 30 天销售额范围 |
| `listingDate` | DateRange | 可选 | 上架时间范围 |
| `reviews` | IntRange | 可选 | 评论数范围 |
| `rating` | DoubleRange | 可选 | 评分范围 |
| `influencer` | IntRange | 可选 | 带货达人数范围 |
| `videos` | IntRange | 可选 | 带货视频数范围 |
| `ft` | string | 可选 | 履约类型：`fbt`=全托管，`pop`=自运营 |
| `lt` | string | 可选 | 物流类型：`local`=本地，`crossborder`=跨境 |
| `pageNum` | integer | 可选 | 页码，默认 `1` |
| `pageSize` | integer | 可选 | 每页数量，默认 `20`，最大 `100` |
| `orderField` | string | 可选 | 排序字段 |
| `orderType` | integer | 可选 | `0` 降序（默认），`1` 升序 |

#### 返回字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `id` | string | 产品 ID |
| `title` | string | 产品标题 |
| `imageUrl` | string | 产品图片 |
| `catId` | string | 类目 ID |
| `catName` | string | 类目名称（英文） |
| `cnCatName` | string | 类目名称（中文） |
| `region` | string | 区域 |
| `shopId` | string | 店铺 ID |
| `shopName` | string | 店铺名称 |
| `shopImageUrl` | string | 店铺图片 |
| `shopSalesVolume` | integer | 店铺销量 |
| `shopProducts` | integer | 店铺商品数 |
| `price` | double | 现价 |
| `originalPrice` | double | 原价 |
| `currency` | string | 货币 |
| `salesVolumeLst7d` | integer | 近 7 天销量 |
| `salesVolumeGr7d` | double | 近 7 天销量增长率 |
| `salesLst7d` | double | 近 7 天销售额 |
| `salesVolumeLst30d` | integer | 近 30 天销量 |
| `salesVolumeGr30d` | double | 近 30 天销量增长率 |
| `salesLst30d` | double | 近 30 天销售额 |
| `totalSalesVolume` | integer | 总销量 |
| `totalSales` | double | 总销售额 |
| `rating` | double | 评分 |
| `reviews` | integer | 评论数 |
| `influencers` | integer | 带货达人数 |
| `videos` | integer | 带货视频数 |
| `lives` | integer | 带货直播数 |
| `listingDate` | datetime | 上架时间 |
| `ft` | string | 履约类型 |
| `lt` | string | 物流类型 |

### `shop_search`：店铺查询

#### 工具参数

| 字段 | 类型 | 必填 | 说明 |
| --- | --- | --- | --- |
| `region` | string | 可选 | 国家/区域，默认 `US` |
| `searchType` | string | 可选 | `N`=店铺 ID，`K`=关键词 |
| `keyword` | string | 可选 | 店铺关键词或名称 |
| `shopType` | string | 可选 | 卖家类型 |
| `serviceType` | string | 可选 | 运营模式 |
| `category` | string[] | 可选 | 店铺类目 |
| `rating` | DoubleRange | 可选 | 评分范围 |
| `influencer` | IntRange | 可选 | 带货达人数范围 |
| `price` | DoubleRange | 可选 | 均价范围 |
| `salesVolume` | IntRange | 可选 | 总销量范围 |
| `salesVolumeLst30d` | IntRange | 可选 | 近 30 天销量范围 |
| `salesVolumeGr30d` | DoubleRange | 可选 | 近 30 天销量增长率 |
| `videos` | IntRange | 可选 | 带货视频数范围 |
| `products` | IntRange | 可选 | 在售商品数范围 |
| `pageNum` | integer | 可选 | 页码，默认 `1` |
| `pageSize` | integer | 可选 | 每页数量，默认 `20`，最大 `100` |
| `orderField` | string | 可选 | 排序字段 |
| `orderType` | integer | 可选 | `0` 降序（默认），`1` 升序 |

#### 返回字段

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `id` | string | 店铺 ID |
| `shopId` | string | 店铺 ID |
| `shopName` | string | 店铺名称 |
| `imageUrl` | string | 店铺图片 |
| `region` | string | 地区 |
| `link` | string | 店铺链接 |
| `categories` | string | 英文类目 |
| `cnCategories` | string | 中文类目 |
| `rating` | double | 评分 |
| `products` | integer | 在售商品数 |
| `avgPrice` | double | 在售商品均价 |
| `currency` | string | 货币单位 |
| `salesVolumeLst30d` | integer | 近 30 天销量 |
| `salesVolumeGr30d` | double | 近 30 天销量增长率 |
| `salesLst30d` | double | 近 30 天销售额 |
| `totalSalesVolume` | integer | 总销量 |
| `totalSales` | double | 总销售额 |
| `influencers` | integer | 带货达人数 |
| `videos` | integer | 带货视频数 |
| `respondRate` | double | 24h 回复率 |
| `shipRate` | double | 2 天内发货率 |
| `ratingRate` | double | 4+ 评分占比 |
| `shopRate` | double | 超过的店铺占比 |
| `shopType` | string | 卖家类型 |
| `serviceType` | string | 运营模式 |

## 限流说明

MCP 与 API 共享后端数据源，但限流计数独立。每个 MCP Key 每月按套餐配额。超过限制时，工具调用会返回：

- `ERROR_API_MINUTE_MAX`
- `ERROR_API_MONTH_MAX`

## 常见问题

### 连接显示 401 / Key 无效

检查 Header 名是否为 `secret-key`（短横线，不是下划线），确认 Key 已开通 MCP 权限，并在管理后台确认 Key 类型为 MCP。

### 客户端连不上或一直 Loading

确认当前网络可访问以下地址：

- `https://mcp.kolsprite.com/caption/mcp`
- `https://mcp.kolsprite.com/universal/mcp`

部分企业代理会截断长连接，可尝试切换网络，或将 `mcp.kolsprite.com` 加入代理白名单。

### 能否同时配置 API 与 MCP？

可以。API Key 用于服务端直接调用 HTTP 接口；MCP Key 用于 AI 客户端通过 MCP 协议访问。两者独立计费、互不影响。

## 跨工具关联

| 起点字段 | 下一步 | 关联规则 |
| --- | --- | --- |
| `product_search.id` | `video_search` | 传入 `productId` |
| `product_search.shopId` | `video_search` | 传入 `shopId` |
| `product_search.shopId` | `shop_search` | 使用 `searchType=N`，将店铺 ID 作为 `keyword` |
| `video_search.creator.handleName` 或 `creator.name` | `creator_search` | 使用 `searchType=N` 按昵称查询；返回后用 `creator.id` 做一致性确认 |
| TikTok 视频 URL | `caption_extract` | 传入视频 URL 提取字幕 |

不要把 `creator_search.searchType=N` 当成达人 ID 查询；该值在当前文档中表示昵称查询。

## 数据完整性规则

- 搜索工具默认 `pageNum=1`、`pageSize=20`，最大 `pageSize=100`。
- 用户要求“全部”时持续分页，直到没有下一页、达到工具上限或遇到额度/限流。
- 如果中途停止，必须标记为“非完整结果”，并报告已完成页数和条数。
- 不混合比较不同统计周期的指标。
- 未返回字段写“未提供”，不得推测或补造。
- 文档没有枚举 `orderField` 合法值时，不得发明枚举；可在已获取范围内按返回字段本地排序，并披露范围。
- 当前公开文档没有说明增长率筛选输入使用百分数还是小数比例。处理“50%”等条件时，先检查实时工具 schema 或官方示例，不得自行假定传入 `50` 或 `0.5`；输出时同时披露筛选输入值与返回字段单位。
- 商店链接使用 `shop_search.link`。视频和达人链接只在 Handle 与 ID 足够时按 TikTok 规范构造；商品链接未返回时写“未提供”。
