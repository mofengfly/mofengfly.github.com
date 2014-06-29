---
layout: default
title: MarkDown Test
---

###目录
* [超链接](#toc_1)  
* [列表](#toc_2)  
* [任务列表](#toc_3)
* [表格](#toc_4)  
* [代码块](#toc_5)

###超链接
```
[靠谱-ing](http://www.mazhuang.org)
<http://www.mazhuang.org>
```
[靠谱-ing](http://www.mazhuang.org)  
<http://www.mazhuang.org>

###列表
```
1. first
2. second
3. third
```
1. first  
2. second  
3. third  

###任务列表
```
- [] Task1
- [] Task2
- [1] Task3
- [1] Task4
```
- [] Task1  
- [] Task2  
- [1] Task3  
- [1] Task4  


###表格
```
| HEADER1 | HEADER2 | HEADER3 | HEADER4 |
| ------- | :------ | :-----: | ------: |
| content | content | content | content |

```
| HEADER1 | HEADER2 | HEADER3 | HEADER4 |
| ------- | :------ | :-----: | ------: |
| content | content | content | content |

1. :----- 表示左对齐
2. :----: 表示中对齐
3. -----: 表示右对齐

###代码块

```python
print 'Hello, World!'
```

{% gist mzlogin/f6dbe25c70131113b7ec %}
