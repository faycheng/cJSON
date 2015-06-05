# cJSON函数列表
---
[常用解析函数](#cyjxhs)
[宏定义](#hdy)

---
## 常用解析函数<span id = "cyjxhs"></span>
```
cJSON *cJSON_Parse(const char *value)
参数：
    value(const char*):char*指针，指向待解析的JSON数据
返回值：
    JSON树的根节点
函数功能：
    解析JSON数据，将数据填入JSON树中
输入示例：
    {
        "name": "Jack (\"Bee\") Nimble", 
        "format": {"type":       "rect", 
        "width":      1920, 
        "height":     1080, 
        "interlace":  false,
        "frame rate": 24
    }
```

```
char  *cJSON_Print(cJSON *item)
参数：
    item(cJSON *)：cJSON根节点
返回值：
    item节点解析后的全树字符串
函数功能：
    从item节点开始递归遍历，将节点树转换为字符串
输出示例：
    {
    	"name":	"Jack (\"Bee\") Nimble",
    	"format":	{
    		"type":	"rect",
    		"width":	1920,
    		"height":	1080,
    		"interlace":	false,
    		"frame rate":	24
    	}
    }
备注：
    使用该函数后，需要根据返回的char *指针释放内存。
    eg：
        out=cJSON_Print(json);
		printf("%s\n",out);
		free(out);
```

```
void   cJSON_Delete(cJSON *c)
参数：
    c(cJSON):cJSON根节点
返回值：
    无
函数功能：
    从根节点c开始递归删除JSON树各个节点，释放内存。
```

```
cJSON *cJSON_CreateObject(void)
参数：
    无
返回值：
    指向一个cJSON_Object类型节点的指针
函数功能：
    创建一个cJSON节点，并设置节点类型为cJSON_Object
```

```
cJSON *cJSON_CreateString(const char *string)
参数：
    string(const char *):新创建节点的名称
返回值：
    cJSON_String类型的节点的指针
函数功能：
    创建一个cJSON_Object类型的节点，并且将节点的值valuestring设置为string
```

```
cJSON *cJSON_CreateNumber(double num)	
参数：
    num(double):新创建节点的值
返回值：
    cJSON_String类型的节点的指针
函数功能：
    创建一个cJSON_Number类型的节点，并且将节点的值valuedouble设置为num, valueint设置为(int)num
```

```
cJSON *cJSON_CreateArray(void)	
参数：
    无
返回值：
    cJSON_Array类型的节点的指针
函数功能：
    创建一个cJSON_Array类型的节点
```

```
cJSON *cJSON_CreateBool(int b)		
参数：
    b(int):新创建节点的类型，非0为cJSON_True，0为cJSON_False
返回值：
    cJSON_False/cJSON_True类型的节点的指针
函数功能：
    创建一个cJSON_False/cJSON_True类型的节点
```

```
cJSON *cJSON_CreateTrue(void)	
参数：
    无
返回值：
    cJSON_True类型的节点的指针
函数功能：
    创建一个cJSON_True类型的节点
```

```
cJSON *cJSON_CreateFalse(void)	
参数：
    无
返回值：
    cJSON_False类型的节点的指针
函数功能：
    创建一个cJSON_False类型的节点
```
```
cJSON *cJSON_CreateNull(void)	
参数：
    无
返回值：
    cJSON_Null类型的节点的指针
函数功能：
    创建一个cJSON_Null类型的节点
```

```
void	cJSON_AddItemToObject(cJSON *object,const char *string,cJSON *item)
参数：
    object(cJSON *):被添加节点的节点
    string(char *):要添加节点的名称
    item(cJSON *):要添加节点
返回值：
    无
函数功能：
    将item节点的名称设置为string。如果object节点有没有子节点，就将item设置为object子节点，否则将item添加object->child链表的尾部，成为object->child的兄弟节点
```


```
void cJSON_AddItemToArray(cJSON *array, cJSON *item)
参数：
    
返回值：
    无
函数功能：
    
```


```
void cJSON_AddItemReferenceToArray(cJSON *array, cJSON *item)
参数：
    
返回值：
    无
函数功能：
    
```


```
void	cJSON_AddItemReferenceToObject(cJSON *object,const char *string,cJSON *item)
参数：
    
返回值：
    无
函数功能：
    
```

##宏定义<span id = "hdy"></span>
```
#define cJSON_AddNullToObject(object,name)		cJSON_AddItemToObject(object, name, cJSON_CreateNull())
功能：
    创建一个string值为name的cJSON_Null节点，并添加到object。
```
```
#define cJSON_AddTrueToObject(object,name)		cJSON_AddItemToObject(object, name, cJSON_CreateTrue())
功能：
    创建一个string值为name的cJSON_True节点，并添加到object。
```
```
#define cJSON_AddFalseToObject(object,name)		cJSON_AddItemToObject(object, name, cJSON_CreateFalse())
功能：
    创建一个string值为name的cJSON_False节点，并添加到object。
```
```
#define cJSON_AddBoolToObject(object,name,b)	cJSON_AddItemToObject(object, name, cJSON_CreateBool(b))
功能：
    创建一个string值为name的cJSON_Bool节点，并添加到object。b非0为cJSON_True，0为cJSON_False。
```
```
#define cJSON_AddNumberToObject(object,name,n)	cJSON_AddItemToObject(object, name, cJSON_CreateNumber(n))
功能：
    创建一个string值为name，valuedouble为n，valueint为(int)n的cJSON_Number节点，并添加到object。
```
```
#define cJSON_AddStringToObject(object,name,s)	cJSON_AddItemToObject(object, name, cJSON_CreateString(s))
功能：
    创建一个string值为name，valuestring为s的cJSON_String节点，并添加到object。
```
```
#define cJSON_SetIntValue(object,val)			((object)?(object)->valueint=(object)->valuedouble=(val):(val))
功能：
    
```
```
#define cJSON_SetNumberValue(object,val)		((object)?(object)->valueint=(object)->valuedouble=(val):(val))
功能：
    
```
