# Mysql 明明实践

## Mysql 8 外网访问

- Reference: 20191126

  - https://lefred.be/content/how-to-grant-privileges-to-users-in-mysql-8-0/

  - https://www.jianshu.com/p/98a6d42e28c8

- 创建新用户 并允许外网访问【默认看不到已有的数据库，需要授权】

  ```mysql
  create user 'userName'@'%' identified 'your_password';
  ```

- 授权已有的数据库给新创建的用户

  ```mysql
  grant alter,create,delete,drop,index,insert,select,update,trigger,alter routine,
  create routine, execute, create temporary tables on user1.* to 'user1';
  ```

  

