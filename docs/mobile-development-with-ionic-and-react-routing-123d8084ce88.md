# 使用 Ionic 和 React-Routing 进行移动开发

> 原文：<https://levelup.gitconnected.com/mobile-development-with-ionic-and-react-routing-123d8084ce88>

![](img/fac007705aea6d8913e08318c8a02768.png)

贾斯汀·劳伦斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

如果我们知道如何创建 React web 应用程序，但希望开发移动应用程序，我们可以使用 Ionic 框架。在本文中，我们将看看如何使用带有 React 的 Ionic 框架开始移动开发。

# 条件路由

我们可以让路由组件有条件地呈现。

为此，我们将`render`道具添加到我们的路线中:

```
import React from 'react';
import { Redirect, Route } from 'react-router-dom';
import {
  IonApp,
  IonIcon,
  IonLabel,
  IonRouterOutlet,
  IonTabBar,
  IonTabButton,
  IonTabs
} from '@ionic/react';
import { IonReactRouter } from '@ionic/react-router';
import { triangle } from 'ionicons/icons';
import Tab1 from './pages/Tab1';
import Bar from './pages/Bar';
import Foo from './pages/Foo';/* Core CSS required for Ionic components to work properly */
import '@ionic/react/css/core.css';/* Basic CSS for apps built with Ionic */
import '@ionic/react/css/normalize.css';
import '@ionic/react/css/structure.css';
import '@ionic/react/css/typography.css';const isAuth = true;const App: React.FC = () => (
  <IonApp>
    <IonReactRouter>
      <IonTabs>
        <IonRouterOutlet>
          <Route path="/tab1" component={Tab1} exact={true} />
          <Route
            exact
            path="/random"
            render={props => {
              return isAuth ? <Foo /> : <Bar />;
            }}
          />
          <Route path="/" render={() => <Redirect to="/tab1" />} exact={true} />
        </IonRouterOutlet>
        <IonTabBar slot="bottom">
          <IonTabButton tab="tab1" href="/tab1">
            <IonIcon icon={triangle} />
            <IonLabel>Tab 1</IonLabel>
          </IonTabButton>
          <IonTabButton tab="random" href="/random">
            <IonIcon icon={triangle} />
            <IonLabel>Random</IonLabel>
          </IonTabButton>
        </IonTabBar>
      </IonTabs>
    </IonReactRouter>
  </IonApp>
);export default App;
```

`render` prop 采用一个带有`props`的函数作为参数，我们可以在函数中返回我们想要呈现的组件。

# 嵌套路由

我们可以用离子反应定义嵌套路径。

为此，我们写道:

`App.tsx`

```
import React from 'react';
import { Redirect, Route } from 'react-router-dom';
import {
  IonApp,
  IonIcon,
  IonItem,
  IonLabel,
  IonRouterOutlet,
  IonTabBar,
  IonTabButton,
  IonTabs
} from '@ionic/react';
import { IonReactRouter } from '@ionic/react-router';
import { triangle } from 'ionicons/icons';
import Tab1 from './pages/Tab1';
import DashboardPage from './pages/DashboardPage';/* Core CSS required for Ionic components to work properly */
import '@ionic/react/css/core.css';/* Basic CSS for apps built with Ionic */
import '@ionic/react/css/normalize.css';
import '@ionic/react/css/structure.css';
import '@ionic/react/css/typography.css';const App: React.FC = () => (
  <IonApp>
    <IonReactRouter>
      <IonTabs>
        <IonRouterOutlet>
          <Route path="/tab1" component={Tab1} exact={true} />
          <Route path="/dashboard" component={DashboardPage} />
          <Route path="/" render={() => <Redirect to="/tab1" />} exact={true} />
        </IonRouterOutlet>
        <IonTabBar slot="bottom">
          <IonTabButton tab="tab1" href="/tab1">
            <IonIcon icon={triangle} />
            <IonLabel>Tab 1</IonLabel>
          </IonTabButton>
        </IonTabBar>
      </IonTabs>
    </IonReactRouter>
  </IonApp>
);export default App;
```

`pages/DashboardPage.tsx`

```
import { IonRouterOutlet } from '@ionic/react';
import React from 'react';
import { Route, RouteComponentProps } from 'react-router';
import Bar from './Bar';
import Foo from './Foo';const DashboardPage: React.FC<RouteComponentProps> = ({ match }) => {
  return (
    <IonRouterOutlet>
      <Route exact path={match.url} component={Bar} />
      <Route path={`${match.url}/foo`} component={Foo} />
    </IonRouterOutlet>
  );
};export default DashboardPage;
```

`pages/Foo.tsx`

```
import React from 'react';
import { IonHeader, IonPage, IonTitle, IonToolbar } from '@ionic/react';const Foo: React.FC = () => {
  return (
    <IonPage>
      <IonHeader>
        <IonToolbar>
          <IonTitle>Foo</IonTitle>
        </IonToolbar>
      </IonHeader>
    </IonPage>
  );
};export default Foo;
```

我们添加了:

```
<Route path="/dashboard" component={DashboardPage} />
```

当我们转到`/dashboard`时加载`DashboardPage`组件。

在`DashboardPage`中，我们有更多的路线。

我们有:

```
<Route path={`${match.url}/foo`} component={Foo} />
```

因此，当我们转到`/dashboard/foo`时，我们会看到`Foo`组件。

# 结论

我们可以用离子反应增加各种途径。