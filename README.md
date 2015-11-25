# cJSON官方文档翻译

项目预期目标
>* 文档翻译
>* 函数列表
>* 项目架构
>* 源码注释
>* 解析流程
>* 快速使用

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

对JSON格式和结构的描述可以查看这个网站：[http://www.json.org/](http://www.json.org/)
JSON是一种类似于XML的格式，不过更加轻量级。你能够使用它去移动数据，存储数据，或者仅仅用于描述项目的状态。

那么，如何使用cJSON呢？
添加cJSON.c到你的项目中，并且把cJSON.h放置到你的项目头文件的搜索路径中。
例如，你可以使用下面命令编译一个使用cJSON的小例子：（tests目录下有相关文件）
```
gcc cJSON.c test.c -o test -lm
./test
```

作为一个库，cJSON尽力完成它能够完成的各种琐碎工作。就像哲学具有两面性一样，你也能够通过两种方式使用cJSON：自动和手动。

接下来，让我们快速地走一遍cJSON的使用流程。

我从这个网站获取了一些JSON文件：http://www.json.org/fatfree.html
正是这个网站页面激发我写一个具有JSON哲学的cJSON解析器，简单，低门槛，别出心裁。
这是一份JSON数据：
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

假设你从一个文件，网站，或者一个神奇的精灵那里得到了这个JSON数据，你首先需要用一个char*指针指向它。然后你可以通过解析获得一个cJSON结构体。
解析JSON的方式：
```c
cJSON *root = cJSON_Parse(my_json_string);
```
cJSON是一个对象。由于我们使用C语言实现，而C语言中没有对象的概念，所以我们使用结构体模拟对象。

接下来，获取framerate节点的值。
```c
cJSON *format = cJSON_GetObjectItem(root,"format");
int framerate = cJSON_GetObjectItem(format,"frame rate")->valueint;
```
改变framerate节点的值。
```c
cJSON_GetObjectItem(format,"frame rate")->valueint=25;
```
对节点完成增删改查之后并不意味着完成了一切，每次完成操作后，都需要删除根节点，否则会出现内存泄露。
```c
cJSON_Delete(root);
```
上述方式就是通过自动模式使用cJSON。当你使用自动模式时，你必须在你要解除对节点的引用前仔细检查指针是否释放。
接下来，将会演示如何使用代码生成一个完整的JSON。
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
一般来说，我们都希望使用少量的代码便完成大部分工作。所以，在cJSON中没有复杂的，不必要的配置和使用。查看test.c，你就能发现这一点。（test文件夹中的JSON大部分来自于[json.org](json.org)，少部分来自于其他地方）

学会自动模式后，那么，手动模式是怎样的呢？
要了解手动模式，必须要先了解一些关于cJSON的细节。让我们一起分析cJSON如何去指代JSON数据。cJSON本身不能够分辨对象中的数组，只能识别值类型。通常每一个cJSON节点都包含一个儿子节点，多个兄弟节点，节点自身的值和节点自身的名称。

根节点包含：节点类型"Object"，子节点"name"。
子节点包含：节点名称"name"，节点值"Jack ("Bee") Nimble"，和兄弟节点"format"。
format节点包含：节点类型"Object"，节点名称"format"，和子节点"type"。
type节点包含：节点类型"String"，节点名称"type"，节点值"rect"，兄弟节点"width"
width节点包含：节点类型"Number"，节点名称"width"，节点值1920，兄弟节点"height"
height节点包含：节点类型"Number"，节点名称"height"，节点值1080，兄弟节点"interlace"
interlace节点包含：节点类型"False"，节点名称"interlace"，兄弟节点"frame rate"
frame rate节点包含：节点类型"Number"，节点名称"frame tate"，节点值25。

这是cJSON结构体的具体代码：
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
只有对象和数组有child节点，并且child节点是双向链表的头节点。child的prev一般为NULL，不指向任何节点，而next一般是有指向的。
双向链表的最后一个兄弟节点的next是无指向的。
type取值如下：Null/True/False/Number/String/Array/Object。这些值类型都在cJSON.h中通过宏定义了。

Number类型节点有valueint和valuedouble。如果你想要整形值，读取valueint，否则读取valuedouble。

所有的节点都是一个链表，都具有string值。string表示节点的名称。在上述例子中，name都是指string变量。你可以将string理解为JSON节点变量的名称（节点的名称）。

你能够调用cJSON_Parse去得到一个解析后的cJSON节点，然后你能够根据这个根节点遍历整个节点树。当你得到cJSON_Parse解析后的结果后，你也可以使用自己的词法分析函数处理节点树。如果你想要构建一个解析器的回调函数，下面的代码是一个不错的例子（这是一个很简单的例子，只适用特定情况，事实上，但你真正使用自己的词法分析时，会比较复杂）。
```
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
除此之外，你可能也想要迭代解析节点。
你能够使用下列方式：
```
void parse_object(cJSON *item)
{
	int i; 
	for (i=0;i<cJSON_GetArraySize(item);i++)
	{
		cJSON *subitem=cJSON_GetArrayItem(item,i);
		// handle subitem.	
	}
}
```
或者，使用手工模式解析节点：
```
void parse_object(cJSON *item)
{
	cJSON *subitem=item->child;
	while (subitem)
	{
		// handle subitem
		if (subitem->child) parse_object(subitem->child);
		
		subitem=subitem->next;
	}
}
```
这是一个很简单的回调解析器。

This should cover most uses you'll find for parsing. The rest should be possible
这篇文章应该包含了大部分需要使用的查询和解析操作。如果你还有所疑惑，那么可以阅读项目源码，该项目源码并不复杂。


上述的示例代码已经能够完成构建解析JSON数据的需求。
当然，你也能够用自己的函数去构建cJSON节点。如果你有一个解析处理方式，你能够手动的去处理。
例如，你能够用以下的方式构建一个数组对象：
```
cJSON *objects[24];

cJSON *Create_array_of_anything(cJSON **items,int num)
{
	int i;cJSON *prev, *root=cJSON_CreateArray();
	for (i=0;i<24;i++)
	{
		if (!i)	root->child=objects[i];
		else	prev->next=objects[i], objects[i]->prev=prev;
		prev=objects[i];
	}
	return root;
}
```

当你使用cJSON_Print, 会返回JSON结构的字符串。

test.c文件中的代码含有大量解析使用cJSON的例子，你可以通过阅读test.c来详细了解cJSON的使用。

----------

