# Javascript 初学者完全指南—第 2 部分(DOM 和 Web)

> 原文：<https://levelup.gitconnected.com/a-complete-beginners-guide-to-javascript-part-2-501ec89af76c>

![](img/a7911a0d954c49151da381f6cb814d77.png)

这是两个故事中的第二个，在这里你将学到开始使用 Javascript 编程所需要知道的一切。

就像第一部分中的}

如果您运行代码，文本将全部为大写和红色。我们可以使用`style`属性来改变我们想要的任何 CSS 值。

最后，我们还可以在元素上设置属性。例如，假设我们想把类名都改成相同的。

```
let vehiclesList = document.getElementById('vehiclesList');
let vehicles = document.getElementsByTagName('li');for (vehicle of vehicles) {
  vehicle.innerHTML = vehicle.innerHTML.toUpperCase();
  vehicle.style.color = "#FF0000";
  vehicle.setAttribute('class', 'vehicle');
}
```

为了测试这是否可行，让我们添加下面的代码，将所有元素的类`vehicle`更改为蓝色。

```
let vehiclesList = document.getElementById('vehiclesList');
let vehicles = document.getElementsByTagName('li');for (vehicle of vehicles) {
  vehicle.innerHTML = vehicle.innerHTML.toUpperCase();
  vehicle.style.color = "#FF0000";
  vehicle.setAttribute('class', 'vehicle');
}let updatedVehicles = document.getElementsByClassName('vehicle');for (vehicle of updatedVehicles) {
  vehicle.style.color = "#0000FF";
}
```

太好了，我们所有的内容现在都是蓝色的。我们现在对如何操作 HTML 元素有了基本的了解。现在让我们更进一步，创建一些元素。

# 创建和删除元素

让我们创建另一个列表，但这次我们将显示颜色而不是车辆。为了创建元素，我们使用了`createElement()`方法。

```
let vehiclesList = document.getElementById('vehiclesList');
let vehicles = document.getElementsByTagName('li');for (vehicle of vehicles) {
  vehicle.innerHTML = vehicle.innerHTML.toUpperCase();
  vehicle.style.color = "#FF0000";
  vehicle.setAttribute('class', 'vehicle');
}let coloursList = document.createElement('ul');
```

这里我们已经创建了无序列表，但是它现在对我们没有多大用处，因为它没有内容，所以让我们创建一些列表项。

```
let vehiclesList = document.getElementById('vehiclesList');
let vehicles = document.getElementsByTagName('li');for (vehicle of vehicles) {
  vehicle.innerHTML = vehicle.innerHTML.toUpperCase();
  vehicle.style.color = "#FF0000";
  vehicle.setAttribute('class', 'vehicle');
}let coloursList = document.createElement('ul');let redItem = document.createElement('li');
let greenItem = document.createElement('li');
let blueItem = document.createElement('li');
```

太好了，我们现在有一些列表项目了。不过，这些对我们来说没什么用，因为它们没有内容，也不在我们的无序列表中。让我们给列表项添加一些内容。

```
let vehiclesList = document.getElementById('vehiclesList');
let vehicles = document.getElementsByTagName('li');for (vehicle of vehicles) {
  vehicle.innerHTML = vehicle.innerHTML.toUpperCase();
  vehicle.style.color = "#FF0000";
  vehicle.setAttribute('class', 'vehicle');
}let coloursList = document.createElement('ul');let redItem = document.createElement('li');
let greenItem = document.createElement('li');
let blueItem = document.createElement('li');redItem.innerHTML = "Red";
greenItem.innerHTML = "Green";
blueItem.innerHTML = "Blue";
```

现在我们已经添加了内容，我们需要将列表项添加到无序列表中。为了将一个元素添加到另一个元素中，我们使用了`appendChild()`方法。

```
let vehiclesList = document.getElementById('vehiclesList');
let vehicles = document.getElementsByTagName('li');for (vehicle of vehicles) {
  vehicle.innerHTML = vehicle.innerHTML.toUpperCase();
  vehicle.style.color = "#FF0000";
  vehicle.setAttribute('class', 'vehicle');
}let coloursList = document.createElement('ul');let redItem = document.createElement('li');
let greenItem = document.createElement('li');
let blueItem = document.createElement('li');redItem.innerHTML = "Red";
greenItem.innerHTML = "Green";
blueItem.innerHTML = "Blue";coloursList.appendChild(redItem);
coloursList.appendChild(greenItem);
coloursList.appendChild(blueItem);
```

太棒了，我们现在有一个完整的列表，但为什么我们看不到它？我们的列表也需要添加到一个元素中，在我们的例子中是`<body>`元素。

```
let vehiclesList = document.getElementById('vehiclesList');
let vehicles = document.getElementsByTagName('li');for (vehicle of vehicles) {
  vehicle.innerHTML = vehicle.innerHTML.toUpperCase();
  vehicle.style.color = "#FF0000";
  vehicle.setAttribute('class', 'vehicle');
}let coloursList = document.createElement('ul');let redItem = document.createElement('li');
let greenItem = document.createElement('li');
let blueItem = document.createElement('li');redItem.innerHTML = "Red";
greenItem.innerHTML = "Green";
blueItem.innerHTML = "Blue";coloursList.appendChild(redItem);
coloursList.appendChild(greenItem);
coloursList.appendChild(blueItem);document.getElementsByTagName('body')[0].appendChild(coloursList);
```

这里我们已经将我们的`coloursList`添加到了第一个`<body>`标签的实例中。如果我们运行代码，那么我们应该看到我们的车辆列表和颜色列表。

也许不是两个列表都有，我们想用颜色列表替换我们的车辆列表。要替换元素，我们可以使用`replaceChild(new, old)`方法。

```
let vehiclesList = document.getElementById('vehiclesList');
let vehicles = document.getElementsByTagName('li');for (vehicle of vehicles) {
  vehicle.innerHTML = vehicle.innerHTML.toUpperCase();
  vehicle.style.color = "#FF0000";
  vehicle.setAttribute('class', 'vehicle');
}let coloursList = document.createElement('ul');let redItem = document.createElement('li');
let greenItem = document.createElement('li');
let blueItem = document.createElement('li');redItem.innerHTML = "Red";
greenItem.innerHTML = "Green";
blueItem.innerHTML = "Blue";coloursList.appendChild(redItem);
coloursList.appendChild(greenItem);
coloursList.appendChild(blueItem);//document.getElementsByTagName('body')[0].appendChild(coloursList);
document.getElementsByTagName('body')[0].replaceChild(coloursList, vehiclesList);
```

这里我们用`replaceChild()`方法替换了`appendChild()`方法，如果我们运行这段代码，你将只能看到我们的颜色列表。

很好，我们现在可以在 DOM 中创建和替换元素了。现在让我们来看看我们是如何删除元素的，为此我们使用了`removeChild()`方法。让我们从颜色列表中删除`blueItem`。

```
let vehiclesList = document.getElementById('vehiclesList');
let vehicles = document.getElementsByTagName('li');for (vehicle of vehicles) {
  vehicle.innerHTML = vehicle.innerHTML.toUpperCase();
  vehicle.style.color = "#FF0000";
  vehicle.setAttribute('class', 'vehicle');
}let coloursList = document.createElement('ul');let redItem = document.createElement('li');
let greenItem = document.createElement('li');
let blueItem = document.createElement('li');redItem.innerHTML = "Red";
greenItem.innerHTML = "Green";
blueItem.innerHTML = "Blue";coloursList.appendChild(redItem);
coloursList.appendChild(greenItem);
coloursList.appendChild(blueItem);document.getElementsByTagName('body')[0].replaceChild(coloursList, vehiclesList);coloursList.removeChild(blueItem);
```

现在，如果我们运行这段代码，我们的颜色列表将只包含红色和绿色项目。

好了，我们现在对如何操作 DOM 有了很好的理解。我们在这里只讨论了基础知识，你还可以用 DOM 做更多的事情，还有更多的方法和属性可供我们使用，太多了，一个故事讲不完。既然您可以从 DOM 中更新、创建、添加、替换和删除元素，我建议尝试所有可用的方法和属性，看看哪些有效，哪些无效。我相信实验是深入理解 DOM 如何工作以及我们可以用它做什么的最好方法。

好了，让我们来看看 Ajax。

# 创建交互式、快速动态网页应用的网页开发技术

首先，什么是 AJAX？AJAX 是 Javascript 最伟大的补充之一，创建于 1999 年(Javascript 创建 4 年后)。AJAX 允许我们发出异步网络请求。换句话说，我们可以在页面加载后从服务器请求数据，并更新我们的 web 页面，而不必重新加载页面。我们还可以在后台向服务器发送数据，而无需重新加载页面。

对于 AJAX，我们使用了`XMLHttpRequest`对象，让我们看看它是如何工作的。我们需要做的第一件事是创建对象的新实例。

```
let xhr = new XMLHttpRequest();
```

一旦我们创建了对象的实例，我们就可以打开一个连接，为此，我们使用了`open`方法。`open`方法有五个参数。这些是方法、url、异步、用户和密码。对于这个故事，我们将只使用前三个参数。

```
let xhr = new XMLHttpRequest();xhr.open("GET", "[https://5cd182d9d4a78300147bec19.mockapi.io/api/users](https://5cd182d9d4a78300147bec19.mockapi.io/api/users)", true);
```

这里我们使用的是`GET`方法，它只是用来从服务器中检索数据。这个 URL 是我为这个故事设置的一个模拟 API，但是它可以是您想要的任何 API URL。最后，我们将 async 参数设置为`true`,以便异步执行请求。

接下来，我们将使用`onreadystatechange`参数，这允许我们检查请求的就绪状态，换句话说就是请求的当前阶段。有五种就绪状态:

```
// This is from the official documentation0 - UNSENT - Client has been created. open() not called yet.1 - OPENED - open() has been called.2 - HEADERS_RECEIVED - send() has been called, and headers and status are available3 - LOADING - Downloading; responseText holds partial data.4 - DONE - The operation is complete.
```

我们将为`onreadystatechange`属性设置一个函数，这是我们能够检查状态变化的方式。我们还将通过检查就绪状态 4 来查看请求是否完成。

```
let xhr = new XMLHttpRequest();xhr.open("GET", "[https://5cd182d9d4a78300147bec19.mockapi.io/api/users](https://5cd182d9d4a78300147bec19.mockapi.io/api/users)", true);xhr.onreadystatechange = () => {if (xhr.readyState === 4) {// Our request is done}}
```

对于这个例子，我们不会检查任何其他就绪状态，因为我们只需要知道请求何时完成。现在我们知道请求已经完成，让我们检查请求的 HTTP 状态。检查 HTTP 状态允许我们确定请求是否成功。

```
let xhr = new XMLHttpRequest();xhr.open("GET", "[https://5cd182d9d4a78300147bec19.mockapi.io/api/users](https://5cd182d9d4a78300147bec19.mockapi.io/api/users)", true);xhr.onreadystatechange = () => {if (xhr.readyState === 4) {if (xhr.status === 200) {// The request was successful} else {// There was an error}}}
```

在这里，我们检查状态是否为`200`，这意味着一切正常。有相当多的 HTTP 状态代码可以检查，这里有一个完整的列表。好了，现在我们知道了请求完成的时间，以及它是否成功，现在让我们得到响应，如果有错误，也显示一个错误。

```
let xhr = new XMLHttpRequest();// Right let's change our endpoint back to users, and have a look at how we access the response.
xhr.open("GET", "[https://5cd182d9d4a78300147bec19.mockapi.io/api/users](https://5cd182d9d4a78300147bec19.mockapi.io/api/users)", true);xhr.onreadystatechange = () => {if (xhr.readyState === 4) {if (xhr.status === 200) {let response = xhr.responseText;
      response = JSON.parse(response);response.forEach((item) => {
        console.log(item);
      });} else {console.log(`Error with request: ${xhr.status}`);
      console.log(xhr.responseText);}}}
```

我们使用`responseText`来获取请求的响应值，在我们的例子中，响应是在 JSON 中，所以我们也在记录之前解析这些数据。如果有错误，那么我们记录 HTTP 状态和 responseText。

只剩下一件事要做，我们现在需要发送请求。为了发送请求，我们使用了`send()`方法。

```
let xhr = new XMLHttpRequest();// Right let's change our endpoint back to users, and have a look at how we access the response.
xhr.open("GET", "[https://5cd182d9d4a78300147bec19.mockapi.io/api/users](https://5cd182d9d4a78300147bec19.mockapi.io/api/users)", true);xhr.onreadystatechange = () => {if (xhr.readyState === 4) {if (xhr.status === 200) {let response = xhr.responseText;
      response = JSON.parse(response);response.forEach((item) => {
        console.log(item);
      });} else {console.log(`Error with request: ${xhr.status}`);
      console.log(xhr.responseText);}}}xhr.send();
```

如果运行这段代码，应该会看到多个日志显示模拟用户的详细信息。使用 Ajax 从服务器获取数据就是这么简单，现在让我们修改代码，将数据发送到服务器。

```
let xhr = new XMLHttpRequest();// Right let's change our endpoint back to users, and have a look at how we access the response.
xhr.open("POST", "[https://5cd182d9d4a78300147bec19.mockapi.io/api/users](https://5cd182d9d4a78300147bec19.mockapi.io/api/users)", true);xhr.onreadystatechange = () => {if (xhr.readyState === 4) {if (xhr.status === 200 || xhr.status === 201) {let response = xhr.responseText;
      response = JSON.parse(response);console.log(response);} else {console.log(`Error with request: ${xhr.status}`);
      console.log(xhr.responseText);}}}xhr.setRequestHeader("Content-Type", "application/json");let data = {
  name: "John Smith",
  email: "johnsmith@johnsmith.com"
};xhr.send(JSON.stringify(data));
```

首先，我们将`open()`方法的方法参数改为`"POST"`。接下来，我们将 HTTP status `201`添加到 if 语句中，检查请求是否成功。HTTP 状态代码`201`意味着已经成功创建了一个资源，这在我们的例子中意味着已经创建了一个新用户。在更新状态检查之后，我们移除了`forEach`循环，只记录响应，这是因为我知道响应将是单个用户，即我们正在创建的用户。接下来，我们使用`setRequestHeader()`方法让服务器知道我们正在发送 JSON。设置头部后，我们创建一些要发送的数据，然后将数据作为 JSON 传递给`send()`方法。

现在，如果我们运行这段代码，一切都将正常工作，我们将看到一个包含我们创建的新用户的响应。

# 结论

操纵 DOM 和使用 Ajax 处理网络请求就是这么简单。现在，您已经了解了开始使用 Javascript 开发网站和 web 应用程序所需的一切。随着您更多地使用 Javascript，您将对它的工作原理以及您可以用它做什么有更深的理解。就像我经常说的，现在最好的办法是开始尝试。也许试着建立一个待办事项列表或者一个天气应用程序，只要你在建立一些东西并推动你的知识向前发展，你建立什么并不重要。