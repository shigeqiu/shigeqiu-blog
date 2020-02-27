# Vim快捷键

## 翻页

- `ctrl-f` 上翻一页
- `ctrl-b` 下翻一页
- `ctrl-u` 上翻半页
- `ctrl-d` 下翻半页
- `ctrl-y` 上滚一行
- `ctrl-e`  下滚一行

## vim显示行数

> 版本： centos 7

方法一：

1. 显示当前行行号，在VI的命令模式下输入 `:nu`
2. 显示所有行号，在VI的命令模式下输入`:set nu`


方法二.仅让当前用户显示行号 

- 输入命令： `vim ~/.vimrc`
- 写入： `set nu`

方法三.让所有用户显示行号 

1. 输入命令： `vim /etc/vimrc`
1. 在vimrc文件的最后添加 `set nu`
1. 保存 ， 手动加载配置：`source /etc/bashrc`
