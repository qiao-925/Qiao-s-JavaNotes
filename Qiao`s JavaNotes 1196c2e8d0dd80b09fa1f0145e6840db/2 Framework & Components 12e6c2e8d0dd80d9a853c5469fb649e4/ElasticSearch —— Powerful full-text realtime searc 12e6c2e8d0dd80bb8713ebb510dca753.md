# ElasticSearch —— Powerful full-text realtime search

# Ref

- Official website: https://www.elastic.co/
- News from official: https://ir.elastic.co/news/press-releases/default.aspx
- Wikipedia: https://en.wikipedia.org/wiki/Elasticsearch
- https://xiaoxiami.gitbook.io/elasticsearch

# Install Elasticsearch

- Install command form：https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html#install-deb

```jsx
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.15.3-amd64.deb
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.15.3-amd64.deb.sha512
shasum -a 512 -c elasticsearch-8.15.3-amd64.deb.sha512 
sudo dpkg -i elasticsearch-8.15.3-amd64.deb
```

```jsx
sudo systemctl start elasticsearch.service
```

- reset password: https://www.elastic.co/guide/en/elasticsearch/reference/current/reset-password.html

`/usr/share/elasticsearch/bin`

bin/elasticsearch-reset-password -u elastic

```jsx
Notebook:PnrYlOe5WLR8WfVzf8QG
Host:6Ryx6oCrbtfD_od7VubO
```

# Install Kibana

- Install：https://www.elastic.co/guide/en/kibana/current/targz.html#install-linux64

```jsx
curl -O https://artifacts.elastic.co/downloads/kibana/kibana-8.15.3-darwin-x86_64.tar.gz
curl https://artifacts.elastic.co/downloads/kibana/kibana-8.15.3-darwin-x86_64.tar.gz.sha512 | shasum -a 512 -c - 
tar -xzf kibana-8.15.3-darwin-x86_64.tar.gz
cd kibana-8.15.3/ 
```

# Integrate ElasticSearch by Java

Tips： high level  and low level means the degree of abstract and package

- Recommended from official: https://www.elastic.co/guide/en/elasticsearch/client/index.html
- Evaluation about client: https://ost.51cto.com/posts/13966

# Build ES DSL

```jsx
GET /_search
{
  "query": { 
    "bool": { 
      "must": [
        { "match": { "title":   "Search"        }},
        { "match": { "content": "Elasticsearch" }}
      ],
      "filter": [ 
        { "term":  { "status": "published" }},
        { "range": { "publish_date": { "gte": "2015-01-01" }}}
      ]
    }
  }
}
```

## Single-query：signle function

- match：Analyze then search the full text
- term：exact match ，for structured data
- terms: actually, in
- range：just for range date,number and so on
- exists：exists or not

# Multi-query: combine signle-base query by bool keyword

- must：and
- should：or
- must_not：not , no score
- filter：and, no score

# Other query

## boosting

```jsx
GET /index_name/_search
{
  "query": {
    "boosting": {
      "positive": {
        "match": {
          "field_name": "apple"
        }
      },
      "negative": {
        "match": {
          "field_name": "pie"
        }
      },
      "negative_boost": 0.5  // 减少负向查询的影响
    }
  }
}

1. 查询类型：boosting
boosting查询允许你定义一个正向查询和一个负向查询，并通过negative_boost参数来调整负向查询的影响。
2. 正向查询：positive
positive部分定义了一个match查询，查找field_name字段中包含"apple"的文档。这些文档将获得正常的相关性得分。
3. 负向查询：negative
negative部分定义了另一个match查询，查找field_name字段中包含"pie"的文档。这些文档的得分将被降低。
4. 负向提升：negative_boost
negative_boost参数设置为0.5，这意味着如果一个文档同时匹配了正向查询和负向查询，它的最终得分将是正向得分乘以0.5。这会降低这些文档在搜索结果中的排名。
使用场景
文档排序：**这个查询适合需要优先显示某些文档，同时降低包含特定不希望出现的内容的文档的场景。**例如，在一个食谱搜索中，想要优先显示包含"苹果"的食谱，同时降低那些包含"派"的食谱。

灵活性：通过调整negative_boost的值，可以灵活地控制负向查询的影响程度，以满足不同的需求。

注意事项
性能：boosting查询的性能通常较好，但在处理复杂查询时，仍需注意查询的效率。
调试：在实际应用中，建议进行调试和测试，以确保查询结果符合预期。
```

## **Constant score query**

```jsx
GET /_search
{
  "query": {
    "constant_score": {
      "filter": {
        "term": { "user.id": "kimchy" }
      },
      "boost": 1.2
    }
  }
}

Constant Score Query 通过将所有匹配文档的得分设置为一个常量值，从而跳过了得分计算的步骤。

**跳过得分计算，提升过滤性能。**

```

## Nested Query

```jsx
GET /blog/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "nested": {
            "path": "comments",
            "query": {
              "bool": {
                "must": [
                  {
                    "match": {
                      "comments.author": "kimchy"
                    }
                  }
                ]
              }
            }
          }
        }
      ],
      "filter": [
        {
          "term": {
            "status": "published"  // 过滤条件
          }
        }
      ]
    }
  }
}

Nested Query 通常与复杂的 JSON 数据结构一起使用，尤其是在处理嵌套文档时。

```