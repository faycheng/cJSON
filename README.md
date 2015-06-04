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






