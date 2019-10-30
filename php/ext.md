### php 扩展


PHP下载地址  http://php.net/get/php-7.1.1.tar.bz2/from/a/mirror 

```
cd ext
./ext_skel --extname=helloworld
```
生成扩展骨架

```
##动态编译，通过.so的方式链接，去掉前面的dnl:

PHP_ARG_WITH(helloworld, for helloworld support,
Make sure that the comment is aligned:
[  --with-helloworld             Include helloworld support])

##静态编译，通过enable来启用，去掉前面的dnl:

 PHP_ARG_ENABLE(helloworld, whether to enable helloworld support,
 Make sure that the comment is aligned:
 [  --enable-helloworld           Enable helloworld support])
```

编写扩展函数 `helloworld`

```
vim helloworld.c
#在PHP_FUNCTION(confirm_myfun_compiled); 下追加一行
PHP_FUNCTION(helloworld);
const zend_function_entry myfun_functions[] = {
        PHP_FE(confirm_myfun_compiled,     NULL)           /* For testing, remove later. */
        PHP_FE(helloWorld,     NULL)  /* <== 加在这里 */
        {NULL, NULL, NULL}      /* Must be the last line in myfun_functions[] */
};


#PHP_FUNCTION(confirm_myfun_compiled)的后面编写
PHP_FUNCTION(helloWorld)
{
        php_printf("Hello World!\n");
        RETURN_TRUE;
}
完成函数的编写
```

编译安装
- ./configure --with-php-config=/usr/local/php/bin/php-config
- make && make install

测试`helloworld`函数
```
php -d enable_dl=On -r "dl('helloworld.so');helloworld();"
Hello World!
php -d enable_dl=On -r "dl('helloworld.so');print confirm_helloworld_compiled('helloworld');"
Congratulations! You have successfully modified ext/helloworld/config.m4. Module helloworld is now compiled into PHP.
```

参考 https://www.cnblogs.com/boystar/p/6904795.html

https://segmentfault.com/a/1190000008114150