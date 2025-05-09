### MarkDown学习

[MarkDown教程](https://markdown.com.cn/)

[MarkDown总览](https://markdown.com.cn/cheat-sheet.html#%E6%80%BB%E8%A7%88)

#### MarkDown扩展语法

##### Markdown 表格

要添加表，请使用三个或多个连字符（---）创建每列的标题，并使用管道（|）分隔每列。您可以选择在表的任一端添加管道。

举例MarkDown脚本如下：
~~~
| Syntax | Description |
| --- | ----------- |
| Header | Title |
| Paragraph | Text |
~~~
效果如下：
| Syntax | Description |
| --- | ----------- |
| Header | Title |
| Paragraph | Text |


您可以通过在标题行中的连字符的左侧，右侧或两侧添加冒号（:），将列中的文本对齐到左侧，右侧或中心。

~~~
| Syntax      | Description | Test Text     |
| :---        |    :----:   |          ---: |
| Header      | Title       | Here's this   |
| Paragraph   | Text        | And more      |
~~~

效果如下：

| Syntax      | Description | Test Text     |
| :---        |    :----:   |          ---: |
| Header      | Title       | Here's this   |
| Paragraph   | Text        | And more      |

[Markdown 表格教程链接](https://markdown.com.cn/extended-syntax/tables.html)


#### 设置字体颜色

[【Markdown笔记】设置字体颜色](https://blog.csdn.net/u012028275/article/details/115445362)

#### 图片大小与位置的调整

[Markdown中图片对齐方式及尺寸设置](https://blog.csdn.net/tugepaopaoo/article/details/130196496)

下面的方式好用

```
<img src="https://i-blog.csdnimg.cn/blog_migrate/348f8ce8a69c97427cf55dde66f1b4c7.png"  width="600" />
```

调整对齐方式：

```
<p align = "center">    
<img  src="https://i-blog.csdnimg.cn/blog_migrate/348f8ce8a69c97427cf55dde66f1b4c7.png" width="400" />
</p>

```

可以支持多个图片一起对齐，记得项目间不要加空行