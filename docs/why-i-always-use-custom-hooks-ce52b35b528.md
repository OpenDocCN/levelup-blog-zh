# 为什么我总是使用定制挂钩

> 原文：<https://levelup.gitconnected.com/why-i-always-use-custom-hooks-ce52b35b528>

![](img/215f89736ce714b935a430e53be3259b.png)

照片由 [Clément H](https://unsplash.com/@clemhlrdt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

钩子已经成为现代 React 项目中的一个标准，它提供了一种简单易行的方法来管理基于函数的组件的状态。由于我有更多使用钩子开发的经验，我已经开始用我的组件函数创建一个定制钩子，不管状态有多简单。先说说我为什么一直用自定义钩子，再看一些例子。

React 钩子的文档展示了定制钩子作为封装通用/可重用状态逻辑的一种方式的基本前提。尽管这确实提供了一种清晰的方式来重用状态逻辑，但是由于各种因素，并不是每个组件状态都可以或者应该被重用。不管组件状态的可重用性如何，创建一个自定义挂钩仍然有好处。

# 自定义挂钩和代码可读性

在开发任何规模的组件时，我总是回到定制钩子的最重要的原因之一是因为代码的易读性。状态逻辑与组件视图的分离提供了关注点的清晰分离，并且更容易阅读组件功能。作为开发人员，这种分离也迫使您批判性地考虑哪些状态是您绝对需要的，以及如何处理更新。考虑到这一点，我想分享我的组件编写风格，以及我发现很有帮助的异步安全状态定制钩子。

## 看一个例子

让我们看一个获取和显示天气的示例组件。该组件将使用来自[的开放天气地图](https://openweathermap.org/current)的 API 调用，从输入的邮政编码中获取当前天气预报。如果不使用定制的钩子，它看起来就像这样。

```
function WeatherDisplay() {
  // define state for the current weather conditions and zip code
  const [zipCode, setZipCode] = useState(null);
  const [currentWeather, setCurrentWeather] = useState(null);

  /**
   * Create the use effect for getting the current weather conditions
   * this will need to fire on changes to the zip code
   */
  useEffect(() => {
    /**
     * make API call to get the current weather for the given zip code
     */
    if (zipCode !== null) {
      var completeEndpoint = getWeatherEndpoint(zipCode);
      fetch(completeEndpoint)
        .then(response => {
          return response.json();
        })
        .then(data => {
          setCurrentWeather(data);
        })
        .then(error => {
          // do something on error
        });
    }
  }, [zipCode]);

  /**
   * create the weather display object based on the current weather state
   * if the currentWeather is null, do not display anything
   */
  var currentWeatherDisplay = null;
  if (currentWeather !== null) {
    var currentTemp = convertKelvinToFahrenheit(currentWeather.main.temp);
    var feelsLike = convertKelvinToFahrenheit(currentWeather.main.feels_like);
    var min = convertKelvinToFahrenheit(currentWeather.main.temp_min);
    var max = convertKelvinToFahrenheit(currentWeather.main.temp_max);

    // determine the icon class here in the future
    var weatherIcon = <div className="icon-sun wetherIcon"></div>;

    currentWeatherDisplay = [
      <span className="weatherHeader">Current weather for {currentWeather.name}</span>,
      <span className="weatherCurrent">
        {currentTemp}° feels like {feelsLike}°
      </span>,
      <span className="weatherRange">
        Min: {min}° Max: {max}°
      </span>,
      <div>{weatherIcon}</div>,
      <div className="weatherDescription">{currentWeather.weather[0].description}</div>
    ];
  }

  return (
    <div>
      <div>
        <span>Weather For Zipcode:</span>
        <input key="zipCodeInput" id="zipCode"></input>
        <input
          type="button"
          value="Get Weather"
          onClick={() => {
            setZipCode(document.getElementById("zipCode").value);
          }}
        ></input>
      </div>

      <div className="weatherCard">{currentWeatherDisplay}</div>
    </div>
  );
}
```

这是一个相当简单的组件，它利用 useState 和 useEffect 来存储和获取 API 中的数据。现在让我们来看看这个组件，它使用了一个自定义钩子，简单地将 useState 和 useEffect 从组件函数中分离出来。

```
function useCurrentWeather(){
    // define state for the current weather conditions and zip code
  const [zipCode, setZipCode] = useState(null);
  const [currentWeather, setCurrentWeather] = useState(null);

  /**
   * Create the use effect for getting the current weather conditions
   * this will need to fire on changes to the zip code
   */
  useEffect(() => {
    /**
     * make API call to get the current weather for the given zip code
     */
    if (zipCode !== null) {
      var completeEndpoint = getWeatherEndpoint(zipCode);
      fetch(completeEndpoint)
        .then(response => {
          return response.json();
        })
        .then(data => {
          setCurrentWeather(data);
        })
        .then(error => {
          // do something on error
        });
    }
  }, [zipCode]);

  return [currentWeather, setZipCode];
}

function WeatherDisplay() {
  // define the useCurrentWeather state object
  const [currentWeather, setZipCode] = useCurrentWeather();

  /**
   * create the weather display object based on the current weather state
   * if the currentWeather is null, do not display anything
   */
  var currentWeatherDisplay = null;
  if (currentWeather !== null) {
    var currentTemp = convertKelvinToFahrenheit(currentWeather.main.temp);
    var feelsLike = convertKelvinToFahrenheit(currentWeather.main.feels_like);
    var min = convertKelvinToFahrenheit(currentWeather.main.temp_min);
    var max = convertKelvinToFahrenheit(currentWeather.main.temp_max);

    // determine the icon class here in the future
    var weatherIcon = <div className="icon-sun wetherIcon"></div>;

    currentWeatherDisplay = [
      <span className="weatherHeader">Current weather for {currentWeather.name}</span>,
      <span className="weatherCurrent">
        {currentTemp}° feels like {feelsLike}°
      </span>,
      <span className="weatherRange">
        Min: {min}° Max: {max}°
      </span>,
      <div>{weatherIcon}</div>,
      <div className="weatherDescription">{currentWeather.weather[0].description}</div>
    ];
  }

  return (
    <div>
      <div>
        <span>Weather For Zipcode:</span>
        <input key="zipCodeInput" id="zipCode"></input>
        <input
          type="button"
          value="Get Weather"
          onClick={() => {
            setZipCode(document.getElementById("zipCode").value);
          }}
        ></input>
      </div>

      <div className="weatherCard">{currentWeatherDisplay}</div>
    </div>
  );
}
```

应用这种状态和显示的分离使控件更容易理解，但是我们可以更进一步，改变钩子返回来减少组件功能逻辑，并提供更清晰的关注点分离。

```
function useCurrentWeather() {
  // define state for the current weather conditions and zip code
  const [zipCode, setZipCode] = useState(null);
  const [currentWeather, setCurrentWeather] = useState(null);

  /**
   * Create the use effect for getting the current weather conditions
   * this will need to fire on changes to the zip code
   */
  useEffect(() => {
    /**
     * make API call to get the current weather for the given zip code
     */
    if (zipCode !== null) {
      var completeEndpoint = getWeatherEndpoint(zipCode);
      fetch(completeEndpoint)
        .then((response) => {
          return response.json();
        })
        .then((data) => {
          setCurrentWeather(data);
        })
        .then((error) => {
          // do something on error
        });
    }
  }, [zipCode]);

  /**
   * create a modified version of the return to reduce
   * code in the WeatherDisplay componet
   */
  var currentWeatherForReturn = null;
  if (currentWeather !== null) {
    currentWeatherForReturn = {
      currentTemp: convertKelvinToFahrenheit(currentWeather.main.temp),
      feelsLike: convertKelvinToFahrenheit(currentWeather.main.feels_like),
      min: convertKelvinToFahrenheit(currentWeather.main.temp_min),
      max: convertKelvinToFahrenheit(currentWeather.main.temp_max),
      name: currentWeather.name,
      description: currentWeather.weather[0].description,
    };
  }

  return [currentWeatherForReturn, setZipCode];
}

function WeatherDisplay() {
  // define the useCurrentWeather state object
  const [currentWeather, setZipCode] = useCurrentWeather();

  /**
   * create the weather display object based on the current weather state
   * if the currentWeather is null, do not display anything
   */
  var currentWeatherDisplay = null;
  if (currentWeather !== null) {
    // determine the icon class here in the future
    var weatherIcon = <div className="icon-sun wetherIcon"></div>;

    currentWeatherDisplay = [
      <span className="weatherHeader">
        Current weather for {currentWeather.name}
      </span>,
      <span className="weatherCurrent">
        {currentWeather.currentTemp}° feels like {currentWeather.feelsLike}°
      </span>,
      <span className="weatherRange">
        Min: {currentWeather.min}° Max: {currentWeather.max}°
      </span>,
      <div>{weatherIcon}</div>,
      <div className="weatherDescription">{currentWeather.description}</div>,
    ];
  }

  return (
    <div>
      <div>
        <span>Weather For Zipcode:</span>
        <input key="zipCodeInput" id="zipCode"></input>
        <input
          type="button"
          value="Get Weather"
          onClick={() => {
            setZipCode(document.getElementById("zipCode").value);
          }}
        ></input>
      </div>

      <div className="weatherCard">{currentWeatherDisplay}</div>
    </div>
  );
}
```

利用这个定制钩子，我们现在已经将所有状态逻辑从组件显示中分离出来，使其更容易理解。除了组件的易读性，我们还为将来更容易的状态逻辑更新打下了基础。如果我们需要向状态添加额外的逻辑，或者更改用于获取天气的 API，我们可以通过对组件代码进行最小的更改来实现。该代码的完整版本可以在 [github repo](https://github.com/StMotorSpark/react-hooks-weather-component) 上找到。

# 回到共享逻辑

当考虑定制钩子和共享逻辑时，我们必须记住，我们不需要共享组件的整个状态。自定义钩子可以用来封装通用的全状态函数，以便在其他钩子中使用。我最喜欢的一个例子是一个用于处理状态的定制钩子，它通过一个 useEffect 与多个异步 API 调用进行交互。为了理解对这个钩子的需求，让我们首先讨论一个可能导致状态管理问题的理论场景，然后让我们看一个可以创建 useState 的异步友好版本的钩子示例。

## 获取状态片段的异步调用

在这个例子中，我们来看一个定制钩子，它使用多个 fetch 命令从不同的 API 获取值。

```
function useExampleState() {
  const [state, setState] = useState({
    value1: null,
    value2: null,
  });

  useEffect(() => {
    // make first fetch to value from the first API
    fetch(exampleApi)
      .then((res) => res.json())
      .then((data) =>
        setState({
          ...state,
          value1: data,
        })
      )
      .then((err) => console.log(err));

    // make another fetch to get value from the second API
    fetch(exampleApi2)
      .then((res) => res.json())
      .then((data) =>
        setState({
          ...state,
          value2: data,
        })
      )
      .then((err) => console.log(err));
  }, []);

  return [state];
}
```

尽管这个钩子是直截了当的，但最终结果将是一个行为不稳定的钩子。为了理解这一点，让我们考虑以下几点。

*   每个 fetch 命令独立异步运行，返回时间未知
*   由于函数声明时的作用域，任一提取完成后的“state”值将不是预期值。这将导致第二次提取返回删除第一次提取的值集。

有多种方法可以解决这个问题，但其中一种方法是通过实现一个自定义挂钩来创建一个异步安全版本的 useState。

```
function useAsyncSafeState(initialState) {
    const [state, orgSetState] = useState(initialState);
    const stateRef = useRef(state);

    function setState(newState) {
        stateRef.current = newState;
        return orgSetState(newState);
    }

    return [stateRef.current, setState];
}

function useExampleState() {
  const [state, setState] = useAsyncSafeState({
    value1: null,
    value2: null,
  });

  useEffect(() => {
    // make first fetch to value from the first API
    fetch(exampleApi)
      .then((res) => res.json())
      .then((data) =>
        setState({
          ...state,
          value1: data,
        })
      )
      .then((err) => console.log(err));

    // make another fetch to get value from the second API
    fetch(exampleApi2)
      .then((res) => res.json())
      .then((data) =>
        setState({
          ...state,
          value2: data,
        })
      )
      .then((err) => console.log(err));
  }, []);

  return [state];
}
```

这个异步安全的 useState 实现了 useRef 钩子来保持从自定义钩子返回的任何“状态”的引用是当前的。你可以在 [React Hooks 文档](https://reactjs.org/docs/hooks-reference.html#useref)上阅读更多关于 useRef 以及为什么它会起作用的信息。

# 最后的想法

我们已经讨论了我关于为什么你应该总是使用定制钩子的想法，以及关于重用定制钩子的一些想法。我希望这能为你下一个基于钩子的 React 组件的设计提供一些启发和帮助。

*原载于 2020 年 4 月 7 日*[*https://just some . codes*](https://justsome.codes/why-i-always-use-custom-hooks/)*。*