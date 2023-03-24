# 引导程序 5 —表单验证

> 原文：<https://levelup.gitconnected.com/bootstrap-5-form-validation-3381458429ed>

![](img/18e00c44122186b6b5d50136eef5bf60.png)

Tania Melnyczuk 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

**写这篇文章时，Bootstrap 5 还处于 alpha 版本，可能会有变化。**

Bootstrap 是任何 JavaScript 应用程序的流行 UI 库。

在本文中，我们将看看如何用 Bootstrap 5 添加表单验证。

# 浏览器默认值

我们可以使用浏览器默认的表单验证。

例如，我们可以写:

```
<form class="row g-3">
  <div class="col-md-4">
    <label for="first-name" class="form-label">First name</label>
    <input type="text" class="form-control" id="first-name" value="james" required>
  </div> <div class="col-md-4">
    <label for="last-name" class="form-label">Last name</label>
    <input type="text" class="form-control" id="last-name" value="smith" required>
  </div> <div class="col-md-4">
    <label for="username" class="form-label">Username</label>
    <div class="input-group">
      <span class="input-group-text">@</span>
      <input type="text" class="form-control" id="username" required>
    </div>
  </div> <div class="col-md-6">
    <label for="city" class="form-label">City</label>
    <input type="text" class="form-control" id="city" required>
  </div> <div class="col-md-3">
    <label for="region" class="form-label">Region</label>
    <select class="form-select" id="region" required>
      <option selected disabled value="">Choose...</option>
      <option>...</option>
    </select>
  </div> <div class="col-md-3">
    <label for="postal-code" class="form-label">Postal Code</label>
    <input type="text" class="form-control" id="postal-code" required>
  </div> <div class="col-12">
    <div class="form-check">
      <input class="form-check-input" type="checkbox" value="" id="agree" required>
      <label class="form-check-label" for="agree">
        I agree
      </label>
    </div>
  </div> <div class="col-12">
    <button class="btn btn-primary" type="submit">Sign up</button>
  </div>
</form>
```

我们添加带有`require`属性的字段，为表单验证添加检查。

因为表单本身没有`novalidate`属性，所以将使用浏览器验证。

# 验证类

可以将`.is-invalid`、`.is-valid`、`.valid-feedback`和`.invalid-feedback`类应用于表单域，以向用户显示不同的样式和消息来表明有效性。

例如，我们可以写:

```
<form class="row g-3">
  <div class="col-md-4">
    <label for="first-name" class="form-label">First name</label>
    <input type="text" class="form-control is-valid" id="first-name" value="james" required>
    <div class="valid-feedback">
      Looks good!
    </div>
  </div> <div class="col-md-4">
    <label for="last-name" class="form-label">Last name</label>
    <input type="text" class="form-control is-valid" id="last-name" value="smith" required>
    <div class="valid-feedback">
      Looks good!
    </div>
  </div> <div class="col-md-4">
    <label for="username" class="form-label">Username</label>
    <div class="input-group">
      <span class="input-group-text">@</span>
      <input type="text" class="form-control is-invalid" id="username" required>
      <div class="invalid-feedback">
        Username is required.
      </div>
    </div>
  </div> <div class="col-md-6">
    <label for="city" class="form-label">City</label>
    <input type="text" class="form-control is-invalid" id="city" required>
    <div class="invalid-feedback">
      City is requird
    </div>
  </div> <div class="col-md-3">
    <label for="region" class="form-label">Region</label>
    <select class="form-select is-invalid" id="region" required>
      <option selected disabled value="">Choose...</option>
      <option>...</option>
    </select>
    <div class="invalid-feedback">
      Region is required
    </div>
  </div> <div class="col-md-3">
    <label for="postal-code" class="form-label">Postal Code</label>
    <input type="text" class="form-control is-invalid" id="postal-code" required>
    <div class="invalid-feedback">
      Postal code is required
    </div>
  </div> <div class="col-12">
    <div class="form-check">
      <input class="form-check-input is-invalid" type="checkbox" value="" id="agree" required>
      <label class="form-check-label" for="agree">
        I agree
      </label>
      <div class="invalid-feedback">
        You must agree before submitting.
      </div>
    </div>
  </div> <div class="col-12">
    <button class="btn btn-primary" type="submit">Sign up</button>
  </div>
</form>
```

添加表单验证样式。

如果字段无效，我们有`invalid-feedback`类来显示消息。

`valid-feedback`类用于显示有效字段的消息。

`is-valid`类用于输入，表示它们是有效的。

`is-invalid`用于显示无效的字段。

# 支持的元素

可以用`form-control`类将验证样式添加到输入、文本区域中。

使用`.form-select`类的 Select 元素也可以应用验证样式。

`.form-check` s 和`.form-file`也可以应用样式。

例如，我们可以写:

```
<form class="was-validated">
  <div class="mb-3">
    <label for="textarea" class="form-label">Textarea</label>
    <textarea class="form-control is-invalid" id="textarea" placeholder="Required example textarea" required></textarea>
    <div class="invalid-feedback">
      text is required
    </div>
  </div><div class="form-check mb-3">
    <input type="checkbox" class="form-check-input" id="checkbox" required>
    <label class="form-check-label" for="checkbox">agree</label>
    <div class="invalid-feedback">invalid checkbox</div>
  </div><div class="form-check">
    <input type="radio" class="form-check-input" id="apple" name="radio-stacked" required>
    <label class="form-check-label" for="radio">apple</label>
  </div>

  <div class="form-check mb-3">
    <input type="radio" class="form-check-input" id="orange" name="radio-stacked" required>
    <label class="form-check-label" for="orange">orange</label>
    <div class="invalid-feedback">More example invalid feedback text</div>
  </div> <div class="mb-3">
    <select class="form-select" required>
      <option value="">Open this select menu</option>
      <option value="1">One</option>
      <option value="2">Two</option>
      <option value="3">Three</option>
    </select>
    <div class="invalid-feedback">invalid select</div>
  </div> <div class="form-file mb-3">
    <input type="file" class="form-file-input" id="file" required>
    <label class="form-file-label" for="file">
      <span class="form-file-text">Choose file...</span>
      <span class="form-file-button">Browse</span>
    </label>
    <div class="invalid-feedback">invalid file</div>
  </div> <div class="mb-3">
    <button class="btn btn-primary" type="submit" disabled>Submit</button>
  </div>
</form>
```

`was-validated`应用于表单。

然后我们将`is-invalid`和`invalid-feedback`类添加到字段和消息中。

一切都会是红色的。

输入和文本区域将带有感叹号。

![](img/89d7326ae569375aae5a1d578d56a0e9.png)

照片由 [Augustine Fou](https://unsplash.com/@augustinefou?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

# 结论

我们可以用 Bootstrap 5 提供的各种类来添加验证样式。