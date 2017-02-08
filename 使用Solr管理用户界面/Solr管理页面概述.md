Solr自带有Web管理界面，使Solr管理者和程序员可以轻松查看Solr配置详细信息，运行查询和分析文档字段，以便调度Solr配置并访问在线文档和其他帮助。

![](/assets/solr_admin.png)

访问链接 `http://hostname:8983/solr/` 将显示首页，其中分为两部分。

页面左侧是Solr商标，在下面可以看到导航到不同的页面菜单。其中第一级菜单用于系统级信息和配置，并提供对其Logging, Collection/Core Administration 和 Java Properties 页面的访问。除这些外在菜单下面至少有一个下拉列表，里面可以看到为当前实例配置的Solr核心。在SolrCloud节点上，一个附加的下拉列表显示此集群中的所有集合。单击集合或核心名称显示指定集合或核心的辅助信息菜单，例如Schema Browser, Config Files, Plugins & Statistics，以及对索引数据执行查询的功能。

页面中心显示所选选项的详细信息。这可以包括用于所请求数据的选项或文本或图形表示的子导航。有关详细信息，请参阅本指南中有关每个页面的部分。

所有客户端可以使用相同的HTTP API接口来操作Solr服务。