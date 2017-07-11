---
- 'Invest 提供一对 app_secret, app_key 给发送方用来签名。'
- 发送方不能泄漏 app_secret 给无关的第三方。
- 同一篇文章，如无更新， 则不推送。
---


# 前置条件

```
http POST
ContentType: "application/json"
```

- 测试环境

  ```
  https://backend-invest.test.dtcj.com/backend/gateway/import_document
  ```

- 生产环境

  ```
  http://gateway-invest.dtcj.com/import_document
  ```

# 报文格式

--------------------------------------------------------------------------------

## Request

```
{
  "data" : { ... 具体字段 ... },
  "app_key" : "app_1", # 前置条件中的 app_key
  "signature" : xxx    # 用签名算法对 data 生成摘要
}
```

## Response

- 成功返回 http_status 200

  ```
  {"message" : "ok"}
  ```

- 应用验证失败返回 http_status 401
- 缺必须字段返回 http_status 422

## 签名算法（附ruby代码）

1. data 按照 key 排序 (字符串正序)
2. data 转成 JSON 字符串 json
3. string = json + app_secret
4. 使用 SHA1 对 string 做摘要为签名(signature)

```
module Signature
  class Generator < Struct.new(:data, :app_secret)
    def signature
      Digest::SHA1.hexdigest("#{Oj.dump(data.sort_by_key)}#{app_secret}")
    end
  end
end
```

# 必填的字段

--------------------------------------------------------------------------------

- 非必填字段可不传
- 非必填字段如果为数组类型, 可以传空数组

```
REQUIRED_KEYS = %w[
  content                  # 正文
  origin_date              # 原文发布时间
  compose_organization     # 撰写机构
  origin_website           # 原文网站名
  source_id                # 发送方文章唯一标识
  title                    # 标题
]
```

# 接收的字段

--------------------------------------------------------------------------------

```
VALID_KEYS = %w[
  title                    # 标题
  author                   # 作者
  compose_organization     # 撰写机构
  subtitle                 # 副标题
  summary                  # 摘要
  content                  # 正文

  origin_url               # 原文网址
  origin_website           # 原文网站名
  origin_date              # 原文发布时间

  source_id                # 发送方文章唯一标识
  article_catagory         # 稿件分类: "实时资讯"|"电子报"|"部委机构"
  title_image_url          # 文章配图地址

  columns                  # 栏目: ["公司"]
  keywords                 # 关键字: ["关键字1", "关键字2"]
  tags                     # 标签: ["标签1", "标签2"]

  locations                # 地区: ["地区1", "地区2"] （测试环境开启，直播栏目专用）
  live_tags                # 直播标签: ["直播标签1", "直播标签2"] （测试环境开启，直播栏目专用）
  content_highlight        # 直播高亮: true | false （测试环境开启，直播栏目专用）
]
```

## 支持的所有栏目

```
要闻
沪深
行业
基金
专家
港股
美股
头条
公司
直播
```

# 样例

--------------------------------------------------------------------------------

```
{
  "article_catagory":"实时资讯",
  "columns":[{"code": "9", "name": "公司"}],
  "compose_organization":"第一财经新闻社",
  "content":"中国证监会网站12月4日公布，南通四方冷链装备股份有限公司、新疆汇嘉时代百货股份有限公司首发申请拟于12月9日（周三）上会。（第一财经新闻社）",
  "keywords":["证监会"],
  "origin_date":"2015-12-04T19:20:14.000+08:00",
  "origin_url":"www.yicai.com",
  "origin_website":"第一财经新闻社",
  "source_id":"2f951f32-27ea-47fe-9071-430efe31339c_5661d4aa76bf511fae000d25",
  "tags":["南通四方"],
  "title":"【四方冷链、新疆汇嘉首发申请9日上会】"
}
```
