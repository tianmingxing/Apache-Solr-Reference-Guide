在SolrCloud模式下运行时，“Logging”和“Collections/Core Admin”之间的管理界面中将显示一个“Cloud”菜单，其中提供有关集群中每个集合和节点的状态信息，以及访问存储在Zookeeper中的低级数据。

> **只有使用SolrCloud模式启动时才有此菜单**
> “Cloud”菜单选项仅适用于以SolrCloud模式运行的Solr实例。Solr的单节点或主/从复制实例不会显示此选项。

单击左侧导航中的“Cloud”菜单下拉弹出子菜单，上面有“Tree”，“Graph”，“Graph (Radial)”和“Dump”菜单。默认视图（“Graph”）显示每个集合，构成这些集合的分片以及每个分片中的每个副本的地址。

此示例使用“bin/solr -e cloud -noprompt”示例命令创建的非常简单的双节点集群。除了2个分片，2个副本“gettingstarted”集合，还有一个额外的“films”集合，包括一个单一的分片/副本：

![](/assets/cloud-graph.png)

“Graph (Radial)”页面提供每个节点的不同视图。使用相同的示例集群，径向图视图看起来像：

![](/assets/cloud-radial.png)

“Tree”选项显示ZooKeeper中数据的目录结构，包括关于“live_nodes”和“ovisorer”状态的集群范围的信息，以及收集特定信息，如state.json，当前主分片和正在使用的配置文件。在这个例子中，我们看到“films”集合的state.json文件定义。

![](/assets/cloud-tree.png)

最后一个选项是“Dump”，它返回一个JSON文档，其中包含所有节点，它们的内容和子节点（递归）。这可以用于导出Solr保存在ZooKeeper中的所有数据的快照，并且可以帮助调试SolrCloud问题。
