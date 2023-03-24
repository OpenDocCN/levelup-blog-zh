# 测试遗留问题

> 原文：<https://levelup.gitconnected.com/testing-legacy-troubles-7f3aedf2db17>

> 如果你遵循 Michael Feathers 对遗留代码的定义(每一个没有被测试覆盖的代码)，那么处理一些需要更新的遗留代码的第一步就是把它放入测试工具中(为它写一个测试)。但这往往说起来容易做起来难。由于处理依赖关系的方式，在测试中实例化一个(遗留)类可能会非常困难。

让我们来看看两个最常见的问题，以及我们如何解决它们。

# 刺激性参数的情况

假设我们正在构建一个网店应用程序。为了计算产品价格，我们需要应用一些业务逻辑(折扣、凭证……)并将增值税(税款)考虑在内，增值税由一些外部服务提供:

```
class **CalculatePrice**
{
	public function **__construct**(private VatDotComClient $vatProvider)
	{
	}

	public function **calculate**(Product $product)
	{
		$taxRate = $this->vatProvider->getRate($product->countryCode());
		// some business logic to calculate price
	}
}
```

为了在测试中实例化我们的`CalculatePrice`服务，我们编写:

```
public function **itAppliesTheTaxRate**()
{
	$calculateTax = new **CalculatePrice**(
		new **VatDotComClient**('api-key', 'secret')
	);
        // ...
}
```

你能发现这里的问题吗？我们想要测试的是我们服务中的业务逻辑——考虑到增值税率(无论可能是什么),它是否计算出正确的价格。但是每次我们运行这个测试时`VatDotComClient`都会对提供者进行 HTTP 调用。这使得我们的测试很慢(并且依赖于提供者)。

不要误解我的意思，通过一个(集成)测试来确认我们的`VatDotComClient`可以与提供者对话是没有错的，但是我们不需要每次在我们的领域中测试一些业务逻辑时都测试那个集成。

如果我们创建一些虚拟的 VAT 客户端，返回一些我们控制的数据(而不是通过网络与一些真实的提供商交谈)，会怎么样呢？然后，我们可以在测试中使用虚拟提供者，在产品代码中使用真实的提供者。听起来不错，但我们的服务依赖于(真实的)`VatDotComClient`。气人吧？

为了解决这个问题，我们可以应用*提取接口*重构。首先，我们创建一个接口(基于`VatDotComClient`):

```
interface **VatProvider**
{
	public function **getRate**(string $countryCode): float;
}
```

然后我们让我们的`CalculatePrice`服务依赖于这个接口(而不是具体的实现)。

```
class **CalculatePrice**
{
	public function **__construct**(private VatProvider $vatProvider)
	{
	}

	public function **calculate**(Product $product)
	{
		$taxRate = $this->vatProvider->getRate($product->countryCode());
		// some business logic to calculate price
	}
}
```

现在我们现有的`VatDotComClient`是我们在生产中使用的`VatProvider`的实现，但是我们还将创建我们的虚拟实现:

```
class **TestProvider** implements **VatProvider**
{
	public function **__construct**(private float $rate)
	{
	}

	public function **getRate**(string $countryCode)
	{
		return $this->rate;
	}
}
```

我们可以在测试中使用:

```
public function **itAppliesTheTaxRate**()
{
	$calculateTax = new **CalculatePrice**(
		new **TestProvider**(0.25)
	);

	// ... Assert price what expected (with 0.25 tax rate)
}
```

这个`TestProvider`的好处是它不会调用一些真实的基础设施(在本例中是 HTTP 调用)，我们还可以控制它返回的虚拟数据，所以我们可以在设置测试断言时考虑到这一点。

# 隐藏依赖的情况

既然我们可以计算产品的价格，人们也在购买产品，我们还需要处理他们的付款。假设我们有这样的服务:

```
class **ProcessPayment**
{
	public function **__construct**()
	{
		// ...
	}

	public function **process**(Payment $payment)
	{
		// ...
	}
}
```

根据其构造函数判断，该服务没有依赖关系，因此很容易在测试中实例化:

```
public function **itRecordsThePayment**()
{
	$payment = new **Payment**(340, 'EUR', 12);

	$processPayment = new **ProcessPayment**();
	$processpayments->process($payment);

	// ... Assert stuff
}
```

我们写下了我们的断言，我们的测试通过了，这一切都很好…但是，等一下，用户突然抱怨他们收到了电子邮件确认他们从来没有支付！

嗯，在进一步检查我们的`ProcessPayment`服务后，我们看到它确实依赖于一个`Mailer`类，但是隐藏在构造函数中(或者在某个方法中),在所有业务逻辑之间的某个地方有一个对它的调用来发送电子邮件。

```
class **ProcessPayment**
{
	public function **__construct**()
	{
		// some code
		$this->mailer = new **Mailer**('api-key', 'secret');
		// some code
	}

	public function **process**(Payment $payment)
	{
		// ...

		$this->mailer->send($payment->receiver(), new PaymentNotificationMessage($payment)):

	}
}
```

现在第一步是使隐式(隐藏的)依赖显式化。为此，我们应用了*参数化构造函数*重构 *:*

```
class **ProcessPayment**
{
	public function **__construct**(Mailer $mailer)
	{
		$this->mailer = $mailer;
	}

	public function **process**(Payment $payment)
	{
		// ...
	}
}
```

现在，我们可以应用 *Extract Interface* 步骤，并实现一些我们可以在测试中使用的虚拟邮件程序，这样我们就不会在 ProcessPayment 服务中测试业务逻辑时实际发送电子邮件(当然，我们仍然可以进行一些集成测试，看看我们真正的邮件程序服务是否可以实际发送电子邮件)。

# 测试和良好设计的协同作用

如果你看看重构后的代码，你会发现它现在使用了一个众所周知的优秀设计原则— [依赖注入](https://en.wikipedia.org/wiki/Dependency_injection#:~:text=In%20software%20engineering%2C%20dependency%20injection,object%20is%20called%20a%20service.&text=The%20service%20is%20made%20part%20of%20the%20client's%20state.)。此外，我们的服务不再依赖于具体的实现，而是依赖于抽象(良好设计的另一个原则)。这允许我们交换这些具体的实现([策略](https://en.wikipedia.org/wiki/Strategy_pattern)设计模式)。因此，现在不仅我们的代码更易测试，而且我们的设计也得到了改进。

在可测试性和良好的设计之间有这种协同作用。设计良好的代码很容易编写测试，反之亦然。

这就是为什么在开发新代码时，您应该努力使用测试驱动开发。至于遗留问题，如果你需要更多关于处理遗留问题的指导，请查看 Michael Feathers ***有效处理遗留代码*** [一书](https://www.goodreads.com/book/show/44919.Working_Effectively_with_Legacy_Code)以获得更多的技巧和建议。

![](img/876d1f3c85d6a07031518736cc77e4c0.png)