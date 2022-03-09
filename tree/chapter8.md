<font size="48"><b>第8章 安全注意事项</b></font><br>
Patroni 集群有两个接口可以防止未经授权的访问：分布式配置存储 (DCS) 和 Patroni REST API。<br>
<b>8.1 保护 DCS</b><br>
Patroni 和patronictl 都向/从DCS 存储和检索数据。 <br>
尽管 DCS 不包含任何敏感信息，但它允许更改某些 Patroni/Postgres 配置。 <br>
因此，首先应该保护的是 DCS 本身。 保护的细节取决于所使用的 DCS 类型。 支持的 DCS 类型的身份验证和加密参数（tokens/basicauth/client certificates）包含在设置中<br>
一般建议是为所有 DCS 通信启用 TLS。<br>
<b>8.2 保护 REST API</b><br>
保护 REST API 是一项更复杂的任务。 <br>
Patroni本身会在领导者竞赛期间使用Patroni REST API，通过patronictl 工具来执行故障转移/切换/重新初始化/重新启动/重新加载，通过HAProxy或任何其他类型的负载平衡器来执行HTTP运行状况检查，当然也可以用于监视。<br>
从安全的角度来看，REST API 包含安全（GET 请求，只检索信息）和不安全（PUT、POST、PATCH 和 DELETE 请求，更改节点状态）端点。 <br>
可以通过设置 restapi.authentication.username和restapi.authentication.password参数使用HTTP basic-auth保护不安全的端点。如果不启用TLS，则无法保护安全端点。<br>
启用REST API的TLS并建立PKI时，可以对所有端点进行API服务器和API客户端的相互身份验证。<br>
restapi 启用对服务器的TLS客户端身份验证。根据verify_client参数的值，API服务器需要成功地对安全和不安全的API调用（verify_client：required），或者仅对不安全的API调用（verify_client：optional），或者没有API调用（verify_client：none），进行客户端证书验证。<br>
ctl 部分参数启用对客户端的 TLS 服务器身份验证（使用与patroni相同的配置的patronictl 工具）。 设置 insecure: true 以禁用客户端的服务器证书验证。 有关 TLS 客户端参数的详细说明，请参阅设置。 <br>
保护 PostgreSQL 数据库免受未经授权的访问超出了本文档的范围，请移步参阅https://www.postgresql.org/docs/current/client-authentication.html<br>