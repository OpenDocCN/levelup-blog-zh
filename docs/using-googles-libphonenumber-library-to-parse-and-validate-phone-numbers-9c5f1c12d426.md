# 使用 Google 的 libphonenumber 库解析和验证电话号码

> 原文：<https://levelup.gitconnected.com/using-googles-libphonenumber-library-to-parse-and-validate-phone-numbers-9c5f1c12d426>

## 处理电话号码并不容易。

![](img/620498333517f2f863f8b99f5173b1bb.png)

> 原载于[我的个人博客](https://blog.contactsunny.com/tech/using-googles-libphonenumber-library-to-parse-and-validate-phone-numbers)2020 年 1 月 9 日。

在几乎任何有人类用户的项目或产品中，我们都与电话号码打交道。当产品面向全球用户时，维护数据库中的有效电话号码变得非常困难。我们需要确保不同地区的电话号码长度适合他们的地区，添加国家代码，或删除它们，以及许多这样的验证。这可能很快会成为一个独立的项目。

我们的一个项目中就有这样的问题。当我在做研究，寻找一个易于使用的轻量级工具，以便我也可以外包这方面的智能时，我偶然发现了谷歌的 [*libphonenumber*](https://github.com/google/libphonenumber) 库。谷歌有你需要的一切。因此，我没有想第二遍(这是不好的)，我只是开始查看文档，以了解如何将它集成到我的项目中，使我的生活更轻松。在这篇文章中，我会试着帮助你理解同样的道理。

正如你已经知道的，我在 Spring Boot 写我的大部分项目，这没有什么不同。我在这个概念验证中使用了 Spring Boot 命令行运行程序。一件很棒的事情是，这个谷歌图书馆的 Java 客户端非常优化，这样你也可以在 Android 应用程序中使用它。体积这么小，反应这么快。事实上，从 Android 4.0(冰淇淋三明治)开始的所有 Android 版本，都将此库烘焙到系统中。那么，我们开始吧。

# 导入库

Maven central repository 中提供了 *libphonenumber* 库，因此将该库导入到您的项目中就像在 *pom.xml* 文件中添加一个依赖项一样简单。只需在您的 *pom.xml* 文件中添加以下依赖项，就大功告成了:

```
<dependency>
    <groupId>com.googlecode.libphonenumber</groupId>
    <artifactId>libphonenumber</artifactId>
    <version>8.10.14</version>
</dependency>
```

# 代码

现在让我们进入有趣的部分。在本例中，我们将采用几个巴西和几个哥伦比亚的电话号码，以及它们各自的国家短代码，并尝试找出关于它们的以下内容:

*   电话号码对他们所在的地区有效吗？
*   他们有国家代码吗？如果有，是什么？
*   他们有国家号码吗？如果有，是什么？
*   将电话号码格式化为 ***E164*** 标准。

在我们开始之前，让我们先来看看现有的电话号码:

```
List<String> brazilPhoneNumbers = Arrays.asList(
        "+5521976923932",
        "5561991474841",
        "055 719 9174 8722",
        "719 9174 8722",
        "55 (719) (9174) (8722)"
);

List<String> columbiaPhoneNumbers = Arrays.asList(
        "573146339375",
        "3137145988",
        "+573154417054",
        "316537219731323123",
        "3013755140",
        "+57 31 5441 7054",
        "+57 (31) (8393) (1081)"
);
```

如您所见，我们有两个国家所有可能格式的电话号码，包括一些明显无效的号码。看看谷歌图书馆对此的看法会很有趣。接下来，我们需要巴西和哥伦比亚的短代码。让我们得到他们:

```
private String brazilShortCode = "BR";
private String columnbiaShortCode = "CO";
```

很好。接下来，我们必须创建一个在`libphonenumber`库中可用的电话号码实用程序的实例:

```
import com.google.i18n.phonenumbers.PhoneNumberUtil;

private PhoneNumberUtil phoneUtil = PhoneNumberUtil.getInstance();
```

现在，我们可以开始使用库了。首先，我们将开始巴西电话号码列表和巴西的短代码。但是在我们开始任何事情之前，我们必须创建一个 PhoneNumber 类的实例，这个类是这个库附带的。我们做的所有其他操作都将使用这个`PhoneNumber`对象。所以让我们先创建它:

```
PhoneNumber phoneNumberProto = phoneUtil.parse(inputPhoneNumber, shortCode);
```

这将在一个循环中被调用，因此,`inputPhoneNumber`变量将拥有每个列表中的电话号码，而`shortCode`变量将是我们正在运行测试的国家的短代码。

为了说明得更清楚，我有一个带有如下签名的方法:

```
validateAndFormatPhoneNumber(String inputPhoneNumber, String shortCode)
```

我在一个循环中调用这个函数，这个循环遍历电话号码列表，就像这样:

```
for (String inputPhoneNumber : brazilPhoneNumbers) {
    validateAndFormatPhoneNumber(inputPhoneNumber, brazilShortCode);
}
```

从现在开始，我们将讨论`validateAndFormatPhoneNumber()`方法中的语句。希望现在清楚了。

首先，我们将查看所提供的电话号码对于短代码所代表的国家是否有效。为此，我们只调用一个方法:

```
boolean isValid = phoneUtil.isValidNumber(phoneNumberProto);

logger.info("Is phone number valid: " + isValid);
```

这并不难，是吗？所有其他方法也是如此简单。因此，我不解释它们中的每一个，我只是把整个代码放在这里，你可以试着自己理解。如果没有，请在下面的评论中联系我们，我会尽力帮助你的。

```
if (phoneNumberProto.hasCountryCode()) {
    logger.info("Country code is present: " + phoneNumberProto.getCountryCode());
} else {
    logger.info("Country code is not present.");
}

if (phoneNumberProto.hasNationalNumber()) {
    logger.info("National number is present: " + phoneNumberProto.getNationalNumber());
} else {
    logger.info("National number is not present.");
}

String formattedPhoneNumber = phoneUtil.format(phoneNumberProto, PhoneNumberUtil.PhoneNumberFormat.E164);

if (formattedPhoneNumber.startsWith("+")) {
    logger.info("Removing leading + from phone number");
    formattedPhoneNumber = formattedPhoneNumber.replace("+", "");
}

logger.info("Formatted phone number: " + formattedPhoneNumber);
```

这不难理解，对吧？现在让我们看看我用来测试这个库的完整代码:

```
public static void main(String[] args) {
    SpringApplication.run(App.class, args);
}

@Override
public void run(String... args) throws Exception {

    List<String> brazilPhoneNumbers = Arrays.asList(
            "+5521976923932",
            "5561991474841",
            "055 719 9174 8722",
            "719 9174 8722",
            "55 (719) (9174) (8722)"
    );

    List<String> columbiaPhoneNumbers = Arrays.asList(
            "573146339375",
            "3137145988",
            "+573154417054",
            "316537219731323123",
            "3013755140",
            "+57 31 5441 7054",
            "+57 (31) (8393) (1081)"
    );

    phoneUtil = PhoneNumberUtil.getInstance();

    for (String inputPhoneNumber : brazilPhoneNumbers) {
        validateAndFormatPhoneNumber(inputPhoneNumber, brazilShortCode);
    }

    for (String inputPhoneNumber : columbiaPhoneNumbers) {
        validateAndFormatPhoneNumber(inputPhoneNumber, columnbiaShortCode);
    }

    logger.info("+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++");
    logger.info("+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++");
    logger.info("+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++");
    logger.info("Interchanging country code, all numbers should be invalid now.");

    for (String inputPhoneNumber : brazilPhoneNumbers) {
        validateAndFormatPhoneNumber(inputPhoneNumber, columnbiaShortCode);
    }

    for (String inputPhoneNumber : columbiaPhoneNumbers) {
        validateAndFormatPhoneNumber(inputPhoneNumber, brazilShortCode);
    }

}

private void validateAndFormatPhoneNumber(String inputPhoneNumber, String shortCode) {

    logger.info("Processing phone number: " + inputPhoneNumber + " with short code: " + shortCode);

    PhoneNumber phoneNumberProto = null;

    try {
        phoneNumberProto = phoneUtil.parse(inputPhoneNumber, shortCode);

        logger.info("phoneNumberProto: " + new Gson().toJson(phoneNumberProto));

        boolean isValid = phoneUtil.isValidNumber(phoneNumberProto);

        logger.info("Is phone number valid: " + isValid);

        if (phoneNumberProto.hasCountryCode()) {
            logger.info("Country code is present: " + phoneNumberProto.getCountryCode());
        } else {
            logger.info("Country code is not present.");
        }

        if (phoneNumberProto.hasNationalNumber()) {
            logger.info("National number is present: " + phoneNumberProto.getNationalNumber());
        } else {
            logger.info("National number is not present.");
        }

        String formattedPhoneNumber = phoneUtil.format(phoneNumberProto, PhoneNumberUtil.PhoneNumberFormat.E164);

        if (formattedPhoneNumber.startsWith("+")) {
            logger.info("Removing leading + from phone number");
            formattedPhoneNumber = formattedPhoneNumber.replace("+", "");
        }

        logger.info("Formatted phone number: " + formattedPhoneNumber);

    } catch (NumberParseException e) {
        logger.error(e.getMessage());
    }

    logger.info("==================================");
}
```

正如您所看到的，我已经在末尾转换了国家代码，以查看库是否能够找出错误并使其他所有内容无效。我们可以从日志中找到答案。所以让我们来看看。

```
phoneNumberProto: {"hasCountryCode":true,"countryCode_":55,"hasNationalNumber":true,"nationalNumber_":21976923932,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 55
National number is present: 21976923932
Removing leading + from phone number
Formatted phone number: 5521976923932
==================================
Processing phone number: 5561991474841 with short code: BR
phoneNumberProto: {"hasCountryCode":true,"countryCode_":55,"hasNationalNumber":true,"nationalNumber_":61991474841,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 55
National number is present: 61991474841
Removing leading + from phone number
Formatted phone number: 5561991474841
==================================
Processing phone number: 055 719 9174 8722 with short code: BR
phoneNumberProto: {"hasCountryCode":true,"countryCode_":55,"hasNationalNumber":true,"nationalNumber_":71991748722,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 55
National number is present: 71991748722
Removing leading + from phone number
Formatted phone number: 5571991748722
==================================
Processing phone number: 719 9174 8722 with short code: BR
phoneNumberProto: {"hasCountryCode":true,"countryCode_":55,"hasNationalNumber":true,"nationalNumber_":71991748722,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 55
National number is present: 71991748722
Removing leading + from phone number
Formatted phone number: 5571991748722
==================================
Processing phone number: 55 (719) (9174) (8722) with short code: BR
phoneNumberProto: {"hasCountryCode":true,"countryCode_":55,"hasNationalNumber":true,"nationalNumber_":71991748722,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 55
National number is present: 71991748722
Removing leading + from phone number
Formatted phone number: 5571991748722
==================================
Processing phone number: 573146339375 with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":3146339375,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 57
National number is present: 3146339375
Removing leading + from phone number
Formatted phone number: 573146339375
==================================
Processing phone number: 3137145988 with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":3137145988,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 57
National number is present: 3137145988
Removing leading + from phone number
Formatted phone number: 573137145988
==================================
Processing phone number: +573154417054 with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":3154417054,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 57
National number is present: 3154417054
Removing leading + from phone number
Formatted phone number: 573154417054
==================================
Processing phone number: 316537219731323123 with short code: CO
The string supplied is too long to be a phone number.
==================================
Processing phone number: 3013755140 with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":3013755140,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 57
National number is present: 3013755140
Removing leading + from phone number
Formatted phone number: 573013755140
==================================
Processing phone number: +57 31 5441 7054 with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":3154417054,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 57
National number is present: 3154417054
Removing leading + from phone number
Formatted phone number: 573154417054
==================================
Processing phone number: +57 (31) (8393) (1081) with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":3183931081,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 57
National number is present: 3183931081
Removing leading + from phone number
Formatted phone number: 573183931081
==================================
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Interchanging country code, all numbers should be invalid now.
Processing phone number: +5521976923932 with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":55,"hasNationalNumber":true,"nationalNumber_":21976923932,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 55
National number is present: 21976923932
Removing leading + from phone number
Formatted phone number: 5521976923932
==================================
Processing phone number: 5561991474841 with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":5561991474841,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: false
Country code is present: 57
National number is present: 5561991474841
Removing leading + from phone number
Formatted phone number: 575561991474841
==================================
Processing phone number: 055 719 9174 8722 with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":571991748722,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: false
Country code is present: 57
National number is present: 571991748722
Removing leading + from phone number
Formatted phone number: 57571991748722
==================================
Processing phone number: 719 9174 8722 with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":71991748722,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: false
Country code is present: 57
National number is present: 71991748722
Removing leading + from phone number
Formatted phone number: 5771991748722
==================================
Processing phone number: 55 (719) (9174) (8722) with short code: CO
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":5571991748722,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: false
Country code is present: 57
National number is present: 5571991748722
Removing leading + from phone number
Formatted phone number: 575571991748722
==================================
Processing phone number: 573146339375 with short code: BR
phoneNumberProto: {"hasCountryCode":true,"countryCode_":55,"hasNationalNumber":true,"nationalNumber_":573146339375,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: false
Country code is present: 55
National number is present: 573146339375
Removing leading + from phone number
Formatted phone number: 55573146339375
==================================
Processing phone number: 3137145988 with short code: BR
phoneNumberProto: {"hasCountryCode":true,"countryCode_":55,"hasNationalNumber":true,"nationalNumber_":3137145988,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 55
National number is present: 3137145988
Removing leading + from phone number
Formatted phone number: 553137145988
==================================
Processing phone number: +573154417054 with short code: BR
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":3154417054,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 57
National number is present: 3154417054
Removing leading + from phone number
Formatted phone number: 573154417054
==================================
Processing phone number: 316537219731323123 with short code: BR
The string supplied is too long to be a phone number.
==================================
Processing phone number: 3013755140 with short code: BR
phoneNumberProto: {"hasCountryCode":true,"countryCode_":55,"hasNationalNumber":true,"nationalNumber_":3013755140,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: false
Country code is present: 55
National number is present: 3013755140
Removing leading + from phone number
Formatted phone number: 553013755140
==================================
Processing phone number: +57 31 5441 7054 with short code: BR
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":3154417054,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 57
National number is present: 3154417054
Removing leading + from phone number
Formatted phone number: 573154417054
==================================
Processing phone number: +57 (31) (8393) (1081) with short code: BR
phoneNumberProto: {"hasCountryCode":true,"countryCode_":57,"hasNationalNumber":true,"nationalNumber_":3183931081,"hasExtension":false,"extension_":"","hasItalianLeadingZero":false,"italianLeadingZero_":false,"hasNumberOfLeadingZeros":false,"numberOfLeadingZeros_":1,"hasRawInput":false,"rawInput_":"","hasCountryCodeSource":false,"countryCodeSource_":"UNSPECIFIED","hasPreferredDomesticCarrierCode":false,"preferredDomesticCarrierCode_":""}
Is phone number valid: true
Country code is present: 57
National number is present: 3183931081
Removing leading + from phone number
Formatted phone number: 573183931081
==================================
```

正如你所看到的，它不是 100%准确，但大多数时候它是正确的。根据您的应用，您可以决定这种精度水平对您来说是否足够。但是当你处理电话号码的时候，这个库就派上用场了。

有了这个库，您可以做更多的事情。我鼓励你去看看这个库的 Github repo(它是开源的),试着自己弄清楚这是否符合你的需要。

如果您有兴趣尝试一个现成的项目，您可以直接运行它，请前往 [my Github repo](https://github.com/contactsunny/NormalizePhoneNumbersPOC) ，在那里您将获得我从中取出这些代码片段的项目，您可以直接运行它并获得输出。