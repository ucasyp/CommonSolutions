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

## 1.开通服务

阿里云产品地图如下，需要用到的有专有网络VPC、E-MapReduce、Elasticsearch。

![](https://yqfile.alicdn.com/c57e5eba9896333042f8a18334efd2d92fa1831f.png)

### 1.1 开通专有网络VPC

因为在公网访问推送数据安全性较差，为保证阿里云Elasticsearch访问环境安全，购买阿里云ES产品，对应区域下必须要有 VPC 和 虚拟交换机,因此首先开通VPC专有网络。按路径：阿里云首页-->产品-->网络->专有网络VPC，然后选择立即开通，进入到管理控制台界面，新建专有网络。

![_2018_06_21_9_47_25](https://yqfile.alicdn.com/4120823a6c2e3bf7fa1be8caaadd6b18f2b46cf2.png)

创建完成之后在控制台中可以进行管理：

![_2018_06_21_9_53_12](https://yqfile.alicdn.com/893cfbb28e68d3df0f12285cf955809f20460736.png)

更多关于专有网络VPC的文档参考这里:[专有网络 VPC](https://help.aliyun.com/product/27706.html)

### 1.2 开通阿里云Elasticsearch

按路径：阿里云首页-->产品-->数据库-->Elasticsearch或阿里云首页-->产品-->大数据基础服务-->Elasticsearch进入到阿里云Elasticsearch产品界面，**新用户可以免费试用30天**。

进入到购买入口，阿里云Elasticsearch提供了按月和按量两种付费模式，选择已经创建的专有网络并设置登录密码。

![_2018_06_21_9_48_31](https://yqfile.alicdn.com/84739e6e2dbea62e5ea66eb9ee8cb2ad57fe3d61.png)


购买成功后，按路径：控制台-->大数据(数加)-->Elasticsearch，可以看到新创建的Elasticsearch集群实例。

![_2018_06_21_3_31_27](https://yqfile.alicdn.com/25c4c12bec4fbf6aee887619ccee74f4ddcbe730.png)

点击"管理"菜单，进入集群管理界面：

![_2018_06_21_3_31_43](https://yqfile.alicdn.com/52382ce97489471410829bb071aa2cb275f4e269.png)

点击"Kibana控制台"按钮即可进入到Kibana操作界面：

![9](https://yqfile.alicdn.com/7b479a731bd51570b3f83357736e41389afe5b40.png)
点击"集群监控"按钮进入到监控界面：

![10](https://yqfile.alicdn.com/9c756e77e0e7b6401f3406d4e5fcbd60279ea0d5.png)

### 1.3 开通阿里云E-MapReduce

按路径：阿里云首页-->产品-->大数据基础服务-->E-MapReduce，之后进入到购买界面：

![_2018_06_21_9_49_17](https://yqfile.alicdn.com/25ebb2da4ee34bf52be50b638a8f46e5fa0ff8d9.png)

下一步进行付费配置、网络配置和节点硬件配置：

![_2018_06_21_9_49_33](https://yqfile.alicdn.com/36f5338eb1cc472e92b619dcf0feae906b0af342.png)

最后设置集群名称、日志路径和集群登录密码：

![_2018_06_21_9_49_57](https://yqfile.alicdn.com/ca1a5658afe35094b22ef16c22196eb17ad0fa5e.png)


其中日志存储在OSS之上，如果没有创建bucket，需要到[OSS管理控制台](https://oss.console.aliyun.com/overview)创建新的bucket。bucket的区域要和EMR集群一致，EMR集群为华东1区，这里的bucket区域也选择华东1区：

![_2018_06_21_3_29_25](https://yqfile.alicdn.com/f9d80bdd5104da900ba0fee134d6510c34034a0d.png)

bucket创建完成以后就可以新建目录、上传文件：

![_2018_06_21_3_38_10](https://yqfile.alicdn.com/3eaf08b7272fcff6b876a4b5488ee3f95919237c.png)


最后确定，完成EMR集群的创建：

![_2018_06_21_9_51_22](https://yqfile.alicdn.com/c4e5abec372ffdf1658b597b69a57c24eda31cd3.png)

集群创建成功后在集群列表中查看：

![_2018_06_21_3_44_20](https://yqfile.alicdn.com/c55173538ad58043dee17f4c35a5048afa4aa17e.png)

点击管理，可以查看master节点和data节点的详细信息：

![_2018_06_21_3_46_01](https://yqfile.alicdn.com/c7c2b1aab988805f5794a8ccd17f6e70d77ae69e.png)

公网IP可以直接访问，远程登录：

```java
ssh root@你的公网IP
```
使用jps命令查看后台进程：

```java
[root@emr-header-1 ~]# jps
16640 Bootstrap
17988 RunJar
19140 HistoryServer
18981 WebAppProxyServer
14023 Jps
15949 gateway.jar
16621 ZeppelinServer
1133 EmrAgent
15119 RunJar
17519 ResourceManager
1871 Application
19316 JobHistoryServer
1077 WatchDog
17237 SecondaryNameNode
16502 NameNode
16988 ApacheDsTanukiWrapper
18429 ApplicationHistoryServer
```
## 2.  编写EMR写数据到ES的MR作业

推荐使用maven来进行项目管理，首先创建一个maven工程。操作步骤如下：

**1.安装 Maven**。首先确保计算机已经正确安装安装[maven](http://maven.apache.org/index.html)

**2.生成工程框架**。在工程根目录处执行如下命令：

```java
mvn archetype:generate -DgroupId=com.aliyun.emrtoes -DartifactId=emrtoes -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```
mvn 会自动生成一个空的 Sample 工程,工程名为emrtoes(和指定的artifactId一致),里面包含一个简单的 pom.xml 和 App 类（类的包路径和指定的 groupId 一致）

3.**加入 Hadoop 和ES-Hadoop依赖。**使用任意 IDE 打开这个工程，编辑 pom.xml 文件。在 dependencies 内添加如下内容：


```java
    <dependency>
         <groupId>org.apache.hadoop</groupId>
         <artifactId>hadoop-mapreduce-client-common</artifactId>
         <version>2.7.3</version>
     </dependency>
     <dependency>
         <groupId>org.apache.hadoop</groupId>
         <artifactId>hadoop-common</artifactId>
         <version>2.7.3</version>
     </dependency>
      <dependency>
          <groupId>org.elasticsearch</groupId>
          <artifactId>elasticsearch-hadoop-mr</artifactId>
          <version>5.5.3</version>
      </dependency>
```

**4.添加打包插件**。由于使用了第三方库，需要把第三方库打包到jar文件中，在pom.xml中添加maven-assembly-plugin插件的坐标：

```java
    <plugins>
      <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <configuration>
          <archive>
            <manifest>
              <mainClass>com.aliyun.emrtoes.EmrToES</mainClass>
            </manifest>
          </archive>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
        <executions>
          <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
      </plugin>

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.1.0</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
              </transformers>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
```

**5.编写代码**。在com.aliyun.emrtoes包下和 App 类平行的位置添加新类 EmrToES.java。内容如下：

```java
package com.aliyun.emrtoes;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
import org.apache.hadoop.util.GenericOptionsParser;
import org.elasticsearch.hadoop.mr.EsOutputFormat;
import java.io.IOException;

public class EmrToES {

    public static class MyMapper extends Mapper<Object, Text, NullWritable, Text> {
        private Text line = new Text();

        @Override
        protected void map(Object key, Text value, Context context)
                throws IOException, InterruptedException {
            if (value.getLength() > 0) {
                line.set(value);
                context.write(NullWritable.get(), line);
            }
        }
    }

    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();

        //阿里云 Elasticsearch X-PACK用户名和密码
        conf.set("es.net.http.auth.user", "你的X-PACK用户名");
        conf.set("es.net.http.auth.pass", "你的X-PACK密码");

        conf.setBoolean("mapred.map.tasks.speculative.execution", false);
        conf.setBoolean("mapred.reduce.tasks.speculative.execution", false);
        conf.set("es.nodes", "你的Elasticsearch内网地址");
        conf.set("es.port", "9200");
        conf.set("es.nodes.wan.only", "true");
        conf.set("es.resource", "blog/yunqi");
        conf.set("es.mapping.id", "id");
        conf.set("es.input.json", "yes");

        Job job = Job.getInstance(conf, "EmrToES");
        job.setJarByClass(EmrToES.class);

        job.setMapperClass(MyMapper.class);
        job.setInputFormatClass(TextInputFormat.class);
        job.setOutputFormatClass(EsOutputFormat.class);
        job.setMapOutputKeyClass(NullWritable.class);
        job.setMapOutputValueClass(Text.class);

        FileInputFormat.setInputPaths(job, new Path(otherArgs[0]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}

```
**6.编译并打包。**在工程的目录下，执行如下命令：
```java
mvn clean package 
```
执行完毕以后，可在工程目录的 target 目录下看到一个emrtoes-1.0-SNAPSHOT-jar-with-dependencies.jar，这个就是作业 jar 包。

![_2018_06_21_3_49_00](https://yqfile.alicdn.com/738b891820831eca3aeaa7fdc2d10bebd515fa33.png)

##  3. EMR中完成作业

### 3.1 测试数据
把下面的数据写入到blog.json中:

```java
{"id":"1","title":"git简介","posttime":"2016-06-11","content":"svn与git的最主要区别..."}
{"id":"2","title":"ava中泛型的介绍与简单使用","posttime":"2016-06-12","content":"基本操作：CRUD ..."}
{"id":"3","title":"SQL基本操作","posttime":"2016-06-13","content":"svn与git的最主要区别..."}
{"id":"4","title":"Hibernate框架基础","posttime":"2016-06-14","content":"Hibernate框架基础..."}
{"id":"5","title":"Shell基本知识","posttime":"2016-06-15","content":"Shell是什么..."}
```
上传到阿里云E-MapReduce集群，使用scp远程拷贝命令上传文件：

```java
scp blog.json root@ES弹性公网IP:/root
```
把blog.json上传至HDFS:

```java
hadoop fs -mkdir /work
hadoop fs -put blog.json /work
```

###  3.2 上传JAR包

把maven工程target目录下的jar包上传至阿里云E-MapReduce集群：

```java
scp target/emrtoes-1.0-SNAPSHOT-jar-with-dependencies.jar root@EMR公网IP:/root
```

###  3.3 执行MR作业
```java
hadoop jar emrtoes-1.0-SNAPSHOT-jar-with-dependencies.jar /work/blog.json
```
运行成功的话，控制台会输出如下图所示信息：

![_2018_06_21_3_54_54](https://yqfile.alicdn.com/fab6bb465a5db121bc4c380f28302f400ad688d5.png)

命令查询Elasticsearch中的数据：
```java
curl -u elastic -XGET es-cn-4590nukw4000xuig3.elasticsearch.aliyuncs.com:9200/blog/_search?pretty
```

![_2018_06_21_3_57_28](https://yqfile.alicdn.com/3eeef43bf0b5af73eb8fdb907679531da4659d67.png)

或者在Kibana中查看：

![](https://yqfile.alicdn.com/c039a480021535ea7104b05e245f94edab781b0b.png)

## 4. API分析

Map过程，按行读入，input kye的类型为Object，input value的类型为Text。输出的key为NullWritable类型，NullWritable是Writable的一个特殊类，实现方法为空实现，不从数据流中读数据，也不写入数据，只充当占位符。MapReduce中如果不需要使用键或值，就可以将键或值声明为NullWritable，这里把输出的key设置NullWritable类型。输出为BytesWritable类型，把json字符串序列化。

因为只需要写入，没有Reduce过程。配置参数说明如下：

- conf.set("es.net.http.auth.user", "你的X-PACK用户名");
  设置X-PACK的用户名
- conf.set("es.net.http.auth.pass", "你的X-PACK密码");
  设置X-PACK的密码
- conf.setBoolean("mapred.map.tasks.speculative.execution", false);

  关闭mapper阶段的执行推测

- conf.setBoolean("mapred.reduce.tasks.speculative.execution", false);

  关闭reducer阶段的执行推测

- conf.set("es.nodes", "你的Elasticsearch内网地址");

   配置Elasticsearch的IP和端口

- conf.set("es.resource", "blog/yunqi");

  设置索引到Elasticsearch的索引名和类型名。

- conf.set("es.mapping.id", "id");

  设置文档id，这个参数”id”是文档中的id字段

- conf.set("es.input.json", "yes");

   指定输入的文件类型为json。

- job.setInputFormatClass(TextInputFormat.class);

   设置输入流为文本类型

- job.setOutputFormatClass(EsOutputFormat.class);

   设置输出为EsOutputFormat类型。

- job.setMapOutputKeyClass(NullWritable.class);

   设置Map的输出key类型为NullWritable类型

- job.setMapOutputValueClass(BytesWritable.class);

   设置Map的输出value类型为BytesWritable类型
- FileInputFormat.setInputPaths(job, new Path(otherArgs[0]));
  传入HDFS上的文件路径


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

