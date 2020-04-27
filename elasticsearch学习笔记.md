## **elasticsearch学习笔记**

### 准备所需要的软件

1. 下载**elasticsearch**地址

   > https://elasticsearch.cn/download/
   >
   > 选择好版本（建议最新的）之后，下载就好，可能比较慢，慢慢等着就行

2. 下载**kibana**地址

   > https://elasticsearch.cn/download/
   >
   > 选择好版本（一定要与elasticsearch的版本一致）之后，下载就好，可能比较慢，慢慢等着就行

3. 下载**elasticseatch-head**

   > https://github.com/mobz/elasticsearch-head

4. 下载**ik中文分词器**

   > https://github.com/medcl/elasticsearch-analysis-ik

### 安装

按照官网的教程一步步进行安装就好，没什么好说的

### 配置

需要注意的几个地方：

1. 在使用**elasticsearch-head**的时候，需要配置一下**elasticseatch**的跨域，如下：

   ```shell
   # 1. 进入elasticsearch的安装目录
   cd /usr/local/elasticsearch-x.x.x
   cd config
   vim elasticsearch.yml
   ```

   **elasticsearch.yml**配置如下：

   ```shell
   # 2. 进行配置
   ...
   #
   # ---------------------------------- Various -----------------------------------
   #
   # Require explicit names when deleting indices:
   #
   
   #action.destructive_requires_name: true
   http.cors.enabled: true
   http.cors.allow-origin: "*"
   # 配置完成之后重启elasticsearch服务
   ```

2. 配置**ik分词器**

   ```shell
   # 把下载好的ik分词器压缩包放入到elasticsearch目录下的plugin目录
   cd /usr/local/elasticsearch-x.x.x
   cd plugin
   mkdir ik
   cd ik
   unzip ik.xxxx.zip
   # 配置完成之后重启elasticsearch服务
   ```

### 启动

1. 启动**elasticsearch**服务

   ```shell
   ./elasticsearch
   ```

2. 启动**kibana**服务

   ```shell
   kibana
   ```

3. 启动**elasticsearch-head**

   ```shell
   cd elasticsearch-head的安装目录
   # 如果第一次下载没有install的话，先install一下，然后再启动，只有第一次的时候这样，如果已经安装过了就可以直接启动 npm run start 即可
   npm install
   npm run start
   ```

### 使用

####  索引操作

1. 打开**kibana**，进入输入面板

   ![image-20200417182449117](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200417182449117.png)

2. 新建一个**Index**相当于mysql的数据库

   ![image-20200417183047499](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200417183047499.png)

   这样就创建好了一个index的数据库，里面包含了**name, age, address**三个字段，在新版的**es**中，要放弃type，所在有创建的时候不指定**type**了

3. 查看一下

   ![image-20200417183847444](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200417183847444.png)

4. 修改一下信息，**es中只支持添加新的字段，不能修改字段名字和类型，也不能删除字段**

   ![image-20200417184147817](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200417184147817.png)

#### 文档操作

文档相当于mysql表中的一条条记录

1. 添加一个新的文档

   + 方式1，能过PUT方式添加的数据，要指定**id**

     > PUT /index/_doc/id
     >
     > {
     >
     > ​	"name": "张三",
     >
     > ​	"age": 10,
     >
     > ​	"address": "济南"
     >
     > }

   + 方式2，通过POST方式添加数据，可以不用指定**id**，若不指定，则由系统生成一串随机的字符串当**id**， 如：**TNQ5i3EBK5lwnu21INwU**，若指定，就用指定的**id**

     >POST /index/_doc
     >
     >{
     >
     >​	"name": "张三",
     >
     >​	"age": 10,
     >
     >​	"address": "济南"
     >
     >}
     >
     >或者
     >
     >POST /index/_doc/1
     >
     >{
     >
     >​	"name": "张三",
     >
     >​	"age": 10,
     >
     >​	"address": "济南"
     >
     >}

2. 修改一个文档

   + 方式1（**推荐**），通过POST的方式来修改一个文档

     > POST /index/_doc/1/__update  在新版中可以使用 POST /index/__update/1  不指定type的方式
     >
     > {
     >  "doc": { // 一定要加上这个 doc 否则修改不了
     >     "name": "修改name"
     >  }
     > }

   + 方式2，通过PUT的方式来修改一个文档

     > PUT /index/_doc/id
     >
     > {
     >
     > ​	"name": "修改一下name"
     >
     > }

     两种方式的不同之处在于，用**POST**，可以实现局部更新，如：只修改name字段，而不会影响其它字段的数据。如果用**PUT**方式，只修改name字段的话，如果没有指定其它字段的数据，则会把其它字段的数据置空，也就是相当于覆盖了这条数据。

3. 删除文档

   > DELETE /index/_doc/id

4. 简单查询文档

   + 方式1， 可以使用url后面带参数的形式

     > GET /index/_search?q=name:张三

   + 方式2，使用DSL查询，完成复杂的查询

     > GET /index/_search
     > {
     >   "query": {
     >     "match": {
     >       "name": "张三"
     >     }
     >   }
     > }

### 查询

#### term, match的区别

> term 在查询时候，不会被分词器解析，会把查询的内容当成一个整体
>
> match在查询的时候，会被指定的分词器解析，把查询内容分解成多个词条，然后底层再通过term, 进行查询

#### **term**

```json
查询name为张三的文档
GET /index/_search
{
  "query": {
    "term": {
      "name": "张三"
    }
  }
}
因为term会把搜索内容当成一个整体，且不会分词器解析，所以在查询的时候不能指定分词器和操作符，如果要想指定多个匹配内容可以使用terms
查询name中带有 张 字 或者 有 李 字的文档 是一个 or 的关系
GET /index/_search
{
  "query": {
    "terms": {
      "name": ["张", "李"]
    }
  }
}
```

#### **match**

```json
查询name中带有 张 或者 带有 李 的文档，如果不指定关系，默认的关系是 or 
GET /index/_search
{
  "query": {
    "match": {
      "name": "张李"
    }
  }
}

查询name中带有 张 并且 带有 李 的文档，关系是 and 
GET /index/_search
{
  "query": {
    "match": {
      "name": {
        "query": "张李",
        "operator": "and"
      }
    }
  }
}

指定分词器，如果不指定分词器，且要查询的字段没有指定的分词器 默认的是 standard，若是查询的字段在创建的时候指定了分词器，则就使用该字段的分词器类型；如果使用ik中文分词器，则需要指定是 ik_smart 还是 ik_max_word，详情见后文 ik 分词器的介绍 
GET /index/_search
{
  "query": {
    "match": {
      "name": {
        "query": "张李",
        "analyzer": "ik_smart"
      }
    }
  }
}
```

#### **match_phrase**

`match_phrase` 称为短语搜索，要求所有的分词必须同时出现在文档中，同时位置必须紧邻一致。

```json
可以搜索出的查询
GET /index2/_search
{
  "query": {
    "match_phrase": {
      "name": "人民 共和国"
    }
  }
}
不可以搜索出的查询
GET /index2/_search
{
  "query": {
    "match_phrase": {
      "name": "共和国 人民"
    }
  }
}
```

#### **multi_match**

`multi_match`是匹配多个字段，比如，从name 和 desc 中搜索匹配的内容，

假如现在`index2`库中有两个记录如下：

![image-20200418135933608](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200418135933608.png)

现在查询出 name 中有 张三， 或者 address 中有 市中区 的文档

```json
GET /index2/_search
{
  "query": {
    "multi_match": {
      "query": "张三市中区",
      "fields": ["name", "address"]
    }
  }
}
```

#### sort

准备`index3`中如下数据：

![image-20200418140906885](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200418140906885.png)

查询出所有的数据并且按`age`从小到大排序，从小到大：`asc`，从大到小：`desc`，和mysql中是一样的

**注意：`sort`和`query`是平级的，并不是`sort`嵌套到`query`中**

![image-20200418141210385](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200418141210385.png)

#### 分页

数据如上

查询出前5名人员信息，需要使用`from` 和 `size`，`from`中从哪个地方开始，`size`是查询出多少条，和mysql中的`limit`一样

![image-20200418141625043](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200418141625043.png)

#### 高亮

数据如上

查询出`name`中有 张 字的文档，并高亮显示

![image-20200418142453178](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200418142453178.png)

#### 范围

数据如上

查询出年龄在10~20之间的人，`range`中的gte, lte, gt, lt

![image-20200418143621539](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200418143621539.png)

#### 取指定字段

数据如上

只获取年龄字段

![image-20200418152256521](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200418152256521.png)

#### 复杂、组合查询

```json
{
   "bool" : {
      "must" :     [],  // 相当于 and
      "should" :   [],  // 相当于 or
      "must_not" : [],  // 相当于 not
      "filter":    [],  // 过滤器
   }
}
```

数据如上

查询出年龄在10~20之间，name 中有 张 字，但不能 name 出现 2 

```json
GET /index3/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "张"
          }
        },
        {
          "range": {
            "age": {
              "gte": 10,
              "lte": 20
            }
          }
        }
      ],
      "must_not": [
        {
          "match": {
            "name": "2"
          }
        }
      ]
    }
  }
}
```



![image-20200418145537138](/Users/lf/Desktop/学习笔记/elasticsearch图片/image-20200418145537138.png)

可以看到把 name 为 张三2 的去除了

### ik 分词器

#### 基本用法

分词:即把一段中文或者别的划分成一个个的关键字,我们在搜索时候会把自己的信息进行分词,会把数据库中或者索引库中的数据进行分词,然后进行一个匹配操作,
 默认的中文分词是将每个字看成一个词，比如"中国的花"会被分为"中","国","的","花",这显然是不符合要求的，所以我们需要安装中文分词器ik来解决这个问题。

**IK提供了两个分词算法
ik_smart 和 ik_max_word
其中 ik_smart 为最少切分，ik_max_word为最细粒度划分**

+ 使用默认的分词器

  ```json
  GET /_analyze
  {
    "text": "进口红酒"
  }
  ```

  结果

  ```json
  {
    "tokens" : [
      {
        "token" : "进",
        "start_offset" : 0,
        "end_offset" : 1,
        "type" : "<IDEOGRAPHIC>",
        "position" : 0
      },
      {
        "token" : "口",
        "start_offset" : 1,
        "end_offset" : 2,
        "type" : "<IDEOGRAPHIC>",
        "position" : 1
      },
      {
        "token" : "红",
        "start_offset" : 2,
        "end_offset" : 3,
        "type" : "<IDEOGRAPHIC>",
        "position" : 2
      },
      {
        "token" : "酒",
        "start_offset" : 3,
        "end_offset" : 4,
        "type" : "<IDEOGRAPHIC>",
        "position" : 3
      }
    ]
  }
  ```

  

+ 使用ik中的ik_smart

  ```json
  GET /_analyze
  {
    "text": "进口红酒",
    "analyzer": "ik_smart"
  }
  ```

  结果

  ```json
  {
    "tokens" : [
      {
        "token" : "进口",
        "start_offset" : 0,
        "end_offset" : 2,
        "type" : "CN_WORD",
        "position" : 0
      },
      {
        "token" : "红酒",
        "start_offset" : 2,
        "end_offset" : 4,
        "type" : "CN_WORD",
        "position" : 1
      }
    ]
  }
  ```

  

+ 使用ik中的ik_max_word

  ```json
  GET /_analyze
  {
    "text": "进口红酒",
    "analyzer": "ik_max_word"
  }
  ```

  结果

  ```json
  {
    "tokens" : [
      {
        "token" : "进口",
        "start_offset" : 0,
        "end_offset" : 2,
        "type" : "CN_WORD",
        "position" : 0
      },
      {
        "token" : "口红",
        "start_offset" : 1,
        "end_offset" : 3,
        "type" : "CN_WORD",
        "position" : 1
      },
      {
        "token" : "红酒",
        "start_offset" : 2,
        "end_offset" : 4,
        "type" : "CN_WORD",
        "position" : 2
      }
    ]
  }
  ```

#### ik分词器支持自定义词库

默认的分词并没有识别“小明”是一个词。如果我们想让系统识别“小明”是一个词，需要编辑自定义词库。
 步骤：
 （1）进入elasticsearch/plugins/ik/config目录
 （2）新建一个my.dic文件，编辑内容：
 `小明`
 修改IKAnalyzer.cfg.xml（在ik/config目录下）

```xml
 <properties>
    <comment>IK Analyzer 扩展配置</comment>
    <!‐‐用户可以在这里配置自己的扩展字典 ‐‐>
    <entry key="ext_dict">my.dic</entry>
     <!‐‐用户可以在这里配置自己的扩展停止词字典‐‐>
    <entry key="ext_stopwords"></entry>
</properties>
```

重新启动elasticsearch即可