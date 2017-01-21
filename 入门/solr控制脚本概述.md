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
|-a "<string>"|使用其他JVM参数（例如以-X开头的那些参数）启动Solr。如果传递以“-D”开头的JVM参数，则可以省略-a选项。|bin/solr start -a "-Xdebug -Xrunjdwp:transport=dt_socket, server=y,suspend=n,address=1044"|
|-cloud|在SolrCloud模式下启动Solr，它还将启动Solr附带的嵌入式ZooKeeper实例<br>此选项可以简化为-c。|bin/solr start -c|
||||
||||
||||
||||
||||
||||
||||
||||
||||

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
