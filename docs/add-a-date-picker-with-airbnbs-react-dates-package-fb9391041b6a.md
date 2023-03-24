# 用 Airbnb 的 React Dates 包添加一个日期选择器

> 原文：<https://levelup.gitconnected.com/add-a-date-picker-with-airbnbs-react-dates-package-fb9391041b6a>

![](img/12d345622010db139af8ddaceb0dcabc.png)

由 [Wiktor Karkocha](https://unsplash.com/@rotkif?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们可以通过 Airbnb 的 React Dates 包轻松添加日期选择器。

# 装置

我们通过运行以下命令来安装所需的软件包:

```
npm install --save react-dates moment
```

# 基本日期选择器

我们通过编写以下代码将日期选择器添加到 React 组件中:

```
import React from "react";
import "react-dates/initialize";
import { DateRangePicker } from "react-dates";
import "react-dates/lib/css/_datepicker.css";export default function App() {
  const [startDate, setStartDate] = React.useState();
  const [endDate, setEndDate] = React.useState();
  const [focusedInput, setFocusedInput] = React.useState();
  return (
    <div className="App">
      <DateRangePicker
        startDate={startDate}
        startDateId="start-date"
        endDate={endDate}
        endDateId="end-date"
        onDatesChange={({ startDate, endDate }) => {
          setStartDate(startDate);
          setEndDate(endDate);
        }}
        focusedInput={focusedInput}
        onFocusChange={(focusedInput) => setFocusedInput(focusedInput)}
      />
    </div>
  );
}
```

我们导入`react-dates/initialize`包来运行初始化代码。

我们导入 CSS。

我们添加了带有`DateRangePicker`组件的日期选择器。

列出的道具都是必须的。

`startDate`有开始日期。

`startDateId`是开始日期字段的 ID。

`endDate`有结束日期。

`endDateId`是结束日期字段的 ID。

`onDatesChanges`让我们更新开始和结束日期状态。

`focusedInput`设置输入是否聚焦。

`onFocusChange`具有设置`focusedInput`状态的功能，以设置聚焦哪个字段。

# 覆盖样式

我们可以通过为一些类设置样式来覆盖样式。

例如，我们可以写:

`styles.css`

```
.CalendarDay__selected_span {
  background: #82e0aa;
  color: white;
  border: 1px solid lightred;
}.CalendarDay__selected {
  background: red;
  color: white;
}.CalendarDay__selected:hover {
  background: orange;
  color: white;
}.CalendarDay__hovered_span:hover,
.CalendarDay__hovered_span {
  background: brown;
}
```

`CalendarDay__selected_span`是日期之间的日历框的类。

`CalendarDay__selected`是所选开始日期的班级。

`CalendarDay__selected:hover`是当鼠标悬停在所选开始日期上时的伪类。

`CalendarDay__hovered_span`设置结束日期框的颜色。

# 单一日期选择器

如果我们只想选择一个日期，我们还可以添加`SingleDatePicker`。

例如，我们可以写:

```
import React from "react";
import "react-dates/initialize";
import { SingleDatePicker } from "react-dates";
import "react-dates/lib/css/_datepicker.css";export default function App() {
  const [date, setDate] = React.useState();
  const [focused, setFocused] = React.useState();
  return (
    <div className="App">
      <SingleDatePicker
        date={date}
        onDateChange={(date) => setDate(date)}
        focused={focused}
        onFocusChange={({ focused }) => setFocused(focused)}
        id="date"
      />
    </div>
  );
}
```

我们添加`SingleDatePicker`组件来添加日期选择器。

`date`有日期。

让我们来确定日期。

`focused`有焦点日期。

`onFocusChanged`让我们改变焦点状态。

`id`有日期字段的 ID。

# 结论

我们可以用 react-dates 库添加一个简单的日期范围和日期选择器。