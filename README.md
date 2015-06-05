# cJSON官方文档翻译

标签（空格分隔）： 未分类

---
###版权说明
  Copyright (c) 2009 Dave Gamble

  Permission is hereby granted, free of charge, to any person obtaining a copy
  of this software and associated documentation files (the "Software"), to deal
  in the Software without restriction, including without limitation the rights
  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
  copies of the Software, and to permit persons to whom the Software is
  furnished to do so, subject to the following conditions:

  The above copyright notice and this permission notice shall be included in
  all copies or substantial portions of the Software.

  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
  THE SOFTWARE.
  
---

欢迎使用cJSON。

cJSON旨在成为一个可以应用在工作中的低门槛json解析器。该项目包含一个.c文件，和一个.h文件。

对JSON最佳的描述在这个网站：[http://www.json.org/](http://www.json.org/)
JSON类似于XML，不过更加轻量级。你能够使用它去移动数据，存储数据，或者仅仅用于描述项目的状态。

那么，如何构建使用cJSON呢？
添加cJSON.c到你的项目中，并且把cJSON.h放置到你的项目头文件的搜索路径中。
例如，你可以使用下面命令构建一个测试程序：


作为一个库，cJSON尽力完成它能够完成的各种琐碎工作，而不是阻碍你解析JSON。就像一种哲学具有两面性一样，你能够通过两种方式使用cJSON：自动和手动。接下来，让我们快速地走一遍cJSON的使用流程。

我从这个网站摘录了一些JSON文件：http://www.json.org/fatfree.html
这个网站页面激发我写一个具有JSON哲学的cJSON解析器。它简单，低门槛，别出心裁。

一个JSON数据：
```JSON
{
    "name": "Jack (\"Bee\") Nimble", 
    "format": {
        "type":       "rect", 
        "width":      1920, 
        "height":     1080, 
        "interlace":  false, 
        "frame rate": 24
    }
}
```

假设你从一个文件，网站，或者一个魔法JSON精灵中得到了这个JSON数据。不管你如果得到这个JSON，你都需要用一个char*指针指向它。一切都可以被包含于cJSON结构体。
解析JSON的方式：
```c
cJSON *root = cJSON_Parse(my_json_string);
```
cJSON是一个对象。由于我们使用C语言实现，而C语言中没有对象概念，所以我们使用结构体处理对象。
那么，如何获取framerate节点的值呢？
```c
cJSON *format = cJSON_GetObjectItem(root,"format");
int framerate = cJSON_GetObjectItem(format,"frame rate")->valueint;
```
如何改变framerate节点的值？
```c
cJSON_GetObjectItem(format,"frame rate")->valueint=25;
```
完成了？不，还需要删除根节点。
```c
cJSON_Delete(root);
```
如你所见，上述方式就是使用cJSON的自动模式。当你使用自动模式时，你必须在你要解除对节点的引用前仔细检查指针。
接下来，将会展示如何使用代码生成一个完整的JSON。
```c
cJSON *root,*fmt;
root=cJSON_CreateObject();	
cJSON_AddItemToObject(root, "name", cJSON_CreateString("Jack (\"Bee\") Nimble"));
cJSON_AddItemToObject(root, "format", fmt=cJSON_CreateObject());
cJSON_AddStringToObject(fmt,"type",		"rect");
cJSON_AddNumberToObject(fmt,"width",		1920);
cJSON_AddNumberToObject(fmt,"height",		1080);
cJSON_AddFalseToObject (fmt,"interlace");
cJSON_AddNumberToObject(fmt,"frame rate",	24);
```
一般来说，我们都希望使用少量的代码便完成大部分工作。所以，在cJSON中没有复杂的，不必要的配置和使用。查看为了解析一系列JSON而编写的test.c，你就能发现这一点。（test文件夹中的JSON大部分来自于[json.org](json.org)，少部分来自于其他地方）

知道自动模式后，那么，手动模式时怎样的呢？
要了解手动模式，必须要先了解一些关于cJSON的细节。让我们一起分析cJSON如何去指代JSON数据。cJSON本身不能够分辨对象中的数组，只能识别值类型。每一个cJSON节点一般都包含一个儿子节点，多个兄弟节点，节点自身的值和节点自身的名称。

根节点包含：节点类型"Object"，子节点"name"。
子节点包含：节点名称"name"，节点值"Jack ("Bee") Nimble"，和兄弟节点"format"。
format节点包含：节点类型"Object"，节点名称"format"，和子节点"type"。
type节点包含：节点类型"String"，节点名称"type"，节点值"rect"，兄弟节点"width"
width节点包含：节点类型"Number"，节点名称"width"，节点1920值，兄弟节点"height"
height节点包含：节点类型"Number"，节点名称"height"，节点值1080，兄弟节点"interlace"
interlace节点包含：节点类型"False"，节点名称"interlace"，兄弟节点"frame rate"
frame rate节点包含：节点类型"Number"，节点名称"frame tate"，节点值25。

这是cJSON结构体的源码：
```c
typedef struct cJSON {
	struct cJSON *next,*prev;
	struct cJSON *child;

	int type;

	char *valuestring;
	int valueint;
	double valuedouble;

	char *string;
} cJSON;
```
cJSON默认所有值都为0，除非额外为其赋有意义的值。

next/prev是一个双向链表中的兄弟。next指向链表中下一个兄弟节点，prev指向本节点前一个节点。
只用对象和数组有child，并且child是双向链表的头节点。child的prev一般为0，不指向任何节点，而next一般是有指向的。
双向链表的最后一个兄弟节点的next是无指向的。
type取值如下：Null/True/False/Number/String/Array/Object。这些值类型都在cJSON.h中定义了。

Number类型节点有valueint和valuedouble。如果你想要整形值，读取valueint，否则读取valuedouble。

所有的节点都是一个链表，都具有string值。string表示节点的名称。在上述例子中，我提及name都是指string。你可以将string理解为JSON节点变量的名称（节点的名称）。

现在你也很容易的递归地遍历链表，用你喜欢的反思解析JSON。
你能够调用cJSON_Parse去得到一个解析后的cJSON节点，然后你能够根据这个根节点遍历整个节点树。当你得到cJSON_Parse解析后的结果后，你也可以使用自己的词法分析函数处理节点树。如果你想要构建一个解析器的回调函数，下面的代码是一个不错的例子（这是一个很简单的例子，只适用特定情况，事实上，但你真正使用自己的词法分析时，会比较复杂）。
```c
void parse_and_callback(cJSON *item,const char *prefix)
{
	while (item)
	{
		char *newprefix=malloc(strlen(prefix)+strlen(item->name)+2);
		sprintf(newprefix,"%s/%s",prefix,item->name);
		int dorecurse=callback(newprefix, item->type, item);
		if (item->child && dorecurse) parse_and_callback(item->child,newprefix);
		item=item->next;
		free(newprefix);
	}
}
```


----------
备注：官方文档翻译暂时停止，因为关于cJSON中callback部分尚不了解，待源码分析到位后，在来分析这部分文档。如果希望了解callback内容，可以查看官方原始文档README_EN。

