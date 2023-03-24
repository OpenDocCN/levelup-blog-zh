# SQL v. Pandas:基本语法比较和备忘单

> 原文：<https://levelup.gitconnected.com/sql-v-pandas-basic-syntax-comparison-cheat-sheet-498289372d45>

在开始使用 Python 中的 Pandas 之前，我已经使用 SQL 好几年了。我最终习惯了它，但是当我开始的时候，我在它们之间的语法差异中挣扎。

我想在这篇文章中收集语法比较，也作为备忘单。我将提到基本语法，但如果有另一个例子在这篇文章中不存在，请让我知道！

![](img/3c6d85580695ecf76df76fceb2901a5a.png)

[https://github.com/shotin93/sql-v-pandas](https://github.com/shotin93/sql-v-pandas)

# 资料组

我将使用这个数据集。

```
import pandas as pddf_list = [
  ["Lamar Jackson", 23, "Ravens", "QB"]
  , ["Russell Wilson", 31, "Seahawks", "QB"]
  , ["Aaron Donald", 29, "Rams", "DT"]
  , ["Patrick Mahomes", 24, "Chiefs", "QB"]
  , ["Michael Thomas", 27, "Saints", "WR"]
  , ["Christian McCaffrey", 24, "Panthers", "RB"]
  , ["George Kittle", 26, "49ers", "TE"]
  , ["DeAndre Hopkins", 28, "Cardinals", "WR"]
  , ["Stephon Gilmore", 29, "Patriots", "CB"]
  , ["Derrick Henry", 26, "Titans", "RB"]
]
df_columns = ["Name", "Age", "Team", "Position"]
players = pd.DataFrame(data=df_list, columns=df_columns)
```

![](img/414c90db184d7ebcdfa225a786cbdb8c.png)

演员

# 挑选

全部提取:

```
SQL: SELECT * FROM playersPandas: players
```

一些列的投影:

```
SQL: SELECT Name, Age FROM playersPandas: players[["Name", "Age"]]
```

![](img/9b7bf85ad3939619f52c7c88ee569f5d.png)

顶部一些行的选择:

```
SQL: SELECT TOP 5 * FROM playersPandas: players.head(5)
```

![](img/e9147db59037a5a181c965321c634f81.png)

独特:

```
SQL: SELECT DISTINCT Position FROM playersPandas: players["Position"][~players["Position"].duplicated()]
```

# 在哪里

带条件的特定行的选择:

```
SQL: SELECT * FROM players WHERE Position = 'QB'Pandas: players[players.Position == "QB"]
also: players[players["Position"] == "QB"]
```

![](img/4f29cd17932ba639a927f59c8bec5b4e.png)

特定行的选择和某些列的投影

```
SQL: SELECT Name, Age FROM players WHERE Position = 'QB'Pandas: players[["Name", "Age"]][players.Position == "QB"]
```

![](img/89eaed14f5b813b2c118bb5c47d50f71.png)

不是:

你必须用“~”而不是“不是”。

```
SQL: SELECT * FROM players WHERE NOT Position =='QB'Pandas: players[~(players.Position == "QB")]
also: players[~(players["Position"] == "QB")]
```

![](img/f3c5efd3387ef0c47e2c7ee4ff9ab0ee.png)

和/或:

```
SQL: SELECT Name, Age FROM players WHERE Position = 'QB' AND Age < 30Pandas: players[["Name", "Age"]][(players.Position == "QB") & (players.Age < 30)]
```

![](img/eae9753859b3aafaf1b212dfc52bc910.png)

```
SQL: SELECT Name, Age FROM players WHERE Position = 'QB' OR Position = 'RB'Pandas: players[["Name", "Age"]][(players.Position == "QB") |(players.Position == "RB")]
```

![](img/85d757b4910b4d070876fe94a0d2ba09.png)

你必须用括号把操作符括起来。

否则，您将得到一个 TypeError:无法对 dtyped [int64]数组和类型为[bool]的标量执行“rand_”

```
# without brackets
players[["Name", "Age"]][players.Position == "QB" & players.Age < 30]
```

![](img/a6d2981f37d20cbd5d2faa179c120441.png)

你还必须用' & '和' | '来代替' and '和' or '。

否则，您将得到一个 ValueError:一个序列的真值是不明确的。使用 a.empty、a.bool()、a.item()、a.any()或 a.all()。

```
# using 'and'
players[["Name", "Age"]][(players.Position == "QB") and (players.Age < 30)]
```

![](img/0f6c9d0be10336ebc03d9ce108b3d010.png)

当你想这样查询的时候；

```
SELECT Name, Age FROM
players
WHERE (Position = 'QB' AND Age < 25) OR (Position = 'RB' AND Age < 25)
```

你必须写作；

```
players[["Name", "Age"]][
(
    (players.Position == "QB") & (players.Age < 25)
) | (
    (players.Position == "RB") & (players.Age < 25)
)]
```

![](img/6caac1432cc6197fdd3e23079876d7dc.png)

不是:

在:

```
SQL: SELECT * FROM players WHERE Position IN ('QB', 'RB')Pandas: players[players.Position.isin(["QB", "RB"])]
```

![](img/2dd9438d377df90ff2f1dc9d0c5d1232.png)

比如:

```
SQL: SELECT * FROM players WHERE Team LIKE '%ns%'Pandas: players[players.Team.str.contains("ns")]
```

![](img/b86d0a3ad7d8ca5375f89c8fafd3d895.png)

# 以...排序

按 1 列排序:

```
SQL: SELECT * FROM players ORDER BY NamePandas: players.sort_values("Name")
```

![](img/60ba74e9d0c1350768c19319b2062105.png)

按 2+列排序:

```
SQL: SELECT * FROM players ORDER BY Age, NamePandas: players.sort_values(["Age", "Name"])
```

![](img/ebc6299d97046323a7f4077de13b7afb.png)

DESC:

```
SQL: SELECT * FROM players ORDER BY Age DESC, NamePandas: players.sort_values(["Age", "Name"], ascending=[False, True])
```

![](img/9f57450bde611b64c531c054335a5c24.png)

# 分组依据

计数:

```
SQL: SELECT COUNT(*) AS COUNT FROM players GROUP BY PositionPandas: players.groupby("Position").agg({"Name": "count"}).rename(columns={"Name": "COUNT"})
```

![](img/1182d551485f22500db0f08f3ddc6269.png)

总和:

```
SQL: SELECT SUM(Age) AS Age FROM players GROUP BY PositionPandas: players.groupby("Position").agg({"Age": "sum"})
```

![](img/3c50c2a211714252853e961fcdb1ff95.png)

AVG:

```
SQL: SELECT AVG(Age) AS Age FROM players GROUP BY PositionPandas: players.groupby("Position").agg({"Age": "mean"})
```

![](img/2b40ca5355eab317be676a8e0546abe6.png)

排名:

```
SQL: SELECT *, RANK() OVER (ORDER BY Age DESC) FROM playersPandas: 
players["AgeRank"] = players.Age.rank(ascending=False)
players
```

![](img/14bfee4148fb7d27190040af1fc24605.png)

等级+分区依据

```
SQL: SELECT *, RANK() OVER (PARTITION BY Position ORDER BY Age DESC) FROM playersPandas: 
players["AgeRank"] = players.groupby("Position")["Age"].rank(ascending=False)
players
```

![](img/36af9fcb2a5aa2b314d97a6224bc6533.png)

密集:

```
SQL: SELECT *, DENSE_RANK() OVER (ORDER BY Age DESC) FROM playersPandas: 
players["AgeRank"] = players.Age.rank(method="dense", ascending=False)
players
```

![](img/f8d9a2f983c81a2c19555ea6849f58a4.png)

# 更新

```
SQL: UPDATE players SET Age = 0 WHERE Age = 24Pandas: players.loc[players.Age == 24, "Age"] = 0
```

![](img/30e5adefd7bfd618a3ca7dc7e2dc0415.png)

# 加入

请看我的另一篇文章！

[](https://medium.com/@sh_in/sql-v-pandas-join-57642dc3ce76) [## SQL v. Pandas:加入

### 在开始使用 Python 中的 Pandas 之前，我已经使用 SQL 好几年了。我最终习惯了，但是我…

medium.com](https://medium.com/@sh_in/sql-v-pandas-join-57642dc3ce76)