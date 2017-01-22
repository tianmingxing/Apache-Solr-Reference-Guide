Solr包含一个称为 `bin/solr` 的脚本，它允许你启动和停止Solr，创建和删除集合或核心，对ZooKeeper执行操作并检查Solr和配置的分片的状态。你可以在Solr安装的 `bin/` 目录中找到该脚本。`bin/solr` 脚本通过提供简单的命令和选项来快速实现共同的目标：使Solr更容易使用。

下面的标题对应于可用的命令。对于每个命令将使用示例描述可用的选项。

更多的使用 `bin/solr` 的例子可以在整个Solr参考指南中找到，特别是在运行Solr和SolrCloud入门的章节。

* [启动和停止](#启动和停止)
 * [启动和重新启动](#启动和重新启动)
 * [停止](#停止)
* [系统信息](#系统信息)
 * [版本](#版本)
 * [状态](#状态)
 * [健康检查](#健康检查)
* [集合和内核](#集合和内核)
 * [创建](#创建)
 * [删除](#删除)
* [ZooKeeper操作](#ZooKeeper操作)
 * [上传配置集](#上传配置集)
 * [下载配置集](#下载配置集)
 * [在本地文件和ZooKeeper znode之间复制](#在本地文件和zookeeper-znode之间复制)
 * [从ZooKeeper中删除znode](#从ZooKeeper中删除znode)
 * [将一个ZooKeeper znode移动到另一个节点（重命名）](#将一个zookeeper-znode移动到另一个节点（重命名）)
 * [列出ZooKeeper znode的子节点](#列出zookeeper-znode的子节点)
 * [创建znode（支持chroot）](#创建znode（支持chroot）)
 
# 启动和停止

## 启动和重新启动

`start` 命令启动Solr。`restart` 命令允许你在Solr已经运行或者已经停止时重新启动它。

启动和重新启动命令有几个选项，允许你在SolrCloud模式下运行，使用示例配置集，以非默认的主机名或端口开始，并指向本地ZooKeeper集合。

```
bin/solr start [options]
bin/solr start -help
bin/solr restart [options]
bin/solr restart -help
```

当使用 `restart` 命令时，必须传递你在启动Solr时最初传递的所有参数。在后台发出停止请求，因此Solr将在再次启动之前先停止。如果没有节点已在运行，重新启动将跳过停止步骤并继续启动Solr。

### 可用参数

|参数|描述|示例|
|---|---|----|
|`-a "<string>"`|使用其他JVM参数（例如以-X开头的那些参数）启动Solr。如果传递以“-D”开头的JVM参数，则可以省略-a选项。|`bin/solr start -a "-Xdebug -Xrunjdwp:transport=dt_socket, server=y,suspend=n,address=1044"`|
|`-cloud`|在SolrCloud模式下启动Solr，它还将启动Solr附带的嵌入式ZooKeeper实例<br>此选项可以简化为-c。<br>如果你已经运行了要使用的ZooKeeper集合而不是嵌入式（单节点）ZooKeeper，则还应该传递-z参数。|`bin/solr start -c`|
|`-d <dir>`|定义服务器目录，默认为server（如$SOLR_HOME/server）。自定义此选项是不常见的。当在同一主机上运行多个Solr实例时，更常见的是为每个实例使用相同的服务器目录，并使用-s选项使用唯一的Solr主目录。|`bin/solr start -d newServerDir`|
|`-e <name>`|使用示例配置启动Solr。提供这些示例是为了帮助你更快地开始使用Solr，或者只尝试一个特定的功能。<br>可用的选项有：cloud、techproducts、dih和schemaless|`bin/solr start -e schemaless`|
|`-f`|在前台启动Solr；当使用-e选项运行示例时，不能使用此选项。|`bin/solr start -f`|
|`-h <hostname>`|使用定义的主机名启动Solr。如果未指定此参数，则将假定为“localhost”。|`bin/solr start -h search.mysolr.com`|
|`-m <memory>`|使用定义的值作为JVM的最小（-Xms）和最大（-Xmx）堆大小启动Solr。|`bin/solr start -m 1g`|
|`-noprompt`|启动Solr并禁止使用其他选项可能看到的任何提示，这将具有隐含地接受所有默认值的副作用。<br>例如，当使用“cloud”示例时，交互式会话将引导你完成SolrCloud群集的几个选项。如果要接受所有默认值，你只需向你的请求中添加-noprompt选项即可。|`bin/solr start -e cloud -noprompt`|
|`-p <port>`|在定义的端口上启动Solr。如果未指定，将使用“8983”。|`bin/solr start -p 8655`|
|`-s <dir>`|设置solr.solr.home系统属性；Solr将在此目录下创建核心目录。这允许你在同一主机上运行多个Solr实例，同时使用-d参数重用相同的服务器目录集。如果设置，指定的目录应该包含solr.xml文件，除非ZooKeeper中存在solr.xml。默认值为 `server/solr`。<br>运行示例（-e）时，将忽略此参数，因为solr.solr.home取决于运行的示例。|`bin/solr start -s newHome`|
|`-v`|更详细的日志输出。这会将log4j的日志记录级别从INFO更改为DEBUG，与你相应地编辑log4j.properties具有相同的效果。|`bin/solr start -f -v`|
|`-z <zkHost>`|使用定义的ZooKeeper连接字符串启动Solr。此选项仅与-c选项一起使用，以在SolrCloud模式下启动Solr。如果未提供此选项，Solr将启动嵌入式ZooKeeper实例，并将该实例用于SolrCloud操作。|`bin/solr start -c -z server1:2181,server2:2181`|
|`-force`|如果尝试以root用户身份启动Solr，脚本将退出并显示一条警告，指出运行Solr作为“root”可能会导致问题。可以使用-force参数覆盖此警告。|`sudo bin/solr start -force`|

### 设置Java系统属性

### SolrCloud模式


### 使用示例配置运行


## 停止
# 系统信息
## 版本
## 状态
## 健康检查
# 集合和内核
## 创建
## 删除
# ZooKeeper操作
## 上传配置集
## 下载配置集
## 在本地文件和ZooKeeper znode之间复制
## 从ZooKeeper中删除znode
## 将一个ZooKeeper znode移动到另一个节点（重命名）
## 列出ZooKeeper znode的子节点
## 创建znode（支持chroot）
