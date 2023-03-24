# 您应该尝试的 15 个 Python 包

> 原文：<https://levelup.gitconnected.com/15-python-packages-that-you-should-try-b8a3468589ad>

## 可能会让你吃惊的有用的 python 包列表

![](img/e1d6f04099b1785aa2074c281fc8cf45.png)

布鲁斯·马斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

这个包是一个模块的集合(即带有。py 扩展)，它提供了在 Python 程序中使用的附加功能。包可以包含模块、子包和其他资源，如数据文件。

这些包允许我们从其他人那里导入和使用代码。这可以节省我们的时间和精力，不必重新发明轮子，还可以帮助我们更快地构建更复杂的项目。

在本文中，我们将浏览 Python 中一些非常有用的包，作为程序员，我们可以在我们的代码中使用它们。我们开始吧！

## 1.NumPy

NumPy (Numerical Python)是一个使用 Python 进行科学计算的包，提供了对大型多维数组和数字数据矩阵的支持，以及对这些数据执行数学运算的函数。

**举例**:

```
import numpy as np

# Create a 1D array with values from 0 to 9
a = np.arange(10)
print(a)  # Output: [0 1 2 3 4 5 6 7 8 9]

# Create a 2D array with random values
b = np.random.rand(3, 3)
print(b)  

# Output: [[0.89686138 0.84725174 0.39576519]
             #          [0.97909542 0.89482735 0.06554892]
             #          [0.97260403 0.77387906 0.12892679]]

# Perform matrix multiplication
c = np.dot(a, b)
print(c) 
```

## 2.Pytest

Pytest 是 Python 的一个测试框架，它使编写和运行代码测试变得很容易。它有一个简单灵活的 API，可以用来测试从小代码单元到整个应用程序的所有东西。

**举例**:

```
# Define a simple function to test
def add(x, y):
    return x + y

# Write a test for the function using Pytest
def test_add():
    assert add(3, 4) == 7
    assert add(-1, 1) == 0
    assert add(0.1, 0.2) == 0.3
```

## **3。请求**

Requests 是一个用 Python 编写 HTTP 请求的库。它简化了与 web APIs 交互的过程，使得发送请求和接收响应变得容易。

**示例**:

```
import requests

# Make a GET request to a website
r = requests.get('https://www.sample.com/')

# Print the status code and content of the response
print(r.status_code)
print(r.text)

# Make a POST request with some data
data = {'key': 'value'}
r = requests.post('https://www.sample.com/', data=data) 
```

## 4.枕头

Pillow 是一个用于在 Python 中处理图像的库。它提供了加载、操作和保存各种格式图像的工具，包括 JPEG、PNG 和 BMP。

**示例**:

```
from PIL import Image

# Open an image file
im = Image.open('image.jpg')

# Resize the image
im = im.resize((200, 200))

# Rotate the image
im = im.rotate(45)

# Save the modified image
im.save('modified_image.jpg')
```

## 5.美味的汤

Beautiful Soup 是一个用于解析和导航 HTML 和 XML 文档的库。它使得从这些文档中提取数据变得容易，并且可以用于 web 抓取和数据挖掘任务。

**示例**:

```
from bs4 import BeautifulSoup

# Parse an HTML document
soup = BeautifulSoup(html_doc, 'html.parser')

# Find all the links in the document
links = soup.find_all('a')

# Print the text and href attributes of each link
for link in links:
    print(link.text, link['href'])

# Find the first element with a certain class
element = soup.find(class_='class_name')
print(element)
```

## **6。阿辛西奥**

Asyncio 是 Python 中异步编程的库。它提供了使用 async/await 语法编写并发代码的工具，可用于提高需要并发执行多个任务的程序的性能。

**示例**:

```
import asyncio

# Define a coroutine that sleeps for a certain amount of time
async def sleep_and_print(delay, message):
    await asyncio.sleep(delay)
    print(message)

# Run the coroutine concurrently with two other tasks
async def main():
    await asyncio.gather(
        sleep_and_print(1, 'task 1'),
```

## 7.PyYAML

PyYAML 是一个用 Python 处理 YAML(另一种标记语言)文档的库。它提供了解析和生成 YAML 的工具，可用于存储和加载配置数据或其他类型的结构化数据。

**例子**:

```
import yaml

# Parse a YAML document
data = yaml.safe_load(yaml_doc)

# Access data in the YAML document
print(data['key'])

# Generate a YAML document from a Python dictionary
yaml_doc = yaml.dump({'key': 'value'})
print(yaml_doc)
```

## 8.瓶

Flask 是 Python 的一个微 web 框架，它使得构建 web 应用程序和 API 变得很容易。它提供了路由、呈现模板和与数据库交互的工具，可以用来构建从简单的网页到复杂的 web 应用程序的任何东西。

**举例**:

```
from flask import Flask, request, render_template

# Create a Flask app
app = Flask(__name__)

# Define a route that displays a template
@app.route('/')
def index():
    return render_template('index.html')

# Define a route that handles form submissions
@app.route('/submit', methods=['POST'])
def submit():
    # Get the form data
    name = request.form['name']
    email = request.form['email']
    message = request.form['message']

    # Do something with the data
    send_email(name, email, message)

    return 'Thanks for your message!'

# Run the app
app.run()
```

## 9.Pygame

Pygame 是一个用 Python 创建游戏的库。它提供了创建图形、处理用户输入和播放声音的工具，可用于构建各种类型的游戏，从简单的街机游戏到更复杂的 3D 游戏。

**举例**:

```
import pygame

# Initialize Pygame
pygame.init()

# Set the screen size
screen = pygame.display.set_mode((640, 480))

# Load an image
image = pygame.image.load('image.png')

# Draw the image on the screen
screen.blit(image, (0, 0))

# Update the display
pygame.display.flip()

# Run the game loop
running = True
while running:
    for event in pygame
```

## 10.皮格莱特

Pyglet 是一个用 Python 创建游戏和其他图形应用程序的库。它提供了创建窗口、渲染图形和处理用户输入的工具，可用于构建需要图形用户界面的游戏和其他应用程序。

**举例**:

```
import pyglet

# Create a Pyglet window
window = pyglet.window.Window()

# Load an image and create a sprite
image = pyglet.image.load('image.png')
sprite = pyglet.sprite.Sprite(image)

# Draw the sprite on the window
@window.event
def on_draw():
    window.clear()
    sprite.draw()

# Run the Pyglet event loop
pyglet.app.run()
```

## 11.海生的

Seaborn 是一个基于 Matplotlib 的数据可视化库，为创建有吸引力和信息丰富的统计图形提供了更高级别的接口。

它对于探索和可视化数据集中的统计关系特别有用。

**举例**:

```
import seaborn as sns

# Load a dataset from the Seaborn library
tips = sns.load_dataset('tips')

# Plot a scatterplot of total bill vs. tip, with each point colored by gender
sns.scatterplot(x='total_bill', y='tip', hue='sex', data=tips)

# Show the plot
plt.show()
```

## 12.XlsxWriter

该库用于创建和写入 Excel 文件，XlsxWriter 可用于生成具有图表、公式和格式的专业外观的电子表格。

**举例**:

```
import xlsxwriter

# Create a new Excel file
workbook = xlsxwriter.Workbook('report.xlsx')

# Add a worksheet
worksheet = workbook.add_worksheet()

# Write some data to the worksheet
worksheet.write('A1', 'Hello, world!')

# Close the workbook
workbook.close()
```

## 13.PyGraphviz

PyGraphviz 是一个用于创建和操作图表的包，可以用来可视化数据结构、算法和其他关系。

**示例**:

```
import pygraphviz as pgv

# Create a new graph
G = pgv.AGraph()

# Add some nodes and edges
G.add_node('A', color='red')
G.add_node('B', color='blue')
G.add_edge('A', 'B')

# Draw the graph
G.draw('graph.png', format='png')
```

## 14.散景

Bokeh 是一个为 web 创建交互式可视化效果的库，它允许您创建图表、绘图和其他图形，这些图形可以嵌入到网站中或显示在 web 应用程序中。

**示例**:

```
from bokeh.plotting import figure, show
from bokeh.io import output_file

# Set up the plot
p = figure(title="Line plot")

# Add some data
x = [1, 2, 3, 4, 5]
y = [1, 4, 9, 16, 25]
p.line(x, y, line_width=2)

# Save the plot to an HTML file
output_file("plot.html")
show(p)
```

## 15.皮袜子

PySocks 是一个使用 Socks 代理的库，允许您在 Python 程序中创建和使用 SOCKS 连接。这对于绕过网络限制或连接到不同位置的服务器非常有用。

**示例**:

```
import socket
import socks

# Use a SOCKS proxy to connect to a remote server
socks.set_default_proxy(socks.SOCKS5, "localhost", 1080)

s = socket.socket()
s.connect(("www.example.com", 80))
```

## 结论

Python 包是有用的，因为使用包可以帮助你作为一个程序员变得更有生产力和效率，并且它们也使你的代码更加模块化和更易于维护。

感谢阅读！

> *在你走之前……*

如果你喜欢这篇文章，并希望**继续关注**更多**精彩的**文章，请考虑使用我的推荐链接[https://pralabhsaxena.medium.com/membership](https://pralabhsaxena.medium.com/membership)成为一名中级会员。