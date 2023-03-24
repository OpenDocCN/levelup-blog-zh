# 如何使用 Koa 构建简单的 Web 应用程序

> 原文：<https://levelup.gitconnected.com/how-to-use-koa-to-build-simple-web-apps-cce430aa0cd6>

![](img/f0189119955e2e23d8ca9a28cc9761f9.png)

**现在在**[**http://jauyeung.net/subscribe/**](http://jauyeung.net/subscribe/)**订阅我的邮件列表。**

【https://twitter.com/AuMayeung[**在推特上关注我**](https://twitter.com/AuMayeung)

**Koa 是一个用于构建后端应用程序的简单 web 框架。默认情况下，它什么都没有。通过添加扩展默认功能的包，您可以得到您需要的东西。这使得构建应用程序变得容易，并使代码尽可能简单。**

**在本文中，我们将构建一个地址簿应用程序。用户可以添加、编辑和删除联系人，还可以在表格中查看他们。我们用 Koa 构建后端，用 React 构建简单的前端。**

**为了启动这个项目，我们为它创建了一个文件夹，并在其中创建了一个`backend`目录。**

# **后端**

**现在我们可以构建后端，我们运行`npm init`并通过输入默认值来回答问题。然后我们安装自己的软件包。Koa 什么都没有，所以我们需要安装一个请求体解析器、一个路由器和一个 CORS 插件来支持跨域请求。我们还需要为数据库添加库。**

**要安装所有这些包，运行`npm i @babel/cli @babel/core @babel/node @babel/preset-env @koa/cors koa-bodyparser koa-router sequelize sqlite3`。我们需要 Babel 包来运行最新的 JavaScript 特性。Sequelize 和 SQLite 分别是我们将要用到的 ORM 和数据库。Koa 包分别用于启用 CORS、解析 JSON 请求体和启用路由。**

**下次运行:**

```
npx sequelize-cli init
```

**这将创建数据库样板代码。**

**然后我们运行:**

```
npx sequelize-cli --name Contact --attributes firstName:string,lastName:string,address:string.city:string,region:string,country:string,postalCode:string,phone:string,email:string,age:number
```

**这将创建一个`Contacts`表，其字段和数据类型列在`attributes`选项中。**

**完成后，运行`npx sequelize-cli db:migrate`创建数据库。**

**接下来在`backend`文件夹的根目录下创建`app.js`,并添加:**

```
const Koa = require("koa");
const cors = require("[@koa/cors](http://twitter.com/koa/cors)");
const Router = require("koa-router");
const models = require("./models");
const bodyParser = require("koa-bodyparser");const app = new Koa();
app.use(bodyParser());
app.use(cors());
const router = new Router();router.get("/contacts", async (ctx, next) => {
  const contacts = await models.Contact.findAll();
  ctx.body = contacts;
});router.post("/contacts", async (ctx, next) => {
  const contact = await models.Contact.create(ctx.request.body);
  ctx.body = contact;
});router.put("/contacts/:id", async (ctx, next) => {
  const { id, ...body } = ctx.request.body;
  const contact = await models.Contact.update(body, { where: { id } });
  ctx.body = contact;
});router.delete("/contacts/:id", async (ctx, next) => {
  const id = ctx.params.id;
  await models.Contact.destroy({ where: { id } });
  ctx.body = {};
});app.use(router.routes()).use(router.allowedMethods());app.listen(3000);
```

**这是包含我们应用程序所有逻辑的文件。我们使用通过导入运行`sequelize-cli init`创建的`models`模块而创建的 Sequelize 模型。**

**然后我们通过添加`app.use(cors());`来启用 CORS，通过添加`app.use(bodyParser());`来启用 JSON 请求体解析。我们通过添加: `const router = new Router();`来添加路由器。**

**在 GET `contacts`路线中，我们得到所有的联系人。这个帖子是用来添加联系人的。PUT route 用于通过按 ID 查找来更新现有联系人。而删除路径用于通过 ID 删除联系人。**

**现在后端完成了。就这么简单。**

# **前端**

**首先，我们需要运行 Create React App 来搭建应用程序。我们运行`npx create-react-app address-book`来创建包含初始文件的 app 项目文件夹。该应用程序将有一个主页来显示联系人，并让我们打开一个模式来添加联系人。将有一个表，显示所有的联系人和编辑和删除按钮，每一行编辑或删除每个联系人。联系人将存储在一个中央 Redux 存储中，以便将联系人存储在一个中央位置，使他们易于访问。React 路由器将用于路由。联系人将保存在使用 JSON 服务器包产生的后端，位于[https://github.com/typicode/json-server](https://github.com/typicode/json-server)。**

**对于表单验证，您需要使用第三方库。Formik 和 [Yup](https://github.com/jquense/yup) 配合得非常好，让我们能够满足大多数表单验证需求。Formik 让我们构建表单、显示错误、处理表单值的变化，这是我们必须亲自做的另一件事。是的，让我们写一个模式来验证我们的表单域。它可以检查几乎任何东西，像电子邮件和内置功能的必填字段可用的共同验证码。它还可以检查依赖于其他字段的字段，比如依赖于国家的邮政编码格式。Bootstrap 表单可以无缝地与 Formik 和 Yup 一起使用。**

**一旦完成，我们必须安装一些库。为了安装我们上面提到的库，我们运行`npm i axios bootstrap formik react-bootstrap mobx mobx-react react-router-dom yup`。Axios 是我们用来向后端发出 HTTP 请求的 HTTP 客户端。`react-router-dom`是 React 路由器最新版本的包名。**

**现在我们已经安装了所有的库，我们可以开始构建应用程序了。所有文件都将在`src`文件夹中，除非另有说明。首先我们在 MobX 商店上工作。我们在`src`文件夹中创建一个名为`store.js`的文件，并添加以下内容:**

```
import { observable, action, decorate } from "mobx";class ContactsStore {
  contacts = [];setContacts(contacts) {
    this.contacts = contacts;
  }
}ContactsStore = decorate(ContactsStore, {
  contacts: observable,
  setContacts: action,
});export { ContactsStore };
```

**这是一个简单的存储联系人的存储库,`contacts`数组是我们存储整个应用程序的联系人的地方。`setContacts`函数让我们设置来自任何组件的联系人，我们将 this store 对象传递给该组件。**

**此区块:**

```
ContactsStore = decorate(ContactsStore, {
  contacts: observable,
  setContacts: action,
});
```

**将`ContactsStore`中的`contacts`数组指定为可被组件观察变化的实体。`setContacts`被指定为可用于设置商店中的`contacts` 数组的功能。**

**在`App.js`中，我们用以下内容替换现有内容:**

```
import React from "react";
import { Router, Route } from "react-router-dom";
import HomePage from "./HomePage";
import { createBrowserHistory as createHistory } from "history";
import Navbar from "react-bootstrap/Navbar";
import Nav from "react-bootstrap/Nav";
import "./App.css";
const history = createHistory();function App({ contactsStore }) {
  return (
    <div className="App">
      <Router history={history}>
        <Navbar bg="primary" expand="lg" variant="dark">
          <Navbar.Brand href="#home">Address Book App</Navbar.Brand>
          <Navbar.Toggle aria-controls="basic-navbar-nav" />
          <Navbar.Collapse id="basic-navbar-nav">
            <Nav className="mr-auto">
              <Nav.Link href="/">Home</Nav.Link>
            </Nav>
          </Navbar.Collapse>
        </Navbar>
        <Route
          path="/"
          exact
          component={props => (
            <HomePage {...props} contactsStore={contactsStore} />
          )}
        />
      </Router>
    </div>
  );
}export default App;
```

**我们将存储传递给任何需要它的组件，如`HomePage`，它将组件传递给`ContactForm`。**

**这是我们添加导航栏并显示 React 路由器路由的路线的地方。在`App.css`中，我们将现有代码替换为:**

```
.App {
  text-align: center;
}
```

**这会使文本居中。**

**接下来，我们建立我们的联系形式。这是我们的应用程序中逻辑最强的部分。我们创建一个名为`ContactForm.js`的文件，并添加:**

```
import React from "react";
import { Formik } from "formik";
import Form from "react-bootstrap/Form";
import Col from "react-bootstrap/Col";
import InputGroup from "react-bootstrap/InputGroup";
import Button from "react-bootstrap/Button";
import * as yup from "yup";
import { COUNTRIES } from "./exports";
import PropTypes from "prop-types";
import { addContact, editContact, getContacts } from "./requests";const schema = yup.object({
  firstName: yup.string().required("First name is required"),
  lastName: yup.string().required("Last name is required"),
  address: yup.string().required("Address is required"),
  city: yup.string().required("City is required"),
  region: yup.string().required("Region is required"),
  country: yup
    .string()
    .required("Country is required")
    .default("Afghanistan"),
  postalCode: yup
    .string()
    .when("country", {
      is: "United States",
      then: yup
        .string()
        .matches(/^[0-9]{5}(?:-[0-9]{4})?$/, "Invalid postal code"),
    })
    .when("country", {
      is: "Canada",
      then: yup
        .string()
        .matches(
          /^[A-Za-z]\d[A-Za-z][ -]?\d[A-Za-z]\d$/,
          "Invalid postal code"
        ),
    })
    .required(),
  phone: yup
    .string()
    .when("country", {
      is: country => ["United States", "Canada"].includes(country),
      then: yup
        .string()
        .matches(/^[2-9]\d{2}[2-9]\d{2}\d{4}$/, "Invalid phone nunber"),
    })
    .required(),
  email: yup
    .string()
    .email("Invalid email")
    .required("Email is required"),
  age: yup
    .number()
    .required("Age is required")
    .min(0, "Minimum age is 0")
    .max(200, "Maximum age is 200"),
});function ContactForm({
  edit,
  onSave,
  contact,
  onCancelAdd,
  onCancelEdit,
  contactsStore,
}) {
  const handleSubmit = async evt => {
    const isValid = await schema.validate(evt);
    if (!isValid) {
      return;
    }
    if (!edit) {
      await addContact(evt);
    } else {
      await editContact(evt);
    }
    const response = await getContacts();
    contactsStore.setContacts(response.data);
    onSave();
  }; return (
    <div className="form">
      <Formik
        validationSchema={schema}
        onSubmit={handleSubmit}
        initialValues={contact || {}}
      >
        {({
          handleSubmit,
          handleChange,
          handleBlur,
          values,
          touched,
          isInvalid,
          errors,
        }) => (
          <Form noValidate onSubmit={handleSubmit}>
            <Form.Row>
              <Form.Group as={Col} md="12" controlId="firstName">
                <Form.Label>First name</Form.Label>
                <Form.Control
                  type="text"
                  name="firstName"
                  placeholder="First Name"
                  value={values.firstName || ""}
                  onChange={handleChange}
                  isInvalid={touched.firstName && errors.firstName}
                />
                <Form.Control.Feedback type="invalid">
                  {errors.firstName}
                </Form.Control.Feedback>
              </Form.Group>
              <Form.Group as={Col} md="12" controlId="lastName">
                <Form.Label>Last name</Form.Label>
                <Form.Control
                  type="text"
                  name="lastName"
                  placeholder="Last Name"
                  value={values.lastName || ""}
                  onChange={handleChange}
                  isInvalid={touched.firstName && errors.lastName}
                /> <Form.Control.Feedback type="invalid">
                  {errors.lastName}
                </Form.Control.Feedback>
              </Form.Group>
              <Form.Group as={Col} md="12" controlId="address">
                <Form.Label>Address</Form.Label>
                <InputGroup>
                  <Form.Control
                    type="text"
                    placeholder="Address"
                    aria-describedby="inputGroupPrepend"
                    name="address"
                    value={values.address || ""}
                    onChange={handleChange}
                    isInvalid={touched.address && errors.address}
                  />
                  <Form.Control.Feedback type="invalid">
                    {errors.address}
                  </Form.Control.Feedback>
                </InputGroup>
              </Form.Group>
            </Form.Row>
            <Form.Row>
              <Form.Group as={Col} md="12" controlId="city">
                <Form.Label>City</Form.Label>
                <Form.Control
                  type="text"
                  placeholder="City"
                  name="city"
                  value={values.city || ""}
                  onChange={handleChange}
                  isInvalid={touched.city && errors.city}
                /> <Form.Control.Feedback type="invalid">
                  {errors.city}
                </Form.Control.Feedback>
              </Form.Group>
              <Form.Group as={Col} md="12" controlId="region">
                <Form.Label>Region</Form.Label>
                <Form.Control
                  type="text"
                  placeholder="Region"
                  name="region"
                  value={values.region || ""}
                  onChange={handleChange}
                  isInvalid={touched.region && errors.region}
                />
                <Form.Control.Feedback type="invalid">
                  {errors.region}
                </Form.Control.Feedback>
              </Form.Group> <Form.Group as={Col} md="12" controlId="country">
                <Form.Label>Country</Form.Label>
                <Form.Control
                  as="select"
                  placeholder="Country"
                  name="country"
                  onChange={handleChange}
                  value={values.country || ""}
                  isInvalid={touched.region && errors.country}
                >
                  {COUNTRIES.map(c => (
                    <option key={c} value={c}>
                      {c}
                    </option>
                  ))}
                </Form.Control>
                <Form.Control.Feedback type="invalid">
                  {errors.country}
                </Form.Control.Feedback>
              </Form.Group> <Form.Group as={Col} md="12" controlId="postalCode">
                <Form.Label>Postal Code</Form.Label>
                <Form.Control
                  type="text"
                  placeholder="Postal Code"
                  name="postalCode"
                  value={values.postalCode || ""}
                  onChange={handleChange}
                  isInvalid={touched.postalCode && errors.postalCode}
                /> <Form.Control.Feedback type="invalid">
                  {errors.postalCode}
                </Form.Control.Feedback>
              </Form.Group> <Form.Group as={Col} md="12" controlId="phone">
                <Form.Label>Phone</Form.Label>
                <Form.Control
                  type="text"
                  placeholder="Phone"
                  name="phone"
                  value={values.phone || ""}
                  onChange={handleChange}
                  isInvalid={touched.phone && errors.phone}
                /> <Form.Control.Feedback type="invalid">
                  {errors.phone}
                </Form.Control.Feedback>
              </Form.Group> <Form.Group as={Col} md="12" controlId="email">
                <Form.Label>Email</Form.Label>
                <Form.Control
                  type="text"
                  placeholder="Email"
                  name="email"
                  value={values.email || ""}
                  onChange={handleChange}
                  isInvalid={touched.email && errors.email}
                /> <Form.Control.Feedback type="invalid">
                  {errors.email}
                </Form.Control.Feedback>
              </Form.Group> <Form.Group as={Col} md="12" controlId="age">
                <Form.Label>Age</Form.Label>
                <Form.Control
                  type="text"
                  placeholder="Age"
                  name="age"
                  value={values.age || ""}
                  onChange={handleChange}
                  isInvalid={touched.age && errors.age}
                /> <Form.Control.Feedback type="invalid">
                  {errors.age}
                </Form.Control.Feedback>
              </Form.Group>
            </Form.Row>
            <Button type="submit" style={{ marginRight: "10px" }}>
              Save
            </Button>
            <Button type="button" onClick={edit ? onCancelEdit : onCancelAdd}>
              Cancel
            </Button>
          </Form>
        )}
      </Formik>
    </div>
  );
}ContactForm.propTypes = {
  edit: PropTypes.bool,
  onSave: PropTypes.func,
  onCancelAdd: PropTypes.func,
  onCancelEdit: PropTypes.func,
  contact: PropTypes.object,
  contactsStore: PropTypes.object
};export default ContactForm;
```

**我们从`HomePage`组件传入`contactsStore`，允许我们使用`contactsStore`中的数据和函数。**

**对于表单验证，我们使用 Formik 来帮助构建我们的联系表单，我们的 Boostrap `Form`组件嵌套在`Formik`组件中，这样我们就可以使用 Formik 的`handleChange`、`handleSubmit`、`values`、`touched`和`errors`参数。`handleChange`是一个让我们不用自己写代码就能从输入中更新表单域数据的函数。`handleSubmit`是我们传递给`Formik`组件的`onSubmit`处理程序的函数。函数中的参数是我们输入的数据，字段名作为键，由每个字段的`name`属性定义，每个字段的值作为那些键的值。注意，在每个`value`属性中，我们都有`||''`，这样我们就不会得到未定义的值，并防止不受控制的表单警告被触发。**

**为了显示表单验证消息，我们必须将`isInvalid`属性传递给每个`Form.Control`组件。`schema`对象是`Formik`进行表单验证时要检查的对象。`required`函数中的参数是验证错误消息。`matches`、`min`和`max`函数的第二个参数也是验证消息。**

**`ContactForm`函数的参数是 props，我们将从稍后构建的`HomePage`组件传入。`handleSubmit`功能检查数据是否有效，如果有效，则根据是添加还是编辑联系人进行保存。然后，当保存成功时，我们在存储中设置联系人并调用`onSave` prop，这是一个关闭表单所在模态的函数。模式将在主页中定义。**

**接下来，我们创建一个名为`exports.js`的文件，并将:**

```
export const COUNTRIES = [
  "Afghanistan",
  "Albania",
  "Algeria",
  "Andorra",
  "Angola",
  "Anguilla",
  "Antigua &amp; Barbuda",
  "Argentina",
  "Armenia",
  "Aruba",
  "Australia",
  "Austria",
  "Azerbaijan",
  "Bahamas",
  "Bahrain",
  "Bangladesh",
  "Barbados",
  "Belarus",
  "Belgium",
  "Belize",
  "Benin",
  "Bermuda",
  "Bhutan",
  "Bolivia",
  "Bosnia &amp; Herzegovina",
  "Botswana",
  "Brazil",
  "British Virgin Islands",
  "Brunei",
  "Bulgaria",
  "Burkina Faso",
  "Burundi",
  "Cambodia",
  "Cameroon",
  "Canada",
  "Cape Verde",
  "Cayman Islands",
  "Chad",
  "Chile",
  "China",
  "Colombia",
  "Congo",
  "Cook Islands",
  "Costa Rica",
  "Cote D Ivoire",
  "Croatia",
  "Cruise Ship",
  "Cuba",
  "Cyprus",
  "Czech Republic",
  "Denmark",
  "Djibouti",
  "Dominica",
  "Dominican Republic",
  "Ecuador",
  "Egypt",
  "El Salvador",
  "Equatorial Guinea",
  "Estonia",
  "Ethiopia",
  "Falkland Islands",
  "Faroe Islands",
  "Fiji",
  "Finland",
  "France",
  "French Polynesia",
  "French West Indies",
  "Gabon",
  "Gambia",
  "Georgia",
  "Germany",
  "Ghana",
  "Gibraltar",
  "Greece",
  "Greenland",
  "Grenada",
  "Guam",
  "Guatemala",
  "Guernsey",
  "Guinea",
  "Guinea Bissau",
  "Guyana",
  "Haiti",
  "Honduras",
  "Hong Kong",
  "Hungary",
  "Iceland",
  "India",
  "Indonesia",
  "Iran",
  "Iraq",
  "Ireland",
  "Isle of Man",
  "Israel",
  "Italy",
  "Jamaica",
  "Japan",
  "Jersey",
  "Jordan",
  "Kazakhstan",
  "Kenya",
  "Kuwait",
  "Kyrgyz Republic",
  "Laos",
  "Latvia",
  "Lebanon",
  "Lesotho",
  "Liberia",
  "Libya",
  "Liechtenstein",
  "Lithuania",
  "Luxembourg",
  "Macau",
  "Macedonia",
  "Madagascar",
  "Malawi",
  "Malaysia",
  "Maldives",
  "Mali",
  "Malta",
  "Mauritania",
  "Mauritius",
  "Mexico",
  "Moldova",
  "Monaco",
  "Mongolia",
  "Montenegro",
  "Montserrat",
  "Morocco",
  "Mozambique",
  "Namibia",
  "Nepal",
  "Netherlands",
  "Netherlands Antilles",
  "New Caledonia",
  "New Zealand",
  "Nicaragua",
  "Niger",
  "Nigeria",
  "Norway",
  "Oman",
  "Pakistan",
  "Palestine",
  "Panama",
  "Papua New Guinea",
  "Paraguay",
  "Peru",
  "Philippines",
  "Poland",
  "Portugal",
  "Puerto Rico",
  "Qatar",
  "Reunion",
  "Romania",
  "Russia",
  "Rwanda",
  "Saint Pierre &amp; Miquelon",
  "Samoa",
  "San Marino",
  "Satellite",
  "Saudi Arabia",
  "Senegal",
  "Serbia",
  "Seychelles",
  "Sierra Leone",
  "Singapore",
  "Slovakia",
  "Slovenia",
  "South Africa",
  "South Korea",
  "Spain",
  "Sri Lanka",
  "St Kitts &amp; Nevis",
  "St Lucia",
  "St Vincent",
  "St. Lucia",
  "Sudan",
  "Suriname",
  "Swaziland",
  "Sweden",
  "Switzerland",
  "Syria",
  "Taiwan",
  "Tajikistan",
  "Tanzania",
  "Thailand",
  "Timor L'Este",
  "Togo",
  "Tonga",
  "Trinidad &amp; Tobago",
  "Tunisia",
  "Turkey",
  "Turkmenistan",
  "Turks &amp; Caicos",
  "Uganda",
  "Ukraine",
  "United Arab Emirates",
  "United Kingdom",
  "United States",
  "United States Minor Outlying Islands",
  "Uruguay",
  "Uzbekistan",
  "Venezuela",
  "Vietnam",
  "Virgin Islands (US)",
  "Yemen",
  "Zambia",
  "Zimbabwe",
];
```

**这些是表单中国家字段的国家。**

**在`HomePage.js`中，我们输入:**

```
import React from "react";
import { useState, useEffect } from "react";
import Table from "react-bootstrap/Table";
import ButtonToolbar from "react-bootstrap/ButtonToolbar";
import Button from "react-bootstrap/Button";
import Modal from "react-bootstrap/Modal";
import ContactForm from "./ContactForm";
import "./HomePage.css";
import { getContacts, deleteContact } from "./requests";
import { observer } from "mobx-react";function HomePage({ contactsStore }) {
  const [openAddModal, setOpenAddModal] = useState(false);
  const [openEditModal, setOpenEditModal] = useState(false);
  const [initialized, setInitialized] = useState(false);
  const [selectedContact, setSelectedContact] = useState({});const openModal = () => {
    setOpenAddModal(true);
  };const closeModal = () => {
    setOpenAddModal(false);
    setOpenEditModal(false);
    getData();
  };const cancelAddModal = () => {
    setOpenAddModal(false);
  };const editContact = contact => {
    setSelectedContact(contact);
    setOpenEditModal(true);
  };const cancelEditModal = () => {
    setOpenEditModal(false);
  };const getData = async () => {
    const response = await getContacts();
    contactsStore.setContacts(response.data);
    setInitialized(true);
  };const deleteSelectedContact = async id => {
    await deleteContact(id);
    getData();
  };useEffect(() => {
    if (!initialized) {
      getData();
    }
  });return (
    <div className="home-page">
      <h1>Contacts</h1>
      <Modal show={openAddModal} onHide={closeModal}>
        <Modal.Header closeButton>
          <Modal.Title>Add Contact</Modal.Title>
        </Modal.Header>
        <Modal.Body>
          <ContactForm
            edit={false}
            onSave={closeModal.bind(this)}
            onCancelAdd={cancelAddModal}
            contactsStore={contactsStore}
          />
        </Modal.Body>
      </Modal><Modal show={openEditModal} onHide={closeModal}>
        <Modal.Header closeButton>
          <Modal.Title>Edit Contact</Modal.Title>
        </Modal.Header>
        <Modal.Body>
          <ContactForm
            edit={true}
            onSave={closeModal.bind(this)}
            contact={selectedContact}
            onCancelEdit={cancelEditModal}
            contactsStore={contactsStore}
          />
        </Modal.Body>
      </Modal>
      <ButtonToolbar onClick={openModal}>
        <Button variant="outline-primary">Add Contact</Button>
      </ButtonToolbar>
      <br />
      <Table striped bordered hover>
        <thead>
          <tr>
            <th>First Name</th>
            <th>Last Name</th>
            <th>Address</th>
            <th>City</th>
            <th>Country</th>
            <th>Postal Code</th>
            <th>Phone</th>
            <th>Email</th>
            <th>Age</th>
            <th>Edit</th>
            <th>Delete</th>
          </tr>
        </thead>
        <tbody>
          {contactsStore.contacts.map(c => (
            <tr key={c.id}>
              <td>{c.firstName}</td>
              <td>{c.lastName}</td>
              <td>{c.address}</td>
              <td>{c.city}</td>
              <td>{c.country}</td>
              <td>{c.postalCode}</td>
              <td>{c.phone}</td>
              <td>{c.email}</td>
              <td>{c.age}</td>
              <td>
                <Button
                  variant="outline-primary"
                  onClick={editContact.bind(this, c)}
                >
                  Edit
                </Button>
              </td>
              <td>
                <Button
                  variant="outline-primary"
                  onClick={deleteSelectedContact.bind(this, c.id)}
                >
                  Delete
                </Button>
              </td>
            </tr>
          ))}
        </tbody>
      </Table>
    </div>
  );
}
export default observer(HomePage);
```

**请注意，我们使用了`export default observer(HomePage);`而不是`export default HomePage;`。我们需要通过`observer`函数调用包装`HomePage`，以便来自存储的最新数据将被传播到这个组件中。**

**它有显示联系人的表格和添加、编辑和删除联系人的按钮。它在第一次加载时获取数据，在`useEffect`的回调函数中调用`getData`函数。每次渲染都会调用`useEffect`的回调，所以我们想设置一个`initialized`标志，并检查它是否只在`true`时才加载。**

**注意，我们将这个组件中的所有道具传递给了`ContactForm`组件。要将参数传递给`onClick`处理函数，我们必须调用函数上的`bind`，并将函数的参数作为第二个参数传递给`bind`。例如，在这个文件中，我们有`editContact.bind(this, c)`，其中`c`是联系对象。`editContact`功能定义如下:**

```
const editContact = (contact) => {
    setSelectedContact(contact);
    setOpenEditModal(true);
  }
```

**`c`是我们传入的`contact`参数。**

**接下来，我们创建一个名为`HomePage.css`的文件，并将:**

```
.home-page {
  padding: 20px;
}
```

**这给我们的主页增加了填充。**

**在`index.js`中，我们将现有代码替换为:**

```
import React from "react";
import ReactDOM from "react-dom";
import "./index.css";
import App from "./App";
import * as serviceWorker from "./serviceWorker";
import { ContactsStore } from "./store";
const contactsStore = new ContactsStore();ReactDOM.render(
  <App contactsStore={contactsStore} />,
  document.getElementById("root")
);// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: [https://bit.ly/CRA-PWA](https://bit.ly/CRA-PWA)
serviceWorker.unregister();
```

**然后我们创建一个名为`requests.js`的文件，并添加:**

```
const APIURL = '[http://localhost:3000'](http://localhost:3000');
const axios = require('axios');
export const getContacts = () => axios.get(`${APIURL}/contacts`);
export const addContact = (data) => axios.post(`${APIURL}/contacts`, data);
export const editContact = (data) => axios.put(`${APIURL}/contacts/${data.id}`, data);
export const deleteContact = (id) => axios.delete(`${APIURL}/contacts/${id}`);
```

**这些函数向后端发出 HTTP 请求，以保存和删除联系人。**

**最后，在`public/index.html`中，我们将现有代码替换为:**

```
<!DOCTYPE html>
<html lang="en"><head>
  <meta charset="utf-8" />
  <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <meta name="theme-color" content="#000000" />
  <meta name="description" content="Web site created using create-react-app" />
  <link rel="apple-touch-icon" href="logo192.png" />
  <link rel="manifest" crossorigin="use-credentials" href="%PUBLIC_URL%/manifest.json" /><!--
      manifest.json provides metadata used when your web app is installed on a
      user's mobile device or desktop. See [https://developers.google.com/web/fundamentals/web-app-manifest/](https://developers.google.com/web/fundamentals/web-app-manifest/)
    -->
  <!--
      Notice the use of %PUBLIC_URL% in the tags above.
      It will be replaced with the URL of the `public` folder during the build.
      Only files inside the `public` folder can be referenced from the HTML.Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
      work correctly both with client-side routing and a non-root public URL.
      Learn how to configure a non-root public URL by running `npm run build`.
    -->
  <title>React Address Book App</title>
  <link rel="stylesheet" href="[https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css](https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css)"
    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous" />
</head><body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
  <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
</body></html>
```

**这将更改标题并添加引导样式表。**

**写完所有代码后，我们就可以运行我们的应用程序了。在运行任何东西之前，通过运行`npm i -g nodemon`来安装`nodemon`，这样当文件改变时，我们就不必自己重启后端了。**

**然后通过运行`backend`文件夹中的`npm start`和`frontend`文件夹中的`npm start`来运行后端，然后如果要求您从不同的端口运行它，请选择“是”。**

**最后，我们有以下内容:**

**![](img/f0189119955e2e23d8ca9a28cc9761f9.png)****![](img/751d2681035b39a9bbd8da8b261ec55b.png)****![](img/9d3348846e2374867d8616963dcd66e0.png)**