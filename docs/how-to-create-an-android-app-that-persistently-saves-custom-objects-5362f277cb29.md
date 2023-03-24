# 如何创建一个持久保存自定义对象的 Android 应用程序

> 原文：<https://levelup.gitconnected.com/how-to-create-an-android-app-that-persistently-saves-custom-objects-5362f277cb29>

几乎在任何应用程序中，你都需要在应用程序中持久保存自定义信息，然后加载并显示给用户。不幸的是，Android 并没有提供这种现成的功能。因此，在本教程中，我将向您展示如何创建一个用 gson 持久保存自定义对象的应用程序。

![](img/32ba327fd169aaddb15dfad010f7786d.png)

照片由 [Rami Al-zayat](https://unsplash.com/@rami_alzayat?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

我将使用的技术如下:

*   安卓工作室
*   安卓 28
*   gson
*   格拉德勒

本文主要基于[这个帖子](https://codinginflow.com/tutorials/android/save-arraylist-to-sharedpreferences-with-gson)。但是因为[一些 API 已经改变](https://developer.android.com/jetpack/androidx/migrate/class-mappings)，如果你按照上面提到的教程，你将无法创建一个应用程序。我花了一段时间才让它运转起来。为了节省你的时间，我为你创建了这个教程。我还添加了一些我上面提到的参考帖子中没有提到的信息，可能对初学者有帮助。

首先通过 android studio 创建一个新的应用程序，选择空的应用程序模板，并将其命名为`MyApplication`。然后将以下依赖项添加到您的`build.gradle`文件中:

```
dependencies {
 implementation 'com.android.support:design:28.0.+'  
 implementation 'com.google.code.gson:gson:2.8.5'
}
```

我们的 UI 需要一些标准 android SDK 没有提供的设计。因此我们必须添加设计依赖:
实现`com.android.support:design:28.0+`

Google 为您提供了 gson 方法，您可以使用这些方法来序列化和反序列化 java 对象:[https://github.com/google/gson](https://github.com/google/gson)

请注意，i.e. `com.android.support:design:28.9.+`中的数字 28 指的是您的目标 SDK 版本。确保这些始终是同一个数字。在我的例子中是 28。你的可能不同。为了知道你正在使用哪个目标版本，你也可以检查你的`gradle`-文件:

```
android {
    compileSdkVersion 28
    defaultConfig {
 ...
        targetSdkVersion 28
 ...
    }
}
```

一旦你添加了上面的依赖项，点击“同步”(在右上角)，这样 gradle 就可以下载需要的依赖项了。

下一步是设计我们的主要布局。我们需要两个输入字段和两个按钮:Insert 和 save，我们在其中输入一些数据(我们稍后将保存这些数据)。Insert 按钮将我们新创建的对象插入到主视图中显示的列表中。“保存”按钮会将其永久保存在我们的应用程序中。在`layout`文件夹中，在`activity_main.xml`文件中写入以下内容:

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="de.pandaquests.myapplication.MainActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_marginBottom="100dp"
        android:background="@android:color/darker_gray" />

    <EditText
        android:id="@+id/edittext_line_1"
        android:layout_width="180dp"
        android:layout_height="wrap_content"
        android:layout_alignParentStart="true"
        android:layout_alignParentBottom="true"
        android:layout_marginBottom="52dp"
        android:hint="Line 1" />

    <EditText
        android:id="@+id/edittext_line_2"
        android:layout_width="180dp"
        android:layout_height="wrap_content"
        android:layout_alignParentStart="true"
        android:layout_alignParentBottom="true"
        android:hint="Line 2" />

    <Button
        android:id="@+id/button_insert"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignTop="@id/edittext_line_1"
        android:layout_marginStart="13dp"
        android:layout_marginTop="23dp"
        android:layout_toEndOf="@id/edittext_line_1"
        android:text="insert" />

    <Button
        android:id="@+id/button_save"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignBaseline="@id/button_insert"
        android:layout_toEndOf="@+id/button_insert"
        android:text="save" />

</RelativeLayout>
```

接下来，我们为自定义元素创建 UI。自定义元素表示我们插入到列表中的数据。在`layout`文件夹中右键创建一个名为`example_item.xml`的文件，选择新建- >布局资源文件。写下`example_item.xml`作为名字，忽略其余的，因为我们将用下面的代码覆盖文件中的所有内容:

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.cardview.widget.CardView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_margin="4dp">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_margin="4dp">

        <TextView
            android:id="@+id/textview_line1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Line 1"
            android:textColor="@android:color/black"
            android:textSize="20sp"
            android:textStyle="bold" />

        <TextView
            android:id="@+id/textview_line_2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_below="@+id/textview_line1"
            android:layout_marginStart="8dp"
            android:text="Line 2"
            android:textSize="15sp" />

    </RelativeLayout>

</androidx.cardview.widget.CardView>
```

一旦我们完成了这些，我们将为我们的主要活动创建所需的功能。在`MainActivity.java`文件中写下以下内容:

```
package de.pandaquests.myapplication;

import android.content.SharedPreferences;
import android.os.Bundle;
import androidx.appcompat.app.AppCompatActivity;
import androidx.recyclerview.widget.LinearLayoutManager;
import androidx.recyclerview.widget.RecyclerView;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import com.google.gson.Gson;
import com.google.gson.reflect.TypeToken;

import java.lang.reflect.Type;
import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {

    ArrayList<ExampleItem> mExampleList;
    private RecyclerView mRecyclerView;
    private ExampleAdapter mAdapter;
    private RecyclerView.LayoutManager mLayoutManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        loadData();
        buildRecyclerView();
        setInsertButton();

        Button buttonSave = findViewById(R.id.button_save);
        buttonSave.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                saveData();
            }
        });
    }

    private void saveData() {
        SharedPreferences sharedPreferences = getSharedPreferences("shared preferences", MODE_PRIVATE);
        SharedPreferences.Editor editor = sharedPreferences.edit();
        Gson gson = new Gson();
        String json = gson.toJson(mExampleList);
        editor.putString("task list", json);
        editor.apply();
    }

    private void loadData() {
        SharedPreferences sharedPreferences = getSharedPreferences("shared preferences", MODE_PRIVATE);
        Gson gson = new Gson();
        String json = sharedPreferences.getString("task list", null);
        Type type = new TypeToken<ArrayList<ExampleItem>>() {}.getType();
        mExampleList = gson.fromJson(json, type);

        if (mExampleList == null) {
            mExampleList = new ArrayList<>();
        }
    }

    private void buildRecyclerView() {
        mRecyclerView = findViewById(R.id.recyclerview);
        mRecyclerView.setHasFixedSize(true);
        mLayoutManager = new LinearLayoutManager(this);
        mAdapter = new ExampleAdapter(mExampleList);

        mRecyclerView.setLayoutManager(mLayoutManager);
        mRecyclerView.setAdapter(mAdapter);
    }

    private void setInsertButton() {
        Button buttonInsert = findViewById(R.id.button_insert);
        buttonInsert.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                EditText line1 = findViewById(R.id.edittext_line_1);
                EditText line2 = findViewById(R.id.edittext_line_2);
                insertItem(line1.getText().toString(), line2.getText().toString());
            }
        });
    }

    private void insertItem(String line1, String line2) {
        mExampleList.add(new ExampleItem(line1, line2));
        mAdapter.notifyItemInserted(mExampleList.size());
    }
}
```

您可能会看到许多错误。但是不要担心，因为仍然有文件丢失。接下来我们创建`ExampleAdapter.java`。在您的项目文件夹(也是您的`MainActivity.java`文件所在的文件夹)中创建一个名为`ExampleAdapter.java`的文件

```
package de.pandaquests.myapplication;

import androidx.recyclerview.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.TextView;

import java.util.ArrayList;

public class ExampleAdapter extends RecyclerView.Adapter<ExampleAdapter.ExampleViewHolder> {
    private ArrayList<ExampleItem> mExampleList;

    public static class ExampleViewHolder extends RecyclerView.ViewHolder {
        public TextView mTextViewLine1;
        public TextView mTextViewLine2;

        public ExampleViewHolder(View itemView) {
            super(itemView);
            mTextViewLine1 = itemView.findViewById(R.id.textview_line1);
            mTextViewLine2 = itemView.findViewById(R.id.textview_line_2);
        }
    }

    public ExampleAdapter(ArrayList<ExampleItem> exampleList) {
        mExampleList = exampleList;
    }

    @Override
    public ExampleViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View v = LayoutInflater.from(parent.getContext()).inflate(R.layout.example_item, parent, false);
        ExampleViewHolder evh = new ExampleViewHolder(v);
        return evh;
    }

    @Override
    public void onBindViewHolder(ExampleViewHolder holder, int position) {
        ExampleItem currentItem = mExampleList.get(position);

        holder.mTextViewLine1.setText(currentItem.getLine1());
        holder.mTextViewLine2.setText(currentItem.getLine2());
    }

    @Override
    public int getItemCount() {
        return mExampleList.size();
    }
}
```

现在我们只需要我们的自定义对象。让我们在您的项目文件夹中创建`ExampleItem.java`文件，并粘贴以下代码:

```
package de.pandaquests.myapplication;
public class ExampleItem {
    private String mLine1;
    private String mLine2;

    public ExampleItem(String line1, String line2) {
        mLine1 = line1;
        mLine2 = line2;
    }

    public String getLine1() {
        return mLine1;
    }

    public String getLine2() {
        return mLine2;
    }
}
```

我们现在应该准备好了。点击 run，你应该会看到一个保存自定义数据的样例应用程序。

你可以从 [my gitHub](https://github.com/pandaquests/AndroidSaveData) 下载整个应用程序，并根据你的喜好进行调整。把它作为你自己项目的基础。

如果你使用了我提供的代码，然后告诉我你打算实施或已经实施的项目。我真的很想知道。再见。