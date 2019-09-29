目录

- 汉化

### 汉化

##### kibana5.*/6.7以下

官方并没有支持中文，需要另外下载补丁包 推荐下面这个 ↓

https://github.com/anbai-inc/Kibana_Hanization

##### kibana6.7(包含)以上/7.*

只需要在配置文件 kibana.yml 中加入

```
i18n.locale: "zh-CN"
```