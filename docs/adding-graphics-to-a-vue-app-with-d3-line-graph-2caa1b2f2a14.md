# 使用 D3 向 Vue 应用添加图形—线图

> 原文：<https://levelup.gitconnected.com/adding-graphics-to-a-vue-app-with-d3-line-graph-2caa1b2f2a14>

![](img/f66d194ea0622a8e6f92371c21096d93.png)

克里斯托弗·伯恩斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 图表

我们可以用 D3 给我们的 Vue 应用添加图表。

例如，我们可以写:

`public/data.csv`

```
year,population
2006,20
2008,35
2010,48
2012,51
2014,63
2016,23
2017,42
```

`App.vue`

```
<template>
  <div id="app"></div>
</template>
<script>
import * as d3 from "d3";
import Vue from "vue";export default {
  name: "App",
  mounted() {
    Vue.nextTick(async () => {
      const margin = { top: 20, right: 20, bottom: 30, left: 50 },
        width = 960 - margin.left - margin.right,
        height = 500 - margin.top - margin.bottom; const x = d3.scaleTime().range([0, width]);
      const y = d3.scaleLinear().range([height, 0]); const valueline = d3
        .line()
        .x(function (d) {
          return x(d.year);
        })
        .y(function (d) {
          return y(d.population);
        }); const svg = d3
        .select("body")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", `translate(${margin.left}, ${margin.top})`); const data = await d3.csv("/data.csv"); data.forEach(function (d) {
        d.population = +d.population;
      }); x.domain(
        d3.extent(data, function (d) {
          return d.year;
        })
      ); y.domain([
        0,
        d3.max(data, function (d) {
          return d.population;
        }),
      ]); svg
        .append("path")
        .data([data])
        .attr("class", "line")
        .attr("d", valueline); svg
        .append("g")
        .attr("transform", `translate(0, ${height})`)
        .call(d3.axisBottom(x)); svg.append("g").call(d3.axisLeft(y));
    });
  },
};
</script><style>
.line {
  fill: none;
  stroke: green;
  stroke-width: 5px;
}
</style>
```

我们有在`public/data.csv`读到的数据。

然后我们通过创建宽度和高度的常量来创建图形。/

然后我们添加`x`和`y`轴对象:

```
const margin = {
    top: 20,
    right: 20,
    bottom: 30,
    left: 50
  },
  width = 960 - margin.left - margin.right,
  height = 500 - margin.top - margin.bottom;const x = d3.scaleTime().range([0, width]);
const y = d3.scaleLinear().range([height, 0]);
```

然后，我们通过编写以下内容为 x 轴和 y 轴创建线对象:

```
const valueline = d3
  .line()
  .x(function(d) {
    return x(d.year);
  })
  .y(function(d) {
    return y(d.population);
  });
```

然后，我们将 SVG 添加到`body`中，方法是:

```
const svg = d3
  .select("body")
  .append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
  .append("g")
  .attr("transform", `translate(${margin.left}, ${margin.top})`);
```

然后，我们通过写入来读入我们想要显示的数据:

```
const data = await d3.csv("/data.csv");
```

接下来，我们通过将`population`转换成数字来转换读取的数据:

```
data.forEach(function(d) {
  d.population = +d.population;
});
```

然后我们添加回调来返回 x 和 y 轴的数据:

```
x.domain(
  d3.extent(data, function(d) {
    return d.year;
  })
);y.domain([
  0,
  d3.max(data, function(d) {
    return d.population;
  }),
]);
```

然后，我们使用以下内容为折线图创建线条:

```
svg
  .append("path")
  .data([data])
  .attr("class", "line")
  .attr("d", valueline);
```

最后，我们将 x 轴和 y 轴相加:

```
svg
   .append("g")
   .attr("transform", `translate(0, ${height})`)
   .call(d3.axisBottom(x));svg.append("g").call(d3.axisLeft(y));
```

我们必须添加一些样式来移除线条的宽度并设置线条的粗细:

```
<style>
.line {
  fill: none;
  stroke: green;
  stroke-width: 5px;
}
</style>
```

现在我们应该会看到一个折线图。

# 结论

我们可以在我们的 Vue 应用程序中添加一个带有 D3 的线图。