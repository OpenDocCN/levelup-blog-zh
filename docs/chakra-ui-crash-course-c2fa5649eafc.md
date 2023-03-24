# 查克拉-UI 速成班

> 原文：<https://levelup.gitconnected.com/chakra-ui-crash-course-c2fa5649eafc>

在这篇博客中，我将教你如何开始使用 Chakra-UI。

![](img/296ce28e55d1cbf82a18c07d2331a627.png)

# 视频教程

# 什么是 Chakra-UI？

Chakra-UI 是一个 React UI 库，它有大量的预先设计好的组件和工具，你可以在网站上使用。

# 装置

*   我会用 Nextjs。

```
yarn create next-app <my-app>
```

*   安装包:

```
cd <my-app>
yarn add @chakra-ui/react @emotion/react@^11 @emotion/styled@^11 framer-motion@^6
```

# 用下一步设置脉轮界面

*   用`ChakraProvider`组件包裹`Component`组件。

```
import { ChakraProvider } from '@chakra-ui/react'

function MyApp({ Component, pageProps }) {
    return (
        <ChakraProvider>
            <Component {...pageProps} />
        </ChakraProvider>
    )
}

export default MyApp
```

现在我们可以使用 chakra-UI 组件。

# 如何导入组件

总是从`@chakra-ui/react`包中导入组件和实用程序作为命名导入。

```
import { Button, Text, Heading, Box, Link, useTheme } from '@chakra-ui/react'

const Index = () => {
    return <Heading>Heading 1</Heading>
}

export default Index
```

![](img/110d23c459bf92c03af30685e4a09d9b.png)

# 自定义样式

有两种方法可以自定义样式。

*   样式道具:有了样式道具，你几乎可以使用任何 CSS 属性作为道具。

```
const Index = () => {
    return (
        <Heading color='red' fontSize='5rem'>
            Heading 1
        </Heading>
    )
}
```

*   SX 道具:使用 sx 道具，你可以使用任何自定义样式作为对象。

```
const Index = () => {
    return (
        <Heading
            sx={{
                color: 'red',
                fontSize: '5rem',
            }}
        >
            Heading 1
        </Heading>
    )
}
```

![](img/1ba775bcb131e7af17826d7ae54af107.png)

# 更改颜色模式

我们可以使用`useColorMode`挂钩改变颜色模式。

```
import React from 'react'

import { IconButton, useColorMode } from '@chakra-ui/react'

import { MoonIcon, SunIcon } from '@chakra-ui/icons'

const ToggleMode = () => {
    const { colorMode, toggleColorMode } = useColorMode()
    return (
        <IconButton
            icon={colorMode === 'dark' ? <SunIcon /> : <MoonIcon />}
            onClick={toggleColorMode}
        />
    )
}

export default ToggleMode
```

# 灯光模式

![](img/419e3bf707995bcf7010592f2a5e0a05.png)

# 黑暗模式

![](img/087f00d14c0122f9296ca2d5ae20ec1d.png)

要了解更多信息，请观看视频教程。