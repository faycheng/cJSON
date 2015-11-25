# cJSON内存管理


---

cJSON库提供了钩子函数来帮助用户自定义内存管理函数，如果不设置，这默认为malloc和free。

## 自动模式下的内存管理
在自动模式下，cJSON使用默认的malloc和free函数管理内存。
在cJSON中，每个节点都是malloc而来，每个节点的string和valuestring也是malloc而来。
使用cJSON库中，使用cJSON_Delete函数可以递归释放JSON树中malloc的节点内存和字符内存。
当使用cJSON_Print函数后，需要手动释放cJSON_Print函数分配的内存，避免内存泄露。

## 手动模式下的内存管理
在手动模式下，需要指定钩子cJSON_Hooks的指向。

cJSON_Hooks结构如下：
```
typedef struct cJSON_Hooks {
	void *(*malloc_fn)(size_t sz);
	void (*free_fn)(void *ptr);
} cJSON_Hooks;
```
只要通过cJSON_Hooks指定了内存分配和释放的函数，在之后的使用中将自动使用指定的函数进行内存分配和释放。
假设已经有了一个自定义的内存分配函数my_malloc， 内存释放函数my_free。使用如下：

```
cJSON_InitHooks *hooks = NULL;
hooks = (cJSON_InitHooks *)calloc(1, sizeof(cJSON_InitHooks));
hooks->malloc_fn = my_malloc;
hooks->free_fn = my_free;
cJSON_InitHooks(hooks);
```
使用上述代码后，程序就能够自动使用自定义的内存释放和分配函数。






