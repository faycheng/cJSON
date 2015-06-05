# cJSON内存管理


---

cJSON库提供了钩子函数来帮助用户自定义内存管理函数，如果不设置，这默认为malloc和free。

## 自动模式下的内存管理
在自动模式下，cJSON使用默认的malloc和free函数管理内存。
在cJSON中，每个节点都是malloc而来，每个节点的string和valuestring也是malloc而来。
使用cJSON库中，使用cJSON_Delete函数可以递归释放JSON树中malloc的节点内存和字符内存。
当使用cJSON_Print函数后，需要手动释放cJSON_Print函数分配的内存，避免内存泄露。

## 手动模式下的内存管理




