目录

- 自带收集日志模块
- 收集状态
  - nginx
  - php-fpm
 

### 自带收集日志模块

##### 启用和配置 nginx 模块

```
./metricbeat modules enable nginx
```

在 `modules.d/nginx.yml` 文件中修改设置。

##### 启动 Filebeat

```
./metricbeat setup
./metricbeat -e
```

### 收集状态

#### nginx 状态

nginx配置

```
#auth_basic "User Authentication";
#auth_basic_user_file /opt/nginx/conf/conf.d/kibana.passwd;
        
location /server-status {
     allow 127.0.0.1; //允许的IP
     stub_status  on;
     access_log   off;
}
```

配置完成重启 nginx

#### php-fpm 状态

1、编辑php-fpm.conf 配置文件 找到pm.status_path配置项

```
pm.status_path = /php-status
```

2、nginx配置

```
location /php-status {
    fastcgi_pass  127.0.0.1:9000;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $fastcgi_script_name;
    allow 127.0.0.1; //允许的IP
    access_log off;
    #deny all;
}
```

配置完成重启 nginx 和 php-fpm