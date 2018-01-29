# elasticsearch 使用说明.md

## 简介

  ElasticSearch 是一个高可用开源全文检索和分析组件。提供存储服务，搜索服务，大数据准实时分析等。一般用于提供一些提供复杂搜索的应用。

## WHY（为什么选用）

- ElasticSearch 是一个准实时的搜索工具，在一般情况下延时少于一秒。
- 支持物理上的水平扩展，并拥有一套分布式协调的管理功能
- 操作比较简单
- restful 风格的API
- Elasticsearch的使用场景深入详解

## REQUIREMENT (需求)

一些复杂数据的查询导出，使用传统的数据库（关系型数据库）查询以及配合java集合操作，很难解决的问题或者效率并不怎么好的问题，可以考虑使用

## weakness（缺点）

- 维护工作量大

## HOWTO (如何去做)

### 安装 & 启动

*** [下载地址](https://www.elastic.co/downloads/elasticsearch) ***

- 解压后进入到bin目录下，eg：


- 运行（binelasticsearch.bat）eg：


- 访问：http://127.0.0.1:9200/可以看到 eg:

```
{
    "name": "bJs1ahK",
    "cluster_name": "elasticsearch",
    "cluster_uuid": "w0fjGOMARzuxt5K5bia07g",
    "version": {
        "number": "5.6.3",
        "build_hash": "1a2f265",
        "build_date": "2017-10-06T20:33:39.012Z",
        "build_snapshot": false,
        "lucene_version": "6.6.1"
    },
    "tagline": "You Know, for Search"
}
```
- *** [配置参考](http://blog.csdn.net/u012270682/article/details/72934270) ***

### 程序中使用

- *** [API使用文档](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/_javadoc.html) ***

- 创建client

```
public class ElasticSearchClient {

private volatile static TransportClient client;

private ElasticSearchClient (){}

public static TransportClient getInstanceClient() {
    if (client == null) {
        synchronized (TransportClient.class) {
            if (client == null) {
                /**
                 * 1. client.transport.ignore_cluster_name  时候校验服务名字 true 校验  false 不校验
                 * 2. client.transport.ping_timeout   链接联通时间 Defaults 5s
                 * 3. client.transport.nodes_sampler_interval  节点调用时间   Defaults 5s
                 */
                Settings settings = Settings.builder()
                        .put("cluster.name", "elasticsearch").build();

                try {
                    client = new PreBuiltTransportClient(settings)
                            .addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("localhost"), 9200));
                } catch (UnknownHostException e) {
                    e.printStackTrace();
                }

            }
        }
    }
    return client;
}
}
```

- 创建字段映射

```
public class ElasticsearchIndex {

    public String jsonIndex() {
        String json = "{" +
                "\"userId\":\"1001\"," +
                "\"userName\":\"test\"," +
                "\"userSex\":true" +
                "\"userAge\":\"28\"" +
                "}";
        return json;
    }

    public Map mapIndex() {
        Map<String, Object> json = new HashMap<String, Object>();
        json.put("userId", "1001");
        json.put("userName", "test");
        json.put("userSex", true);
        json.put("userAge", "28");
        return json;
    }


    public byte[] objIndex() {
        ObjectMapper mapper = new ObjectMapper(); // create once, reuse
        User user = new User();
        user.setUserId("1001");
        user.setUserName("test");
        user.setUserAge(28);
        user.setUserSex(true);
        byte[] json = new byte[0];
        try {
            json = mapper.writeValueAsBytes(user);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return json;
    }

    public String buildIndex() {
        XContentBuilder builder = null;
        try {
            builder = jsonBuilder()
                    .startObject()
                    .field("userId", "1001")
                    .field("userName", "test")
                    .field("userSex", true)
                    .field("userAge", 28)
                    .endObject();
        } catch (IOException e) {
            e.printStackTrace();
        }

        try {
            return builder.string();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

-  操作测试

*** 新增 ***

```
public void signInsert() {
    TransportClient client = ElasticSearchClient.getInstanceClient();
    for(int i=0;i<100;i++) {
        IndexResponse response = client.prepareIndex("users", "user", ("" + i))
                .setSource(ElasticsearchIndex.buildIndex()).get();
        System.out.println(response.getIndex());
    }
}
```

*** 修改 **

```
public void signUpdate() {
   TransportClient client = ElasticSearchClient.getInstanceClient();
   UpdateRequest updateRequest = null;
   try {
       updateRequest = new UpdateRequest("users", "user", "2")
               .doc(jsonBuilder()
                       .startObject()
                       .field("userName", "male")
                       .endObject());
   } catch (IOException e) {
       e.printStackTrace();
   }
   try {
       UpdateResponse response = client.update(updateRequest).get();
       System.out.println(response.getIndex());
   } catch (InterruptedException e) {
       e.printStackTrace();
   } catch (ExecutionException e) {
       e.printStackTrace();
   }
}
```

*** 根据ID删除 ***

```
public void signDel(){
   TransportClient client = ElasticSearchClient.getInstanceClient();
   DeleteResponse response = client.prepareDelete("users", "user", "3").get();
   System.out.println(response.getIndex());
}
```

*** 根据查询条件删除 ***

```
public void searchDel(){
   TransportClient client = ElasticSearchClient.getInstanceClient();

   BulkByScrollResponse response =
           DeleteByQueryAction.INSTANCE.newRequestBuilder(client)
                   .filter(QueryBuilders.matchQuery("userName", "male"))
                   .source("users")
                   .get();

   long deleted = response.getDeleted();

   System.out.println(deleted);
}
```

*** 查询单条 ***

```
public void get(){
    TransportClient client = ElasticSearchClient.getInstanceClient();
    GetResponse response = client.prepareGet("users", "user", "1").get();
    if(response.isExists()){
        System.out.println(response.getSourceAsString());
    }
    System.out.println(response.getIndex());
}
```

*** 批量查询 ***

- [查询说明](http://blog.csdn.net/geloin/article/details/8907731)

```
public void search(){
    TransportClient client = ElasticSearchClient.getInstanceClient();

    /**
     * index 可以多个
     * type 可以多个
     */
    SearchResponse response = client.prepareSearch("users")
            .setTypes("user")
            .setSearchType(SearchType.DFS_QUERY_THEN_FETCH)
            .setQuery(QueryBuilders.termQuery("userName", "test"))                 // Query
            .setPostFilter(QueryBuilders.rangeQuery("userAge").from(12).to(30))     // Filter
            .setFrom(0).setSize(20).setExplain(true)
            .get();

    System.out.println(response.getHits().getHits().length);
    System.out.println(response.getHits().getAt(0).getSourceAsString());
}
```

## 参考博客

- [elasticsearch 配置说明](http://www.cnblogs.com/ljhdo/p/4959412.html)

- [elasticsearch 索引说明](http://www.cnblogs.com/ljhdo/p/4981928.html)
