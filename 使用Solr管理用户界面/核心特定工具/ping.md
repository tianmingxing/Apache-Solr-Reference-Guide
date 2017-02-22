选择Core名称下的Ping会发出ping请求，以检查核心是否已启动并响应请求。

![](/assets/ping.png)

Ping执行的搜索使用“Request Parameters API”进行配置。 请参见“Implicit RequestHandlers”，以便用于“/admin/ping”端点的参数集。

点击Ping菜单并不会打开新的页面，但可以在单击集合名称时显示在核心概述页面上看到请求的状态。请求已采取的时间长度显示在Ping选项旁边（以毫秒为单位）。

# API示例

虽然在页面上可以轻松查看ping响应时间，但是当远程监控工具执行时，基础ping命令可能更有用：

## Input

```
http://localhost:8983/solr/<core-name>/admin/ping
```

此命令将ping响应的Core名称。

```
http://localhost:8983/solr/<collection-name>admin/ping?wt=json&distrib=true&indent=true
```

此命令将ping给定集合名称的所有副本以获取响应

# Sample Output

```
<response>
   <lst name="responseHeader">
      <int name="status">0</int>
      <int name="QTime">13</int>
      <lst name="params">
         <str name="q">{!lucene}*:*</str>
         <str name="distrib">false</str>
         <str name="df">_text_</str>
         <str name="rows">10</str>
         <str name="echoParams">all</str>
      </lst>
   </lst>
   <str name="status">OK</str>
</response>
```

两个API调用具有相同的输出。`status=OK` 表示节点正在响应。

# SolrJ Example

```
SolrPing ping = new SolrPing();
ping.getParams().add("distrib", "true"); //To make it a distributed request against a collection
rsp = ping.process(solrClient, collectionName);
int status = rsp.getStatus();
```