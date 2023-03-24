# 使用 Python 和 Pulp 拯救生命(和岁月)

> 原文：<https://levelup.gitconnected.com/using-python-pulp-to-save-lives-years-e3f5903a088b>

![](img/22b4f374cc35fad20c7390771b67fe2a.png)

通风机现在是一种重要的资源

据报道，意大利的医生被迫做出一个令人心碎的决定，即是否要给 ICU 的新来病人使用呼吸机。在我写的这篇[文章中，我讨论了分析如何帮助支持有望改善所有患者的患者结果的决策。](https://medium.com/swlh/optimised-ventilator-deployment-95c2a6cf98b7)

在这篇文章中，我将向您展示如何使用 Stuart Mitchell 在“奥克兰大学”创建的优秀的 [Pulp python 库](http://coin-or.github.io/pulp/)(无耻插件——我在奥克兰大学学习，也是语言“R”的发源地)来解决这个问题(并有望挽救生命)，使用的数据与我在文章中使用的数据相同——可供您个人使用[这里](https://drive.google.com/open?id=1lO52vkPz9_1fYufYlKKLSltAjC7h_EcU)。

那么什么是纸浆呢？Pulp 是一个 Python 库，用于创建和求解整数程序。

那么什么是“整数程序”呢？整数规划是一个数学问题，其目标是最大化或最小化一些可量化的值，这些值取决于一些问题变量，受到一些约束集的约束，这些约束集限制了变量可以取的值，并且限制了一些(或所有)变量必须是整数。如果变量不一定是整数值(整数)，那么就叫线性规划(不是整数规划)。

这听起来可能有点拗口！首先要知道的是，世界各地的大学通常在数学或工程学位快结束时教授线性和整数编程(所以对它们感到有点害怕是正常的！).

我在这里要举的例子也是一个有点挑战性的整数程序，所以，如果有些方面令人生畏，也不用太担心！每当你试图开始一个整数程序时，我总是建议你通过同样的几个步骤来简化它。

1.  陈述问题的决策变量——这些是你还不知道其值，但你想知道其值的数字。
2.  定义你试图最大化或最小化的值，以及它们与变量的关系。这就是所谓的“目标函数”。
3.  定义步骤 1 中的“决策变量”是如何界定的，以及它们必须如何相互关联。这些被称为“约束”。

那么这如何寻找呼吸机部署问题呢？好吧，让我们假设我们已经能够估计(或知道)；可用或即将可用的呼吸机供应可用性、使用和不使用呼吸机的患者存活可能性(按年龄段)、患者的紧急需求概况(按人数和年龄段)以及患者在释放呼吸机之前使用呼吸机的时间分布——这些都包含在上面链接的数据文件夹中。我建议在继续之前阅读[这篇文章](https://medium.com/swlh/optimised-ventilator-deployment-95c2a6cf98b7)以了解这个问题的背景。完成了吗？下面是我如何定义这个问题的 3 个步骤:

1.  我们想决定*从现在开始，每天到达 ICU 的每个年龄段的患者是否应该被允许使用呼吸机，或者拒绝使用*。这些决定是*二进制* (0 或 1)，我们用 1 来表示应该为患者提供呼吸机的决定，用 0 来表示拒绝使用呼吸机的决定。
2.  我们希望最大化到达重症监护室的病人继续存活的年数。如果一个病人死于这种疾病，这个数字是可悲的 0 年。如果一个病人活了下来，那就是我们期望他们能活多久。这意味着更年轻的病人更重要，因为他们有更多的生命。
3.  这里只有一种资源可以利用——呼吸机。对于这个问题，我们唯一的限制是我们只能使用一组固定的呼吸机。也就是说，我们的呼吸机可用性不能为负——我们每天只能发放尽可能多的呼吸机。

首先，下面是我用来加载相关库和数据的代码:

```
# imports
import pandas as pd
from pulp import *# directory the data is saved
in_dir = 'C:/Users/james/Google Drive/Ventilator Supply Management/Data'# load data
age_data_df = pd.read_csv(os.path.join(in_dir, 'Age Data.csv'))
daily_data_df = pd.read_csv(os.path.join(in_dir, 'Daily Expectations.csv'))
vent_usage_exp_df = pd.read_csv(os.path.join(in_dir, 'Vent Usage Days.csv'))# Convert the impt data into dicts with keys as the day number, age-brackets & days of use of ventilator
vent_usage_days = vent_usage_exp_df['Days'].tolist()
age_brackets = age_data_df['Age Bracket'].tolist()
days = daily_data_df['t'].tolist()vent_usage_prob_days = dict(zip(vent_usage_days, vent_usage_exp_df['Prob'].tolist()))
exp_pat_arr = dict(zip(days, daily_data_df['Exp Pat Arr'].tolist()))
vent_curr_and_back = dict(zip(days, daily_data_df['Vent Curr and Back'].tolist()))arr_rates_age = dict(zip(age_brackets, age_data_df['Arrival Rate'].tolist()))
surv_wo_vent_rates = dict(zip(age_brackets, age_data_df['Surv WO Vent'].tolist()))
surv_w_vent_rates = dict(zip(age_brackets, age_data_df['Surv W Vent'].tolist()))
ass_rm_yrs = dict(zip(age_brackets, age_data_df['Ass Rem Yrs'].tolist()))
```

太好了！现在，让我们通过创建包含我们为这个问题定义的所有数据的问题对象来开始使用 Pulp:

```
model = pulp.LpProblem("Max Life", pulp.LpMaximize)
```

下一步，我们必须为问题创建决策变量。在我们的案例中，决定是否给某一年龄段的患者使用呼吸机*可能会在一天之内发生变化*。因此，该变量需要按日期和年龄段进行索引:

```
x = pulp.LpVariable.dicts('Allow_Ventilator',
                          [(day, age_b) for age_b in age_brackets for day in days],
                            lowBound = 0,
                            upBound = 1,
                            cat = pulp.LpInteger)
```

上面的第二行定义了这个变量是由患者到达的日期和每个年龄段索引的。

接下来我们需要定义目标函数。这部分肯定是有挑战性的。我们正试图最大限度地延长所有病人的存活时间。对于任何单个年龄段和日期组合，这可以计算为 1 * (2 + 3)，其中:

1.  在患者到达的一天/年龄段内，有多少生命年处于危险之中。
2.  是提供呼吸机情况下的预期存活率(仅在 x[(day，age_b)] = 1 时有效)。
3.  是未提供呼吸机时的预期存活率(仅在 x[(day，age_b)] = 0 时有效)。

然后使用 lpSum 函数对所有年龄段和所有日期的数字-1 *(2+3)求和。它被添加到模型对象中，并带有纸浆固有的+=操作符:

```
model += lpSum([(arr_rates_age[ab]*exp_pat_arr[d]*ass_rm_yrs[ab]) * \# 1
                (x[(d,ab)] * surv_w_vent_rates[ab] +  \# 2
                (1 - x[(d,ab)]) * surv_wo_vent_rates[ab]) \# 3
                for ab in age_brackets for d in days]) # for all
```

最后，我们只需要在这个问题上加上我们的约束。这个问题只有一个限制，那就是每天使用的呼吸机数量必须少于我们认为当天可用的原始供应量。

我们预计每天使用的呼吸机数量由当天发布的数字(1)、*和*给出，即前几天*仍在使用的数字*(2)。

但是，我们能指望有多少以前发放的呼吸机现在还在使用呢？那部分有点棘手，最简单的解释方法是用一个例子；在第 5 天，我们在第 1 天发放的仍在使用的呼吸机的数量是那些使用了 5 天或更长时间的呼吸机的数量(因为它们在第 6、7…天被退回)。这等于呼吸机的数量乘以使用持续时间超过 4 天的概率，我们可以从呼吸机使用天数的概率分布中计算出来。同样，添加了 model +=操作符(我排除它是为了格式化下面的文本):

```
lpSum([x[(day, ab)]*(arr_rates_age[ab]*exp_pat_arr[day]) \ 
              for ab in age_brackets]) + \ # 1 
lpSum([(arr_rates_age[ab]*exp_pat_arr[d])*x[(d,ab)] * \ 
       vent_usage_prob_days[days_used] \ 
       for ab in age_brackets \ 
       for d in range(max(day-vent_usage_exp_df.shape[0], 0),day) \ 
       for days_used in vent_usage_days if d + days_used > day]) # 2
<= vent_curr_and_back[day], f"Vent supp day {day}"
```

现在模型已经完全定义好了，让我们求解并得出结果:

```
model.solve()
print("Status:", LpStatus[model.status])
print("Value:", model.objective.value())# A nice table for our results:
decision_table_df = pd.DataFrame(index=days, columns=age_brackets)
for key in [(day, age_b) for age_b in age_brackets for day in days]:
    print(x[key].name, "=", x[key].varValue)
    decision_table_df.at[key[0], key[1]] = x[key].varValue# take a look at how many 'leftover', ventilators we had on each day
for name, c in list(model.constraints.items()):
    print(name, ":\t\t", round(-1 * c.slack))
```

要看结果，看一看我在[的其他帖子](https://medium.com/swlh/optimised-ventilator-deployment-95c2a6cf98b7)。结果表明，与仅使用“先来先服务”的方法来决定每天允许/拒绝使用呼吸机的年龄段相比，使用模拟结果可以节省 2.25%以上的生命年(或 1153 年以上)。