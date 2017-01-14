如果你使用的是Solr 6.3或6.4版本则不会出现什么大问题。为了安全起见你还是应该查看Solr软件包中的 `CHANGES.txt` 文件，查看可能影响现有实现的更改和更新。升级Solr集群的详细步骤可以在附录中找到：升级Solr集群。

# 从6.3.x升级

* `/export` 输出处理程序已更改，因此不再返回不在原始文档中的数字字段的零（0）。 这种变化的一个后果是，你必须知道一些元组在原始文档中没有值。
* 已删除 `org.apache.solr.util.stats` 中的与度量相关的类，以使用 `Dropwizard metrics library`。使用这些类的任何自定义插件都应该更改为使用度量库中的等效类。作为其中的一部分，对Overseer Status API的输出进行了以下更改：
  * `totalTime` 指标已删除，因为不再支持。
  * 监督状态API中的度量“75thPctlRequestTime”，“95thPctlRequestTime”，“99thPctlRequestTime”和“999thPctlRequestTime”已经重命名为“75thPcRequestTime”，“95thPcRequestTime”等等，以与Solr的其他部分中的统计输出一致。
  * 度量“avgRequestsPerMinute”，“5minRateRequestsPerMinute”和“15minRateRequestsPerMinute”已经被相应的每秒速率替换。 “avgRequestsPerSecond”，“5minRateRequestsPerSecond”和“15minRateRequestsPerSecond”，以与Solr其他部分中的统计信息输出一致。
* 已添加名为 `UnifiedHighlighter` 的新高亮显示。建议你通过设置 `hl.method = unified` 和 `report feedback` 来测试 `UnifiedHighlighter`。它可能成为7.0中的默认值，原因在于它比原来的高亮显示更高效/更快。`hl.useFastVectorHighlighter` 现在被视为已弃用，而不是 `hl.method = fastVector`。
* `maxWarmingSearchers` 参数现在默认为1，如果超过此限制不再抛出异常（一件好事）。因此，重叠提交中不再有风险。尽管如此，用户应该继续避免过度提交。建议用户从其 `solrconfig.xml` 文件中删除任何预先存在的 `maxWarmingSearchers` 条目。
* 复杂语法查询解析器现在支持通配符。如果觉得影响性能你可以在索引时间分析中使用 `ReversedWildcardFilter`。

# 从早期的6.x版本升级
* 如果你使用历史日期字段索引，值具体在1582年或之前，你应该升级到此版本后重新索引。
* 如果你使用带有 `method = stream` 的JSON Facet API（json.facet），那么现在必须设置 `sort ='index asc'` 以获取流行为；否则不会有流。提醒：`method`是一个提示，不会更改其他参数的默认值。
* 如果使用JSON Facet API（json.facet）在数字字段上分片，并且如果使用`mincount = 0` 或设置前缀，那么现在将会出现错误，因为这些选项与数字分面不兼容。
* Solr在INFO级别的日志记录已经大大减少，你可能需要更新日志配置以使用DEBUG级别来查看以前在INFO级别使用的所有日志消息。
* 我们不再永远备份带日期戳的副本中的 `solr.log` 和 `solr_gc.log` 文件。如果你依赖 `solr_log_ <date>` 或 `solr_gc_log_ <date>` 在日志文件夹中不再是这种情况。
* `MiniSolrCloudCluster` 上的 `create/deleteCollection` 方法已被弃用，客户端应该使用CollectionAdminRequest API。此外 `MiniSolrCloudCluster＃uploadConfigDir（File，String）`已被弃用，支持 `#uploadConfigSet（Path，String）`。
* 默认情况下，`bin/solr.in.sh`（Windows上的 `bin/solr.in.cmd`）现已完全注释。以前，这不是这样，这具有掩蔽现有环境变量的效果。
* `_version_` 字段不再索引，并且现在默认情况下使用 `indexed = false` 进行定义，因为该字段已启用DocValues。


# 从5.5.x升级

* The deprecated SolrServer and subclasses have been removed, use SolrClient instead.
* The deprecated <nrtMode> configuration in solrconfig.xml has been removed. Please remove it from solrconfig.xml.
* SolrClient.shutdown() has been removed, use SolrClient.close() instead.
* The deprecated zkCredientialsProvider element in solrcloud section of solr.xml is now removed. Use the correct spelling ( zkCredentialsProvider ) instead.
* Internal/expert - ResultContext was significantly changed and expanded to allow for multiple full query results (DocLists) per Solr request. TransformContext was rendered redundant and was removed. See SOLR-7957 for details.
* Several changes have been made regarding the " Similarity " used in Solr, in order to provide better default behavior for new users. There are 3 key impacts of these changes on existing users who upgrade:
 * DefaultSimilarityFactory has been removed. If you currently have DefaultSimilarityFactory explicitly referenced in your schema.xml, edit your config to use the functionally identical ClassicSimilarityFactory. See SOLR-8239 for more details.
 * The implicit default Similarity used when no <similarity/> is configured in schema.xml has been changed to SchemaSimilarityFactory. Users who wish to preserve back-compatible behavior should either explicitly configure ClassicSimilarityFactory, or ensure that the luceneMatchVersion for the collection is less then 6.0. See SOLR-8270 + SOLR-8271 for details.
 * SchemaSimilarityFactory has been modified to use BM25Similarity as the default for fieldTypes that do not explicitly declare a Similarity. The legacy behavior of using ClassicSimilarity as the default will occur if the luceneMatchVersion for the collection is less then 6.0, or the 'defaultSimFromFieldType' configuration option may be used to specify any default of your choosing. See SOLR-8261 + SOLR-8329 for more details.
* If your solrconfig.xml file doesn't explicitly mention the schemaFactory to use then Solr will choose the ManagedIndexSchemaFactory by default. Previously it would have chosen ClassicIndexSchemaFactory. This means that the Schema APIs (/<collection>/schema) are enabled and the schema is mutable. When Solr starts your schema.xml file will be renamed to managed-schema. If you want to retain the old behaviour then please ensure that the solrconfig.xml explicitly uses the ClassicIndexSchemaFactory or your luceneMatchVersion in the solrconfig.xml is less than 6.0. See the Schema Factory Definition in SolrConfig section for more details
* SolrIndexSearcher.QueryCommand and QueryResult were moved to their own classes. If you reference them in your code, you should import them under o.a.s.search (or use your IDE's "Organize Imports").
* The ' useParams ' attribute specified in request handler cannot be overridden from request params. See SOLR-8698 for more details.
* When requesting stats in date fields, "sum" is now returned as a double value instead of a date. See SOLR-8671 for more details.
* The deprecated GET methods for schema are now accessible through the bulk API. These methods now accept fewer request parameters, and output less information. See SOLR-8736 for more details.  Some of the removed functionality will likely be restored in a future version of Solr - see SOLR-8992.
* In the past, Solr guaranteed that retrieval of multi-valued fields would preserve the order of values. Because values may now be retrieved from column-stored fields (docValues="true"), in conjunction with the fact that DocValues do not currently preserve order, means that users should set useDocValuesAsStored="false" to prevent future optimizations from using the column-stored values over the row-stored values when fields have both stored="true" and docValues="true".
* Formatted date-times from Solr have some differences. If the year is more than 4 digits, there is a leading '+'. When there is a non-zero number of milliseconds, it is padded with zeros to 3 digits. Negative year (BC) dates are now possible. Parsing: It is now an error to supply a portion of the date out of its, range, like 67 seconds.
* SolrJ no longer includes DateUtil. If for some reason you need to format or parse dates, simply use Instant.format() and Instant.parse().
* If you are using spatial4j, please upgrade to 0.6 and edit your spatialContextFactory to replace com.spatial4j.core with org.locationtech.spatial4j .


# 从旧版本的Solr升级

强烈建议从旧版本升级的用户查阅 `CHANGES.txt` 文件以了解自从升级的版本以来的所有更改的详细信息。Solr 5.x和Solr 6.0之间的重大变化的总结可以在Solr 5到Solr 6的主要变化部分中找到。

