“Documents”页面提供了一个简单的形式，允许你直接从浏览器以各种格式执行各种Solr索引命令。

![](/assets/documents_add_screen.png)

你可以做如下操作：
* 复制JSON，CSV或XML文档，并将其提交到索引
* 上传文档（JSON，CSV或XML）
* 通过选择字段和字段值构造文档

第一步是定义要使用的RequestHandler（aka，'qt'）。默认情况下会定义/update。例如，要使用Solr Cell，请将请求处理链接更改为/update/extract。

然后选择文档类型以定义要加载的文档类型。其余参数将根据所选的文档类型而改变。

## JSON

当使用JSON文档类型时，其功能类似于在命令行上使用requestHandler。而不是将文档放在curl命令中，而是可以将其输入到文档输入框中。文档结构应该仍然是正确的JSON格式。

Commit Within选项表示选择何时将文档添加到索引，Overwrite选项表示如果传入的文档id已经存在则默认会覆盖现有文档，为false是不做任何操作。

此选项只会将文档添加或覆盖到索引；对于其他更新任务，请参阅Solr命令选项。

## CSV

当使用CSV文档类型时，功能类似于在命令行上使用requestHandler。不是将文档放在curl命令中，它们可以输入到文档输入框中。文档结构仍应为正确的CSV格式，列分隔，每个文档一行。

Commit Within选项表示选择何时将文档添加到索引，Overwrite选项表示如果传入的文档id已经存在则默认会覆盖现有文档，为false是不做任何操作。

## Document Builder

文档生成器提供了类似向导的界面，用于输入文档的字段

## File Upload

文件上传选项允许选择一个准备好的文件并上传它。如果对Request-Handler选项仅使用/update，则将限制为XML，CSV和JSON。

## Solr Command

Solr命令选项允许你使用XML或JSON对文档执行特定操作，例如定义要添加或删除的文档，仅更新文档的某些字段，或者在索引上提交和优化命令。

文档应该按照在命令行中使用/ update时的结构。

## XML

当使用XML文档类型时，功能类似于在命令行上使用requestHandler。而不是将文档放在curl命令中，而是可以将其输入到文档输入框中。文档结构应该仍然是正确的Solr XML格式，每个文档由 `<doc>` 标签和每个字段定义。

Commit Within选项表示选择何时将文档添加到索引，Overwrite选项表示如果传入的文档id已经存在则默认会覆盖现有文档，为false是不做任何操作。

此选项只会将文档添加或覆盖到索引；对于其他更新任务，请参阅Solr命令选项。