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
|`-z <zkHost>`|使用定义的ZooKeeper连接启动Solr。此选项仅与-c选项一起使用，以在SolrCloud模式下启动Solr。如果未提供此选项，Solr将启动嵌入式ZooKeeper实例，并将该实例用于SolrCloud操作。|`bin/solr start -c -z server1:2181,server2:2181`|
|`-force`|如果尝试以root用户身份启动Solr，脚本将退出并显示一条警告，指出运行Solr作为“root”可能会导致问题。可以使用-force参数覆盖此警告。|`sudo bin/solr start -force`|

To emphasize how the default settings work take a moment to understand that the following commands are equivalent:
（译者注：下面两条语句执行的结果是相同，像第一条语句不指定参数则默认第二条语句的那些参数）

```
bin/solr start
bin/solr start -h localhost -p 8983 -d server -s solr -m 512m
```

如果默认值适合你的需要，则不需要在启动时定义所有选项。

### 设置Java系统属性

`bin/solr` 脚本将以-D开头的任何其他参数传递给JVM，这允许你设置任意的Java系统属性。例如，要将 `autoSoftCommit` 提交频率设置为3秒，你可以：

```
bin/solr start -Dsolr.autoSoftCommit.maxTime=3000
```

### SolrCloud模式

-c和-cloud选项是等效的：

```
bin/solr start -c
bin/solr start -cloud
```

如果指定ZooKeeper连接（如-z 192.168.1.4:2181），则Solr将连接到ZooKeeper并加入集群。如果在"cloud"模式下启动Solr时未指定-z选项，则Solr将启动一个嵌入式ZooKeeper服务器，监听Solr端口 + 1000，如果Solr在端口8983上运行，则嵌入式ZooKeeper将在端口9983上监听。

> 重要：如果你的ZooKeeper连接使用chroot，例如 `localhost:2181/solr`，则需要在使用 `bin/solr` 脚本启动SolrCloud之前创建 `/solr` znode。为此，使用下面概述的“mkroot”命令，例如：`bin/solr zk mkroot /solr -z 192.168.1.4:2181`。

在SolrCloud模式下启动时，交互式脚本会话将提示你选择要使用的配置项。

### 使用示例配置运行

```
bin/solr start -e <name>
```

示例配置允许你快速入门，以反映你希望使用Solr完成的配置。

每个示例使用托管模式启动Solr，这允许使用Schema API进行schema编辑，但不允许手动编辑schema文件如果你希望直接手动修改 `schema.xml` 文件，可以按照本节中所述更改此默认值 `Schema Factory Definition in SolrConfig`。

除非在下面的描述中另有说明，示例不启用SolrCloud也不启用 `Schemaless Mode`。

下面是安装包自带的例子：

* **cloud** 此示例在单台计算机上启动1-4个节点的SolrCloud群集。选择此项时，交互式会话将开始指导你选择要使用的初始配置项，你的示例集群的节点数、要使用的端口以及要创建的集合名称。使用此示例时，你可以从 `$SOLR_HOME/server/solr/configsets` 目录中找到相关可用配置文件。
* **techproducts** 此示例以单机模式启动Solr，它是以 `$SOLR_HOME/example/exampledocs` 目录中的示例文档设计的模式启动。所使用的配置项可以在 `$ SOLR_HOME/server/solr/configsets/sample_techproducts_configs` 中找到。
* **dih** 此示例在启用DataImportHandler（DIH）的单机模式下启动Solr，并为DIH支持的不同类型的数据（例如数据库内容、电子邮件和RSS订阅源等）预先配置了几个示例dataconfig.xml文件。所使用的配置集是为DIH定制的，可在 `$SOLR_HOME/example/example-DIH/solr/conf` 中找到。
* **schemaless** 本示例使用托管模式在单机模式下启动Solr，如SolrConfig中的“Schema Factory Definition in SolrConfig”部分所述，并提供了非常小的预定义模式。Solr将以这种配置在“Schemaless Mode”中运行，其中Solr将在模式中即时创建字段，并猜测传入文档中使用的字段类型。所使用的配置项可以在 `$SOLR_HOME/server/solr/configsets/data_driven_schema_configs` 中找到。

> 注意：运行in-foreground选项（-f）与-e选项不兼容，因为脚本需要在启动Solr服务器后执行其他任务。

## 停止

stop命令向正在运行的Solr节点发送STOP请求，从而允许它正常关闭。该命令将等待5秒钟，如果Solr仍然未正常停止，则将强制杀死进程（kill -9）。

```
bin/solr stop [options]
bin/solr stop -help
```

### 可用参数




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
