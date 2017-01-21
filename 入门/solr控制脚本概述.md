Solr包含一个称为 `bin/solr` 的脚本，它允许你启动和停止Solr，创建和删除集合或核心，对ZooKeeper执行操作并检查Solr和配置的分片的状态。你可以在Solr安装的 `bin/` 目录中找到该脚本。`bin/solr` 脚本通过提供简单的命令和选项来快速实现共同的目标：使Solr更容易使用。

下面的标题对应于可用的命令。对于每个命令将使用示例描述可用的选项。

更多的使用 `bin/solr` 的例子可以在整个Solr参考指南中找到，特别是在运行Solr和SolrCloud入门的章节。

* [启动和停止](#启动和停止)
 * [启动和重新启动](#启动和重新启动)
 * [停止](#停止)
* [系统信息](#系统信息)
 * 版本
 * 状态
 * 健康检查
* 集合和内核
 * 创建
 * 删除
* ZooKeeper操作
 * 上传配置集
 * 下载配置集
 * 在本地文件和ZooKeeper znode之间复制
 * 从ZooKeeper中删除znode
 * 将一个ZooKeeper znode移动到另一个节点（重命名）
 * 列出ZooKeeper znode的子节点
 * 创建znode（支持chroot）
 
# 启动和停止
## 启动和重新启动
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
