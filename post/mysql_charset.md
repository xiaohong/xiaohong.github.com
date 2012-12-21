## mysql字符集问题

这个上线遇到Mysql乱码的问题，主要是使用存储过程到表A向新表导数据时汉字出现乱码。其中表A中的数据正常。

刚开始通过` set names utf8 来设置到Mysql的连接字符集使用utf8,这个表示所有从客户端到服务器的数据使用utf8编码，但是这个并没有解决问题，重新用存储过程导数据后还是乱码，事后猜测在存储过程中的select出来然后传递到后面的数据并没有使用connection charset来进行编码。

随后看到表A的引擎是innodb, 而表b的引擎是MyISAM, 然后又猜测难道是表引擎不同，读出来的数据就有乱码，为了不是这个原因造成的，直接在把线上表A的引擎改为innodb,然后又重新导了一遍数据，还是乱码。事先就不太相信是这个原因造成的，但是还是冒很大风险去尝试。

剩余的尝试就是不停的重复执行导数据，很显然这根本不会解决问题。

最后想到用程序先把数据读取出来，然后在根据读取的数据对表B进行更新，这种方法解决了乱码，因为表A可以正确读取数据，表B可以正确插入数据，但是两者在存储过程中进行组合出现乱码，在程序中进行组合就没有问题。因为程序到mysql的连接是utf8,读取数据也是utf8,然后再把utf8数据插入到表B中。

那为什么在存储过程中会出现乱码呢？随后在搜索了一下，说存储过程使用变量character_set_server作为过程中传输数据的字符编码，而且只使用mysqld启动时的默认值，而不是启动后修改过后的数据。经过测试确实如此，如果mysqld启动不明确设置character_set_server的值，默认为latin1, 然后存储过程中传输数据的字符编码为latin1,即使在执行之前set character_set_server = utf8也不行，重新启动Mysqld并在配置文件中设置character_set_server=utf8后就可以了，所有猜测问题问题的根本原因是存储过程中字符串变量解析数据的默认编码为启动时的character_set_server的值，但是不是可以明确指定这个编码解析数据的编码，实际上完全可以在声名变量时自定字符集CHARACTER SET utf8，这样就可以避免依赖于默认值，从这个特点推断mysql的存储过程中的sql查询出来的数据都是二进制，然后根据使用方的编码来解析这些数据。

## mysql字符集编码

  mysql字符集包括2个概念，character_set(字符集编码) 和 collation(字符排序规则)， 

  字符集可以在不同的层次内设置， server -> database -> table -> column -> character String(例如select语句中的字符常量), 字符编码表示存储数据使用的规则，这些字符集都表示mysql服务器存储数据的编码依据，

  另外一个字符编码表示客户端到服务器的编码，通过set names charset, 这个语句会设置变量character_set_results，character_set_connection，character_set_client。

## 总结

    遇到不理解的问题要多思考，分析考虑各种可能性，最后再去搜索答案。

## 参考资料

  [Mysql文档](http://dev.mysql.com/doc/refman/5.0/en/globalization.html)