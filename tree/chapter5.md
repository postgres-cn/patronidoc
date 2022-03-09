<font size="48"><b>第5章 PostgreSQL 大版本升级</b></font><br>
目前唯一可行的方法是：<br>
1.停止 Patroni <br>
2.升级PostgreSQL二进制文件并在master节点上执行pg_upgrade <br>
3.更新patroni.yml<br>
4.从 DCS 中删除initialize或从 DCS 中擦除完整的集群状态。 第二个可以实现 通过运行patriotictl remove <cluster-name>。 这是必要的，因为 pg_upgrade 运行 initdb 它实际上创建了一个具有新 PostgreSQL 系统标识符的新数据库。 <br>
5.如果你在上一步删除了集群状态，你可能希望从旧数据目录复制patoni.dynamic.json 到新的。 它将帮助您保留您之前设置的一些 PostgreSQL 参数。 <br>
6.在主节点上启动 Patroni。 <br>
7.升级PostgreSQL二进制文件，更新patroni.yml并移除备节点上的data_dir。 <br>
8.在备用节点上启动 Patroni 并等待复制完成。<br>
PostgreSQL 不支持在备用节点上运行 pg_upgrade。 如果您知道自己在做什么，您可以尝试 https://www.postgresql.org/docs/current/pgupgrade.html 中描述的 rsync 过程，而不是在备用节点上移除data_dir。 然而，最安全的方法是让 Patroni 为您复制数据。<br>
<b>5.1 常见问题</b><br>
• 在Patroni 启动过程中，Patroni 抱怨它无法绑定到PostgreSQL 端口。<br>
您需要验证postgresql.conf中的 listen_addresses 和 port ，以及patroni.yml中的 postgresql.listen 。别忘了 pg_hba.conf 应该允许这样的访问。• 在要求 Patroni 重新启动节点后，PostgreSQL 显示错误信息：could not open configuration file "/etc/postgresql/10/main/pg_hba.conf": No such file or directory<br>
根据您管理 PostgreSQL 配置的方式，它可能意味着多种含义。 如果您指定 post gresql.config_dir，则 Patroni 仅在引导新集群时根据引导部分中的设置生成 pg_hba.conf。 在这种情况下，PGDATA 不为空，因此没有发生引导程序。 该文件必须事先存在。<br>