目录

- 安装
- 插件

### 安装

> 如果想安装其他版本请自行修改进入目录下的 elasticsearch.yml 文件

1、进入目录

```
cd /your/path/to/dockerfiles/sercives/elasticsearch/
```

2、执行

启动后报错请先看[常见问题](elastic-issue.md)

```
docker-compose up

// 成功后执行
docker-compose up -d
```

### 插件

1、进入插件映射目录

```
cd /your/path/to/dockerfiles/data/elasticsearch/plugins/
```

2、插件安装比较简单直接下载解压到当前目录即可

ik分词器

https://github.com/medcl/elasticsearch-analysis-ik/releases

拼音

https://github.com/medcl/elasticsearch-analysis-pinyin/releases