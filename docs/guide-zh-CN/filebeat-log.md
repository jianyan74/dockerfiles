目录

- 自带收集日志模块

### 自带收集日志模块

##### 启用和配置 nginx 模块

```
./filebeat modules enable nginx
```

在 `modules.d/nginx.yml` 文件中修改设置。

##### 启动 Filebeat

```
./filebeat setup
./filebeat -e
```