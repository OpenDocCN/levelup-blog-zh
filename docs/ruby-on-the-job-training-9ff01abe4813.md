# Ruby 在职培训

> 原文：<https://levelup.gitconnected.com/ruby-on-the-job-training-9ff01abe4813>

## 我在前两个月的工作中学到的东西

![](img/2f1818946f9f3289cf2612d991b97320.png)

**& —安全操作员**

如果您已经了解该方法，那么“&”符号是(:try)的一个很好的替代符号。它基本上翻译成，“如果这个存在，那么在它上面调用下面的方法”。在代码中，可能是这样的:

```
chicken&.add_special_sauce
```

如果 chicken 对象不为 nil，它将对 chicken 调用 add_special_sauce 方法。如果为零，这一行不会出错。

**委派**

委托是一种“委托”的方式——或者将方法从一个模型传递到另一个模型。在本例中，我们有以下关系:

一个音乐家。具有我们希望提取的 global_id。(例如。音乐家. global_id)

一场演出。属于一个音乐家。有一个音乐家 id。

客户演出。客户和 gig 之间的连接表。有一个 gig_id 和一个 client_id(未使用)。

歌单。属于 client_gig。有一个客户端标识。

我们想问歌单模型，“你的音乐人的 global_id 是多少？”可以想象，有一种长格式的方法可以做到这一点:

```
songlist = Songlist.find (1234)
client_gig = ClientGig.find_by_id(songlist.client_gig_id)
gig = Gig.find_by_id(client_gig.gig_id)
musician = Musician.find_by_id(gig.musician_id)
musician.global_id = '0029e1b9-f20e-4f95-9db9-78f753edd1c5'
```

如您所见，这是对数据库的大量调用，并且退出了大量代码。相反，我们可以创建一个方法，称为#musician_global_id，我们可以将它从一个模型委托给另一个模型。

因为音乐家已经有了属性 global_id，所以我们先来看看 Gig 模型。我们想称之为:

```
gig.musician_global_id
```

在 ruby 中，我们实际上可以将音乐家的#global_id 方法委托给 gig，就像这样:

```
#Gig.rb
delegate :global_id, to: :musician, prefix: :musician, allow_nil: true
```

我们委托了 global_id，在它前面加上了音乐家，因此它变成了一个名为# museum _ global _ id 的方法，并允许它返回 nil(可选的，但在某些情况下是有帮助的)，是音乐家给了 Gig 这个方法。所以，让我们继续把火炬传递到下一个关系，在演出和客户演出之间。

```
#ClientGig.rb
delegate :musician_global_id, to: :gig, allow_nil: true
```

有道理吗？现在，Gig 上已经有了#musician_global_id，我们可以将该方法从 Gig 委托给 ClientGig，并允许 nil(同样是可选的)。

最后，我们允许歌曲列表知道这个方法，因为它属于一个客户演出。

```
#Songlist.rb
delegate :musician_global_id, to: :client_gig, allow_nil: true
```

现在我们有 songlist.musician_global_id 可供调用。因此，概括一下，代替这一长串代码:

```
songlist = Songlist.find (1234)
client_gig = ClientGig.find_by_id(songlist.client_gig_id)
gig = Gig.find_by_id(client_gig.gig_id)
musician = Musician.find_by_id(gig.musician_id)
musician.global_id = '0029e1b9-f20e-4f95-9db9-78f753edd1c5'
```

我们可以将所有内容缩减到几行代码来得到结果:

```
songlist = Songlist.find (1234)
songlist.musician_global_id
```

我们使用 delegate 来通过关系返回所需的属性，而不是在每个模型中编写一个自定义方法来生成更多的代码，看起来像这样:

```
#Gig.rb
def musician_global_id(musician)
    musician.global_id
end#ClientGig.rbdef musician_global_id(gig)
    gig.musician_global_id
end#Songlist.rb
def musician_global_id(client_gig)
     client_gig.musician_global_id
end
```

**# frozen _ string _ literal:true**

这个注释存在于我看到的大多数 Ruby 文件的顶部。它基本上是说，如果文件中有任何字符串文字(例如:)

```
"Hi, Mom!"
```

它们不能被修改。就好像对所有这些方法都调用了#freeze 方法，就像这样:

```
"Hi, Mom!".freezestringy = "Hi, Mom!"
stringy.freeze
```

**耙任务**

在最初的几个星期里，我不知道 rake 任务是什么，也不知道它们对现实生活中的用例有多大用处。

Rake 任务基本上是用描述和名称定义的脚本或代码块。它们可以用名称隔开，这样您就可以具体说明如何从终端运行它们。

让我们为真实场景创建一个 rake 任务。我们需要获取一个 CSV 文件，并获取它的内容，以在我们已经存在的数据库表中创建文件。

求解步骤:

1.  将 CSV 文件插入您的代码库
2.  编写一个 rake 任务，使用该文件在表中创建记录

CSV 文件如下所示(缩短):

```
First Name, Last Name, Instrument, Email
Kelsey,Shiba,Piano,kshiba@music.com
Bob,George,Trombone,bgeorge@music.com
```

耙子任务如下:

```
namespace :db do  
   desc 'Updates fields for the list of musicians from data/musicians.csv'
   task :add_musicians, %i[csv_file] => :environment do |_, args|    
   # rake db:add_musicians['path/to/file.csv'] abort('input file not found') unless args[:csv_file].present?

   CSV.foreach(args[:csv_file], headers: true) do |row|
        Musician.find_or_create_by!(
          first_name: row['First Name'],
          last_name: row['Last Name'],
          instrument: row['Instrument'],
          email: row['Email']
        )
   end
  end
end
```

rake 任务有一个描述，然后是一个任务名称，它获取一个文件(csv_file)，如果没有输入文件，则停止任务，并使用 Ruby 类 csv 遍历每一行，或者查找已经存在的记录，或者用每一行中的每个信息创建一个新记录。

我们可以从命令行调用 rake 任务，如下所示:

```
rake db:add_musicians['/data/musicians.csv']
```

**结论**

我很兴奋每天都能学到新东西，并分享这些信息。感谢阅读！