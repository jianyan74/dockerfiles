
配置 | 说明
---|---
server.port | 默认值 : 5601 Kibana 由后端服务器提供服务。此设置指定要使用的端口。
server.host | 默认值 : “localhost” 此设置指定后端服务器的主机。
server.basePath | 使您能够指定如果您在代理后运行的 Kibana 的路径。这只影响 Kibana 生成的 URL，您的代理应该在转发请求到 Kibana 之前删除 basePath 值。此设置不能以斜杠（/）结尾。
server.maxPayloadBytes | 默认值 : 1048576 传入服务器请求的最大有效负载大小（以字节为单位）。
server.name | 默认值 : “your-hostname” 用于标识此 Kibana 实例的可读的显示名称。
server.defaultRoute | 默认值 : “/app/kibana” 此设置指定打开 Kibana 时的默认路由。您可以使用此设置在打开 Kibana 时修改登录页。
elasticsearch.url | 默认值 : “http://localhost:9200” 要用于所有查询的 Elasticsearch 实例的 URL。
elasticsearch.preserveHost | 默认值 : true 当此设置的值为 true 时，Kibana 使用 server.host 设置中指定的主机名。当此设置的值为 false 时，Kibana 使用连接到此 Kibana 实例的主机的主机名。
kibana.index | 默认值 : “.kibana”Kibana 使用 Elasticsearch 中的索引来存储保存的搜索，可视化和仪表板。如果索引不存在，Kibana 将创建一个新索引。
kibana.defaultAppId | 默认值 : “discover” 要加载的默认应用程序。
tilemap.url | Kibana 用于在 tilemap 可视化中显示地图图块的图块服务的 URL。默认情况下，Kibana 从外部元数据服务读取此 URL，但用户仍可以覆盖此参数以使用自己的 Tile Map Service。例如 : "https : //tiles.elastic.co/v2/default/{z}/{x}/{y}.png?elastic_tile_service_tos=agree&my_app_name=kibana"
tilemap.options.minZoom | 默认值 : 1 最小缩放级别。
tilemap.options.maxZoom | 默认值 : 10 最大缩放级别。
tilemap.options.attribution | 默认 : "© [Elastic Tile Service](https : //www.elastic.co/elastic-tile-service)" 地图属性字符串。
tilemap.options.subdomains | tile 服务使用的子域的数组。指定带有令牌的 URL 的子域的位置 {s}。
elasticsearch.username | 如果您的 Elasticsearch 受基本认证保护，这些设置提供 Kibana 服务器用于在启动时对 Kibana 索引执行维护的用户名和密码。您的 Kibana 用户仍需要使用通过 Kibana 服务器代理的 Elasticsearch 进行身份验证。
elasticsearch.password | 
server.ssl.cert | 分别指向 PEM 格式 SSL 证书和 SSL 密钥文件的路径。这些文件为从 Kibana 服务器到浏览器的传出请求启用 SSL。
server.ssl.key | 
elasticsearch.ssl.cert | 提供 PEM 格式 SSL 证书和密钥文件路径的可选设置。这些文件验证您的 Elasticsearch后端使用相同的密钥文件。
elasticsearch.ssl.key | 
elasticsearch.ssl.ca | 可选设置，使您能够为 Elasticsearch 实例指定证书颁发机构的 PEM 文件的路径。
elasticsearch.ssl.verify | 默认值 : true 要忽略 SSL 证书的有效性，请将此设置的值更改为 false。
elasticsearch.pingTimeout | 默认值 : 值 elasticsearch.requestTimeout 设置以毫秒为单位的时间等待 Elasticsearch 对 PING 作出响应。
elasticsearch.requestTimeout | 默认值 : 30000 等待来自后端或 Elasticsearch 的响应的时间（以毫秒为单位）。此值必须为正整数。
elasticsearch.requestHeadersWhitelist | 默认值 : [ 'authorization' ] 要发送到 Elasticsearch 的 Kibana 客户端头标列表。要发送任何客户端头，请将此值设置为 []（一个空列表）。
elasticsearch.customHeaders | 默认值 : {} 要发送到 Elasticsearch 的（header name）标题名称和值。不管如何配置 elasticsearch.requestHeadersWhitelist，任何自定义的 header 都不能被客户端头覆盖
elasticsearch.shardTimeout | 默认值 : 0 Elasticsearch 等待来自分片的响应的时间（以毫秒为单位）。设置为 0 可禁用。
elasticsearch.startupTimeout | 默认值 : 5000 重试前在 Kibana 启动时等待 Elasticsearch 的时间（以毫秒为单位）。
pid.file | 指定 Kibana 创建进程标识文件的路径。
logging.dest | 默认值 : stdout 允许您指定 Kibana 存储日志输出的文件。
logging.silent | 默认值 : false 将此设置的值设置 true 为禁止所有日志记录输出。
logging.quiet | 默认值 : false 将此设置的值设置 true 为禁止除错误消息之外的所有日志记录输出。
logging.verbose	 | 默认值 : false 将此设置的值设置为 true 记录所有事件，包括系统使用信息和所有请求。
ops.interval	 | 默认值 : 5000 设置示例系统和过程性能指标的间隔（以毫秒为单位）。最小值为 100。
status.allowAnonymous	 | 默认值 : false 如果启用了验证，则将此设置为 true 允许未经身份验证的用户访问 Kibana 服务器状态 API 和状态页。
console.enabled	 | 默认值 : true 设置为 false 可禁用控制台。切换这将导致服务器在下次启动时重新生成资产，这可能会导致在开始投放网页之前出现延迟。
console.proxyFilter	 | 默认值 : .* 用于验证来自控制台的任何传出请求的正则表达式列表。如果没有匹配，请求将被拒绝。
console.proxyConfig	 | 基于代理目标的配置选项列表。使用此选项可以为特定主机设置自定义超时或 SSL 设置。这是通过 match 使用通配符 /globs 定义一组标准来完成的，这将根据每个请求进行检查。然后，来自所有匹配规则的配置将合并在一起，以配置用于该请求的代理。有效匹配关键字 match.protocol，match.host，match.port 和 match.path。所有这些键默认为 *，这意味着它们将匹配任何值。有关示例，请参阅 配置控制台。
elasticsearch.tribe.url | 用于所有查询的 Elasticsearch 部落实例的可选 URL。
elasticsearch.tribe.username | 如果您的 Elasticsearch 受基本认证保护，这些设置提供 Kibana 服务器用于在启动时对 Kibana 索引执行维护的用户名和密码。您的 Kibana 用户仍需要使用通过 Kibana 服务器代理的 Elasticsearch 进行身份验证。
elasticsearch.tribe.password | 
elasticsearch.tribe.ssl.cert | 提供 PEM 格式 SSL 证书和密钥文件路径的可选设置。这些文件验证您的 Elasticsearch后端使用相同的密钥文件。
elasticsearch.tribe.ssl.key | 
elasticsearch.tribe.ssl.ca | 可选设置，使您能够为 Elasticsearch 实例指定证书颁发机构的 PEM 文件的路径。
elasticsearch.tribe.ssl.verify | 默认值 : true 要忽略 SSL 证书的有效性，请将此设置的值更改为 false。
elasticsearch.tribe.pingTimeout | 默认值 : 值 elasticsearch.tribe.requestTimeout 设置以毫秒为单位的时间等待 Elasticsearch 对 PING 作出响应。
elasticsearch.tribe.requestTimeout | 默认值 : 30000 等待来自后端或 Elasticsearch 的响应的时间（以毫秒为单位）。此值必须为正整数。
elasticsearch.tribe.requestHeadersWhitelist | 默认值 : [ 'authorization' ] 要发送到 Elasticsearch 的 Kibana 客户端 header 列表。要发送任何客户 header，请将此值设置为[]（一个空列表）。
elasticsearch.tribe.customHeaders | 默认值 : {} 要发送到 Elasticsearch 的 header name 和 value。不管如何配置 elasticsearch.tribe.requestHeadersWhitelist，任何自定义的 header 都不能被客户端的 header 覆盖。
