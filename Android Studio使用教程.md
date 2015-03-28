# Android Studio使用

## 1. 导入第三方jar包及开源库

> ###导入本地jar

将jar文件拷贝至libs，右键点击jar文件，选择`add as a library` 即可导入，在模块的build.gradle的compile会增加

	compile files('libs/butterknife-6.1.0.jar')

如果builder.gradle中已有

	compile fileTree(include: ['*.jar'], dir: 'libs')

就不需要再 add 了，Sync 一下 gradle 就可以了。

> ###添加远程开源库

可以下载开源库，进入模块的build.gradle 文件，添加如下图的内容即可：

![import libs](http://i.imgur.com/QNotIIz.png)

添加的内容一般会在开源库的readme.md中有说明。

> ###添加本地开源库

将开源库下载下来，放置在与 app 目录同级的目录下，然后编辑 setting.gradle 文件，加入":开源库文件夹名"。如：在 app 同级的目录下放置了 volley 的开源库文件夹，然后编辑  setting.gradle，内容改为：

	include ':app', ":volley"

然后再回到你的 app 目录下的 build.gradle 文件，在 dependencies { }节点下加入：

	compile project(':volley')

之后就可以在代码中引用那个保存在你本地的开源库（其实叫Module）


##2. 常用插件

[常用插件大全](https://plugins.jetbrains.com/?androidstudio)

> * Butterknife Zelezny
> * Parcelable Code Generator
> * Prettify  
> * ADB Idea  

###Butterknife Zelezny  
- 功能：专注于Android系统的View注入框架
- 下载：[Android ButterKnife Zelezny](https://plugins.jetbrains.com/plugin/7369?pr=androidstudio)   
- 安装：Setting -> Preferences -> Plugins -> Browse repositories，查找Butterknife Zelezny，或下载后从磁盘安装    
- 使用：  
	1. 确保最新的Bufferfknife jar文件导入classpath  
	2. 右键选择代码中的layout引用，然后选择`Generate`或直接按下`alt+Insert`，再选择`Generate ButterKnife Injections`
	3. 在打开的对话框中选择需要注解的组件，也可以给Adapter设置一个ViewHolder
	4. 点击`Confirm`就会生成组件的注解代码
- 特性
	- 支持Activity中的View注入
	
			class ExampleActivity extends Activity {
				@InjectView(R.id.title) TextView title;
				@InjectView(R.id.subtitle) TextView subtitle;
				@InjectView(R.id.footer) TextView footer;
	
				@Override public void onCreate(Bundle savedInstanceState) {
	  				super.onCreate(savedInstanceState);
	  				setContentView(R.layout.simple_activity);
	  				ButterKnife.inject(this);
	  				// TODO Use "injected" views...
				}
			}

	- 支持View中的View注入
	
			public class FancyFragment extends Fragment {
				@InjectView(R.id.button1) Button button1;
				@InjectView(R.id.button2) Button button2;
				
				@Override
				View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
				  View view = inflater.inflate(R.layout.fancy_fragment, container, false);
				  ButterKnife.inject(this, view);
				  // TODO Use "injected" views...

				  return view;
				}
			}

	- 支持ViewHolder中的注入
	
			public class MyAdapter extends BaseAdapter {
			  @Override public View getView(int position, View view, ViewGroup parent) {
			    ViewHolder holder;
			    if (view != null) {
			      holder = (ViewHolder) view.getTag();
			    } else {
			      view = inflater.inflate(R.layout.whatever, parent, false);
			      holder = new ViewHolder(view);
			      view.setTag(holder);
			    }
			
			    holder.name.setText("John Doe");
			    // etc...
			
			    return convertView;
			  }
			
			  static class ViewHolder {
			    @InjectView(R.id.title) TextView name;
			    @InjectView(R.id.job_title) TextView jobTitle;
			
			    public ViewHolder(View view) {
			      ButterKnife.inject(this, view);
			    }
			  }
			}

	- 支持View事件回调函数注入
	
			View: @OnLongClick and @OnFocusChanged  
			TextView: @OnEditorAction  
			AdapterView: @OnItemClick and @OnItemLongClick  
			CompoundButton: @OnCheckedChanged  

		示例代码：

			// 带有 Button 参数
			@OnClick(R.id.submit)
			public void sayHi(Button button) {
			  button.setText("Hello!");
			}
			
			// 不带参数
			@OnClick(R.id.submit)
			public void submit() {
			  // TODO submit data to server...
			}
			
			// 同时注入多个 View 事件
			@OnClick({ R.id.door1, R.id.door2, R.id.door3 })
			public void pickDoor(DoorView door) {
			  if (door.hasPrizeBehind()) {
			    Toast.makeText(this, "You win!", LENGTH_SHORT).show();
			  } else {
			    Toast.makeText(this, "Try again", LENGTH_SHORT).show();
			  }
			}
 - 使用截图：
		<img alt="Screenshot #14384" onclick="showImage('http:\/\/plugins.jetbrains.com\/files\/7369\/screenshot_14384.png')" src="http://plugins.jetbrains.com/files/7369/screenshot_14384.png" border="0">


###Parcelable Code Generator
- 功能：Parcelable接口是Android特有的序列化方法，效率比Serializable接口高，还可以用在IPC中，但是实现稍微复杂一些。该插件可以帮助实现Parcelable接口所需的字段和方法的代码
- 下载：[Parcelable Code Generator](https://plugins.jetbrains.com/plugin/7332?pr=androidstudio)   
- 安装：Setting -> Preferences -> Plugins -> Browse repositories，查找Parcelable Code Generator，或下载后从磁盘安装  
- 使用：  
	1. 新建一个实体类，定义好属性
	2. 右键选择`Generator`或直接按下`Alt+Insert`，选择`Parcelable`，选择需要的属性，点击`OK`，即可生成相应的代码。
	3. 截图  
	![Parcelable Code Generator](http://i.imgur.com/wubt1wW.png)

###Prettify
- 功能：从布局文件中生成对View的声明（不使用注解）
- 下载：[Android Studio Prettify](https://plugins.jetbrains.com/plugin/7405?pr=androidstudio)   
- 安装：Setting -> Preferences -> Plugins -> Browse repositories，查找Prettify，或下载后从磁盘安装  
- 特性：
	1. inflaterhe activity的setContentView的view变量的生成
	2. 在上下文菜单中加入`Extract String resource`快捷键
	3. view的cast检查的注解
	4. views的field和method的变量生成
	5. 内部类的相关layout的文件
	6. xml文件中的layout的id的查找
- 使用截图  

	<img alt="Screenshot #14418" onclick="showImage('http:\/\/plugins.jetbrains.com\/files\/7405\/screenshot_14418.png')" src="http://plugins.jetbrains.com/files/7405/screenshot_14418.png" border="0">

###ADB Idea
- 功能：此插件可以轻松完成以下操作，而不用手动输入ADB命令
	- 卸载应用
	- 杀死应用进程
	- 启动应用
	- 重启应用
	- 清除应用数据
	- 清除应用数据并重启应用  
- 下载：[ADB Idea](http://plugins.jetbrains.com/plugin/7380?pr=idea)
- 安装：Setting -> Preferences -> Plugins -> Browse repositories，查找ADB Idea，或下载后从磁盘安装  
- 使用：在Tools -> Android -> ADB Idea中选择相应的菜单项进行操作。
![ADB Idea](http://i.imgur.com/iMd4DTU.png)

###

##Gradle使用  

http://shanksleo.gitbooks.io/cookbook/content/gradle/gradle.html