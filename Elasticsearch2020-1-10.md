# Elasticsearch

## 一些操作

### 创建索引+添加mapping

```dsl
PUT /index
{
  "settings": {
    "number_of_replicas": 1
  },
  "mappings": {
    "type":{
      "properties": {
        "id": {
          "type": "keyword"
        },
        "name":{
          "type": "text"
        },
        "age":{
          "type":"double"
        }
      }
    }
  }
}
```

### 插入一条数据

```dsl
PUT index/type/001
{
  "id":"001",
  "name":"王孟娜",
  "age":22.0
}
```

### 修改某个字段的值

```dsl
POST index/type/001/_update
{
  "doc": {
    "age":21.9
  }
}
```

### 查看index/type下全部数据

```dsl
GET index/type/_search
{
  "query": {
    "match_all": {}
  }
}
```

### 判断文档是否存在

```dsl
HEAD index/type/001
```


