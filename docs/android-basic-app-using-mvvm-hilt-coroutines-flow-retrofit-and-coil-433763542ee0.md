# Android:使用 MVVM、刀柄、协程、流程、翻新和线圈的基本应用程序。

> 原文：<https://levelup.gitconnected.com/android-basic-app-using-mvvm-hilt-coroutines-flow-retrofit-and-coil-433763542ee0>

![](img/f26967e5ff0a90bb20e3dee4eda63e51.png)

卢肯·萨贝拉诺在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

**Android 架构组件**为应用架构提供指导，为生命周期管理和数据持久化等常见任务提供库。**架构组件**帮助你用更少的样板代码构建一个健壮的、可测试的、可维护的应用程序，通过这篇文章，我们将做同样的事情。我已经完成了几个项目，并根据我的理解选择了最好的方法。

对于下面的实现，我假设你知道 MVVM、刀柄、协程、流、改型和线圈的基本知识。在我们实施的过程中，我会根据我们的要求解释这些。(如果您希望我就这些主题写文章，请随时发表评论或联系我)。

对于当前的实现，我们将使用 [Dog API](https://dog.ceo/dog-api/) ，对于我们的用例，我们将需要以下依赖关系:

```
dependencies **{** def coroutinesVersion = '1.4.3'
    def retrofitVersion = "2.9.0"
    def lifeCycleVersion = '2.2.0'
    def daggerHiltVersion = '2.31.2-alpha'
    def hiltLifeCycleVersion = '1.0.0-alpha03'
    def coilVersion = '1.2.2' implementation "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
    implementation 'androidx.core:core-ktx:1.5.0'
    implementation 'androidx.appcompat:appcompat:1.3.0'
    implementation 'com.google.android.material:material:1.3.0'
    implementation 'androidx.constraintlayout:constraintlayout:2.0.4'
    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.2'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.3.0' def activity_version = "1.1.0"
    implementation "androidx.activity:activity-ktx:$activity_version"
    implementation "androidx.fragment:fragment-ktx:$activity_version" //ViewBinding
    implementation 'com.android.databinding:viewbinding:4.2.1' //Coroutines
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-core:$coroutinesVersion"
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:$coroutinesVersion"
    implementation "org.jetbrains.kotlinx:kotlinx-coroutines-play-services:$coroutinesVersion" //Retrofit
    implementation "com.squareup.retrofit2:retrofit:$retrofitVersion"
    implementation "com.squareup.retrofit2:converter-gson:$retrofitVersion"
    implementation "com.squareup.okhttp3:logging-interceptor:4.9.1"
    implementation "com.squareup.retrofit2:converter-scalars:$retrofitVersion" //material design
    implementation 'com.google.android.material:material:1.3.0' // ViewModel
    implementation "androidx.lifecycle:lifecycle-extensions:$lifeCycleVersion"
    implementation "androidx.lifecycle:lifecycle-runtime-ktx:$lifeCycleVersion"
    implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:$lifeCycleVersion"
    implementation "androidx.lifecycle:lifecycle-livedata-ktx:$lifeCycleVersion" //Hilt for di
    implementation "com.google.dagger:hilt-android:$daggerHiltVersion"
    kapt "com.google.dagger:hilt-android-compiler:$daggerHiltVersion"
    // Hilt ViewModel extension
    implementation "androidx.hilt:hilt-lifecycle-viewmodel:$hiltLifeCycleVersion"
    kapt "androidx.hilt:hilt-compiler:$hiltLifeCycleVersion" //for image rendering
    implementation "io.coil-kt:coil:$coilVersion"**}**
```

我已经在我们使用的每个依赖项上添加了注释。此外，我们将使用视图绑定，为此我们需要在模块 build.gradle 文件中的 android 标签下添加以下内容:

```
android **{
    ....** buildFeatures **{** viewBinding true
    **}
}**
```

我们将在清单文件中添加 internet 权限，

```
<uses-permission android:name="android.permission.INTERNET"/>
```

我们的 API 非常简单，不需要我们注册 API 密钥。我们将使用随机图像获取端点，

```
[https://dog.ceo/api/breeds/image/random](https://dog.ceo/api/breeds/image/random)
```

JSON 响应非常简单，

```
{
    "message": "https://images.dog.ceo/breeds/mexicanhairless/n02113978_147.jpg",
    "status": "success"
}
```

响应有两个字符串键，一个是狗图片的 url，另一个是响应状态。

我更喜欢使用数据类[插件](https://github.com/wuseal/JsonToKotlinClass)从 JSON 获取我的数据类。根据您的需求，您可以选择多种其他设置，变量类型(val、var)、它们的默认类型(不可为空、可为空)、序列化库等。在我们的例子中，我们将使用不可空的 val 类型和 Gson 进行序列化和反序列化。我们的数据类看起来像这样，

```
data class DogResponse(
    @SerializedName("message")
    val message: String,
    @SerializedName("status")
    val status: String
)
```

我们将在单独的类中维护常数，

```
class Constants { companion object { const val BASE_URL = "https://dog.ceo/"
        const val RANDOM_URL = "api/breeds/image/random"
    }
}
```

我已经在这个类中定义了基本 url 和 API url。我们的 API 调用的服务类如下所示，

```
interface DogService { @GET(Constants.RANDOM_URL)
    **suspend** fun getDog(): **Response**<DogResponse>}
```

这是一个用 GET 注释的简单方法。我们将使用 suspend 函数来非阻塞地执行我们的任务。挂起函数是协程的核心。暂停功能只是一个可以暂停并在以后恢复的功能。他们可以执行一个长时间运行的操作，并在没有阻塞的情况下等待它完成。我们的响应包含在改进响应类中。

我们将使用 Hilt 进行依赖注入，并开始生成 Hilt 组件的代码，我们需要一个应用程序类，用@HiltAndroidApp 注释

```
**@HiltAndroidApp**
class DogApplication : Application() {
}
```

不要忘记在清单文件中添加 DogApplication 类，

```
<application
        android:name=".DogApplication"
        ..... 
       >
</application>
```

接下来，让我们创建一个 DogService 接口的实例，

```
**@Module
@InstallIn(SingletonComponent::class)**
object NetworkModule {
    **@Singleton
    @Provides**
    fun provideHttpClient(): **OkHttpClient** {
        return OkHttpClient
            .Builder()
            .readTimeout(15, TimeUnit.*SECONDS*)
            .connectTimeout(15, TimeUnit.*SECONDS*)
            .build()
    } **@Singleton
    @Provides**
    fun provideConverterFactory(): **GsonConverterFactory** =
         GsonConverterFactory.create() **@Singleton
    @Provides**
    fun provideRetrofit(
        okHttpClient: OkHttpClient,
        gsonConverterFactory: GsonConverterFactory
    ): **Retrofit** {
        return Retrofit.Builder()
            .baseUrl(*BASE_URL*)
            .client(okHttpClient)
            .addConverterFactory(gsonConverterFactory)
            .build()
    } **@Singleton
    @Provides**
    fun provideCurrencyService(retrofit: Retrofit): **DogService** =
        retrofit.create(DogService::class.*java*)}
```

我们已经定义了创建 DogService 接口实例所需的下列依赖项的单例实例，Hilt 通过检查其他带注释方法的返回类型来自动检查所需的依赖项。由于这些依赖项是第三方库，我们需要用@Module 注释来注释我们的对象。

> 挂起函数只能从协程或另一个挂起函数中调用

现在我们有了网络模块，让我们在远程数据源中注入服务类，并将其传播到存储库。

```
class RemoteDataSource @Inject constructor(private val dogService: DogService) { suspend fun getDog() =
        dogService.getDog()}
```

这里我们只是构造函数注入服务并调用 getDog()方法。在到达存储库之前，我们将使用我们可能的响应类型创建一个网络结果类，

```
sealed class NetworkResult<T>(
    val data: T? = null,
    val message: String? = null
) { class Success<T>(data: T) : NetworkResult<T>(data) class Error<T>(message: String, data: T? = null) : NetworkResult<T>(data, message) class Loading<T> : NetworkResult<T>()}
```

上面的类是一个通用的密封类，具有可能的响应类型——为了更好地理解这一点，请阅读一下 [this](https://medium.com/codex/kotlin-sealed-classes-for-better-handling-of-api-response-6aa1fbd23c76) ,我在那里详细阐述了密封类对于更好地处理 API 响应的必要性。

为了封装来自 viewmodel 类的基本 API 响应检查，我们将使用一个抽象类来处理这些检查并传播 API 响应，

```
abstract class BaseApiResponse { suspend fun <T> **safeApiCall**(apiCall: suspend () -> Response<T>): NetworkResult<T> {
        try {
            val response = apiCall()
            if (response.*isSuccessful*) {
                val body = response.body()
                body?.*let* **{** return NetworkResult.Success(body)
                **}** }
            return error("${response.code()} ${response.message()}")
        } catch (e: Exception) {
            return error(e.message ?: e.toString())
        }
    } private fun <T> error(errorMessage: String): NetworkResult<T> =
        NetworkResult.Error("Api call failed $errorMessage")}
```

BaseApiResponse 类将由我们的存储库扩展，并从存储库调用，

```
**@ActivityRetainedScoped**
class Repository @Inject constructor(
    private val remoteDataSource: RemoteDataSource
) : **BaseApiResponse()** { suspend fun getDog(): Flow<NetworkResult<DogResponse>> {
        return *flow*<NetworkResult<DogResponse>> **{
            emit**(**safeApiCall { remoteDataSource.getDog() }**)
        **}**.*flowOn*(Dispatchers.IO)
    }}
```

在上面的两个代码片段中，我们创建了一个基本响应处理类，它是从存储库中调用的，扩展了我们的抽象 BaseApiResponse 类。我们将 **getDog()** 方法作为参数传递给 **safeApiCall** 方法，后者依次执行该方法并检查响应。我们在这里添加了基本的响应检查，但是您可以根据您的 API 预期响应类型来更新这些检查。最后，存储库中的 suspend 函数发出数据。这样，我们的视图模型将更加清晰。

```
**@HiltViewModel**
class MainViewModel **@Inject constructor
    (
    private val repository: Repository,**
    application: Application
) : AndroidViewModel(application) { private val **_response**: MutableLiveData<NetworkResult<DogResponse>> = MutableLiveData()
    val **response**: LiveData<NetworkResult<DogResponse>> = _response fun fetchDogResponse() = ***viewModelScope***.*launch* **{** repository.getDog().collect **{** values **->** _response.*value* = values
    **}
  }**}
```

我们用@HiltViewModel 注释的 ViewModel 类通过构造函数注入来获取存储库。既然我们已经在 BaseApiResponse 类中添加了响应检查，我们只需要从 API 响应中收集发出的值。为了调用该方法，我们启动一个 viewModelScope ，并将响应设置为 our _response 变量，该变量是可变的实时数据。我们将 _response 变量作为私有变量，因为它是可变的，并将它的值设置为 response 变量，它是不可变的实时数据。我们将在我们的活动中观察同样的情况。

我们现在处于演示应用程序的最后阶段，我们需要做的就是调用 fetchDogResponse()方法并绑定响应，

```
@AndroidEntryPoint
class MainActivity : AppCompatActivity() { private val mainViewModel by *viewModels*<MainViewModel>()
    private lateinit var _binding: ActivityMainBinding override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        _binding = ActivityMainBinding.inflate(*layoutInflater*)
        setContentView(_binding.*root*) fetchData()
 }
}
```

这里我们已经通过 *viewModels* delegate 初始化了 viewmodel，并使用视图绑定来绑定视图。如果你想了解更多关于视图绑定的内容，你可以在这里阅读。最后，我们调用 fetchData()，将视图绑定到数据，或者根据响应使用适当的操作，

```
private fun fetchData() {
    mainViewModel.fetchDogResponse()
    mainViewModel.response.observe(this) **{** response **->** when (response) {
            is **NetworkResult.Success -> {
               // bind data to the view
            }** is **NetworkResult.Error -> {
                // show error message
            }** is **NetworkResult.Loading -> {
                // show a progress bar
            }**
        }
    **}** }
```

现在我们调用 fetchDogResponse()方法，开始观察响应，并做必要的工作。

为了通过响应渲染图像，我们将使用比其他图像加载库[更好的【coil](https://coil-kt.github.io/coil/#:~:text=An%20image%20loading,Coroutine%20Image%20Loader.)

```
_binding.imgDog.***load***(
    response.data.message
) **{** transformations(RoundedCornersTransformation(16f))
**}**
```

我们将使用 id imgDog 在 ImageView 中呈现图像，并将图像 url 传递给 load，这是线圈库提供的扩展函数。我们可以给我们的图像添加大量的修改，但是这里我只是为圆角添加了一个简单的变换。你可以用线圈做更多这样的事情，它是从盒子里出来的。

你可以在这里找到最终项目。希望你今天学到了一些东西，干杯！