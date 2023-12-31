```
      /\_/\  
     ( o.o )
      > ^ <
     HashMap
    ┌──────┐
    │      │
    └──────┘

 ┌──────┐  ┌──────┐  ┌──────┐
 │ Key1 │  │ Key2 │  │ Key3 │
 └──────┘  └──────┘  └──────┘
    │          │          │
    │          │          │
 ┌──┴───┐  ┌──┴───┐  ┌──┴───┐
 │ Val1 │  │ Val2 │  │ Val3 │
 └──┬───┘  └──┬───┘  └──┬───┘
    │          │          │
    │          │          │
┌───┴────┐ ┌───┴────┐ ┌───┴────┐
│ Hash1  │ │ Hash2  │ │ Hash3  │
└────────┘ └────────┘ └────────┘

```

the ASCII art is powered by ChatGPT

# overview 综述

This is a hash table that meets STL requirements and its source is [CS106L-22winter-assignment3]

这是一个满足STL要求的哈希表，其来源是[CS106L-22winter-assignment3](https://web.stanford.edu/class/archive/cs/cs106l/cs106l.1224/assignment3.html)

According to veteran C++ programmers, there are two projects which bring together all the knowledge that a proficient C++ programmer should have:

根据资深 C++ 程序员的说法，有两个项目汇集了熟练的 C++ 程序员应具备的所有知识：

- Implementing a STL-compliant template class; and 实现符合STL标准的模板类
- Implementing a macro to hash string literals at compile-time. 实现一个宏以在编译时散列字符串文字。

In this assignment, we will be putting #1 on your resume by completing a STL-compliant HashMap.

在本次作业中，我们将通过完成符合 STL 的 HashMap 来为您的简历加一分。

# what did I do 我做了什么

- 我理解了代码的结构
- 为之增加了const correctness 
- 增加`copy assigenment`的普通版本和移动语义版本
- 增加`operator=` 的普通版本和移动语义版本
- 修复析构函数内存溢出的bug
- 修复测试用例关乎移动语义的未定义行为的bug
- 根据build.sh，增加CMakeList

- I understand the structure of the code
- Added const correctness to it
- Added normal version and mobile semantic version of `copy assigenment`
- Added normal version and mobile semantic version of `operator=`
- Fixed destructor memory overflow bug
- Fixed a bug with undefined behavior of test cases related to move semantics
- According to build.sh, add CMakeList

# If you are intersted in this Proj 如果你对这个项目感兴趣

- 去学[CS106L](https://web.stanford.edu/class/archive/cs/cs106l/cs106l.1224/index.html)吧
    - 特别地，和这个项目高度相关的内容是[lec7](https://web.stanford.edu/class/archive/cs/cs106l/cs106l.1224/lectures/lecture7.pdf)和[lec13](https://web.stanford.edu/class/archive/cs/cs106l/cs106l.1224/lectures/lecture13.pdf)

- Go and study [CS106L](https://web.stanford.edu/class/archive/cs/cs106l/cs106l.1224/index.html)
     - In particular, the contents that are highly relevant to this project are [lec7](https://web.stanford.edu/class/archive/cs/cs106l/cs106l.1224/lectures/lecture7.pdf) and [lec13](https ://web.stanford.edu/class/archive/cs/cs106l/cs106l.1224/lectures/lecture13.pdf)

# special thanks

- 感谢我的朋友[@wendyl42](https://github.com/Wendyl42)，ta帮我修复了一个在初学者看来复杂的问题
- 感谢ChatGPT，它孜孜不倦地帮我复习已然生疏的C++语法

- Thanks to my friend [@wendyl42](https://github.com/Wendyl42), he helped me fix a problem that seems complicated to a beginner
- Thanks to ChatGPT, it worked tirelessly to help me review the unfamiliar C++ grammar.