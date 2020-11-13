# Mysql 踩坑记录

- 大小写问题

  - https://blog.csdn.net/fdipzone/article/details/73692929

  - lower_case_table_names = 1时，mysql会先把表名转为小写，再执行操作。

  - > https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html
    >
    > You should *not* set [`lower_case_table_names`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names) to 0 if you are running MySQL on a system where the data directory resides on a case-insensitive file system (such as on Windows or macOS).

