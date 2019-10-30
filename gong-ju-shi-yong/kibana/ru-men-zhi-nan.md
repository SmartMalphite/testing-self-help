# 入门指南

## 简介

Kibana 是一款开源的数据分析和可视化平台，它是 Elastic Stack 成员之一，设计用于和 Elasticsearch 协作。您可以使用 Kibana 对 Elasticsearch 索引中的数据进行搜索、查看、交互操作。您可以很方便的利用图表、表格及地图对数据进行多元化的分析和呈现。

Kibana 可以使大数据通俗易懂。它很简单，基于浏览器的界面便于您快速创建和分享动态数据仪表板来追踪 Elasticsearch 的实时数据变化。

搭建 Kibana 非常简单。您可以分分钟完成 Kibana 的安装并开始探索 Elasticsearch 的索引数据 — 没有代码、不需要额外的基础设施。

## 数据导入

{% embed url="https://www.elastic.co/guide/cn/kibana/current/tutorial-load-dataset.html" %}

示例数据导入后 查询如下

```text
health status index               pri rep docs.count docs.deleted store.size pri.store.size
yellow open   bank                  5   1       1000            0    418.2kb        418.2kb
yellow open   shakespeare           5   1     111396            0     17.6mb         17.6mb
yellow open   logstash-2015.05.18   5   1       4631            0     15.6mb         15.6mb
yellow open   logstash-2015.05.19   5   1       4624            0     15.7mb         15.7mb
yellow open   logstash-2015.05.20   5   1       4750            0     16.4mb         16.4mb
```

## 创建索引模式

加载到 Elasticsearch 的每组数据都有一个索引模式（Index Pattern）

一个 _索引模式_ 是可以匹配多个索引的带可选通配符的字符串。例如一般在通用日志记录中，一个典型的索引名称一般包含类似 YYYY.MM.DD 格式的日期信息。例如一个包含五月数据的索引模式： `logstash-2015.05*`

 单击 **Management** 选项，然后单击 **Index Patterns** 选项。点击 **Add New** 定义一个新的索引模式。演示数据共有两个样本数据集，莎士比亚戏剧和金融账户，不包含时间序列数据。当您为这些数据集创建索引模式时，请确保 **Index contains time-based events** 没有被选中。指定 `shakes*` 作为 Shakespeare 数据集的索引模式，然后点击 **Create** 创建索引模式，然后同样的方法再创建一个名为 `ba*` 的索引模式。

Logstash 数据集会包含时间序列数据，所以点击 **Add New** 来定义这个数据集的索引，确保 **Index contains time-based events** 被选中，并从 **Time-field name** 下拉列表中选择 `@timestamp` 字段



## 数据查看

单击侧面导航中的 **Discover** 进入 Kibana 的数据探索功能

在查询框里，您可以输入 [Elasticsearch 查询语句](https://www.elastic.co/guide/en/elasticsearch/reference/6.0/query-dsl-query-string-query.html#query-string-syntax) 来搜索您的数据。您可以在 Discover 页面下查看搜索结果并在 Visualize 页面下生成已保存搜索的可视化效果。

当前索引模式显示在查询栏下面。索引模式决定了当您提交查询时搜索哪些索引。要搜索一组不同的索引，可以从下拉菜单中选择不同的模式。要添加索引模式，请到 **Management/Kibana/Index Patterns** 界面下点击 **Add New** 。

