# Firebase:什么是原子性&如何在云 Firestore 中精确地增加/减少值

> 原文：<https://levelup.gitconnected.com/firebase-what-is-atomicity-how-to-accurately-increment-decrement-values-in-cloud-firestore-bfe6c6ed5eb7>

![](img/584ff64003ae42b04acd6b47558c9de4.png)

**备注:**

*   本指南假设你知道一些 **React Native，Redux，和**[**Redux Saga**](/react-native-redux-implementing-redux-saga-for-an-asynchronous-flow-90a0e9d7d8e8)**。**
*   **Github 回购:**[https://github.com/jefelewis/firebase-atomicity-demo](https://github.com/jefelewis/firebase-atomicity-demo)

# 1.什么是原子性？

在计算机科学中， [ACID](https://en.wikipedia.org/wiki/ACID) (原子性、一致性、隔离性、持久性)是数据库事务的一组属性，旨在即使在出现错误、连接问题、电源故障等情况下也能保证有效性。

一个**原子事务**是唯一的，使得所有的操作成功发生或者整个原子事务失败。

# 2.为什么我们使用原子性？

## A.数据准确性

因为如果所有操作都成功，原子事务会完全失败，所以原子性降低了从部分完成的*、**、**更新数据库的风险，这可能会导致应用程序扩展时出现数据问题。*

例如，假设您正在创建一个音乐会门票预订应用程序，一个用户购买了一张门票。如果应用程序必须购买机票，但未能预订座位，则机票购买不应通过。要么购票和座位都被预订，要么什么都不会发生。

如果您有一群用户购买机票，但由于流程的这一部分失败而没有预订座位，会发生什么情况？许多愤怒的顾客没有预订音乐会的座位。

## B.数据并发

原子性还有利于解决数据并发问题。数据并发意味着多个用户可以同时读写数据。

例如，团队中的所有用户都更新了共享计数。如果多个用户对同一个字段值读写数据，我们希望该值是准确的。如果两个用户同时更新计数，或者一个用户在更新计数时出现连接问题，该怎么办？原子性解决了这些问题。

# 3.何时使用原子性及其防止的问题

*   银行交易(账户提款和账户存款都发生或什么都不发生)
*   计数器(用户 A +用户 B 同时更新一个计数器，这可能导致数据并发问题)
*   喜欢(用户 A +用户 B 同时更新喜欢，这可能导致数据并发问题)
*   审核计数(用户 A +用户 B 同时更新审核计数，这可能会导致数据并发问题)
*   预订航班(收到付款和预订座位都发生或不发生)
*   预订音乐会门票(收到付款和预订座位都发生或不发生)

# 4.我们如何使用原子性+ Firebase 云 Firestore？

在[2019 年 3 月](https://firebase.googleblog.com/2019/03/increment-server-side-cloud-firestore.html)，Firebase 引入了[**field value . increment()**](https://firebase.google.com/docs/reference/js/firebase.firestore.FieldValue#static-increment)**对 Firebase 云 Firestore 中的值进行原子级递增和递减。**

## **A.应用概述**

****Github 回购:**[https://github.com/jefelewis/firebase-atomicity-demo](https://github.com/jefelewis/firebase-atomicity-demo)**

**这个演示应用程序将使用[Redux](https://redux.js.org)+[Redux Saga Firebase](https://redux-saga-firebase.js.org)。我们将使用 [Redux Saga](/react-native-redux-implementing-redux-saga-for-an-asynchronous-flow-90a0e9d7d8e8) 对 Firebase Cloud Firestore 进行异步调用，以增加和减少 Firebase Cloud Firestore 中存储的值。使用 Firebase 的`FieldValue.increment()`将允许我们自动增加和减少数据库中的值。**

**`FieldValue.increment()`接受一个数字作为参数，所以因为我们只是要增加和减少 1，我们将传递它如下:**

*   ****递增 1:****
*   ****递减 1:****

## **B.App 截图**

**![](img/6da4c0726d7a0100cc05a45713ab1356.png)**

**Counter.js**

## **C.应用程序文件结构**

**本例将使用 8 个文件:**

1.  **firebase.js (Firebase 配置+原子递增/递减)**
2.  **App.js (React 原生应用)**
3.  **Counter.js(计数器屏幕)**
4.  **store.js (Redux 商店)**
5.  **index.js (Redux Root Reducer)**
6.  **counterReducer.js (Redux 计数器 Reducer)**
7.  **index.js (Redux 根传奇)**
8.  **countersaga . js(Redux Counter Saga)**

## **D.应用程序文件**

****firebase.js****

```
*// Imports: Dependencies* import firebase from 'firebase';
import '@firebase/firestore';
import ReduxSagaFirebase from 'redux-saga-firebase';*// Imports: Firebase Config* import firebaseConfig from '../config/config.js';*// Firebase: Initialize* const firebaseApp = firebase.initializeApp({
  apiKey: firebaseConfig.apiKey,
  authDomain: firebaseConfig.authDomain,
  databaseURL: firebaseConfig.databaseURL,
  projectId: firebaseConfig.projectId,
  storageBucket: firebaseConfig.storageBucket,
  messagingSenderId: firebaseConfig.messagingSenderId,
});*// Redux Saga Firebase: Initialize* const reduxSagaFirebase = new ReduxSagaFirebase(firebaseApp);*// Increment/Decrement* **export const atomicIncrement = firebase.firestore.FieldValue.increment(1);
export const atomicDecrement = firebase.firestore.FieldValue.increment(-1);***// Exports* export default reduxSagaFirebase;
```

****App.js****

```
*// Imports: Dependencies* import React from 'react';
import { Provider } from 'react-redux';*// Imports: Screens* import Counter from './screens/Counter';*// Imports: Redux Store* import { store } from './redux/store';*// React Native App* export default function App() {
  return (
    *// Redux: Global Store* <Provider store={store}>
      <Counter />
    </Provider>
  );
}
```

****Counter.js****

```
*// Imports: Dependencies* import React, { Component } from 'react';
import { Button, Dimensions, SafeAreaView, StyleSheet, Text, TouchableOpacity, View } from 'react-native';
import { connect } from 'react-redux';*// Imports: Redux Actions* **import { increaseCounter, decreaseCounter } from '../redux/actions/counterActions';***// Screen Dimensions* const { height, width } = Dimensions.get('window');*// Screen: Counter* class Counter extends React.Component {
  render() {
    return (
      <SafeAreaView style={styles.container}>
        <Text style={styles.counterTitle}>Counter</Text>        <View style={styles.counterContainer}>
 **<TouchableOpacity onPress={this.props.reduxIncreaseCounter}>
            <Text style={styles.buttonText}>+</Text
          </TouchableOpacity>** **<TouchableOpacity onPress={this.props.reduxDecreaseCounter}>
            <Text style={styles.buttonText}>-</Text
          </TouchableOpacity>**
        </View>
      </SafeAreaView>
    )
  }
}*// Styles* const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
  },
  counterContainer: {
    display: 'flex',
    flexDirection: 'row',
    justifyContent: 'center',
    alignItems: 'center',
  },
  counterTitle: {
    fontFamily: 'System',
    fontSize: 32,
    fontWeight: '700',
    color: '#000',
  },
  buttonText: {
    fontFamily: 'System',
    fontSize: 50,
    fontWeight: '300',
    color: '#007AFF',
    marginLeft: 40,
    marginRight: 40,
  },
});*// Map Dispatch To Props (Dispatch Actions To Reducers. Reducers Then Modify The Data And Assign It To Your Props)* const mapDispatchToProps = (dispatch) => {
  return {
    *// Increase Counter
***reduxIncreaseCounter: () => dispatch(increaseCounter()),
**    *// Decrease Counter
***reduxDecreaseCounter: () => dispatch(decreaseCounter()),
**  };
};*// Exports* export default connect(null, mapDispatchToProps)(Counter);
```

****store.js****

```
*// Imports: Dependencies* import { createStore, applyMiddleware } from 'redux';
import { createLogger } from 'redux-logger';
import createSagaMiddleware from 'redux-saga';*// Imports: Redux Root Reducer* import rootReducer from '../reducers/index';*// Imports: Redux Root Saga* import { rootSaga } from '../sagas/index';*// Middleware: Redux Saga* const sagaMiddleware = createSagaMiddleware();*// Redux: Store* const store = createStore(
  rootReducer,
  applyMiddleware(
sagaMiddleware**,
**    createLogger(),
  ),
);*// Middleware: Redux Saga* sagaMiddleware.run(rootSaga);*// Exports* export {
  store,
}
```

****index.js(根减速器)****

```
*// Imports: Dependencies* import { combineReducers } from 'redux';*// Imports: Reducers* import counterReducer from './counterReducer';*// Redux: Root Reducer* const rootReducer = combineReducers({
  counter: counterReducer,
});*// Exports* export default rootReducer;
```

****counterReducer.js****

```
*// Initial state* const initialState = {
  loading: false,
};*// Document Reducer* export default function documentReducer (state = initialState, action) {
  switch (action.type) {
    *// Increase Counter* case 'INCREASE_COUNTER':
      return {
        ...state,
        loading: true,
      } *// Decrease Counter* case 'DECREASE_COUNTER':
      return {
        ...state,
        loading: false,
      } *// Default* default:
      return state;
  }
}
```

****index.js (Root Saga)****

```
*// Imports: Dependencies* import { all, fork} from 'redux-saga/effects';*// Imports: Redux Sagas* **import { watchIncreaseCounter, watchDecreaseCounter } from './counterSaga';***// Redux Saga: Root Saga* export function* rootSaga () {
  yield all([ **fork(watchIncreaseCounter),
    fork(watchDecreaseCounter),**  ]);
};
```

****counterSaga.js****

```
*// Imports: Dependencies* import { call, takeEvery } from 'redux-saga/effects';*// Imports: Firebase + Atomic Incrementers* import reduxSagaFirebase from '../../firebase/firebase';
**import { atomicIncrement, atomicDecrement } from '../../firebase/firebase';***// Redux Saga: Increase Counter* **function* increaseCounter() {
**  try {
 ***// Update Data: Increment Counter By 1* yield call(reduxSagaFirebase.firestore.updateDocument, 'counter/counter', {
      counter: atomicIncrement,
    });**
  }
  catch (error) {
    console.log(error);
  }
**};***// Redux Saga: Decrease Counter* **function* decreaseCounter() {
**  try {
    *// Update Data: Decrement Counter By 1
***yield call(reduxSagaFirebase.firestore.updateDocument, 'counter/counter', {
      counter: atomicDecrement,
    });**
  }
  catch (error) {
    console.log(error);
  }
**};***// Watcher: Increase Counter* **export function* watchIncreaseCounter() {
**  *// Take Every Action
***yield takeEvery('INCREASE_COUNTER', increaseCounter);
};***// Watcher: Decrease Counter* **export function* watchDecreaseCounter() {
**  *// Take Every Action
***yield takeEvery('DECREASE_COUNTER', decreaseCounter);
};**
```

# **结论**

**就是这样！该值现在在 Firebase Cloud Firestore 中自动递增/递减。**

**没有人是完美的。如果您发现了任何错误，想要提出改进建议，或者扩展某个主题，请随时给我发消息。我一定会包括任何改进或纠正任何问题。**