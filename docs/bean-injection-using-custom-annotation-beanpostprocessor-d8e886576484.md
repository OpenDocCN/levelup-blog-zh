# 使用定制注释的 Bean 注入(BeanPostProcessor)

> 原文：<https://levelup.gitconnected.com/bean-injection-using-custom-annotation-beanpostprocessor-d8e886576484>

注释用于向程序要使用的类/字段/方法提供一条附加信息。自定义注释现在对我们的开发人员来说已经不是什么新鲜事了，你会发现很多关于它的文章。说到这里，在这篇文章中，我试图分享我们如何通过使用自定义注释来为您的接口注入特定的实现。

![](img/f057f26e1d2bacc01d5b4d000604ba5d.png)

# 什么是 BeanPostProcessor？

它是一个接口，我们可以在实例化 bean 时实现它来放置一些自定义逻辑。它利用了 bean 创建生命周期，并为我们提供了围绕它定制任何逻辑的灵活性。

它有两个方法可以在任何 bean 创建生命周期中调用:

1.  `**postProcessAfterInitialization()**`它在 bean 创建后被调用。
2.  `**postProcessBeforeInitialization()**`它在 bean 创建之前被调用。因此，如果您需要为给定的接口注入特定的 bean。这里是您的自定义逻辑。

为字段创建自定义注释需要几个步骤。让我们逐一了解。

## [步骤 1]创建一个文件来定义您的自定义注释。

```
@Documented
@Target({ElementType.TYPE,ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
public @interface CustomMock {
}
```

## [步骤 2]创建一个类(必须是 spring bean)来覆盖 bean 创建生命周期。

它使用一个字段回调类来放置一个使用反射的自定义逻辑。

```
@Component
public class CustomMockBeanPostProcessor implements BeanPostProcessor {
    @Autowired
    private ConfigurableListableBeanFactory configurableBeanFactory;

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        if (bean.getClass().isAnnotationPresent(RunWith.class)) {
            ReflectionUtils.doWithFields(bean.getClass(), new CustomMockFieldCallBack(configurableBeanFactory, bean));
        }
        return bean;
    }
}
```

上面的实现需要理解一些非常重要的东西。我们没有向`afterInitialization()`添加任何定制逻辑，因为一旦 bean 被创建或返回，我们不打算修改任何东西。然而，`beforeInitialization()`有定制的逻辑，因为每当一个字段用`@CustomMock`注释时，我们必须返回我们自己的假 bean。

> 我们的应用程序中的每个 bean 都会调用这个实现。因此，在将定制逻辑应用于任何 bean 之前，我们不可避免地要进行适当的验证/约束。

在我们的例子中，我们有一个只对用`@RunWith`注释标注的类的字段有效的验证。对于其他类，所有的 beans 都将使用默认的流来创建。

## [步骤 3]为实际定制逻辑所在的定制模拟字段回调创建一个类。

```
public class CustomMockFieldCallBack implements ReflectionUtils.FieldCallback {

  private ConfigurableListableBeanFactory configurableBeanFactory;
  private Object bean;

  public CustomWiredFieldCallBack(ConfigurableListableBeanFactory configurableBeanFactory, Object bean) {
      this.configurableBeanFactory = configurableBeanFactory;
      this.bean = bean;
  }

  @Override
  public void doWith(Field field) throws IllegalArgumentException, IllegalAccessException {
      if (field.isAnnotationPresent(CustomMock.class)) {
          Class<?> interface = field.getType();
          ReflectionUtils.makeAccessible(field);
          Object mockImplementation = getBeanInstance(InterfaceMockBeanMapper.getMockBeanName(interface), interface);
          field.set(bean, mockImplementation);
      }

  }

  public Object getBeanInstance(String beanName, Class interface) {
      Object mockImplementation = null;
      if (!configurableBeanFactory.containsBean(beanName)) {
          Object newInstance = null;
          try {
              Constructor<?> ctr = interface.getConstructor();
              newInstance = ctr.newInstance();
          } catch (Exception e) {
              throw new RuntimeException(e);
          }

          mockImplementation = configurableBeanFactory.initializeBean(newInstance, beanName);
          configurableBeanFactory.autowireBeanProperties(mockImplementation, AutowireCapableBeanFactory.AUTOWIRE_BY_NAME, true);
          configurableBeanFactory.registerSingleton(beanName, mockImplementation);
      } else {
          mockImplementation = configurableBeanFactory.getBean(beanName);
      }
      return mockImplementation;
  }
}
```

1.  上面的类利用 Java 反射来访问类的私有字段，并将目标 bean 分配给它。
2.  它还对字段进行了验证，即字段必须使用`@CustomMock`注释才能工作。
3.  **InterfaceMockBeanMapper** 用于获取给定接口的模拟 bean 名称。基于 bean 名称，如果给定的 bean 不存在，将从`getBeanInstance()`返回一个 bean，然后创建并返回一个具有给定名称的新 bean。

## 如何在我们的代码中使用`@CustomMock`？

```
@RunWith(SpringRunner.class)public FeatureTestClass { @CustomMock private Interface1 bean1; @CustomMock private Interface2 bean2; @Test public void testFeatureClassMethod() { FeatureClass featureClass = new FeatureClass(bean1, bean2); featureClass.method(); ... //assert }}
```

感谢阅读！！

# 分级编码

感谢您成为我们社区的一员！在你离开之前:

*   👏为故事鼓掌，跟着作者走👉
*   📰查看[关卡升级编码](https://levelup.gitconnected.com/)中的更多内容
*   🔔关注我们: [Twitter](https://twitter.com/gitconnected) | [LinkedIn](https://www.linkedin.com/company/gitconnected)
*   🚀👉 [**软件工程师的顶级工作**](https://jobs.levelup.dev/)