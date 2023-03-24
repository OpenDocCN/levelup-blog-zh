# 用 Selenium 和 Java 截图的好，坏，丑

> 原文：<https://levelup.gitconnected.com/the-good-the-bad-and-the-ugly-of-taking-screenshots-with-selenium-and-java-c9da23e09842>

![](img/7f45ee8410376278d3026576b27e7d24.png)

当然，我借用了克林特·伊斯特伍德主演的同名电影的名字。

这篇文章是关于什么的？

它讨论了用 Selenium 和 Java 截屏的 3 种方法。

你猜，坏的，丑的，好的:)

# 丑陋的那个

糟糕的方法是用 try/catch 语句包装 Selenium 测试，如下所示:

```
import org.testng.Assert;
import org.testng.annotations.Test;
import PageObjects1.HomePage;
import PageObjects1.ResultsPage;
import framework.Screenshot; public class TestClass extends TestBase{

  private static String URL = “[http://www.vpl.ca](http://www.vpl.ca)";

  @Test
  public void search_For_Keyword_Works() {     try {
       HomePage homePage = openSite(); ResultsPage resultsPage = homePage.searchFor(“java”);

       Assert.assertTrue(resultsPage.header().contains(“java”), 
                         “Results Page header is incorrect!”);
    }
    catch (Exception e) {
       Screenshot screenshot = new Screenshot(driver);
       screenshot.capture(“Search_For_Keyword_Works”);
    } }

   private HomePage openSite() {
     driver.get(URL);

     return new HomePage(driver);
   }

}
```

如果页面类(HomePage 和 ResultsPage)生成了异常或者断言失败，则在 catch 子句中捕获一个屏幕截图。

截图将被存储在 Maven 项目的/target/screens 文件夹中。该文件夹是在基本测试类的设置方法中创建的:

```
import java.io.File;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.BeforeMethod;public class TestBase {

  public WebDriver driver;
  private String chromeDriverPath =   
           “./src/test/resources/chromedriver.exe”;

  @BeforeClass
  public void setUp_Class() {
     new File(“./target/screenshots”).mkdir();
  }

  @BeforeMethod
  public void setUp() {
    System.setProperty(“webdriver.chrome.driver”, chromeDriverPath);
    this.driver = new ChromeDriver();
  }

  @AfterMethod
  public void tearDown() { 
    driver.quit();
  }

}
```

基本测试类有两种设置方法:

*   一个使用 BeforeClass 注释并创建目标/截图文件夹
*   另一个使用 BeforeMethod 注释并创建驱动程序对象

截屏由以下类获取并保存:

```
package framework;import java.io.FileOutputStream;
import java.time.LocalDateTime;
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebDriver;public class Screenshot {

  private WebDriver driver; 
  private String folderPath = “./target/screenshots/”;

  public Screenshot(WebDriver driver)
  { 
    this.driver = driver;
  } public void capture(String fileName) 
  { 
    try { 
      String now = LocalDateTime.now().toString(); now = now.replace(“:”, “_”)
               .replace(“;”, “_”)
               .replace(“.”, “_”);

      FileOutputStream file = new FileOutputStream(
                 folderPath + fileName + now + “.png”);

      byte[] info = 
      ((TakesScreenshot)driver).getScreenshotAs(OutputType.BYTES);

      file.write(info);
      file.close(); 
    } 
    catch (Exception ex) {
      throw new RuntimeException(“cannot create screenshot;”, ex);
    }

  }}
```

截图文件名包括唯一性的 now 值。

## 这种截图方式有什么问题？

2 件事是错误的:

1.  好的单元测试不包括任何 Java 语句，如 IF/ELSE、FOR、WHILE、SWITCH 或 TRY/CATCH。它只使用页面方法来打开站点，导航到被测试的页面，并在其上做出断言。
2.  **每个测试都需要截图的重复代码。**

在这种情况下，测试类看起来并不好。

将其与干净简单的页面对象类进行比较。

这是主页类:

```
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;public class HomePage { private WebDriver driver;
 private static String TITLE = “Vancouver Public Library |”;

 private static By SEARCH_BOX_ID = By.id(“edit-search”);
 private static By SEARCH_BUTTON_ID = By.id(“edit-submit1”);

 public HomePage(WebDriver driver) { this.driver = driver;

   if (!driver.getTitle().equals(TITLE))
     throw new RuntimeException(“Home Page is not displayed!”); }

 public ResultsPage searchFor(String keyword) { WebElement searchBox = driver.findElement(SEARCH_BOX_ID);
   searchBox.sendKeys(keyword);

   WebElement searchButton = driver.findElement(SEARCH_BUTTON_ID);
   searchButton.click();

   return new ResultsPage(driver); }

}
```

这是 ResultsPage 类:

```
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;public class ResultsPage {

 private WebDriver driver;

 private static String URL = “vpl.bibliocommons.com”;

 private static By HEADER_XPATH = 
    By.xpath(“//h1[[@data](http://twitter.com/data)-test-id = ‘searchTitle’]”);

 public ResultsPage(WebDriver driver) {

   this.driver = driver;

   if (!driver.getCurrentUrl().contains(URL))
     throw new RuntimeException(“Results Page is not displayed!”);

 }

 public String header() { WebElement header = driver.findElement(HEADER_XPATH);
   return header.getText().toLowerCase(); }}
```

# 坏的那个

如果测试不能包含截图的代码，也许我们可以将这些代码转移到页面对象类中。

HomePage.java 的变化如下:

```
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;import framework.Screenshot;public class HomePage { private WebDriver driver;
  private static String TITLE = “Vancouver Public Library |”;

  private static By SEARCH_BOX_ID = By.id(“edit-search”);
  private static By SEARCH_BUTTON_ID = By.id(“edit-submit1”);

  public HomePage(WebDriver driver) { this.driver = driver;

    if (!driver.getTitle().equals(TITLE))
      throw new RuntimeException(“Home Page is not displayed!”); }

  public ResultsPage searchFor(String keyword) { try {

      WebElement searchBox = driver.findElement(SEARCH_BOX_ID);
      searchBox.sendKeys(keyword);

      WebElement searchBtn = driver.findElement(SEARCH_BUTTON_ID);
      searchBtn.click();

      return new ResultsPage(driver); }
    catch (Exception e) { Screenshot screenshot = new Screenshot(driver);
      screenshot.capture(“HomePage_SearchFor_”);

      throw new RuntimeException(“cannot search!”); }
  }

}
```

ResultsPage 有类似的代码:

```
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;import framework.Screenshot;public class ResultsPage {

  private WebDriver driver;

  private static String URL = “vpl.bibliocommons.com”;

  private static By HEADER_XPATH = 
        By.xpath(“//h1[[@data](http://twitter.com/data)-test-id = ‘searchTitle’]”);

  public ResultsPage(WebDriver driver) { this.driver = driver;

    if (!driver.getCurrentUrl().contains(URL))
      throw new RuntimeException(“Results Page is not displayed!”); }

  public String header() {

    try { WebElement header = driver.findElement(HEADER_XPATH);
      return header.getText().toLowerCase(); }
    catch (Exception e) { Screenshot screenshot = new Screenshot(driver);
      screenshot.capture(“ResultsPage_Header_”);

      throw new RuntimeException(“cannot get the header!”); }

 }}
```

在这种情况下，每个页面方法的代码都被包装在 TRY/CATCH 中，以便在出现异常时获取屏幕截图。

不过，测试看起来不错:

```
import org.testng.Assert;
import org.testng.annotations.Test;import PageObjects2.HomePage;
import PageObjects2.ResultsPage;public class TestClass extends TestBase{

  private static String URL = “http://www.vpl.ca";

  @Test
  public void search_For_Keyword_Works() {     HomePage homePage = openSite(); ResultsPage resultsPage = homePage.searchFor(“java”);

    Assert.assertTrue(resultsPage.header().contains(“java”), 
                      “Results Page header is incorrect!”);
  }

  private HomePage openSite() {
    driver.get(URL);
    return new HomePage(driver);
  }

}
```

**这为什么不好？**

代码复制从单元测试转移到了页面方法。

每个页面方法都需要用 TRY/CATCH 包装其代码，以便在出现异常时获取屏幕截图。

好的，所以截图不能在测试中获取，也不能在页面方法中获取。他们会被带到哪里？

# 好的那个

TestNG 监听器是正确的解决方案。

监听器监听测试并为每个测试结果执行定制代码:

```
import java.lang.reflect.Field;import org.testng.ITestContext;
import org.testng.ITestListener;
import org.testng.ITestResult;import framework.Screenshot;import org.openqa.selenium.WebDriver;/*
   the test listener captures in the OnTestFailure() method the    
   screenshot of the current page both 
   — when an exception happens in a test script
   — when a test script fails because of an assertion none of the other listener methods are implemented.
 */
public class TestListener implements ITestListener {

  @Override
  public void onTestStart(ITestResult result) { 
  }

  @Override
  public void onTestSuccess(ITestResult result) { 
  } /*
   the driver parameter of Screenshot constructor is provided by the 
   driver() method;

   testResult.getName() returns the name of the test script being 
   executed;

   we pass this as parameter to capture() so that the screenshot is 
   named after the test script;
  */
  @Override
  public void onTestFailure(ITestResult testResult) {    try {
      Screenshot screenshot = new Screenshot(driver(testResult));
      screenshot.capture(“listener_” + testResult.getName());
    } 
    catch (IllegalAccessException e) {
      e.printStackTrace();
    }    } @SuppressWarnings(“unchecked”)
  private WebDriver driver(ITestResult testResult) 
     throws IllegalAccessException 
  {

    try {

      /*
       we use reflection and generics to find the driver object:

       1\. testResult.getInstance() 
          returns the running test class object

       2\. testResult.getInstance().getClass() 
          returns the class of testResult (TestClass)

       3\. testClass.getSuperclass() 
          returns the parent of the test class (BaseTestClass)

       4\. Field driverField = 
                baseTestClass.getDeclaredField(“driver”)
          returns the driver field of the BaseTestClass

       5\. driver = 
           (WebDriver)driverField.get(testResult.getInstance());
          gets the value of the driver field from the BaseTestClass
      */ Class<?extends ITestResult> testClass = 
        (Class<? extends ITestResult>) 
           testResult.getInstance().getClass(); Class<?extends ITestResult> baseTestClass = 
        (Class<? extends ITestResult>) 
           testClass.getSuperclass(); Field driverField = baseTestClass.getDeclaredField(“driver”);
      WebDriver driver =   
        (WebDriver)driverField.get(testResult.getInstance()); 

      return driver;
    }  
    catch (SecurityException | 
           NoSuchFieldException | 
           IllegalArgumentException e) {      throw new RuntimeException("could not get the driver"); }

  } @Override
  public void onTestSkipped(ITestResult result) {
  } @Override
  public void onTestFailedButWithinSuccessPercentage(ITestResult   
  result) {
  } @Override
  public void onStart(ITestContext context) {
  } @Override
  public void onFinish(ITestContext context) {
  }
}
```

代码应该相当容易理解。

每个侦听器方法都获得一个 ITestResult 类型的参数，它是测试类的实例。

使用这个参数，我们可以使用反射从基本测试类中获取驱动程序对象。

一旦驱动程序对象可用，我们就用它来获取以运行单元测试命名的屏幕截图。

我们如何使用监听器？

页面类中不需要修改代码。它们不包括任何截屏代码。

```
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;public class HomePage { private WebDriver driver;
  private static String TITLE = “Vancouver Public Library |”;

  private static By SEARCH_BOX_ID = By.id(“edit-search”);
  private static By SEARCH_BUTTON_ID = By.id(“edit-submit1”);

  public HomePage(WebDriver driver) { this.driver = driver;

    if (!driver.getTitle().equals(TITLE))
      throw new RuntimeException(“Home Page is not displayed!”); }

  public ResultsPage searchFor(String keyword) { WebElement searchBox = driver.findElement(SEARCH_BOX_ID);
    searchBox.sendKeys(keyword);

    WebElement searchButton = driver.findElement(SEARCH_BUTTON_ID);
    searchButton.click();

    return new ResultsPage(driver); }

}import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;public class ResultsPage {

  private WebDriver driver;

  private static String URL = “vpl.bibliocommons.com”;

  private static By HEADER_XPATH = 
        By.xpath(“//h1[[@data](http://twitter.com/data)-test-id = ‘searchTitle’]”);

  public ResultsPage(WebDriver driver) { this.driver = driver;

    if (!driver.getCurrentUrl().contains(URL))
      throw new RuntimeException(“Results Page is not displayed!”); }

  public String header() {

    WebElement header = driver.findElement(HEADER_XPATH);
    return header.getText().toLowerCase();

  }}
```

测试类也没有任何截图的代码。但是，它使用@Listeners 注释和侦听器类的名称:

```
import org.testng.Assert;
import org.testng.annotations.Listeners;
import org.testng.annotations.Test;import PageObjects3.HomePage;
import PageObjects3.ResultsPage;@Listeners(TestListener.class)
public class TestClass extends TestBase{

  private static String URL = “http://www.vpl.ca";

  @Test
  public void search_For_Keyword_Works() {

    HomePage homePage = openSite();

    ResultsPage resultsPage = homePage.searchFor(“java”);

    Assert.assertTrue(resultsPage.header().contains(“java”), 
                      “Results Page header is incorrect!”);
  }

  private HomePage openSite() {
    driver.get(URL);
    return new HomePage(driver);
  }

}
```

在这种情况下会发生什么？

单元测试运行。如果它生成一个异常或断言失败，监听器将会注意到这一点，并在 OnTestFailure()方法中截取当前页面的屏幕截图。

为什么这是正确的解决方案应该是显而易见的:

*   单元测试不包括任何截屏代码
*   页面对象类不包含任何截屏代码
*   截图都是在一个地方拍的
*   如果一个测试类需要它的单元测试来获取屏幕截图，它只需要@Listeners 注释，后跟监听器类名

希望你喜欢这篇文章，也许能从中学到一些东西。