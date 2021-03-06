#Inception 对线上配置需求
1.  线上服务器必须要打开 binlog，在启动时需要设置参数log_bin、log_bin_index等关于 binlog 的参数。不然不会备份及生成回滚语句。
2. 参数binlog_format必须要设置为 mixed 或者 row 模式，通过语句： set global binlog_format=mixed/row 来设置，如果是 statement 模式，则不做备份及回滚语句的生成。
3.  参数 server_id 必须要设置为非0及非1，通过语句：set global server_id=server_id;来设置，不然在备份时会报错。
4. 使用备份及生成回滚语句功能，必须设置`inception_remote_system_password`不为空，否则会报 `Invalid remote backup information.` 错误。
5. 线上服务器一定要有指定用户名的权限，这个是在语句前面的注释中指定的，做什么操作就要有什么权限，否则还是会报错，如果需要执行的功能，则要有线上执行语句的权限，比如DDL及DML，同时如果要执行inception show 等远程命令的话，有些语句是需要特殊权限的，这些权限也是需要授予的，关于权限这个问题，因为一般Inception是运行在一台固定机器上面的，那么在选项中指定的用户名密码，实际上是Inception机器对线上数据库访问的权限，所以建议在使用过程中，使用专门固定的帐号来让Inception使用，最好是一个只读一个可写的即可，这样在执行时用可写，审核或者查看线上状态或者表库结果时用只读即可，这样更安全。

#需要额外注意的点
1. 在执行时，不能将 DML 语句及 DDL 语句放在一起执行，否则会因为备份解析binlog时由于表结构的变化出现不可预知的错误，如果要有同时执行 DML 及 DDL，则请分开多个语句块儿来执行，如果真的这样做了，Inception 会报错，不会去执行。
