# 30 秒内 10 次 Javascript 破解

> 原文：<https://levelup.gitconnected.com/10-javascript-hacks-in-30-seconds-a9b0213971bd>

![](img/ec7154c4b473044136224e4f77f2ad98.png)

## **1。生成 UUID**

```
 /**
 * Generates a random UUID
 * @param a - The parameter a is a number that is used to generate the UUID. If you don't pass a
 * parameter, the function will generate a random number.
 */
const generateRandomUUID = (a) => (a ? (a ^ ((Math.random() * 16) >> (a / 4))).toString(16) : ([1e7] + -1e3 + -4e3 + -8e3 + -1e11).replace(/[018]/g, generateRandomUUID));
```

## **2。洗牌阵列**

```
/**
 * Return a new array with the elements of the original array in a random order.
 * @param arr - The array to shuffle.
 */
const shuffleArray = (arr) => arr.sort(() => Math.random() - 0.5);
```

## 3.唯一数组

```
/**
 * It takes an array, converts it to a Set, and then converts it back to an array
 * @param arr - The array to be filtered.
 */
const getUnique = (arr) => [...new Set(arr)];
```

## 4.展平数组

```
 /**
 * If the current element is an array, flatten it, otherwise, add it to the accumulator.
 */
const flatten = arr => arr.reduce((a, b) => a.concat(Array.isArray(b) ? flatten(b) : b), []);
```

## 5.生成随机颜色

```
/**
 * Generate a random hex colour code.
 */
const generateRandomHexColor = () => `#${Math.floor(Math.random() * 0xffffff).toString(16)}`;
```

## 6.RGB < = >十六进制代码

```
/**
 * It takes three numbers, one for red, one for green, and one for blue, and returns a hexadecimal
 * string representing the color
 * @param r - red value
 * @param g - The green value of the color.
 * @param b - blue
 */
const rgbToHex = (r, g, b) => "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1);

/**
 * It converts a hexadecimal color code to an RGB color code.
 * @param hex - The hexadecimal color code.
 */
const hexToRgb = (hex) => hex.replace(/^#?([a-f\d])([a-f\d])([a-f\d])$/i, (m, r, g, b) => '#' + r + r + g + g + b + b).substring(1).match(/.{2}/g).map(x => parseInt(x, 16));
```

## 7.求数组的平均值

```
/**
 * Given an array of numbers, return the average of those numbers.
 */
const average = arr => arr.reduce((p, c) => p + c, 0) / arr.length;
```

## 8.套管转换

```
/**
 * It converts a string to camel case.
 */
const toCamelCase = str => str.replace(/(?:^\w|[A-Z]|\b\w)/g, (word, index) => index == 0 ? word.toLowerCase() : word.toUpperCase()).replace(/\s+/g, '');

/**
 * Replace all words in a string with the first letter capitalized and the rest of the letters
 * lowercase.
 */
const toTitleCase = str => str.replace(/\w\S*/g, txt => txt.charAt(0).toUpperCase() + txt.substr(1).toLowerCase());

/**
 * Replace all whitespace characters with underscores and convert the string to lowercase.
 */
const toSnakeCase = str => str.replace(/\s+/g, '_').toLowerCase();
```

## 9.度数< = >弧度

```
/**
 * Convert a degree value to a radian value.
 */
const degToRad = deg => deg * (Math.PI / 180);

/**
 * Convert radians to degrees.
 */
const radToDeg = rad => rad * (180 / Math.PI);
```

## 10.在表格中可视化 json 数组

```
var data = [{ city: "New York" }, { city: "Chicago" }, { city: "Los Angeles" }];
console.table(data);
// output
┌─────────┬───────────────┐
│ (index) │     city      │
├─────────┼───────────────┤
│    0    │  'New York'   │
│    1    │   'Chicago'   │
│    2    │ 'Los Angeles' │
└─────────┴───────────────┘
```

## 进一步阅读

*   [71 个用于平台工程的 Linux 命令](https://medium.com/dev-genius/71-linux-commands-for-platform-engineering-563cf8f16c5?source=your_stories_page-------------------------------------)
*   [8 C 提示&1 分钟内的招数](https://medium.com/towardsdev/8-c-tips-tricks-in-1-minute-f433f7882467?source=your_stories_page-------------------------------------)
*   [30 秒内 10 次 SQL 攻击](https://medium.com/@caopengau/10-sql-hacks-in-30-seconds-bc2be9b6f4d9?source=your_stories_page-------------------------------------)
*   [5 分钟 10 个设计图案](https://medium.com/gitconnected/10-design-patterns-in-5-minutes-a8643b1e1b1?source=your_stories_page-------------------------------------)
*   [30 秒内 10 次科特林攻击](https://medium.com/@caopengau/10-kotlin-hacks-in-30-seconds-f5fd5a81224f?source=your_stories_page-------------------------------------)
*   [30 秒内 10 次 Java 破解](https://medium.com/@caopengau/10-java-hacks-in-30-seconds-c3ba2771f381?source=your_stories_page-------------------------------------)

如果你觉得这个指南有帮助，请鼓掌并跟我来。通过[链接](https://medium.com/@caopengau/membership)加入 medium，在 medium 上访问我和所有其他优秀作家的所有优质文章。