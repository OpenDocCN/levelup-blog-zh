# 在 SwiftUI 中构建小部件

> 原文：<https://levelup.gitconnected.com/building-a-widget-in-swiftui-81e949185a95>

![](img/f8b47c1e84104cf39f7651b71f45781c.png)

照片由[米盖尔·托马斯](https://unsplash.com/@miguelavtomas?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/widgets?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

目标是构建一个小部件，可以从 [TronaldDump](https://tronalddump.io/) 获取报价，并将其显示在主屏幕上。它还会每 10 分钟自动更新一次。

为此，您需要最新的 Xcode 12 测试版。

用 iOS 启动一个新项目，然后用 WidgetExtension 添加一个新目标。您可以暂时删除样板代码。

添加一个名为`Quotes.swift`的 Swift 文件。这将包含获取报价的结构和函数。

```
//Structure of the quote
  struct Quotes: Codable {
	  var value: String?
  }

  class QuoteController {

    let url = URL(string: "https://tronalddump.io/random/quote")!
    static let shared = QuoteController()

    //Get a random quote from tronalddump
    func getQuote(completion : @escaping(Quote)->Void) {
        let session = URLSession.shared
        let task = session.dataTask(with: url) { (data, response, error) in
            guard let data = data else { return }
            let quote = try! JSONDecoder().decode(Quote.self, from: data)
            completion(quote)
        }

        task.resume()
    }

  }
```

该文件将处理所有与报价相关的内容。下一步是前往`QuoteWidget.swift`文件。

小部件的最小元素是小部件内部的项目结构，即报价

```
struct QuoteEntry: TimelineEntry {
    var date: Date
    var quote: Quote
}
```

下一部分是小部件的视图。它将显示 QuoteEntry 结构中的报价。

```
struct WidgetEntryView: View {
     var entry: QuoteEntry

     var body: some View {
       VStack(alignment: .leading, spacing: 5) {
         Text(entry.quote.value ?? "")
             .italic()
             .font(.caption)
         Text("- Donald Trump")
             .foregroundColor(.secondary)
             .font(.footnote)
      }
      .padding()
    }
  }
```

第三部分是内容/时间线提供者，它决定什么时候显示什么。

```
struct Provider: TimelineProvider {

   // Our controller class
    var loader = QuoteController()

    // the snapshot view shown in the widget library. It'll be constant
    func snapshot(with context: Context,
                  completion: @escaping (QuoteEntry) -> ()) {
        let entry = QuoteEntry(date: Date(), quote: Quote(value: "President of the US of A"))
        completion(entry)
    }

    // The timeline for the widget
    func timeline(with context: Context,
                  completion: @escaping (Timeline<QuoteEntry>) -> ()) {

        //time points
        let components = DateComponents(minute: 10)
        let futureDate = Calendar.current.date(byAdding: components, to: Date())!

        //load a quote
        loader.getQuote { (quote) in
            let entry = QuoteEntry(date: Date(), quote: quote)

            //reload this after 10 mins
            let timeline = Timeline(entries: [entry], policy: .after(futureDate))
            completion(timeline)
        }
     }
  }
```

最后，实际的小部件

```
@main
  struct QuoteWidget: Widget {
     var body: some WidgetConfiguration {
         StaticConfiguration(
             kind: "QuoteWidget",
             provider: Provider(),
             placeholder: Text("Cool Stuff by Dolan Trum"),
             content: { entry in
                 WidgetEntryView(entry: entry)
             }
         )
         .configurationDisplayName("Quote Widget")
         .description("Shows the dumbest stuff said by Donald Trump")
     }
  }
```

注意，这使用了 SwiftUI 中新的`@main`起点，它决定了应用程序的起点。

有了这个，你就会有一个小部件显示唐纳德·特朗普的 dum dum 报价。和新东西一起工作是非常令人着迷的，真的，✨.

在这里找到完整的源代码。对于更深入的东西，我推荐约翰·桑德尔的 [WWDCbySundell](https://wwdcbysundell.com/2020/getting-started-with-widgetkit/) 。