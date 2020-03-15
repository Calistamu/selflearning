# 软件与系统安全学习笔记
## 课程总述
软件安全：
1. 是有什么安全问题，安全问题产生的原因。 
分为两个方面：   
1）二进制方面，主要是内存相关问题和逻辑错误问题。有明确的机制，比如缓冲区溢出，空指针，格式化字符串。逻辑错误：多种多样了。  
2）Web方面：典型安全问题：xss\sql注入等。非典型：代码的逻辑错误，每个漏洞都可能有不同的原理。
2. 如何去发现问题。  

 漏洞挖掘技术：分为白盒分析和黑盒分析。  
 白盒分析：就是通过分析软件的源代码去寻找问题。这里面，有手工代码分析，就是我们的软件测试技术，和代码review。但是软件的源代码通常比较庞大，手工分析起来很费劲。所以，又有人研究如何自动化的分析代码。典型的技术呢，就是符号执行。  
黑盒分析：就是完全不管软件的内部机理，不去看源码，把需要分析的软件当做一个黑盒子。黑盒分析法，典型的就是Fuzzing技术，中文叫模糊测试。对软件来说，就是看输入和输出。给它什么样的输入，得到什么样的输出，再去猜测他内部的机制，根据表面现象来分析。

3. 如果有人利用这些安全问题，如何防御。  

补丁：软件由于复杂性，不可能没有问题，只要发现了问题及时修补就是好样的。经过多年的发展，大家终于形成了一套行之有效的方法。就是通过漏洞数据库来披露和管理各种漏洞，厂家有义务定期发布软件补丁或者更新，软件用户应该及时升级软件。第三方人员，如果发现了安全问题，应该通报给厂家，而不是在漏洞修补以前，去利用漏洞搞破坏，或者不负责任的披露漏洞。  
但是呢，难免会有人就是要利用这些漏洞。所以在其他方面，也需要防御机制。如何去寻找记录攻击的痕迹，然后分析这些数据。数据的来源又分为了 主机层面 网络层面，这就是我们入侵防御系统所做的事情。  
攻击的人会进行伪装，不让防御的人那么容易发现，攻击者可能会做哪些方面的伪装，常用的伪装技术有哪些，如何去对抗伪装，也是我们这个课的内容。这方面，典型的技术就是加壳脱壳技术，和Rootkit技术。

* 计算机科学的特点就是，越底层的东西，越难。开发操作系统比开发app难多了。
## 第一方面
重点：缓冲区溢出和xss，这两个也代表了二进制软件和脚本软件（包括绝大多数Web软件都是脚本软件开发的）两大技术方向。

二进制软件和脚本软件的区别：  
二进制软件：计算机的底层，是CPU直接执行在内存中的机器指令，C和C++这类编程语言开发的软件，有一个过程，叫编译链接，其实就是为了把程序变成CPU可以直接执行的二进制指令。这类软件的一个特点，就是需要直接操作内存。内存是所有在运行态的软件及其数据保存的地方。内存分为细小的单元，每个单元有一个唯一的地址。所有要访问数据，必须知道数据的地址，要保存新的数据，就必须分配内存，获得可用的地址。那么地址也是数，如果搞不好，不小心，计算错误了。那么就会访问到不该访问的数据，就会造成数据的泄露或者破坏。这就是二进制软件安全问题的根源。二进制程序的编程，有很大的难度，就在于CPU之能做出这样了，他是电路，是物理的东西，不可能设计得机制太复杂。使用C语言和C++就不可避免的一个东西：直接操作内存，也就是指针。然后在C和C++发展成熟以后，就有人去研究如何降低编程的难度，可不可以避免程序员编程时直接操作内存，把需要操作内存的地方，都封装起来。屏蔽在编程语言的内部。就发明了脚本语言。
脚本语言：是干脆用C和C++这样的二进制程序开一个软件来执行一种新的程序。也就是用软件来模拟CPU工作。但是软件的可定制性比CPU就高多了，可以想定义什么指令就定义什么指令。把所有需要操作内存的东西，全部封闭在执行器内部，只给程序员接口，不给程序员操作内存的机会。这就是对象。比如把字符串封装为string对象。只能调用string.len()这样的方法来操作这个对象。这样就避免了由于编程不慎造成的内存相关问题。也降低了编程难度。所有大家看到python java js这样的程序，都有一个二进制程序的执行器。比如python.exe java.exe Web浏览器等。这些脚本程序的执行器，都是二进制程序。  

xss的由来：虽然这些脚本程序没有了内存相关问题，有引出了其他的问题。事物总是复杂的。比如XSS的问题。就是web程序，存在一种高交互性。当初为了网页动态的需求，开发了网页的前端脚本，比如js。直接把脚本嵌入到网页中。浏览器只要发现了script标签，就去当做脚本来执行。把网页按照程序员的定制，变的丰富多彩，变得富于变化。但是，恰恰另外一种需求，就是UGC软件，所谓用户产生内容。也就是网页的内容来自于用户提交的内容，这种软件已经非常常见了。比如BBS、博客、微博，电商视频网站的用户评论，都会涉及到用户提交的内容在页面上呈现。这两种机制，放在一起就产生了神奇的效果。当用户提交的内容里含有脚本呢？如果直接将用户提交的内容放在页面上，那么用户提交的内容中的脚本会不会被浏览器解析执行呢？那么一个用户提交了一个脚本就可以在这个页面的所有用户主机上执行呢？用户能提交程序执行了，怎么才能不保证这个程序不是恶意的呢？要知道，前端脚本，除了渲染页面元素这样的功能，还有获得用户的输入跳转页面到其他地址等等丰富的功能。
* 只有能执行，就能干很多事情。