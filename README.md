# jiangdaxia
leetcode算法笔记
## 178. 分数排名
编写一个 SQL 查询来实现分数排名。

如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”。
```
+----+-------+
| Id | Score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+
```

例如，根据上述给定的 Scores 表，你的查询应该返回（按分数从高到低排列）：
```
+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+
```

本题代码如下：
```
select 
b.score,a.rank 
from 
  (select  score ,convert((@rank := @rank + 1), signed) 'rank' 
  from 
  (select @rank := 0)  ranktable,(select distinct score from Scores order by score desc) s) a, scores b 
where 
a.score=b.score order by a.rank;
```
其中@符号：可以自定义一个临时变量

首先使用`(select @rank := 0)`初始化临时变量表，将这个表与去重之后的分数表连接，再通过外层select中`@rank := @rank + 1`每次rank+1得到分数排名。因为此时的分数是去过重的，所以在最外层再将未去重的score表和查询到的rank表连接即可。

注：单独命名别名为rank，'rank'需要用引号扩起来，因为rank是mysql中的关键字，否则会报错。

注：convert((@rank := @rank + 1), signed)表示将得到的排名为1.0、2.0这种小数转变为整数
