# Stupid-Legal-Search
搜索引擎课的大作业：司法搜索

## 已经支持的功能(置顶)

+ 简单的关键词搜索
+ 基于BM25的结果排序
+ 搜索结果的可调分页展示
+ 含关键词高亮的文书摘要和文书详情展示
+ 简单的标签筛选搜索
+ 基于Trie的引导词提示
+ 十分垃圾的同法官案件推荐和同法律案件推荐

## 基于BM25的排序器(待完善)

Ranker Version2：在倒排索引中增加字段“score”记录每个查询词在各个文档中出现时的BM25得分，排序时对所有词在相应文档中的score进行简单相加

详情：https://my.oschina.net/stanleysun/blog/1617727

## 后端使用

在`source/SearchEngine-BackEnd/SearchEngine`目录下依次运行

```shell
python preprocess.py # 数据集预处理+数据库构建
cd ..
python manage.py runserver # 启动后端服务器
```

数据库结构：

```
Appearance (7kw)
{
	'pid': (int),
	'freq': (int),
	'score': (float)
}

InvertedIndex (100w)
{
	'term': (string),
	'appear_list': [Appearace, ...]
}

Paper(16w)
{
	'pid': (int), 
	'path': (string),
	'AJLB': (string), # 案件类别
	'SPCX': (string), # 审判程序
	'WSZL': (string), # 文书类别
	'CPSJ': (string), # 裁判时间
	'XZQH_P': (string), # 行政区划（省）
	'XZQH_C': (string), # 行政区划（市）
	'XZQH_CC': (string), # 行政区划（区县）
	'JBFY': (string), # 经办法院
	'FGRYWZ ': (list) # 法官成员完整
	'WS': (string) # 文首
	'FLFTFZ': (list) # 法律法条分组
}
```

记录所有文书所在的**绝对路径**的文件：

```
temp/filename.pkl
[name1, name2, ...]
```

## 前端使用

在`source/SearchEngine-FrontEnd`下运行

```
npm install
npm run dev
```

在前端代码中指定了后端端口为8000

如果发送请求的时候chrome浏览器报CORS错误(控制台可见)，就右键打开桌面chrome的快捷方式“属性”，在“目标”最后添加

```
--disable-web-security --user-data-dir=<某个目录>
```

关闭浏览器安全控制。用这种方式打开chrome，个人设置和历史记录会全部清空（不用这种方式打开即可恢复）。

## 后端接口

### 引导词

前端以`GET`方式访问`/recommend_words?prefix=...`，`prefix`为所给前缀，返回`json`数据，格式如下：

```
{
	'result': [recommended_word, ...]
}
```

### 文本推荐

前端以`GET`方式访问`/recommend_docs?text=...`，`text`为所给文本，返回`json`数据，格式如下：

```
{
	'result': [pid, ...]	
}
```

### 相似文书

前端以`GET`方式访问`/similar_docs?pid=...`，`pid`为查询的文书id，返回`json`数据，格式如下：

```
{
	'result': [pid, ...]
}
```

