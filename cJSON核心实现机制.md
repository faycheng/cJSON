# cJSON核心实现机制

在cJSON库中，最重要的两块分别为JSON解析和JSON节点的增删改查。

---
## JSON树结构
在cJSON库中使用树结构存储JSON的各个节点。而这个JSON树是使用双向链表实现的。
示例JSON：
```
{
        "name": "Jack (\"Bee\") Nimble", 
        "format": {"type":       "rect", 
        "width":      1920, 
        "height":     1080, 
        "interlace":  false,
        "frame rate": 24
}
```
![双向链表](http://7xjfjn.com1.z0.glb.clouddn.com/list.jpg)
双向链表的每一个节点都是cJSON指针。一个完整的双向链表表示一连串兄弟节点。在示例JSON中，name和format节点组合成一个链表，type，width，height，interlace和frame rate节点组成一个链表。

![JSON树](http://7xjfjn.com1.z0.glb.clouddn.com/tree.jpg)
JSON树的每一层都是一个双向链表，表示一堆兄弟节点。当前层的所有节点都是当前链表头节点的父节点的子节点。在示例JSON中，type，width，height，interlace和frame rate节点都是format节点的子节点。实线表示节点间有真实的引用关系，而虚线表示逻辑上的引用关系。

---
## JSON正反词法分析
正向词法分析：将字符串文本（char *）转换为JSON树。
反向词法分析：将JSON树转换为可存储打印的字符串文本（char *）。
### 正向词法分析流程
```flow
st=>start: Start 
op1=>operation: skip
op2=>operation: parse_value 
c1=>condition: is null
c2=>condition: parse null
c3=>condition: parse false
c4=>condition: parse true
c5=>condition: parse string
c6=>condition: parse number
c7=>condition: parse array
c8=>condition: parse object
op3=>operation: parse_number
op4=>operation: parse_string
op5=>operation: parse_object
op6=>operation: parse_null
op7=>operation: parse_false
op8=>operation: parse_true
op9=>operation: parse_array
op10=>operation: error
e=>end

st->op1->op2->c1
c1(yes)->e 
c1(no)->c2
c2(yes)->op6
c2(no)->c3
c3(yes)->op7
c3(no)->c4
c4(yes)->op8
c4(no)->c5
c5(yes)->op4
c5(no)->c6
c6(yes)->op3
c6(no)->c7
c7(yes)->op9
c7(no)->c8
c8(yes)->op5
c8(no)->op10

```
上述流程图为正向词法分析的简要流程。在实际分析中当解析object和array节点对象时，还会调用parse_value递归解析节点数据。递归思路较为简单，具体实现可以参考cJSON.c中的parse_value方法。

### 反向词法分析流程
![JSON树](http://7xjfjn.com1.z0.glb.clouddn.com/tree.jpg)
在cJSON中，反向词法分析较为简陋，没有提供封装较为完整的方法，当我们得到JSON树后，只能通过移动指针手动遍历JSON树，从而得到节点信息，完成反向词法分析。

---
## 节点增删改
### 添加节点（cJSON_String类型）
```flow
st=>start: Start 
io=>inputoutput: 输入节点名称name和值value
op1=>operation: 确定添加位置：节点A
op2=>operation: 创建一个名称为name，值为value的节点B
op3=>operation: 将节点B添加为节点A的子节点
op4=>operation: 将节点B添加为节点A的子节点的兄弟节点
c1=>condition: 判断节点A是否具有子节点
e=>end

st->io->op1->op2->c1
c1(yes)->op4->e
c1(no)->op3

```

### 删除节点
```flow
st=>start: Start 
op1=>operation: 遍历JSON树，找到待删除节点A
op2=>operation: B->child = A->next, A->prev = null 
op3=>operation: B->next = A->next, A->prev = B
op4=>operation: 释放A节点占用的内存
c1=>condition: 判断节点A是否为上一节点B的子节点
e=>end

st->op1->c1
c1(yes)->op2->op4->e
c1(no)->op3->op4->e
```

### 修改节点
```flow
st=>start: Start 
io=>inputoutput: 输入节点新的值value
op1=>operation: 遍历JSON树，找到待删除节点A
op2=>operation: 将节点A的值设置为value
e=>end

st->io->op1->op1->e
```








