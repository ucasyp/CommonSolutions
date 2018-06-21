# ES-hadoop写数据到阿里云Elasticsearch {#concept_hdn_554_f2b .concept}

## 行业背景 {#section_g5g_dv4_f2b .section}

当前流行的企业级搜索引擎Elasticsearch可为企业用户提供日志搜索、站内搜索、地图搜索等搜素服务。Hadoop具有批处理的优势。通过ES-Hadoop可以联接Elasticsearch与Hadoop生态间，融合Hadoop的批处理优势和Elasticsearch强大的全文检索引擎，为企业提供更优质的交互式数据搜索服务。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15074/6467_zh-CN.png)

其中：

-   Elasticsearch是一个基于Lucene的分布式搜索引擎，具有分布式、全文检索、近实时搜索和分析、高可用、模式自由、RESTFul API等诸多优点，在实时搜索、日志处理\(ELK\)、大数据分析等领域有着广泛的应用。
-   Hadoop是一个由Apache基金会所开发的分布式系统基础架构，核心组件有HDFS和MapReduce，分别提供海量数据存储和海量数据计算。
-   ES-Hadoop（Elasticsearch for Apache Hadoop）是一个用于Elasticsearch和Hadoop进行交互的开源独立库，在Hadoop和Elasticsearch之间起到桥梁的作用，完美地把Hadoop的批处理优势和Elasticsearch强大的全文检索引擎结合起来。

ES-Hadoop开辟了更加广阔的应用空间，通过ES-Hadoop可以索引Hadoop中的数据到Elasticsearch，充分利用其查询和聚合分析功能，也可以在Kibana中做进一步的可视化分析，同时也可以把Elasticsearch中的数据放到Hadoop生态系统中做运算，ES-Hadoop支持Spark、Spark、 Streaming、SparkSQL，除此之外，不论是使用Hive、 Pig、Storm、Cascading还是运行单独的Map/Reduce，通过ES-Hadoop提供的接口都支持从Elasticsearch中进行索引和查询操作。

## 解决方案架构与核心产品 {#section_j5g_dv4_f2b .section}

阿里云平台提供成熟的Elasticsearch及E-MapReduce服务，相较于使用开源Elasticsearch和分布式计算平台来自建搜索和计算环境，使用阿里云服务有以下优势：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15074/6469_zh-CN.png)

基于阿里云E-MapReduce和阿里云Elasticsearch，通过ES-Hadoop连通Hadoop生态系统和Elasticsearch，典型架构如下所示。![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/15074/6470_zh-CN.png)

**架构说明**：

-   阿里云E-MapReduce通过ES-Hadoop将数据直接写入阿里云Elasticsearch，无需落盘存储，并在Kibana中可视化分析呈现。
-   阿里云Elasticsearch通过ES-Hadoop直接使用E-MapReduce进行数据计算。
-   阿里云Elasticsearch可通过快照的方式，将冷数据存储至Hadoop中的HDFS，节约实时存储空间。

**核心部件**：

基于阿里云服务的本解决方案，使用阿里云Elasticsearch、E-MapReduce、OSS、VPC，与部分开源产品搭配使用。其中：

-   阿里云Elasticsearch：

    阿里云Elasticsearch提供开源Elasticsearch 5.5.3版本及商业版X-pack插件服务，致力于数据分析、数据搜索等场景服务。在开源Elasticsearch基础上提供企业级权限管控、安全监控告警、自动报表生成等功能。阿里云Elasticsearch架构图如下，详细Elasticsearch产品介绍请参考[阿里云Elasticsearch文档](https://help.aliyun.com/product/57736.html)。

    ![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/71216/cn_zh/1524741532609/fig_04.png)

-   阿里云E-MapReduce：

    阿里云E-MapReduce基于开源的 Apache Hadoop 和 Apache Spark，让用户可以方便地使用Hadoop和Spark生态系统中的其他周边系统（如 Apache Hive、Apache Pig、HBase 等）来分析和处理自己的数据。用户还可以通过E-MapReduce将数据非常方便的导入和导出到阿里云其他的云数据存储系统和数据库系统中，如阿里云 OSS、阿里云 RDS 等。本示例架构中使用OSS配合E-MapReduce，将阿里云OSS作为日志存储。详细E-MapReduce产品介绍请参考[阿里云E-MapReduce文档](https://help.aliyun.com/product/28066.html)。


## 操作演示 {#section_p5g_dv4_f2b .section}

基于阿里云Elasticsearch和E-MapReduce，通过ES-Hadoop可直接将数据写入阿里云Elasticsearch，操作时需有以下步骤：

1.  开通服务
2.  编写EMR写数据到ES的MR作业
3.  EMR中完成作业
4.  API分析

详细操作步骤请参考[ES-hadoop写数据到阿里云Elasticsearch](https://help.aliyun.com/document_detail/71216.html)一文。

## **FAQ** {#section_r5g_dv4_f2b .section}

**ClassNotFoundException异常**

遇到找不到EsOutputFormat所在类，导致的ClassNotFoundException异常：

```
java.lang.NoClassDefFoundError: org/elasticsearch/hadoop/mr/EsOutputFormat
```

解决办法，使用maven-assembly-plugin插件，把第三方库打到jar包中。

**连接不到Elasticsearch集群**

-   第一个原因：没有配置X-PACK 的用户名和密码，加上以下两行配置：

    ```
    conf.set("es.net.http.auth.user", "你的X-PACK用户名");
    conf.set("es.net.http.auth.pass", "你的X-PACK密码");
    ```

-   第二个原因：EMR集群和Elasticsearch集群网络不通，在创建集群的时尽量选择同一区域，比如EMR集群在华东1区，Elasticsearch集群也在华东1区，事先用Ping命令测试。
-   第三个原因：一般TCP端口\(比如使用Java客户端\)是9300，ES-Hadoop中使用的仍然是9200端口，不要设置错了。

**Reduce过程中格式错误**

注意测试文件中每一行都是一个JSON，在设置中加上：

```
conf.set("es.input.json", "yes");
```

否则会出现解析文件格式异常。

