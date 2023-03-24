# 反应:上传图像

> 原文：<https://levelup.gitconnected.com/react-uploading-images-a224e0fb4561>

![](img/c91cf9a0601d31c0613f9cd32ee239a5.png)

React.js

继我之前的帖子，我想分享如何从你的 React 前端上传图片到你的服务器。它只需要几行代码，但是如果一切都没有排列好，你将会非常恼火。

> 这个例子是用 Ionic 构建的。这种形式对于 React 框架来说是通用的，但是对于 Ionic 来说渲染头像是独特的

为了简单起见，我们将构建一个组件，它既可以呈现您的图像，又允许您上传新的图像。姑且称之为`Avatar.tsx`

首先，让我们渲染一个图像。

*/组件/Avatar.tsx*

```
import React, { useState } from "react";
import { IonText, IonAvatar } from "@ionic/react";

interface AvatarProps {
  imageURL: string;
  userID: string;
}

const Avatar: React.FC<AvatarProps> = ({ imageURL, userID }) => {
  const [url, setURL] = useState<string>("https://images.ctfassets.net/0jkr5d02o14t/4Tsq7upvRUHBdW4HwzeNEy/7f140b351543035dae54015d634c0df4/placeholder.png?h=250");

  const placeholder = "https://images.ctfassets.net/0jkr5d02o14t/4Tsq7upvRUHBdW4HwzeNEy/7f140b351543035dae54015d634c0df4/placeholder.png?h=250";

  if (imageURL && url === placeholder) {
    setURL(imageURL);
  }

  return (
    <div>
      <IonAvatar className="avatar">
        <img src={`${url}`} alt="avatar" />
      </IonAvatar> <IonText>
            className="edit-pic"
            onClick={}
          >
            Change Profile Picture
          </h5>
      </IonText>
    </div>
  );
};

export default Avatar;
```

因此，我将从父组件中获取的一些用户数据传递给这个组件。如果数据不包含`imageURL`，那么它将呈现一个占位符头像。

![](img/bfbb4360fb7dc04c6323a86716bf1f15.png)

呈现占位符

现在来设置表单。我们希望有条件地呈现文件的输入字段，因此我们将创建一个带有一些状态逻辑的开关。

*/组件/头像*

```
**const [changeProfilePicture, setChangeProfilePicture] = useState<boolean>(
    false
  );**
...
return (
    <div>
      <IonAvatar className="avatar">
        <img src={`${url}`} alt="avatar" />
      </IonAvatar>
     ** 
        <form className="image-upload-container">
          {changeProfilePicture ? (
            <input
              name="profilePicture"
              type="file"
              accept="image/png, image/jpeg"
              onChange={(e: any) => {
                handleFileUpload(e.target.files ? e.target.files[0] : url);
              }}
            />
          ) : null}** **<IonText>
           <h5
            className="edit-pic"
            onClick={() => setChangeProfilePicture(!changeProfilePicture)}
          >
            Change Profile Picture
          </h5>
        </IonText>
      </form>** </div>
  );
};
...
```

现在创建提交处理程序。要通过 HTTP 发送文件，您需要创建一个新的 FormData 对象，然后将文件附加到该对象。如果您使用的是`fetch`，最好不要在 POST 请求中添加标题，这样`fetch`就会为您生成标题。

> 非常重要—您必须使用您在服务器逻辑中使用的相同键将文件追加到表单数据中。对于这个例子，我使用了 multer singleUpload 来上传一个“图像”，如果你没有匹配这些图像将不会上传。

```
const handleFileUpload = async (file: any) => {

    const imageData = new FormData();
    imageData.append("image", file);

    const url = `http://localhost:4000/users/${userID}/add-profile-picture`;

    const config = {
      method: "POST",
      body: imageData,
    };

    try {
      const req = await fetch(url, config);
      if (req.ok) {
        const res = await req.json();
        console.log(res);
        if (res.success) {
          setURL(res.user.profilePicture);
        }
      }
    } catch (err) {
      console.log(err);
    }
  };
```