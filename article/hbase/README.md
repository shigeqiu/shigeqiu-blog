# HABSE

```
create 'Student', {NAME => 'Stulnfo', VERSIONS => 3}, {NAME =>'Grades', BLOCKCACHE => true}
```


大括号内是对列族的定义，

NAME、VERSION 和 BLOCKCACHE 是参数名，无须使用单引号，符号=>表示将后面的值赋给指定参数。例如，VERSIONS => 3是指此单元格内的数据可以保留最近的 3 个版本，BLOCKCACHE => true指允许读取数据时进行缓存。