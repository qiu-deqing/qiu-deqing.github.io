---
title: 无法获取数据库连接
---

# java连接mysql数据库

[stackoverflow: ][2]

# 无法获取数据库连接

    com.mysql.jdbc.exceptions.jdbc4.CommunicationsException: Communications link failure  The last packet sent successfully to the server was 0 milliseconds ago. The driver has not received any packets from the server.  at sun.reflect.NativeConstructorAccesso


可能原因:

- IP address or hostname in JDBC URL is wrong.
- Hostname in JDBC URL is not recognized by local DNS server.
- Port number is missing or wrong in JDBC URL.
- DB server is down.
- DB server doesn't accept TCP/IP connections.
- DB server has run out of connections.
- Something in between Java and DB is blocking connections, e.g. a firewall or proxy.

解决办法:

- Verify and test them with ping.
- Refresh DNS or use IP address in JDBC URL instead.
- Verify it based on my.cnf of MySQL DB.
- Start the DB.
- Verify if mysqld is started without the --skip-networking option.
- Restart the DB and fix your code accordingly that it closes connections in finally.
- Disable firewall and/or configure firewall/proxy to allow/forward the port.

[stackoverflow: com-mysql-jdbc-exceptions-jdbc4-communicationsexception-communications-link-fai][1]

# 通过mysql show processlist 命令检查mysql锁的方法

[http://blog.sina.com.cn/s/blog_54eeb5d90102wf9l.html]()

[2]: http://stackoverflow.com/questions/2839321/connect-java-to-a-mysql-database/2840358#2840358
[1]: http://stackoverflow.com/a/2985169
