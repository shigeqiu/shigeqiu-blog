# Elasticsearch

- [官方文档](https://www.elastic.co/guide/index.html)
- [中文版-权威指南](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)


## 分析与分析器

使用 analyze API 来看文本是如何被分析的

```
GET /_analyze
{
  "analyzer": "standard",
  "text": "Text to analyze"
}
```

结果：
```
{
   "tokens": [
      {
         "token":        "text",
         "start_offset": 0,
         "end_offset":   4,
         "type":         "<ALPHANUM>",
         "position":     1
      },
      {
         "token":        "to",
         "start_offset": 5,
         "end_offset":   7,
         "type":         "<ALPHANUM>",
         "position":     2
      },
      {
         "token":        "analyze",
         "start_offset": 8,
         "end_offset":   15,
         "type":         "<ALPHANUM>",
         "position":     3
      }
   ]
}
```
- token 是实际存储到索引中的词条。
- position 指明词条在原始文本中出现的位置。
- start_offset 和 end_offset 指明字符在原始字符串中的位置。
