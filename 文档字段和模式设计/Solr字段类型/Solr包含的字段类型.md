下表列出了Solr中可用的字段类型。 org.apache.solr.schema包中包含此表中列出的所有类。

|类|描述|
|----|--------------|
|BinaryField|二进制数据。|
|BoolField|包含true或false。第一个字符中的“1”，“t”或“T”的值被解释为真。第一个字符中的任何其他值都被解释为false。|
|CollationField|支持Unicode排序规则的分类和范围查询。如果可以使用ICU4J，ICUCollat​​ionField是一个更好的选择。|
|CurrencyField|支持货币和汇率。|
|DateRangeField|支持索引日期范围，包括时间点日期实例（单毫秒持续时间）。考虑使用此字段类型，即使它仅适用于日期实例，特别是当查询通常落在UTC年/月/日/小时等边界时。|
|ExternalFileField|从磁盘上的文件中获取值。|
|EnumField|允许定义一个枚举的值集合，这些值可能不容易按字母或数字顺序排序。此字段类型接受配置文件，其中列出了字段值的正确顺序。|
|ICUCollationField|支持Unicode排序规则的分类和范围查询|
|LatLonType|纬度/经度坐标对。|
|PointType|一个任意的n维点，用于搜索蓝图或CAD图纸等来源。|
|PreAnalyzedField|提供一种发送到Solr序列化令牌流的方式，可选地使用字段的独立存储值，并将此信息存储和编入索引，而不进行任何其他文本处理。|
|RandomSortField|不包含值。对此字段类型进行排序的查询将以随机顺序返回结果。使用动态字段来使用此功能。|
|SpatialRecursivePrefixTreeFieldType|以WKT格式接受纬度逗号经度字符串或其他形状。|
|StrField|字符串（UTF-8编码字符串或Unicode）。字符串用于小字段，不以任何方式进行标记化或分析。他们的限制稍低于32K。|
|TextField|文字，通常是多个单词或标记。|
|TrieDateField|日期字段。以毫秒精度表示一个时间点。precisionStep =“0”实现了高效分选的日期和索引大小最小化; precisionStep =“8”（默认），能够实现高效范围查询。|
|TrieDoubleField|双字段（64位IEEE浮点）。 precisionStep =“0”可以进行有效的数字排序并最小化索引大小; precisionStep =“8”（默认值）可实现有效的范围查询。|
|TrieField|如果使用此字段类型，还必须指定“类型”属性，有效值为：integer，long，float，double，date。使用此字段与使用任何Trie字段相同。 precisionStep =“0”可以进行有效的数字排序并最小化索引大小; precisionStep =“8”（默认值）可实现有效的范围查询。|
|TrieFloatField|浮点字段（32位IEEE浮点数）|
|TrieIntField|整型字段（32位有符号整数）。|
|TrieLongField|长整型（64位有符号整数）。|
|UUIDField|通用唯一标识符（UUID）。传递值为“NEW”，Solr将创建一个新的UUID。注意：在使用SolrCloud时，大多数情况下不建议使用默认值“NEW”的UUIDField实例（如果UUID值被配置为唯一键字段则不可行），因为结果将是每个文档的每个副本将获得唯一的UUID值。建议使用UUIDUpdateProcessorFactory在添加文档时生成UUID值。|
