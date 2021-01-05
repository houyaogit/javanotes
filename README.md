# javanotes



# Git常用命令

## Git搜索： 关键词 、 技术栈都有 awesome项目 ，脚手架（关键词 + boilerplate）
寻找demo      技术栈关键车 <br>
项目搜索       in:name React <br>
脚手架         技术栈+boilerplate <br>
笔记          类型+笔记 <br>
免费编程中文书  free-programming-book <br>
搜索轮子       xxx-like <br>
数据爬虫工具    scrapy dianping.com <br>
学习资料  awesome redis  (awesome 关键字)<br>
xxx in:name  <br>
xxx in:readme  <br>
xxx in:description  <br>
stars:>xxx <br>
forks:>30000 <br>

地区搜索<br>
language:xxx <br>
location:xxx <br>

区域活跃：<br>
location:shanghai language:java <br>

经典搜索 t ，项目中按键盘t
地址 +#L数字
https://github.com/codingXiaxw/seckill/blob/master/src/main/java/cn/codingxiaxw/dao/SeckillDao.java#L13
https://github.com/codingXiaxw/seckill/blob/master/src/main/java/cn/codingxiaxw/dao/SeckillDao.java#L13-L23

### Github 帮助文档：
https://help.github.com/en/articles/using-keyboard-shortcuts

seckill in:name,readme

1、进入项目文件夹，通过命令 git init 把这个目录变成git可以管理的仓库<br>
2、把文件添加到版本库中，使用命令 git add .添加到暂存区里面去，不要忘记后面的小数点“.”，意为添加文件夹下的所有文件 git add . <br>
3、用命令 git commit告诉Git，把文件提交到仓库。引号内为提交说明<br>
   git commit -m 'first commit'<br>
4、关联到远程库<br>
   git remote add origin 你的远程库http地址<br>
6、把本地库的内容推送到远程，使用 git push<br>
   git push -u origin master

