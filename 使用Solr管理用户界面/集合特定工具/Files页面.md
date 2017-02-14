“Files”页面允许你浏览和查看所选集合的各种配置文件（例如solrconfig.xml和schema文件）。

![](/assets/files-screen.png)

如果使用SolrCloud，显示的文件是存储在ZooKeeper中的此集合的配置文件（使用upconfig），对于单节点（单机模式）安装的Solr，将显示 `./conf` 目录中的所有文件。

虽然solrconfig.xml定义了Solr怎样为内容创建索引并如何响应每个查询请求，但schema允许你定义内容的数据类型（字段类型），传入的文档将被定义的字段所分解，在传入文档中基于schema中定义的字段名生成任何动态字段。

不能在本页面编辑配置文件，因此你必须使用某种文本编辑器。

此页面与“Schema Browser”页面相关，因为它们都可以显示schema中的信息，但是“Schema Browser”页面提供了一种钻取分析链并显示字段类型，字段和动态字段规则之间的联系。

