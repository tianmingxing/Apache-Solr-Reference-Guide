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

|参数|描述|示例|
|-----|-----|------|
|`-p <port>`|停止在指定端口上运行的Solr进程。如果运行多个实例或以SolrCloud模式运行，则需要在单独的请求中指定端口或使用-all选项。|`bin/solr stop -p 8983`|
|`-all`|停止所有运行的具有有效PID的Solr实例。|`停止所有运行的具有有效PID的Solr实例。`|
|`-k <key>`|停止键用于保护不会意外停止Solr;默认为“solrrocks”。|`bin/solr stop -k solrrocks`|

# 系统信息

## 版本

version命令简单地返回当前安装的Solr的版本，并立即存在。

```
$ bin/solr version
X.Y.0
```

## 状态

status命令显示在本地系统上找到的任何Solr节点的基本信息，以JSON格式显示出来。status命令使用 `SOLR_PID_DIR` 环境变量来查找Solr进程ID文件以查找运行的Solr实例；`SOLR_PID_DIR` 变量默认为bin目录。

```
bin/solr status
```

输出将包括集群的每个节点的状态，如本示例中所示：

```
Found 2 Solr nodes:
 
Solr process 39920 running on port 7574
{
  "solr_home":"/Applications/Solr/example/cloud/node2/solr/",
  "version":"X.Y.0",
  "startTime":"2015-02-10T17:19:54.739Z",
  "uptime":"1 days, 23 hours, 55 minutes, 48 seconds",
  "memory":"77.2 MB (%15.7) of 490.7 MB",
  "cloud":{
    "ZooKeeper":"localhost:9865",
    "liveNodes":"2",
    "collections":"2"}}
 
Solr process 39827 running on port 8865
{
  "solr_home":"/Applications/Solr/example/cloud/node1/solr/",
  "version":"X.Y.0",
  "startTime":"2015-02-10T17:19:49.057Z",
  "uptime":"1 days, 23 hours, 55 minutes, 54 seconds",
  "memory":"94.2 MB (%19.2) of 490.7 MB",
  "cloud":{
    "ZooKeeper":"localhost:9865",
    "liveNodes":"2",
    "collections":"2"}}
```

## 健康检查

healthcheck命令在以SolrCloud模式运行时为集合生成JSON格式的运行状况报告。运行状况报告提供有关集合中所有分片的每个副本的状态的信息，包括已提交文档的数量及其当前状态。

```
bin/solr healthcheck [options]
bin/solr healthcheck -help
```

### 可用参数

|参数|描述|示例|
|-----|----|----|
|`-c <collection>`|针对（必需）运行healthcheck的集合的名称。|`bin/solr healthcheck -c gettingstarted`|
|`-z <zkhost>`|ZooKeeper连接默认为 `localhost:9983`。如果你在除8983之外的端口上运行Solr，则必须指定ZooKeeper连接信息。|`bin/solr healthcheck -z localhost:2181`|

下面是使用非标准ZooKeeper连接的健康检查请求和响应示例，运行2个节点：

```
$ bin/solr healthcheck -c gettingstarted -z localhost:9865
 
{
  "collection":"gettingstarted",
  "status":"healthy",
  "numDocs":0,
  "numShards":2,
  "shards":[
    {
      "shard":"shard1",
      "status":"healthy",
      "replicas":[
        {
          "name":"core_node1",
          "url":"http://10.0.1.10:8865/solr/gettingstarted_shard1_replica2/",
          "numDocs":0,
          "status":"active",
          "uptime":"2 days, 1 hours, 18 minutes, 48 seconds",
          "memory":"25.6 MB (%5.2) of 490.7 MB",
          "leader":true},
        {
          "name":"core_node4",
          "url":"http://10.0.1.10:7574/solr/gettingstarted_shard1_replica1/",
          "numDocs":0,
          "status":"active",
          "uptime":"2 days, 1 hours, 18 minutes, 42 seconds",
          "memory":"95.3 MB (%19.4) of 490.7 MB"}]},
    {
      "shard":"shard2",
      "status":"healthy",
      "replicas":[
        {
          "name":"core_node2",
          "url":"http://10.0.1.10:8865/solr/gettingstarted_shard2_replica2/",
          "numDocs":0,
          "status":"active",
          "uptime":"2 days, 1 hours, 18 minutes, 48 seconds",
          "memory":"25.8 MB (%5.3) of 490.7 MB"},
        {
          "name":"core_node3",
          "url":"http://10.0.1.10:7574/solr/gettingstarted_shard2_replica1/",
          "numDocs":0,
          "status":"active",
          "uptime":"2 days, 1 hours, 18 minutes, 42 seconds",
          "memory":"95.4 MB (%19.4) of 490.7 MB",
          "leader":true}]}]}
```

# 集合和内核

`bin/solr` 脚本还可以帮助你创建新集合（在SolrCloud模式下）或核心（在单机模式下），或删除集合。

## 创建

create命令检测Solr运行的模式（单击或SolrCloud），然后根据模式创建核心或集合。

```
bin/solr create [options]
bin/solr create -help
```
### 可用参数

|参数|描述|示例|
|----|-----|-----|
|`-c <name>`|要创建的核心或集合的名称（必需）。|`bin/solr create -c mycollection`|
|`-d <confdir>`|配置目录。默认为data_driven_schema_configs。|`bin/solr create -d basic_configs`|
|`-n <configName>`|配置名称。此默认值与核心或集合的名称相同。|`bin/solr create -n basic`|
|`-p <port>`|本地Solr实例的端口发送create命令；默认情况下，脚本尝试通过查找正在运行的Solr实例来检测端口。<br>如果您在同一主机上运行多个独立的Solr实例，则此选项很有用，因此需要您具体说明创建核心的实例。|`bin/solr create -p 8983`|
|`-s <shards>`<br>`-shards`|拆分集合的分片数，默认为1;仅适用于Solr在SolrCloud模式下运行时。|`bin/solr create -s 2`|
|`-rf <replicas>`<br>`-replicationFactor`|集合中每个文档的副本数。默认值为1（无复制）。|`bin/solr create -rf 2`|
|`-force`|如果用root用户尝试运行create，则脚本将退出并显示一条警告，指出运行Solr或针对Solr的操作为“root”可能会导致问题。可以使用-force参数覆盖此警告。|`bin/solr create -c foo -force`|

### 配置目录和SolrCloud

在SolrCloud中创建集合之前，集合使用的配置目录必须上传到ZooKeeper。 create命令支持如何使用集合和配置目录工作的多个用例。你需要做的主要决定是在ZooKeeper中的配置目录是否应该在多个集合之间共享。让我们通过几个例子来说明配置目录在SolrCloud中如何工作。

首先，如果不提供-d或-n选项，那么默认配置（$SOLR_HOME/server/solr/configsets/data_driven_schema_configs/conf）将使用与集合相同的名称并上传到ZooKeeper。例如，使用以下命令 ` bin/solr create -c contacts` 将导致 `data_driven_schema_configs` 配置上传到ZooKeeper的 `/configs/contacts` 中。如果创建另一个集合，通过执行 `bin/solr create -c contacts2`，`data_driven_schema_configs` 目录的另一个副本将被上传到ZooKeeper的 `/configs/contacts2` 下。对contacts集合的配置所做的任何更改都不会影响contacts2集合。简单地说，使用默认参数时服务会为你创建的每个集合创建唯一的配置目录。

你可以使用-n选项覆盖ZooKeeper中配置目录默认的名称。例如，命令 `bin/solr create -c logs -d basic_configs -n basic` 将 `server/solr/configsets/basic_configs/conf` 目录上传到ZooKeeper的 `/configs/basic`。

请注意，我们使用-d选项指定与默认值不同的配置。Solr在 `server/solr/configsets` 下提供了几个内置配置。但是，你也可以使用-d选项提供自己的配置目录路径。例如，命令 `bin/solr create -c mycoll -d /tmp/myconfigs`，将 `/tmp/myconfigs` 上传到ZooKeeper的 `/configs/mycoll` 下面。再一次提醒你的是：配置目录以集合命名，除非你使用-n选项覆盖它。

其他集合可以通过使用-n选项指定共享配置的名称来共享相同的配置。例如，以下命令将以某个存在的基本配置来创建新的集合：`bin/solr create -c logs2 -n basic`。

### 数据驱动架构和共享配置

当数据被索引时data_driven_schema_configs的schema可以改变。因此，我们建议你不要在集合之间共享数据驱动配置，除非你确定所有集合都应该继承将数据索引到其中一个集合时所做的更改。

## 删除

delete命令检测Solr运行的模式（单机或SolrCloud），然后根据需要删除指定的核心（单机）或集合（SolrCloud）。

```
bin/solr delete [options]
bin/solr delete -help
```

如果在SolrCloud模式下运行，delete命令将检查你要删除的集合所使用的配置目录是否被其他集合使用。如果没有，那么配置目录也会从ZooKeeper中删除。例如，如果你通过执行创建集合 `bin/solr create -c contacts`，然后删除命令 `bin/solr delete -c contacts` 将检查 `/configs/contacts` 配置目录是否被任何其他集合使用。如果没有，那么 `/configs/contacts` 目录从ZooKeeper中删除。

### 可用参数

|参数|描述|示例|
|----|-----|-----|
|`-c <name>`|要删除的核心或集合的名称（必需）。|`bin/solr delete -c mycoll`|
|`-deleteConfig <true或false>`|从ZooKeeper中删除配置目录。默认值为true。<br>如果配置目录正在被另一个集合使用，即使你将-deleteConfig设置为true，它也不会被删除。|`bin/solr delete -deleteConfig false`|
|`-p <port>`|要向其发送delete命令的本地Solr实例的端口。默认情况下，脚本尝试通过查找运行的Solr实例来检测端口。<br>如果你在同一主机上运行多个独立的Solr实例，则此选项非常有用，因此需要你具体了解从哪个实例删除核心。|`bin/solr delete -p 8983`|

# ZooKeeper操作

`bin/solr` 脚本可以对ZooKeeper进行某些操作。这些操作仅适用于SolrCloud模式。操作作为子命令可用，每个子命令都有自己的选项集。

```
bin/solr zk [sub-command] [options]
bin/solr zk -help
```

提示：Solr应该至少已经启动一次，然后发出这些命令来使用znode Solr预期来初始化ZooKeeper。一旦ZooKeeper初始化，Solr不需要在任何节点上运行就可以使用这些命令。

## 上传配置集

使用zk upconfig命令将预配置的配置集或自定义配置集中的一个上传到ZooKeeper。

### 可用参数（所有参数都是必需的）

|参数|描述|示例|
|-----|-----|------|
|`-n <name>`|ZooKeeper中配置集的名称。此命令将上传配置集到“configs” ZooKeeper节点。|`-n myconfig`|
|`-d <configset dir>`|配置设置为上传的路径。它应该有一个“conf”目录，紧接在它下面，依次包含solrconfig.xml等。<br>如果只提供一个名称，将会检查 `$SOLR_HOME/server/solr/configsets` 这个名称。可以填写绝对路径。|`-d directory_under_configsets -d /path/to/configset/source`|
|`-z <zkHost>`|ZooKeeper连接信息。如果ZK_HOST有在solr.in.sh或solr.in.cmd中定义，则不必要。|`-z 123.321.23.43:2181`|

这些命令的一个例子，这些参数是：

```
bin/solr zk upconfig -z 111.222.333.444:2181 -n mynewconfig -d /path/to/configset
```

> **更改配置时重新加载集合**
> 此命令不会自动使更改生效！它只是将配置集上传到ZooKeeper。你可以使用Collection API的RELOAD命令重新加载此配置集的任何集合。

## 下载配置集

使用 `zk downconfig` 命令将配置集从ZooKeeper下载到本地文件系统。

### 可用参数（所有参数都是必需的）

|参数|描述|示例|
|----|----|----|
|`-n <name>`|ZooKeeper中下载的配置名称。进入后台管理页面Cloud -> Tree -> configs节点列出所有可用的配置集。|`-n myconfig`|
|`-d <configset dir>`|将下载的配置集写入的路径。如果只提供一个名称，`$ SOLR_HOME/server/solr/configsets` 将是父级。也可以提供绝对路径。<br>_在任一情况下，目标上的预先存在的配置将被覆盖！_|`-d directory_under_configsets -d /path/to/configset/destination`|
|`-z <zkHost>`|ZooKeeper连接信息。如果ZK_HOST在solr.in.sh或solr.in.cmd中定义，则不必要。|`-z 123.321.23.43:2181`|

这些命令的一个例子，这些参数是：

```
bin/solr zk downconfig -z 111.222.333.444:2181 -n mynewconfig -d /path/to/configset
```

“最佳实践”是将你的配置集保存到某种形式的版本控制系统。在这种情况下，downconfig应该很少使用。

## 在本地文件和ZooKeeper znode之间复制

使用 `zk cp` 命令在ZooKeeper znode和本地驱动器之间传输文件和目录。此命令将从本地驱动器复制到ZooKeeper，从ZooKeeper到本地驱动器或从ZooKeeper到ZooKeeper。

### 可用参数

|参数|描述|示例|
|----|----|----|
|`-r`|可选的。做递归复制。如果`<src>`有子节点，除非指定了“-r”，否则命令将失败。|`-r`|
|`<src>`|要从中复制的文件或路径。如果前缀为zk：那么源被假定为ZooKeeper。如果没有前缀或前缀是file：，这表示是本地驱动器。`<src>`或`<dest>`中的至少一个必须以“zk：”作为前缀，否则命令将失败。|`zk:/configs/myconfigs/solrconfig.xml`<br>`file:/Users/apache/configs/src`|
|`<dest>`|要复制到的文件或路径。如果前缀为zk：那么源被假定为ZooKeeper。如果没有前缀或前缀是file：，这表示是本地驱动器。`<src>`或`<dest>`中的至少一个必须以zk：开头，否则命令将失败。如果`<dest>`以斜杠字符结尾，它命名一个目录。|`zk:/configs/myconfigs/solrconfig.xml`<br>`file:/Users/apache/configs/src`|
|`-z <zkHost>`|ZooKeeper连接信息。如果ZK_HOST在solr.in.sh或solr.in.cmd中定义，则不必要。|`-z 123.321.23.43:2181`|

这些命令的一个例子，这些参数是：
递归地将目录从本地复制到ZooKeeper。

```
bin/solr zk cp -r  file:/apache/confgs/whatever/conf zk:/configs/myconf -z 111.222.333.444:2181
```

将单个文件从ZooKeeper复制到本地。
```
bin/solr zk cp zk:/configs/myconf/managed_schema /configs/myconf/managed_schema -z 111.222.333.444:2181
```

## 从ZooKeeper中删除znode

使用 `zk rm` 命令从ZooKeeper中删除znode（所有可选子节点）

### 可用参数

|参数|描述|示例|
|----|----|----|
|`-r`||`-r`|
|`<path>`||`/configs`<br>`/configs/myconfigset`<br>`/config/myconfigset/solrconfig.xml`|
|`-z <zkHost>`||`-z 123.321.23.43:2181`|

## 将一个ZooKeeper znode移动到另一个节点（重命名）
## 列出ZooKeeper znode的子节点
## 创建znode（支持chroot）
