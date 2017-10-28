---
layout: post
title: Static types in mypy
date: 2016-11-03 00:06:32
category: Weekly
tags: Python Mypy
---

http://blog.zulip.org/2016/10/13/static-types-in-python-oh-mypy/

### Benefits

- Static type annotations improve readability
    * 最重要的好处
    * 读代码的时候更加清晰
- We can refactor with confidence
    * 现状：重构 Python 代码的时候要非常小心
    * 如果有类型提示，实践中发现对重构会非常有用
- We upgraded to Python 3
    * 升级的时候可以检测出不兼容的问题（重构）
- mypy frequently catches bugs
    * mypy 可以检测出 bug
- Static types highlight bad interfaces
    * 可以提醒你改善 interfaces 的设计（比如有的接口有时候返回 `List` 有时候返回 `Tuple`）

### Pain points

- Interactions with “unused import” linters
    * 其他的 linter（e.g. pyflakes）不会解析注释，所以会报很多的 `unused import`
- Import cycles
    * 由于 linter 需要 import 一些符号，所以很容易会出现循环引用

### Non-pain points

开始认为会有问题，实际用了之后觉得不是问题的点

- Training
    * 额外的学习成本
- False positives
    * mypy 只是被设计用来检查程序中类型不一致的问题
- Mypy and typeshed bugs
    * 坑我们已经趟过了，你们就放心用
- Performance
    * 和 pyflakes 一样快
    * 但是不能实时的探测错误（< 100ms）
- Community
    * 响应非常快

### Finding bugs in your first few days with mypy

主要介绍了你需要做些什么才能从 mypy 获得收益

- Read the mypy cheat sheet
  * RTFM
- Standardize how you’ll run mypy
  * 把 mypy 集成到你的项目中，让所有人的行为保持一致
    * 制定白名单、黑名单
    * 定制执行参数，参考例子：`mypy --py2 --silent-imports --fast-parser -i <paths>`
- Get mypy running on your codebase with clean output
  * 不要被噪音污染
- Check for basic consistency
  * 加个 `--check-untyped-defs` 参数能够帮你检查出一些 bug
  * 可以把测试加上 `#type: ignore`
- Run mypy in continuous integration
  * 持续集成

因为 type annotations 是可选的，所以做完上面的几件事情之后就可以运行 mypy 来检查你的项目了

### Fully annotating a large codebase

可以分为以下几个步骤

1. Annotate the core library
   * 毫无疑问
2. Annotate the bulk of the codebase
   * 集中精力快速搞完 （他们在 PyCon sprints 上花了几天时间搞完）
3. Get to 100%
   * 收尾的时候可以使用 `--disallow-untyped-defs` 选项
4. Celebrate and write a blog post!

### Recommendations for annotating code

- Make sure to handle `str` vs.` Text` correctly
  * 字符串要处理好（Python3）
- Remember to annotate class variables
- Avoid using a guess-and-check approach when adding annotations
  * 做这个事情的时候不要靠猜
- Use precise types where possible (e.g. avoid using `Any` ).
- 谨慎使用 `type: ignore`
  * 像这样用：`bad_code # type: ignore # https://github.com/python/typeshed/issues/372`

### Conclusion

- mypy 对我们的项目（zulip）起到了很大的帮助
- 改善了代码的可读性，帮助发现了代码中的很多 bug
- 帮助我们很容易的迁移到 Python3
