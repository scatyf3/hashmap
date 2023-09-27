> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [web.stanford.edu](http://web.stanford.edu/class/cs106l/assignment2.html)

[CS 106L](index.html)*   [assignments](#) [Assignment Setup](assignment-setup) [Assignment 1: WikiRacer](assignment1.html) [Assignment 2: STL HashMap](assignment2.html)
*   [resources](#) [Paperless Link (Submit Code)](http://paperless.stanford.edu) [Python to C++ Guide](https://docs.google.com/document/d/1Y6r7U8Bz2sqAiV23kKpUS5uXQkga32X3ZFasHoKyq_8) [C++ Documentation](https://en.cppreference.com/w/) [EdStem (for Enrolled Students)](https://edstem.org/us/courses/48152/discussion/) [Original 106L Course Reader](full_course_reader.pdf)
[schedule](index.html#calendar) [policies](policies.html)

 

Assignment 2: STL HashMap  
作业 2：STL 哈希图
========================================

_Assignment due on Paperless on Wednesday, June 7th at 11:59 PM (FIRM DEADLINE)  
作业将于 6 月 7 日星期三晚上 11：59 无纸化（确定截止日期）到期_

### Introduction and Assignment Goals  
介绍和作业目标

According to veteran C++ programmers, there are two projects which bring together all the knowledge that a proficient C++ programmer should have:  
根据资深 C++ 程序员的说法，有两个项目汇集了熟练 C++ 程序员应该具备的所有知识：

1.  Implementing a STL-compliant template class; and  
    实现符合 STL 标准的模板类; 和
2.  Implementing a macro to hash string literals at compile-time.  
    实现宏以在编译时对字符串文本进行哈希处理。

In this assignment, we will be putting **#1** on your resume by completing a STL-compliant **HashMap**.  
在这个任务中，我们将通过完成一个符合 STL 的 HashMap，在你的简历上加上 #1。

Recall that the `Map` abstract data type stores key → value pairs, and provides efficient lookup of the value of any key. There are two versions of this in the STL: `std::map`, traditionally implemented using a [balanced binary search tree](https://en.wikipedia.org/wiki/Self-balancing_binary_search_tree), and `std::unordered_map` (introduced in C++11), which uses a clever technique called hashing and supports the most map operations in constant time (!).  
回想一下， `Map` 抽象数据类型存储键→值对，并提供对任何键的值的有效查找。STL 中有两个版本： `std::map` 传统上使用平衡的二叉搜索树实现，以及 `std::unordered_map` （在 C++11 中引入），它使用一种称为散列的巧妙技术，并支持常量时间内的大多数映射操作 （！）。

In this assignment, you will be given a nearly complete implementation of a HashMap (the name of this data structure in the Stanford C++ library). The starter code is nearly complete, but is not "STL-compliant"; for example, copy and move semantics are not supported yet. Your goal is to extend it so it becomes an STL-compliant, industrial strength, robust, and blazingly fast data structure.  
在本作业中，您将获得一个几乎完整的 HashMap 实现（斯坦福 C++ 库中此数据结构的名称）。起始代码几乎完成，但不符合 STL; 例如，尚不支持复制和移动语义。您的目标是扩展它，使其成为符合 STL 标准、工业强度、健壮且速度极快的数据结构。

A hashmap is a bit of a complicated data structure, but we've given you nearly all of the implementation of it. In brief, this is how it works:  
哈希图是一种有点复杂的数据结构，但我们已经为您提供了几乎所有的实现。简而言之，这就是它的工作原理：

*   A hashmap stores an array (represented by the private vector `_buckets_array` in our HashMap) of "buckets".  
    哈希映射存储 “桶” 的数组（由我们的 HashMap 中的私有向量 `_buckets_array` 表示）。
*   Each "bucket" is a linked list: linked lists are made up of `node`s, which are defined in `hashmap.h`.  
    每个 “桶” 都是一个链表：链表由 s 组成 `node` ，在 中 `hashmap.h` 定义。
*   Each `node` in these linked lists contains one key-value pair that is contained in our map. In the `node` struct, this is the field `value`, of type `value_type`. `value_type` is a **type-alias** for a `std::pair<K, M>`. Check out its definition near the top of the .h file!  
    这些链表中的每一个 `node` 都包含一个包含在我们地图中的键值对。在结构中 `node` ，这是 的字段 `value` 。 `value_type` `value_type` 是 的类型 `std::pair<K, M>` 别名。在. h 文件顶部附近查看其定义！
*   When a user of the hashmap class tries to look up or insert a key, the HashMap class first converts the key, no matter what type it is, to an integer. It does this using the hash function, stored in the private member varibale `_hash_function`. For our purposes, this will always be the default hash function in the STL. Then, the HashMap class mods that integer by the number of buckets in `_buckets_array`. This is the index at which HashMap will insert or look for the key. From here, its just a matter of traversing the linked list stored at that index to find the key in question (it is possible that multiple keys are stored in the same index because their hashes, or the mod of their hashes, are the same!)  
    当 hashmap 类的用户尝试查找或插入键时，HashMap 类首先将键（无论它是什么类型）转换为整数。它使用哈希函数来做到这一点，该函数存储在私有成员变量 `_hash_function` 中。出于我们的目的，这将始终是 STL 中的默认哈希函数。然后，HashMap 类根据 中的 `_buckets_array` 桶数对该整数进行修改。这是 HashMap 将插入或查找键的索引。从这里开始，只需遍历存储在该索引中的链表即可找到有问题的键（可能多个键存储在同一个索引中，因为它们的哈希或哈希的模组是相同的！
*   The HashMap will occasionally increase the number of buckets and re-hash and store everything to make sure each index doesn't have too many keys stored there. This keeps all operations very fast, because it is very quick to look up an index in an array, and we are guaranteed to have very short linked lists there!  
    HashMap 偶尔会增加存储桶的数量，并重新哈希和存储所有内容，以确保每个索引不会存储太多键。这使得所有操作都非常快，因为在数组中查找索引非常快，并且我们保证那里有非常短的链接列表！

Dont worry if you don't feel super comfortable with HashMaps! We have implemented all of this logic for you, and we highly encourage you to read through the code to understand what is happening. However, this assignment does not ask you to implement anything except some SMFs, which only deal with initializing the private member variables.  
如果您对 HashMaps 感到不太舒服，请不要担心！我们已经为您实现了所有这些逻辑，我们强烈建议您通读代码以了解正在发生的事情。但是，此赋值不要求您实现除某些 SMF 之外的任何内容，这些 SMF 仅处理初始化私有成员变量。

**You will not necessarily write a lot of code on this assignment** (~20 lines), but some of your time will be spent on understanding the starter code given to you, and reasoning about design choices and common C++ idioms. **You may optionally work with a partner. We recommend having a partner to discuss the design questions.** If you choose to work with a partner, we ask that you and your partner submit a single version of the assignment among both of you. This means that you will ideally be working with your partner on a shared version of the code and short answer questions. Feel free to start a shared Google Doc, a private GitHub repository, or anything else that you'd need to keep track of a shared version of the code. You may also choose to schedule video chat sessions with each other to work on the assignment.  
你不一定会在这个作业上写很多代码（~20 行），但你的一些时间将花在理解给你的起始代码上，以及关于设计选择和常见 C++ 习语的推理。您可以选择与合作伙伴合作。我们建议找一个合作伙伴来讨论设计问题。如果您选择与合作伙伴合作，我们会要求您和您的合作伙伴在你们双方之间提交一个版本的作业。这意味着理想情况下，您将与您的合作伙伴一起处理代码的共享版本和简答题。随意启动共享的 Google Doc、私有 GitHub 存储库或跟踪代码共享版本所需的任何其他内容。您还可以选择安排彼此的视频聊天会话来处理作业。

### Download And Set Up Assignment  
下载并设置作业

1.  We suggest running the code on the Myth machines to prevent any issues with your computer's setup. If you haven't already, set yourself up in VS Code. The instructions for this are on the assignment setup page. We recommend the alternate SSH setup.  
    我们建议在 Myth 计算机上运行代码，以防止计算机设置出现任何问题。如果尚未设置，请在 VS Code 中进行设置。有关此操作的说明，请参阅分配设置页面。我们建议使用备用 SSH 设置。
2.  Download the starter code here: [Starter Code](assignments/HashMap_Starter.zip)  
    在此处下载起始代码：起始代码
3.  In terminal, `scp` your Assignment 3 starter code to your 106L folder in myth. `scp LOCAL_PATH <yoursunetid>@myth.stanford.edu:MYTH_PATH`  
    在终端中， `scp` 您的分配 3 起始代码到您的 106L 文件夹在神话中。 `scp LOCAL_PATH <yoursunetid>@myth.stanford.edu:MYTH_PATH`  
    LOCAL_PATH is the place on your laptop where you just downloaded the starter code. It will probably be something like `Downloads/HashMap_Starter.zip`.  
    LOCAL_PATH 是笔记本电脑上您刚刚下载入门代码的地方。它可能会像. `Downloads/HashMap_Starter.zip`  
    MYTH_PATH is the path on myth where you keep your CS106L folder.  
    MYTH_PATH 是神话中的路径，您保存 CS106L 文件夹。  
    You can find this by 1) `ssh`ing into Myth using `ssh <yoursunetid>@myth.stanford.edu`, 2) entering your password and then 3) `cd`ing to your CS106L directory and finally 4) printing out the path to this folder using `pwd`.  
    您可以通过 1） 使用 进入 Myth 找到它，2） 输入您的密码，然后 3） 进入您的 CS106L 目录，最后 4） `ssh` `cd` 使用 `pwd` `ssh <yoursunetid>@myth.stanford.edu` 打印出此文件夹的路径。
4.  Unzip the folder in myth by running `unzip HashMap_Starter.zip`.  
    通过运行 `unzip HashMap_Starter.zip` 解压缩神话中的文件夹。
5.  Now, you should be able to open your Assignment 3 code using whichever VS Code setup instructions you have been using all quarter!  
    现在，您应该能够使用整个季度使用的 VS Code 设置说明打开作业 3 代码！

Feel free to email the lecturers if you are having trouble with setup!  
如果您在设置时遇到问题，请随时向讲师发送电子邮件！

### Starter Code 起始代码

[Please download the starter code here](assignments/HashMap_Starter.zip). The starter code contains the following files:  
请在此处下载入门代码。起始代码包含以下文件：

*   `hashmap.h`: This file contains the (incomplete) class function prototypes for the HashMap, and also contains thorough comments about each function. **You will need to modify many of these function prototypes for milestone 1, and add a few for milestone 2** Like in CS 106B, you may add any private helper functions (though it is unlikely you will need to), but do not add or change the prototypes of any public functions. Imagine what a disaster it would be for the world if the vector’s size function was renamed to length or if the erase function had a few extra parameters...a good chunk of the world’s C++ code would stop compiling!  
    `hashmap.h` ：此文件包含 HashMap 的（不完整）类函数原型，还包含有关每个函数的全面注释。您需要为里程碑 1 修改其中的许多函数原型，并为里程碑 2 添加一些 与 CS 106B 一样，您可以添加任何私有帮助程序函数（尽管您不太可能需要），但不要添加或更改任何公共函数的原型。想象一下，如果矢量的大小函数被重命名为长度，或者如果擦除函数有一些额外的参数，这对世界来说将是一场灾难...... 世界上很大一部分 C++ 代码将停止编译！
*   `hashmap.cpp`: This file contains the implementation of the HashMap, and also contains thorough comments about implementation details. **Besides changing function signatures for milestone 1, you will have to add the implementations for milestone 2 to this file.**  
    `hashmap.cpp` ：此文件包含 HashMap 的实现，还包含有关实现细节的全面注释。除了更改里程碑 1 的函数签名外，还必须将里程碑 2 的实现添加到此文件中。
*   `hashmap_iterator.h` This file contains the complete class declaration and implementation for a HashMapIterator, and also contains thorough comments about each function. You will be reading the code here for milestone 1, but you will not need to modify this file at all. **We have never used a class as our iterator type before, make sure you read through these files and try to understand what is going on!** **Importantly, check out the difference between the `iterator` and `const_iterator` aliases in `hashmap.h`**  
    `hashmap_iterator.h` 此文件包含 HashMapIterator 的完整类声明和实现，还包含有关每个函数的完整注释。您将在此处阅读里程碑 1 的代码，但根本不需要修改此文件。我们以前从未使用过类作为迭代器类型，请确保您通读这些文件并尝试了解发生了什么！重要的是，请查看 `iterator` 和 `const_iterator` 别名 `hashmap.h` 之间的区别
*   `short_answer.txt`: You will write your solutions to the short answer questions in this file.  
    `short_answer.txt` ：您将在此文件中编写简答题的解决方案。
*   `build.sh`: This file contains the compilation instructions for the project. To check if your Hashmap compiles, run `./build.sh` from your assignment 4 directory.  
    `build.sh` ：此文件包含项目的编译说明。要检查您的 Hashmap 是否编译，请从您的 assignment 4 目录运行 `./build.sh` 。
*   `main.sh`: This file contains example code using your The hashmap. To run this code, first build it and then run `./main`  
    `main.sh` ：此文件包含使用哈希图的示例代码。若要运行此代码，请先生成它，然后运行 `./main`
*   `tests.cpp` and `test_settings.cpp`: These files contain crazy amounts of code meant to test your hashmap! You shouldn't need to edit them!  
    `tests.cpp` 和 `test_settings.cpp` ：这些文件包含大量旨在测试您的哈希图的代码！您不需要编辑它们！

#### Tips 技巧

You compile and run this code the same way you would lecture code! In the terminal from your `HashMap_Starter` directory, run `./build.sh` to compile and `./main` to run your code!  
你编译和运行这段代码的方式与讲授代码的方式相同！在 `HashMap_Starter` 目录的终端中，运行以编译和 `./main` 运行 `./build.sh` 代码！

Make sure the lines marked "UNCOMMENT FOR MILESTONE 2" are commented out for now. You will get tons of compiler errors!  
确保标记为 “取消注释里程碑 2” 的行暂时已注释掉。你会得到大量的编译器错误！

Before changing any functions, your code should compile. After chaning the functions in main, don't freak out about the loads of compile errors! Just keep looking for functions in HashMap to mark const until you get to a reasonable point in the compile mesagqes, and then try to use those to find more functions that need a const or an overload!  
在更改任何函数之前，应编译代码。在 main 中追逐函数之后，不要为编译错误的负载而惊慌失措！只需继续在 HashMap 中寻找函数来标记 const 直到您在编译 mesagqes 中达到一个合理的点，然后尝试使用这些函数来查找更多需要 const 或重载的函数！

Make sure you change your function signatures in two places: the .h **and** the .cpp  
确保在两个位置更改函数签名：.h 和 .cpp

The functions in main are declared before theyre implemented, so any signatures you change there should also change where they are first defined as well!  
main 中的函数在实现之前就已声明，因此您在那里更改的任何签名也应该更改它们最初定义的位置！

Its easy to make a lot of copy-paste errors here. Remember when you are overloading, you are also changing the return type.  
在这里很容易犯很多复制粘贴错误。请记住，当您重载时，您也会更改返回类型。

Notice how `begin()` is overloaded with a const version! Even though only the return type is changed, this type of overloading is ok because the function is marked `const`, which means the compiler can figure out which version of `begin()` to use based on whether the HashMap its been called on is const or not!  
`begin()` 请注意 const 版本是如何重载的！即使只更改了返回类型，这种类型的重载也是可以的，因为该函数被标记 `const` 了，这意味着编译器可以根据调用它的 HashMap 是否是 const 来确定要使用哪个版本 `begin()` ！

### Milestone 1: Const-Correctness  
里程碑 1：恒定正确性

Your first task will be to make a **const-interface** for the HashMap class. Recall that we often mark variables, especially function parameters `const` to avoid a specific kind of bug: modifying a piece of data when we weren't supposed to. **We highly recommend checking out the end of the Template Classes and Const-Correctness lecture before starting!** We have already marked a couple member functions const for you (check out the const overload of `begin()`, it is up to you to find the rest! Here is what you need to do to complete this milestone:  
你的第一个任务是为 HashMap 类创建一个 const-interface。回想一下，我们经常标记变量，尤其是函数参数 `const` 以避免特定类型的错误：在不应该修改数据时修改一段数据。我们强烈建议您在开始之前查看模板类和恒常正确性讲座的结尾！我们已经为您标记了几个成员函数 const （查看 的 `begin()` const 重载，由您来找到其余的！以下是完成此里程碑所需执行的操作：

1.  Head to `main.cpp` and pay special attention to all the helper functions called by `student_main()`. None of them have bugs, but some of them don't properly mark their parameters `const` when necessary. Mark the appropriate parameters `const`. Now, if you try to build, you will notice your code doesn't compile! Thats because most of your HashMap functions are not marked `const`.  
    前往并 `main.cpp` 特别注意 调用 `student_main()` 的所有帮助程序函数。它们都没有错误，但其中一些在必要时没有正确标记其参数 `const` 。标记相应的参数 `const` 。现在，如果你尝试构建，你会发现你的代码无法编译！那是因为你的大多数 HashMap 函数都没有标记 `const` .
2.  Head to your `hashmap.cpp` and `hashmap.h` files. Go ahead and mark any existing functions `const`, as long as this is appropriate. This is step 1 for creating your const-interface! Remember, any signatures you change `hashmap.h`, you also need to change in `hashmap.cpp`.  
    前往您的 `hashmap.cpp` 和 `hashmap.h` 文件。继续标记任何现有函数 `const` ，只要这是合适的。这是创建常量接口的第 1 步！请记住，您更改 `hashmap.h` 的任何签名 ，您还需要在 中 `hashmap.cpp` 进行更改。
3.  Finally, you will need to add a few functions (really, you will need to overload existing ones to have a `const` version) to make your const-interface robust. Recall that iterators by default allow us to dereference and change their underlying value, but sometimes we just want to use an iterator to, well, **iterate**, without changing any values. Make use of the `const_iterator` alias we provided you in `hashmap.h` to create const versions of the appropriate functions.  
    最后，您需要添加一些函数（实际上，您需要重载现有函数才能获得 `const` 版本）以使 const 接口健壮。回想一下，默认情况下迭代器允许我们取消引用和更改其基础值，但有时我们只想使用迭代器进行迭代，而不更改任何值。利用我们为您提供的别名 `hashmap.h` 来创建相应函数的 `const_iterator` const 版本。
    *   **HINT:** The main challenge here is identifying which functions to add. You will have to do little else than call the non-const version of the function in your new functions. As an example, we have given you a const version of `begin()`. Use the `static_cast/const_cast` trick demonstrated there in the rest of your functions!  
        提示：这里的主要挑战是确定要添加哪些功能。除了在新函数中调用函数的非常量版本外，您几乎无需执行任何其他操作。例如，我们为您提供了 的 `begin()` const 版本。在其余函数中使用那里演示的 `static_cast/const_cast` 技巧！
    *   **HINT:** Look at all the functions which return an iterator. Which of these should be overloaded to return a `const_iterator` when necessary?  
        提示：查看所有返回迭代器的函数。其中哪一个应该重载以在必要时返回？ `const_iterator`
    *   **HINT:** If you have marked the appropriate parameters `const` in `main.cpp`, any call to a non-const function will error, you can use this as a guide for which functions you might want to overload! There is one function that does not return an iterator that still must be overloaded, can you find it?  
        提示：如果您在 中 `main.cpp` 标记了适当的参数 `const` ，则对非 const 函数的任何调用都会出错，您可以将其用作您可能想要重载哪些函数的指南！有一个函数不返回仍然必须重载的迭代器，你能找到它吗？
4.  Finally, uncomment lines 5, 6, and 28 (the ones with the comment "UNCOMMENT FOR MILESTONE 2"), and try to compile again. If you missed any functions that needed to be marked const or overloaded, you will see that calls to those functions error in the compiler error messages!  
    最后，取消注释第 5、6 和 28 行（带有注释 “取消注释里程碑 2” 的行），然后尝试再次编译。如果您错过了任何需要标记为 const 或重载的函数，您将在编译器错误消息中看到对这些函数的调用错误！

#### Writeup 写起来

Please answer these questions in `short_answer.txt`  
`short_answer.txt` 请在

1.  ###### at() vs [] at（） 与 []
    
    Explain the difference between `at()` and the implementation of the operator `[]`. Wy did you have to overload one and not the other?  
    解释运算符 `[]` 和实现之间的 `at()` 区别。Wy 您是否必须超载一个而不是另一个？  
    _Hint: You will likely only need to read the header comments to do this  
    提示：您可能只需要阅读标题注释即可执行此操作_
    
2.  ###### Find vs. 𝓕𝓲𝓷𝓭 查找与查找
    
    In addition to the `HashMap::find` member function, there is also a `std::find` function in the STL algorithms library. If you were searching for key k in HashMap m, is it preferable to call `m.find(k)` or `std::find(m.begin(), m.end(), k)`?  
    除了 `HashMap::find` 成员函数，STL 算法库中还有一个 `std::find` 函数。如果你在 HashMap m 中搜索键 k，是否最好调用 `m.find(k)` 或 `std::find(m.begin(), m.end(), k)` ？  
    _Hint: on average, there are a constant number of elements per bucket. Also, one of these functions has a faster Big-O complexity because one of them uses a loop while another does something smarter.  
    提示：平均而言，每个存储桶的元素数量是恒定的。此外，其中一个函数具有更快的 Big-O 复杂性，因为其中一个函数使用循环，而另一个函数执行更智能的操作。_
    
3.  ###### RAII? 雷伊？
    
    This HashMap class is RAII-compliant. Explain why.  
    这个 HashMap 类是符合 RAII 的。解释原因。
    
4.  ###### Increments 增量
    
    Briefly explain the implementation of HashMapIterator's operator++, which we provide for you. How does it work and what checks does it have?  
    简要解释我们为您提供的 HashMapIterator 的运算符 ++ 的实现。它是如何工作的，它有什么检查？
    

### Milestone 2: Special Member Functions and Move Semantics  
里程碑 2：特殊成员函数和移动语义

Any good STL-compliant class must have correct **special member functions.**. Recall that there are six major special member functions:  
任何良好的 STL 兼容类都必须具有正确的特殊成员函数。回想一下，有六个主要的特殊成员函数：

*   Default constructor _(implemented for you)_  
    默认构造函数（为您实现）
*   Destructor _(implemented for you)_  
    析构函数（为您实现）
*   Copy constructor 复制构造函数
*   Copy assignment operator  
    复制赋值运算符
*   Move constructor 移动构造函数
*   Move assignment operator  
    移动赋值运算符

The first two are implemented for you; your job is to implement the last four. Specifically, the **copy** operations should create an identical copy of the provided HashMap, while the **move** operations should move the contents of the provided HashMap to `this` (the instance of the HashMap upon which the move operation is called). Avoid memory leaks and perform your copy/move as efficiently and safely as possible.  
前两个是为您实现的; 你的工作是实现最后四个。具体来说，复制操作应创建所提供 HashMap 的相同副本，而移动操作应将所提供 HashMap 的内容移动到 `this` （调用移动操作的 HashMap 实例）。避免内存泄漏，并尽可能高效安全地执行复制 / 移动。

#### Tips 技巧

Check out the Special Member Functions lecture and the Move Semantics lecture!  
查看特殊成员函数讲座和移动语义讲座！

Try to use the **member initializer list** (colon after constructor) whenever possible for efficiency!  
为了提高效率，尽可能尝试使用成员初始值设定项列表（构造函数后的冒号）！

We do not provide the headers, so you will have to add them to the .h file yourself.  
我们不提供标头，因此您必须自己将它们添加到 .h 文件中。

Read over the .h file again and make sure you understand each private member variable! You will need to fill these in your SMFs!  
再次阅读 .h 文件，确保您了解每个私有成员变量！您需要在 SMF 中填写这些内容！

We have given you helpers which cover the vast majority of the actual nitty gritty of filling a copied/moved hashmap. Pay special attention to `insert()` and `clear()`!  
我们为您提供了帮助，涵盖了填充复制 / 移动哈希图的绝大多数实际细节。特别注意 `insert()` 和 `clear()` ！

Hints 提示

*   You will need to move/copy the _buckets_array. Here is the one place you may have to work with pointers. One step of the first steps of copy should be to create a vector<node*> that is initialized to nullptrs. Then, you have a valid empty HashMap that you can start copying elements over using the public member functions.  
    您将需要移动 / 复制_buckets_array。这是您可能必须使用指针的一个地方。复制的第一步应该是创建一个初始化为 nullptrs 的向量 <节点 *>。然后，您有一个有效的空 HashMap，您可以使用公共成员函数开始复制元素。
*   `clear()` and `insert()` change the `_size` member variable, so keep that in mind when changing _size yourself. If you see that size is twice what you expect it to be, then you are probably incrementing _size twice - once in insert(), and once in your code.  
    `clear()` 并 `insert()` 更改 `_size` 成员变量，因此在自己更改_size 时请记住这一点。如果您看到大小是预期大小的两倍，那么您可能会_size 增加两次 - 一次在 insert（） 中，一次在代码中。
*   Be careful about self-assignment! **HINT:** Use `clear()` and `insert()` :)  
    小心自我分配！提示：使用 `clear()` 和 `insert()` :)
*   In the edge case tests you may see chained assignments (e.g. map = map = map). The assignment operator is right-associative, and also returns a reference to the HashMap itself, so this gets interpreted as (map = (map = map)). You don't need to worry about this - as long as the headers of your special member functions are correct and you avoid self-assignment, this case will be automatically handled.  
    在边缘情况测试中，您可能会看到链式分配（例如 map = map = map）。赋值运算符是右关联的，并且还返回对 HashMap 本身的引用，因此这被解释为 （map = （map = map））。你不需要担心这一点 - 只要你的特殊成员函数的头是正确的，并且你避免了自赋值，这种情况就会被自动处理。
*   Remember, you have iterators and the insert member function at your disposal!  
    请记住，您可以使用迭代器和插入成员函数！

#### Testing  测试

**You can run the Milestone 2 tests by uncommenting the lines marked "UNCOMMENT FOR MILESTONE 2" and then selecting option 2 when you run your code. Remember to `./build.sh` first!  
可以通过取消注释标记为 “取消注释里程碑 2” 的行，然后在运行代码时选择选项 2 来运行里程碑 2 测试。记得先！ `./build.sh`**

You don't need to look at them, and the testing file actually contains more tests than we run, but Test 4A, 4B, and 4C create copies of your HashMap using the copy constructor and copy assignment operator, and verifies their correctness. In addition, there are tests for edge cases such as self-assignment. Tests 4D, 4E, and 4F try to move HashMaps from one to another using the move constructor and move assignment, and verifies correctness just like test 4A.  
你不需要查看它们，测试文件实际上包含的测试比我们运行的要多，但是测试 4A、4B 和 4C 使用复制构造函数和复制赋值运算符创建 HashMap 的副本，并验证它们的正确性。此外，还有针对自我分配等边缘情况的测试。测试 4D、4E 和 4F 尝试使用 move 构造函数和 move 赋值将 HashMap 从一个移动到另一个，并像测试 4A 一样验证正确性。

Note that the move operations and time tests may pass even if you haven't implemented the move operations (recall that if no copy/move operations are declared, the compiler generates default ones, which will pass the move but not copy tests). See the test_settings.cpp for more information. The tests use some compiler directives (#pragma) to silence compiler warnings about self-assignment. You can safely ignore those.  
请注意，即使您尚未实现移动操作，移动操作和时间测试也可能通过（回想一下，如果未声明任何复制 / 移动操作，编译器将生成默认操作，这将通过移动，但不会通过复制测试）。有关详细信息，请参阅 test_settings.cpp。这些测试使用一些编译器指令 （#pragma） 来消除有关自赋值的编译器警告。您可以放心地忽略这些。

Test 4G and Test 4H time your move operations to verify that you are actually moving the contents of the HashMaps rather than copying the contents. The tests try the move operations on HashMaps of different sizes, and verify that the runtime of the move operations is O(1), not O(n). You can see the results of the time test printed in the test harness.  
测试 4G 和测试 4H 对您的移动操作进行计时，以验证您是否确实在移动 HashMap 的内容，而不是复制内容。测试在不同大小的 HashMap 上尝试移动操作，并验证移动操作的运行时是 O（1），而不是 O（n）。可以看到时间测试的结果打印在测试工具中。

  

#### Writeup 写起来

Please answer these questions in `short_answer.txt`  
`short_answer.txt` 请在

1.  ###### Attachment Issues 附件问题
    
    Why is it that we need to implement the special member functions in the HashMap class, but we can default all the special member functions in the HashMapIterator class?  
    为什么我们需要在 HashMap 类中实现特殊成员函数，而我们可以默认 HashMapIterator 类中的所有特殊成员函数？  
    _Hint: your answer should reference the Rule of Five (the Rule of 3 including move operations) vs. the Rule of Zero, and also talk about std::vector's copy constructor/assignment operator.  
    提示：你的答案应该参考五法则（包括移动操作的 3 法则）与零法则，并讨论 std：：vector 的复制构造函数 / 赋值运算符。_
  
3.  ###### Move Semantics 移动语义
    
    In your move constructor or move assignment operator, why did you have to std::move each member, even though the parameter (named rhs) is already an r-value reference?  
    在移动构造函数或移动赋值运算符中，为什么必须 std：：move 每个成员，即使参数（名为 rhs）已经是 r 值引用？  
    

### Submitting 提交

Please submit `hashmap.h`, `hashmap.cpp` and `short_answer.txt` to Paperless!  
请提交 ， `hashmap.cpp` 并 `short_answer.txt` 提交 `hashmap.h` 到无纸化！