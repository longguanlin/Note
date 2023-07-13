# WebForm

## 什么是ASP.NET

ASP.NET指的是动态服务器页面(Active Server Pages),运行在IIS（Internet Information Service)上，文件类似于HTML,后缀为.aspx.



## Classic ASP的局限性：

代码块必须放置在需要进行输出的位置

```html
<html>
<body style="background-color:#e5eecc; text-align:center;">
<h2>Hello W3School!</h2>
<p>代码块：<%Response.Write(now())%></p>
</body>
</html>
```

通过 Classic ASP，我们是无法将可执行代码与 HTML 本身进行分离的。这使得页面难以阅读，也难以维护



## ASP.NET-服务器控件

有三种类型的服务器控件：

- HTML 服务器控件 - 传统的 HTML 标签
- Web 服务器控件 - 新的 ASP.NET 标签
- Validation 服务器控件 - 用于输入验证



### ASP.NET-HTML服务器控件

- 原本的HTML 元素添加 *runat="server" 属性*。该属性指示，此元素是一个服务器控件。同时要添加 id 属性来标识该服务器控件。该 id 引用可用于操作运行时的服务器控件。

- 所有 HTML 服务器控件必须位于带有 runat="server" 属性的 <form> 标签内。

- 事件处理程序（event handler）是一种针对给定事件执行代码的子例程。

  ```html
  <script runat="server">
  Sub Page_Load
  link1.HRef="http://www.w3school.com.cn"
  End Sub
  </script>
  
  <html>
  <body>
  
  <form runat="server">
  	此处的可执行代码已被移到html外：<a id="link1" runat="server">Visit W3School!</a>
  </form>
  
  </body>
  </html>
  ```



### ASP.NET - Web 服务器控件

似 HTML 服务器控件，Web 服务器控件也在服务器上创建，它们同样需要 runat="server" 属性以使其生效。不过，Web 服务器控件没有必要映射任何已存在的 HTML 元素，它们代表更复杂的元素。

```asp
语法：<asp:control_name id="some_id" runat="server" />
例子：<form runat="server">
		<asp:Button id="button1" Text="Click me!" runat="server" OnClick="submit"/>
	</form>
```



### ASP.NET - Validation 服务器控件

Validation 服务器控件用于验证用户输入。如果用户输入没有通过验证，将向用户显示一条错误消息。

```html
<html>
<body>
    <form runat="server">

        <p>
            Enter a number from 1 to 100:
            <asp:TextBox ID="tbox1" runat="server" />
            <br />
            <br />
            <asp:Button Text="Submit" runat="server" />
        </p>

        <p>
            <asp:RangeValidator
                ControlToValidate="tbox1"
                MinimumValue="1"
                MaximumValue="100"
                Type="Integer"
                Text="The value must be from 1 to 100!"
                runat="server" />
        </p>

    </form>
</body>
</html>

```

![image-20210519161203366](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210519161203366.png)



## ASP.NET-事件

**事件句柄（event handler）是一种针对给定事件来执行代码的子例程。**



### Page_Load事件

Page_Load 事件是众多 ASP.NET 可理解的事件之一。Page_Load 事件会在页面加载时被触发，然后 ASP.NET 会自动调用子例程 Page_Load，并执行其中的代码：

```html
<script runat="server">
Sub Page_Load
lbl1.Text="The date and time is " & now()
End Sub
</script>

<html>
<body>
<form runat="server">
<h3><asp:label id="lbl1" runat="server" /></h3>
</form>
</body>
</html>
```

![image-20210519162151840](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210519162151840.png)



### Page.IsPostBack 属性

Page_Load 子例程会在页面每次加载时运行。如果您仅希望在页面第一次加载时执行 Page_Load 子例程中的代码，您可以使用 Page.IsPostBack 属性。如果 Page.IsPostBack 属性为 false，则页面第一次被载入，如果为 true，则页面传回服务器

```html
<script runat="server">
Sub Page_Load
if Not Page.IsPostBack then
  lbl1.Text="The date and time is " & now()
end if
End Sub

Sub Submit(s As Object, e As EventArgs)
lbl2.Text="Hello World!"
End Sub
</script>

<html>
<body>
<form runat="server">
<h3><asp:label id="lbl1" runat="server" /></h3>
<h3><asp:label id="lbl2" runat="server" /></h3>
<asp:button text="Submit" onclick="submit" runat="server" />
</form>
</body>
</html>
```



## ASP.NET Web窗体



### ASP.NET Web表单

**所有服务器控件必须出现在 <form> 标签内，同时 <form> 标签必须包含 runat="server" 属性。runat="server" 属性指示该表单必须在服务器上进行处理。一个 .aspx 仅能包含一个 <form runat="server"> 控件！**

表单总是向自身页面进行提交。如果您规定了一个 action 属性，它会被忽略。如果您省略了 method 属性，它将被默认地设置为 method="post"。同时，如果您没有规定 name 和 id 属性，它们则由 ASP.NET 自动分配。

如果您查看一个 .aspx 页面的源代码，而其中包含的表单不带有 name, method, action 或 id 属性，那么您将看到 ASP.NET 已经把这些属性添加到该表单。类似这样：

```html
<form name="_ctl0" method="post" action="page.aspx" id="_ctl0">...some code</form>
```



### ASP.NET-维持ViewState

-  **classic ASP:** 表单被提交时，所有的表单值都会被清空。
- **ASP.NET:**表单被提交时，表单会随所有表单值一同重新出现。

维持 ViewState 是 ASP.NET Web 表单的默认设置。如果您不希望维持 ViewState，请在 .aspx 页面的顶部包含指令： <%@ Page EnableViewState="false" %>，或为任意控件添加属性：EnableViewState="false"。



### ASP.NET-TextBox控件

**TextBox 控件用于创建用户可输入文本的文本框。**

**属性**

| 属性                                                         | 描述                                                       | .NET |
| :----------------------------------------------------------- | :--------------------------------------------------------- | :--- |
| [AutoCompleteType](https://www.w3school.com.cn/aspnet/prop_webcontrol_textbox_autocompletetype.asp) | 规定 TextBox 控件的 AutoComplete 行为。                    | 2.0  |
| [AutoPostBack](https://www.w3school.com.cn/aspnet/prop_webcontrol_textbox_autopostback.asp) | 布尔值，规定当内容改变时，是否回传到服务器。默认是 false。 | 1.0  |
| CausesValidation                                             | 规定当 Postback 发生时，是否验证页面。                     | 2.0  |
| [Columns](https://www.w3school.com.cn/aspnet/prop_webcontrol_textbox_columns.asp) | textbox 的宽度。                                           | 1.0  |
| [MaxLength](https://www.w3school.com.cn/aspnet/prop_webcontrol_textbox_maxlength.asp) | 在 textbox 中所允许的最大字符数。                          | 1.0  |
| [ReadOnly](https://www.w3school.com.cn/aspnet/prop_webcontrol_textbox_readonly.asp) | 规定能否改变文本框中的文本。                               | 1.0  |
| [Rows](https://www.w3school.com.cn/aspnet/prop_webcontrol_textbox_rows.asp) | textbox 的高度（仅在 TextMode="Multiline" 时使用）。       | 1.0  |
| runat                                                        | 规定该控件是否是服务器控件。必须设置为 "server"。          |      |
| TagKey                                                       |                                                            |      |
| [Text](https://www.w3school.com.cn/aspnet/prop_webcontrol_textbox_text.asp) | textbox 的内容。                                           | 1.0  |
| [TextMode](https://www.w3school.com.cn/aspnet/prop_webcontrol_textbox_textmode.asp) | 规定 TextBox 的行为模式（单行、多行或密码）。              | 1.0  |
| ValidationGroup                                              | 当 Postback 发生时，被验证的控件组。                       |      |
| [Wrap](https://www.w3school.com.cn/aspnet/prop_webcontrol_textbox_wrap.asp) | 布尔值，指示 textbox 的内容是否换行。                      | 1.0  |
| OnTextChanged                                                | 当 textbox 中的文本被更改时，被执行的函数的名称。          |      |



### ASP.NET-Button控件

**Button 控件用于显示一个按钮。**

**属性**

| 属性                                                         | 描述                                                         | .NET |
| :----------------------------------------------------------- | :----------------------------------------------------------- | :--- |
| [CausesValidation](https://www.w3school.com.cn/aspnet/prop_webcontrol_button_causesvalidation.asp) | 规定当 Button 被点击时是否验证页面。                         | 1.0  |
| CommandArgument                                              | 有关要执行的命令的附加信息。                                 | 1.0  |
| CommandName                                                  | 与 Command 相关的命令。                                      | 1.0  |
| [OnClientClick](https://www.w3school.com.cn/aspnet/prop_webcontrol_button_onclientclick.asp) | 当按钮被点击时被执行的函数的名称。                           | 2.0  |
| [PostBackUrl](https://www.w3school.com.cn/aspnet/prop_webcontrol_button_postbackurl.asp) | 当 Button 控件被点击时从当前页面传送数据的目标页面 URL。     | 2.0  |
| runat                                                        | 规定该控件是服务器控件。必须设置为 "server"。                | 1.0  |
| [Text](https://www.w3school.com.cn/aspnet/prop_webcontrol_button_text.asp) | 按钮上的文本。                                               | 1.0  |
| [UseSubmitBehavior](https://www.w3school.com.cn/aspnet/prop_webcontrol_button_usesubmitbehavior.asp) | 一个值 ，该值指示 Button 控件使用浏览器的提交机制，还是使用 ASP.NET 的 postback 机制。 | 2.0  |
| [ValidationGroup](https://www.w3school.com.cn/aspnet/prop_webcontrol_button_validationgroup.asp) | 当 Button 控件回传服务器时，该 Button 所属的哪个控件组引发了验证。 | 2.0  |



### ASP.NET-数据绑定

**我们可以使用数据绑定（Data Binding）来完成带有可选项目的列表，这些可选项目来自某个导入的数据源，比如数据库、XML 文件或脚本。**

下面的控件是支持数据绑定的列表控件：

- asp:RadioButtonList
- asp:CheckBoxList
- asp:DropDownList
- asp:Listbox



### ASP NET - ArrayList 对象

**ArrayList 对象是包含单一数据值的项目的集合。**

#### 把数据绑定到 ArrayList

ArrayList 对象可向下面这些控件自动地生成文本和值：

- asp:RadioButtonList
- asp:CheckBoxList
- asp:DropDownList
- asp:Listbox

如需把数据绑定到一个 RadioButtonList 控件，首先请在一个 .aspx 页面中创建 RadioButtonList 控件（请注意，没有任何 asp:ListItem 元素）：

```html
<form runat="server">
	<asp:RadioButtonList id="rb" runat="server" />
</form>
```

然后添加构建列表的脚本，并把列表中的值绑定到该 RadioButtonList 控件：

```html
<script runat="server">
Sub Page_Load
if Not Page.IsPostBack then
  dim mycountries=New ArrayList
  mycountries.Add("China")
  mycountries.Add("Sweden")
  mycountries.Add("France")
  mycountries.Add("Italy")
  mycountries.TrimToSize()
  mycountries.Sort()
  rb.DataSource=mycountries
  rb.DataBind()
end if
end sub
</script>
```

RadioButtonList 控件的 DataSource 属性被设置为该 ArrayList，它定义了这个 RadioButtonList 控件的数据源。RadioButtonList 控件的 DataBind() 方法把 RadioButtonList 控件与数据源绑定在一起。

**注释：**数据值作为控件的 Text 和 Value 属性来使用。如需添加不同于 Text 的 Value，既可以使用 Hashtable 对象，也可以使用 SortedList 对象。



### ASP.NET - Hashtable 对象

**Hashtable 对象包含用键/值对表示的项目。**

#### 把数据绑定到Hashtable

Hashtable 对象可为下面这些控件自动地生成文本和值：

- asp:RadioButtonList
- asp:CheckBoxList
- asp:DropDownList
- asp:Listbox

如需把数据绑定到某个 RadioButtonList 控件，首先请在一个 .aspx 页面中创建 RadioButtonList 控件（没有任何 asp:ListItem 元素），然后添加构建列表的脚本：

```html
<script runat="server">
sub Page_Load
if Not Page.IsPostBack then
  dim mycountries=New Hashtable
  mycountries.Add("C","China")
  mycountries.Add("S","Sweden")
  mycountries.Add("F","France")
  mycountries.Add("I","Italy")
  rb.DataSource=mycountries
  rb.DataValueField="Key"
  rb.DataTextField="Value"
  rb.DataBind()
end if
end sub
</script>

<html>
<body>

<form runat="server">
	<asp:RadioButtonList id="rb" runat="server" AutoPostBack="True" />
</form>

</body>
</html>
```

如需添加子例程，则添加onSelectedIndexChanged属性

```html
<asp:RadioButtonList id="rb" runat="server"
AutoPostBack="True" onSelectedIndexChanged="displayMessage" />
```



### ASP.NET - SortedList 对象

**SortedList 对象兼有 ArrayList 和 Hashtable 对象的特性。**

SortedList 对象包含用**键/值对**表示的项目。SortedList 对象可按照字符顺序或数字顺序自动地对项目进行排序。

通过 Add() 方法向 SortedList 添加项目。SortedList 可通过 **TrimToSize()** 方法调整为最终尺寸。

### ASP .NET - XML 文件

**我们可以把 XML 文件绑定到列表控件。**

**XML文件：**

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<countries>
    <country>
      <text>China</text>
      <value>C</value>
    </country>
    <country>
      <text>Sweden</text>
      <value>S</value>
    </country>
</countries>
```

#### 把 DataSet 绑定到 List 控件

首先，导入 "System.Data" 命名空间。我们需要该命名空间与 DataSet 对象一起工作。把下面这条指令包含在 .aspx 页面的顶部：

```html
<%@ Import Namespace="System.Data" %>
```

接下来，为这个 XML 文件创建一个 DataSet，并在页面首先加载时把这个 XML 文件载入该 DataSet：

```js
<script runat="server">
sub Page_Load
if Not Page.IsPostBack then
  dim mycountries=New DataSet
  mycountries.ReadXml(MapPath("countries.xml"))
  rb.DataSource=mycountries
  rb.DataValueField="value"
  rb.DataTextField="text"
  rb.DataBind()
end if
end sub
```

### ASP.NET-Repeater控件

**Repeater 控件用于显示重复的项目列表，这些项目被限制在该控件。**

Repeater 控件用于显示重复的项目列表，这些项目被限制在该控件。Repeater 控件可被绑定到数据库表、XML 文件或者其他项目列表。

 Repeater 控件包含三个元素。<HeaderTemplate> 元素中的内容在输出中仅出现一次，而 <ItemTemplate> 元素的内容会对应 DataSet 中的 "record" 重复出现，最后，<FooterTemplate> 的内容在输出中仅出现一次：

```html
<form runat="server">
    <asp:Repeater id="cdcatalog" runat="server">
        <table border="1" width="100%">
        	<HeaderTemplate> 
                <tr>
                    <th>Title</th>
                    <th>Artist</th>
                    <th>Country</th>
                </tr>
            </HeaderTemplate>

            <ItemTemplate>
                <tr>
                    <td><%#Container.DataItem("title")%></td>
                    <td><%#Container.DataItem("artist")%></td>
                    <td><%#Container.DataItem("country")%></td>
                </tr>
            </ItemTemplate>

            <FooterTemplate>
            
        	</FooterTemplate>
         </table>
    </asp:Repeater>
</form>
```

#### 使用 `<AlternatingItemTemplate>`

您可以在 <ItemTemplate> 元素后添加 <AlternatingItemTemplate> 元素，这样就可以描述交替行的外观了。

```html
<ItemTemplate>
<tr>
<td><%#Container.DataItem("title")%></td>
<td><%#Container.DataItem("artist")%></td>
</tr>
</ItemTemplate>

<AlternatingItemTemplate>
<tr bgcolor="#e8e8e8">

<td><%#Container.DataItem("title")%></td>
<td><%#Container.DataItem("artist")%></td>
</tr>
</AlternatingItemTemplate>
```

![image-20210520142800377](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210520142800377.png)

#### 使用 `<SeparatorTemplate>`

```html

<ItemTemplate>
<tr>
<td><%#Container.DataItem("title")%></td>
<td><%#Container.DataItem("artist")%></td>
</tr>
</ItemTemplate>

<SeparatorTemplate>
<tr>
<td colspan="6"><hr /></td>
</tr>
</SeparatorTemplate>
```

![image-20210520143037076](C:\Users\15911\AppData\Roaming\Typora\typora-user-images\image-20210520143037076.png)

### ASP.NET - DataList 控件

**DataList 控件，类似于 Repeater 控件，用于显示限制于该控件的项目的重复列表。不过，DataList 控件会默认地在数据项目上添加表格。**



## ASP.NET - 数据库连接



## ASP.NET 2.0 - 母版页（Master Pages）

**母版页（Master Pages）为网站内的其他页面提供模版。**

1. Master Page 使您有能力为 web 应用程序中的所有页面（或页面组）创建一致的外观和行为。
2. Master Page 为其他页面提供了模版，带有共享的布局和功能。Master Page 为内容定义了可被内容页面覆盖的占位符。而输出结果就是 Master Page 和内容页面的组合。
3. 内容页包含您希望显示的内容。
4. 当用户请求内容页时，ASP.NET 会对页面进行合并以生成输出，输出结果对 Master Page 的布局和内容页面的内容进行了合并。

**Master Page实例：**

```html
<%@ Master %>
<html>
<body>
<h1>Standard Header For All Pages</h1>

<asp:ContentPlaceHolder id="CPH1" runat="server">
</asp:ContentPlaceHolder>

</body>
</html>
```

Master Page 是一张为其他页面设计的普通 HTML 模版页。

*@ Master 指令*把它定义为一张 master page。

这个 master page 为单独的内容包含了一个占位符标签 *<asp:ContentPlaceHolder>*。

*id="CPH1"* 属性标识该占位符，在相同的 master page 中允许多个占位符。

该 master page 被保存为 *"master1.master"*。

**注释：**该 master page 也能够包含代码，允许动态的内容。

**内容页实例：**

```html
<%@ Page MasterPageFile="master1.master" %>

<asp:Content ContentPlaceHolderId="CPH1" runat="server">

<h2>Individual Content</h2>
<p>Paragrap 1</p>
<p>Paragrap 2</p>

</asp:Content>
```



## ASP.NET 2.0 - 导航（Navigation）

在 ASP.NET 2.0 中，菜单可存储在文件中，这样维护起来更加方便。这个文件通常名为 *web.sitemap*，被存放在网站的根目录中。

ASP.NET 2.0 拥有三种新的导航控件：

- Dynamic menus
- TreeViews
- Site Map Path

**Sitemap 文件实例：**

```xml
<?xml version="1.0" encoding="ISO-8859-1" ?>
<siteMap>
  <siteMapNode title="Home" url="/aspnet/w3home.aspx">
  <siteMapNode title="Services" url="/aspnet/w3services.aspx">
    <siteMapNode title="Training" url="/aspnet/w3training.aspx"/>
    <siteMapNode title="Support" url="/aspnet/w3support.aspx"/>
  </siteMapNode>
  </siteMapNode>
</siteMap>
```

**创建 sitemap 文件的规则：**

- XML 文件必须包含围绕内容的 <siteMap> 标签
- <siteMap> 标签只能有一个 <siteMapNode> 子节点 ("home" page)
- 每个 <siteMapNode> 可以有多个子节点 (web pages)
- 每个 <siteMapNode> 拥有定义页面标题和 URL 的属性

**注释：**sitemap 文件必须存放在站点的根目录中，URL 属性必须相对于该根目录。

### Dynamic Menu

`<asp:Menu>`控件可显示标准的站点导航菜单。

```html
<asp:SiteMapDataSource id="nav1" runat="server" />

<form runat="server">
<asp:Menu runat="server" DataSourceId="nav1" />
</form>
```

在上面的例子中的 *`<asp:Menu>` 控件*是一个供服务器创建导航菜单的占位符。

控件的数据源由 *DataSourceId 属性*进行定义。*id="nav1"* 把它连接到 *`<asp:SiteMapDataSource>` 控件*。

*`<asp:SiteMapDataSource>` 控件*会自动连接默认的 sitemap 文件(*web.sitemap*)。

### TreeView

`<asp:TreeView>` 控件可显示多级导航菜单。

这种菜单看上去像一棵带有枝叶的树，可通过 + 或 - 符号来打开或关闭。

```html
<asp:SiteMapDataSource id="nav1" runat="server" />

<form runat="server">
<asp:TreeView runat="server" DataSourceId="nav1" />
</form>
```

上面例子中的 *`<asp:TreeView>` 控件*是一个供服务器创建导航菜单的占位符。

控件的数据源由 *DataSourceId 属性*进行定义。*id="nav1"* 把它连接到 *`<asp:SiteMapDataSource>` 控件*。

*`<asp:SiteMapDataSource>` 控件*会自动连接默认的 sitemap 文件(*web.sitemap*)。

### SiteMapPath

SiteMapPath 控件可显示指向当前页面的指针（导航路径）。该路径显示为指向上级页面的可点击链接。

与 TreeView 和 Menu 控件不同，SiteMapPath 控件*不使用* SiteMapDataSource。SiteMapPath 控件默认使用 web.sitemap 文件。

**提示：**如果 SiteMapPath 没有正确显示，很可能是由于 web.sitemap 文件中存在 URL 错误。

```HTML
<form runat="server">
<asp:SiteMapPath runat="server" />
</form>
```

