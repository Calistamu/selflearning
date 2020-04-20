# 软件与系统安全学习笔记
按照[课程网站](http://programtrace.com/)清单上检测自己的能力。
* mspaint.exe是画图软件
* 学习了二维码制作
* 出于好奇网站的搭建，找到了[linux用户-个人网站快速搭建过程](https://cuijiahua.com/blog/2018/10/website-20.html)以及[WordPress博客网站的教程](https://www.zhihu.com/question/21535441)再或者[使用宝塔面板和Wordpress搭建独立网站](https://www.zhujibiji.com/2018/04/how-to-use-bandwagonhost-build-a-website/)，但是都只大致看了一遍，了解了一些东西，还没有实操解决。
## 课程总述
软件安全概括为三个方面：
1. 是有什么安全问题，安全问题产生的原因。   
分为两个方面：   
1）【二进制方面】：主要是内存相关问题和逻辑错误问题。有明确的机制，比如缓冲区溢出，空指针，格式化字符串。逻辑错误：多种多样了。  
2）【Web方面】：典型安全问题：xss\sql注入等。非典型：代码的逻辑错误，每个漏洞都可能有不同的原理。
2. 如何去发现问题，如何发现安全漏洞。  
漏洞挖掘技术：分为白盒分析和黑盒分析。  
【白盒分析】就是通过分析软件的源代码去寻找问题。这里面，有手工代码分析，就是我们的软件测试技术，和代码review。但是软件的源代码通常比较庞大，手工分析起来很费劲。所以，又有人研究如何自动化的分析代码。典型的技术呢，就是符号执行。  
【黑盒分析】就是完全不管软件的内部机理，不去看源码，把需要分析的软件当做一个黑盒子。看不见或根本不去看内部，从表面现象进行分析。对软件来说，就是看输入和输出。给它什么样的输入，得到什么样的输出，再去猜测他内部的机制，根据表面现象来分析。黑盒分析法，典型的就是Fuzzing技术，中文叫模糊测试。

3. 如果有人利用这些安全问题，如何防御。  
补丁：软件由于复杂性，不可能没有问题，只要发现了问题及时修补就是好样的。经过多年的发展，大家终于形成了一套行之有效的方法。就是通过漏洞数据库来披露和管理各种漏洞，厂家有义务定期发布软件补丁或者更新，软件用户应该及时升级软件。第三方人员，如果发现了安全问题，应该通报给厂家，而不是在漏洞修补以前，去利用漏洞搞破坏，或者不负责任的披露漏洞。  
但是呢，难免会有人就是要利用这些漏洞。所以在其他方面，也需要防御机制。如何去寻找记录攻击的痕迹，然后分析这些数据。数据的来源又分为了【主机层面】和【网络层面】，这就是我们入侵防御系统所做的事情。  
攻击的人会进行伪装，不让防御的人那么容易发现，攻击者可能会做哪些方面的伪装，常用的伪装技术有哪些，如何去对抗伪装，也是我们这个课的内容。这方面，典型的技术就是加壳脱壳技术，和Rootkit技术。


## 第一方面
重点：缓冲区溢出和xss，这两个也代表了二进制软件和脚本软件（包括绝大多数Web软件都是脚本软件开发的）两大技术方向。

* 计算机科学的特点就是，越底层的东西，越难。开发操作系统比开发app难多了。

二进制软件和脚本软件的区别：  
二进制软件：计算机的底层，是CPU直接执行在内存中的机器指令，C和C++这类编程语言开发的软件，有一个过程，叫编译链接，其实就是为了把程序变成CPU可以直接执行的二进制指令。这类软件的一个特点，就是需要直接操作内存。内存是所有在运行态的软件及其数据保存的地方。内存分为细小的单元，每个单元有一个唯一的地址。所有要访问数据，必须知道数据的地址，要保存新的数据，就必须分配内存，获得可用的地址。那么地址也是数，如果搞不好，不小心，计算错误了。那么就会访问到不该访问的数据，就会造成数据的泄露或者破坏。这就是二进制软件安全问题的根源。二进制程序的编程，有很大的难度，就在于CPU只能做出这样了，他是电路，是物理的东西，不可能设计得机制太复杂。使用C语言和C++就不可避免的一个东西：直接操作内存，也就是指针。然后在C和C++发展成熟以后，就有人去研究如何降低编程的难度，可不可以避免程序员编程时直接操作内存，把需要操作内存的地方，都封装起来。屏蔽在编程语言的内部。就发明了脚本语言。  
脚本语言：是干脆用C和C++这样的二进制程序开一个软件来执行一种新的程序。也就是用软件来模拟CPU工作。但是软件的可定制性比CPU就高多了，可以想定义什么指令就定义什么指令。把所有需要操作内存的东西，全部封闭在执行器内部，只给程序员接口，不给程序员操作内存的机会。这就是对象。比如把字符串封装为string对象。只能调用string.len()这样的方法来操作这个对象。这样就避免了由于编程不慎造成的内存相关问题。也降低了编程难度。所有大家看到python java js这样的程序，都有一个二进制程序的执行器。比如python.exe java.exe Web浏览器等。这些脚本程序的执行器，都是二进制程序。  


### XSS
xss的由来：  
虽然这些脚本程序没有了内存相关问题，有引出了其他的问题。事物总是复杂的。比如XSS的问题。就是web程序，存在一种高交互性。web是互联网时代的软件的基本框架，所以一定会有用户提交数据。当初为了网页动态的需求，开发了网页的前端脚本，比如js。直接把脚本嵌入到网页中。浏览器只要发现了script标签，就去当做脚本来执行。把网页按照程序员的定制，变的丰富多彩，变得富于变化。但是，恰恰另外一种需求，就是UGC软件，所谓用户产生内容。也就是网页的内容来自于用户提交的内容，这种软件已经非常常见了。比如BBS、博客、微博，电商视频网站的用户评论，都会涉及到用户提交的内容在页面上呈现。这两种机制，放在一起就产生了神奇的效果。当用户提交的内容里含有脚本呢？如果直接将用户提交的内容放在页面上，那么用户提交的内容中的脚本会不会被浏览器解析执行呢？那么一个用户提交了一个脚本就可以在这个页面的所有用户主机上执行呢？用户能提交程序执行了，怎么才能不保证这个程序不是恶意的呢？要知道，前端脚本，除了渲染页面元素这样的功能，还有获得用户的输入跳转页面到其他地址等等丰富的功能。
* 只有能执行，就能干很多事情。

#### xss实验  
实验原理提示  
1. 首先，你需要学习编写一个简单的html文件，这个文件只要有一个表单，用户就可以在表单中输入数据，向服务器提交。手写一个这样的网页大约只需要一分钟。
```
<html>
<body>
<form method="post">
        <input type="text">
        <button>提交</button>
      </form>
</body>
</html>
```
2. 下面呢，搭建一个web服务器，再有一些简单的处理过程，就可以重现xss了。  如果这个html是放在web服务器上，用户输入了数据，点击提交，浏览器就会把编辑框中的数据封装为一个POST请求，发给服务器。服务器会把这个数据发给后端脚本来处理。你可以通过定义 from的属性来指明需要哪个脚本文件来处理。比如PHP程序，他有一个POST超级变量，当用户提交了数据以后，对应的php脚本的post变量就是用户提交的数据。
3. 假设服务器现在把用户提交的数据放在user_input.html的body标签中。然后保存在服务器文件的根目录中。当有网站的用户访问http://xxxx.com/user_input.html的时候。就会看到刚才哪个表单用户提交的内容。当然实际的情况是这两个用户可能不是同一个用户，于是A用户提交的内容B用户就访问到了。当服务器脚本是原封不动的把用户输入的数据写到html里是，如果用户提交的数据中包括script标签，就会被执行。然后需要简单的学一下js语言。比如alert函数，弹出一个消息框。既然能执行alert函数，就能执行其他功能，比如给 window.location.href
赋值，让用户莫名其妙的跳转到另外一个网站。  
最简单的实验环境，就是在vscode中，安装一个php插件，然后编写一个简单的php脚本，调试运行这个脚本。F5 vscode会自动选择脚本运行的方式 ，把用户的表单输入写入到html文件。在通过浏览器访问这个文件html文件，这就是一个最简单的xss运行环境了。  
实际的XSS漏洞可能很复杂，比如还会有数据库啊，登录啊。等等，但是万变不离其宗，基本原理都是这样。  

[我的完成](https://github.com/Calistamu/Software-and-system-security/tree/master/test0x01-xss)-POST超级变量的思路
* 如果html出现乱码，在<head>标签中通过meta指定，是网页编码问题

#### 老师的演示

使用python最基本的http 服务器的方式。代码具体如下：
>files/httpserver.py

[http请求的多种methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)  

```
# -*- coding: utf-8 -*-

import sys
import cgi
from http.server import HTTPServer, BaseHTTPRequestHandler


class MyHTTPRequestHandler(BaseHTTPRequestHandler):
    field_name = 'a'
    form_html = \
        '''
        <html>
        <body>
        <form method='post' enctype='multipart/form-data'>
        <input type='text' name='%s'>
        <input type='submit'>
        </form>
        </body>
        </html>
        ''' % field_name

    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        try:
            file = open("."+self.path, "rb")
        except FileNotFoundError as e:
            print(e)
            self.wfile.write(self.form_html.encode())
        else:
            content = file.read()
            self.wfile.write(content)

    def do_POST(self):
        form_data = cgi.FieldStorage(
            fp=self.rfile,
            headers=self.headers,
            environ={
                'REQUEST_METHOD': 'POST',
                'CONTENT_TYPE': self.headers['Content-Type'],
            })
        fields = form_data.keys()
        if self.field_name in fields:
            input_data = form_data[self.field_name].value
            file = open("."+self.path, "wb")
            file.write(input_data.encode())

        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        self.wfile.write(b"<html><body>OK</body></html>")


class MyHTTPServer(HTTPServer):
    def __init__(self, host, port):
        print("run app server by python!")
        HTTPServer.__init__(self,  (host, port), MyHTTPRequestHandler)


if '__main__' == __name__:
    server_ip = "0.0.0.0"
    server_port = 8080
    if len(sys.argv) == 2:
        server_port = int(sys.argv[1])
    if len(sys.argv) == 3:
        server_ip = sys.argv[1]
        server_port = int(sys.argv[2])
    print("App server is running on http://%s:%s " % (server_ip, server_port))

    server = MyHTTPServer(server_ip, server_port)
    server.serve_forever()
```

* 代码讲解如下：  
使用python原生的cgi和http.server两个库运行的一个简单的http服务器程序。
61行，是程序入口。55行MyHTTPServer类，是继承自原生的HTTPServer。重写了 init函数，增加了打印输出语言，然后字节调用父类的 init 传递了服务器运行需要的地址、端口等参数。监听地址和端口是 0.0.0.0:8080
MyHTTPRequestHandler 类，这个是 HTTPServer 的回调。用来处理到达的请求。.0.0.0:8080 上有任何的HTTP请求到达时，都会调用 MyHTTPRequestHandler来处理。
MyHTTPRequestHandler 直接 继承自 BaseHTTPRequestHandler，其中 
8-52：BaseHTTPRequestHandler 的 do_GET和do_POST两个方法被重写。在 python 的 BaseHTTPRequestHandler 类中 ，do_XXX函数，就是处理对应的客户端请求的函数（浏览器所发送的数据包里包括请求类型， 在http 的headers里，会说明方法）。这个HTTP请求的处理类是整个代码的主体，也是出问题的地方。
http请求有很多种，我们通常使用得最多的，是GET和POST。大家直接在浏览器中输入链接，浏览器拿到地址以后，默认是采用GET方式向服务器发送请求。
代码14行，这里的表单是使用的post方法提交数据。通常来说，从服务器获取数据，使用get方面，向服务器提交数据，使用post方法。
所以在 58行指定了 MyHTTPRequestHandler 来处理 http请求，那么当用get方法请求，就会调用 do_GET,POST方法请求，就会调用 do_POST函数。这是python最基本的http 服务器的方式
27，self.path 是这个请求的路径。这里的 http://127.0.0.1:8080/a.html 其中 http://127.0.0.1:8080是协议服务器地址和端口。/a.html就是路径。通常，一个静态的http服务器，这里的路径就是http服务器根目录下的文件，动态服务器这里可能是文件和参数，或者是对应其他服务器后台的处理过程。通常，一个静态的http服务器，这里的路径就是http服务器根目录下的文件，动态服务器这里可能是文件和参数，或者是对应其他服务器后台的处理过程。指定有a.php来处理这个请求，参数是p1=x。问号后面是参数，可以有多个。那么所以我们就去读 a.html文件。
一般来说，如果文件不存在，应该返回什么？404。23行那个 self.send_response(200)200按照协议 应该是404。这里做了一个特殊的处理，如果指定的文件不存在，我还是返回200，表示请求路径是正确的，可以处理，然后返回一个默认的页面。这个页面就是 form_html的变量，在FileNotFoundError异常处理过程中写回。wfile和rfile对应http响应和请求的body部分。GET处理完成以后，浏览器就拿到了 200 状态的 "Content-type"为"text/html"的 form_html。
打开浏览器的调试模式，chrome在菜单-更多工具，开发者工具里面。到sources这个tab就看到了服务器向浏览器返回的数据，就是我们的form_html变量，这一段 html 浏览器渲染出来，就是那个带一个编辑框的表单。表单指定了使用post方式向服务器提交数据。network tab里可以看到完整的请求响应过程。
在表单中填入数据。点提交按钮。然后服务器的do_POST函数回被调用。这里通过 cgi.FieldStorage解析了客户端提交的请求，原始的请求的头部在self.headers。body部分在self.rfile。解析完成以后放到 form_data变量里，其中form_data['field_name'].value，就是你在编辑框中填入的数据。通常，一个服务器会根据业务逻辑处理用户提交的数据，比如用户发表的商品评论，你们在我的在线教学系统中填入的作业，一般会写入数据库，但是这些数据，在某些情况下又会被显示出来，比如我批改你们的作业，其他用户看你的商品评论的时候。我们这里为了模拟这个过程，简化了一下，没有用户系统，也没有数据库。直接写入了文件。而且是写入path对应的文件。如果写入成功，就返回一个200状态的OK。在 49-52行返回。44-47行处理了用户提交，写入文件。
如果这时大家提交了一个 123，这里获得是，对应的是form中input的name，15行，表单以变量名变量值的方式组织，input的name相当于变量名，你填入的数据就是变量值。python的cgi.FieldStorage将form组织为python的dict数据类型，所以可以通过  form_data['field_name'].value 获得所填入的数据。如果填入了 123 那么123被写入了a.html文件，执行完成后，你的目录下会多一个a.html，内容为123，然后你下次再访问 http://127.0.0.1:8080/a.html 时，在浏览器地址栏里回车。由于这个时候a.htm已经存在了所以是运行的31-33行的else部分，直接把文件内容会写给浏览器，这里时在简化模拟用户提交数据-存入数据-其他用户获取这个数据的过程。这里有就XSS漏洞了。下面大家再访问一个不存在的页面，比如b.html，又会出现那个默认的form。如果这时我们输入，```<html><body><script>alert('XSS')</script></form></body></html>```，这段内容就会被写入b.html，然后在访问b.html的时候，整个页面被载入 script在浏览器上执行。理论上，任何的js都是可以被执行的。js可以实现非常丰富的功能。比如可以让你扫码支付等等。这么复杂的功能我们不演示了，反正大家理解是可以实现的。下面我们稍微改一下。比如在 c.html里填入，```<html><body><script>window.location.href='http://by.cuc.edu.cn'</script></form></body></html>```，下次再访问c.html的时候。页面跳转了。这就是 ```window.location.href='http://by.cuc.edu.cn' ```这段脚本的功能。比如有一个商品A，你在评论里输入了一段js代码。如果服务器不做处理直接保存。后面的的用户访问商品A是，看评论，你输入的代码就会在其他用户的页面上执行。比如骗去用户支付，实际到账你的账户。
 下面还有更严重的漏洞，如果大家在浏览器中访问，```http://127.0.0.1:8080/httpserver.py```，由于服务器没有做任何过滤，只要是存在的文件，就发送给客户端，源代码文件也发送给了客户端。现在黑客可以知道我整个后台的逻辑了。如果还有一些配置文件，比如数据库地址和访问口令等。那就更严重了。
* 更严重的是，黑客甚至可以注入后端代码。由于我们是回写到文件，你可以构造一个http post请求，把httpserver.py文件改写了。但是构造这个请求用浏览器就不行了，需要采用curl等更基础的工具裸写post请求发送给服务器的.所以我们需要把input的name改为a,把 name="%s" 改为 name="a" 再提交,httpserver.py，它变了。所以，我们甚至可以给后端注入代码。如果只是注入一个hahaha 服务器就挂了。再也跑不起来了,因为他不是一个可以运行的python,这是一个及其简单，但是漏洞百出的web服务器。  
对于这一步，其实为什么这么做，我有了新的理解，如下图所示，因为httpserver.py多了引号，浏览器没有正常识别到field_name的值，因此input name的值为%s，当点击的提交的时候，提供给了(%s,xxx)这样的键值对，所以需要更改为a，而为什么智能改成a呢，因为Post里面对键值对处理有要求。如果改成的值和fiels_name的不一样，那么其实存储了但没能正确识别。
![](images/httpserverpy.png)

* vs vode直接打开文件运行与打开文件夹运行的区别  
[我的csdn文章](https://editor.csdn.net/md/?articleId=104938833)（当初迷迷糊糊的自己多么愚蠢）：  
对于之前的演示的a.html我还是能够访问到，并且我重新实验了一次，比如提示了h.html，提示没有此文件，但是依然创建了文件，输入了内容后，依然能够访问h.html
![](images/try-test.jpg)
如果不是打开文件夹的方式，并不是凭空存储的，是默认保存在了我设置的python的工作路径下，因为正好有一个.python_history来记录，那么中间文件就会出现在与.python_history同级的目录下。
![](images/source-to-python.png)
如果是打开文件夹的方式，那么所有结果输出和中间文件都会出现在所打开的文件夹下。  
而且二者在vs code上查看到的目录结构不一样，如下图。
![](images/openmethod-vs.png)

页面刷新和重新输入网址跳转：  
先来说“刷新”，它是在你现有页面的基础上，检查网页是否有更新的内容。在检查时，会保留之前的一些变量的值，因此有可能会造成刷新后网页出现错误，或者打不开的情况；“转到”和在地址栏回车，则相当于你重新输入网页的URL访问，这种情况下，浏览器会尽量使用已经存在于本机中的缓存。也就是说，“刷新” 是取网页的新内容来更新本机缓存，在更新的同时保留之前的一些变量；“转到”则是一种全新的访问，它会尽量使用本机缓存中的文件，但不会保留之前的变量

### web程序开发和web程序非常常见的一种漏洞-sql注入

#### http和html的基础知识 
首先，客户端（浏览器）和服务器要区分清楚。用户使用浏览器访问服务器，服务器在云端，我们看不见，只有一个域名对应的IP地址。浏览器通过发送请求到服务器，服务器收到请求以后返回响应。这里面数据的发送和接受，底层是计算机网络的东西的工作。对web程序来说，主要关心就是这一收一发的过程，Requests and responses 。
* 这里面，大家一定要做的一个实验，就是使用抓包器，去抓取一次web访问过程中的数据包，分析一下Requests and responses 数据的数据包格式。可以看到在tcp数据包的payload部分是http协议数据包，又分为了headers和body等部分。
然后发送的Request，最核心的部分是一个url。就是大家通常说的网址。其中/前的部分，是主机地址，会被解析为ip地址。第一个/后的部分为路径和参数。会对应服务器上的文件或者功能。然后，服务器处理完毕了，返回response，而response一般是一个html格式的文本。浏览器，会直接把html渲染为我们所看到的页面的不同样式。html有自己专门的语法。又可以嵌入js和css，用来要求浏览器执行动态的效果，或者根据css的配置，对各种html的内部的标签和元素，显示不同的样式。html的格式是以标签树的结构组织的。学习html和js最好的工具就是chrome的调试模式。
* 这里有第二个大家必做的实验，就是用chrome的开发者工具的 elements、sources和network几个工具，分析网页。

#### web程序开发的基本过程
框架：上周给大家演示的程序使用python内置的库开发了一个基本的http服务器端。涉及到了 刚才说的Requests and responses html 表单 get post url等基本概念了。现在的web程序一般都比较复杂，会使用数据库。而且一般会有很多功能和页面。为了方便web程序的开发，大家发明了一种开发web程序的基础架构。就是把web程序开发的一些底层的东西，已经处理好了。开发者只需要关注功能的构建就可以了。是现在主流的开发技术，称为框架。各种web后端编程语言，比如java、php、python都有自己的框架。其中大家最熟悉的python现在最火的框架是Django，然后是flask。
##### 以django为例，web开发的主要流程
[安装 django ](https://docs.djangoproject.com/en/3.0/),以下[官方文档教程四个命令](https://docs.djangoproject.com/en/3.0/intro/tutorial01/)，构建了一个基于Django的基本框架的web应用程序。然后访问 'http://127.0.0.1:8000/'，可以看到结果:  
* 如果pip安装速度很慢，大家可以修改pip使用的镜像源，改为国内的源，速度就会很快，比如我图里使用的aliyun的镜像。
```
# 下载安装Django
pip install Django```或```python -m pip install Django
# 四个命令，构建了一个基于Django的基本框架的web应用程序
django-admin startproject mysite
cd  mysite
python manage.py startapp polls
python manage.py runserver
```
在命令行里，可以看到服务器的打印输出，表示服务器收到了 request。大家看到的页面就是框架自动返回给大家的response。说明，request和response，请求相应的处理过程已经搭建起来了。
#### 做一个简单的教务管理系统-理解web开发流程
mvc:  
教务管理系统肯定要用到数据库了。我说一下Django框架的基本编程的结构。其他的框架也差不多。在大一的时候，给大家介绍过mvc的概念。编写大型程序的时候，一定要做到mvc分离，m数据模型，我们要先建立基础的底层的数据结构。然后在处理用户输入，构建每个用户输入对应的处理函数。就是c 控制。然后，在底层m数据模型的基础上，绘制用户界面。比如写贪吃蛇游戏，最先做的事情，使用链表来保存蛇和食物的相应的数据，写一些处理这个数据的函数，供上层的c和v来调用，我们把这个叫做封装。这是基本的编程思想，和正确的工作组织流程。大到一个复杂的大型web程序，其实底层思想还是mvc。  

mvt：  
只是换了个名字，叫mvt，t是页面模板。  

写Django的程序，或者任何框架程序。主要就是写三大部分：  
第一，数据模型，models，第二，views和url。是用户请求对应的处理程序。第三，前端页面模板。处理完的结果如何显示的问题。其中url部分，又称为路由。是把用户请求的url，对应到处理函数的过程。Django的处理函数，有一个专门名称，叫views。其基本过程就是框架收到用户的request ，其中有URL。框架根据urls.py中的配置。将用户请求对应到一个处理函数。一般在views.py中。views.py中的函数，参数就是request对象，Django中是HttpRequest类。然后views函数需要返回一个HTTPResponse类型的request，Django把这个reqeust变化为http协议的request数据包，返回给浏览器。一般在views的处理过程中，会访问数据库，也就是models。models吧底层的数据库操作，比如sql全部封装为了对象化的处理。比如底层操作数据库是用sql语句，这个大家在数据的课程中学习过。所以我们最原始的web程序，一般会程序员拼装sql语句。但是在Django中不用。我们把这种底层数据的封装，称为orm（Object-relational Mapper）。

框架：框架第一，把web开发流程变成了mvc结构。第二，提供了非常多丰富的web开发过程中需要使用的库。框架处理了最基本的请求响应过程。把请求映射到了处理函数，程序员就不用管很多麻烦的底层过程。只需要专心业务逻辑的处理。  

使用数据库，现在我们使用的数据库分两种，一种叫关系型数据库，一种叫非关系型数据库。其中教务系统这种信息管理类的软件，一般是使用关系型数据库。关系型数据库的基本结构是表。那如何体现“关系”呢？关系其实是指表与表之间的关系。首先就是设计数据库表结构，一个教务系统，最少需要三张表：学生信息、课程信息、选课信息。
```
# 建一个app：edu_admin
python manage.py startapp edu_admin
```
修改表结构：替换edu_admin中models.py，内容如下：
```
from django.db import models
from django.contrib.auth.models import AbstractUser

class Course(models.Model):
    name = models.CharField(verbose_name='课程名', max_length=100)
    number = models.IntegerField(verbose_name='编号', default=0)
    summary = models.CharField(verbose_name='摘要', max_length=500, null=True)

class Student(models.Model):
    class_name = models.CharField(verbose_name="班级", max_length=100, blank=True, null=True)
    name = models.CharField(verbose_name="姓名", max_length=100, blank=True, null=True)
    number = models.IntegerField(verbose_name="学号", default=0)
    phone_number = models.CharField(verbose_name='手机号', max_length=11, null=True)

class Score(models.Model):
    course = models.ForeignKey(Course,verbose_name='课程', on_delete=models.CASCADE, related_name='students')
    student = models.ForeignKey(Student,verbose_name='学生', on_delete=models.CASCADE, related_name='my_courses')
    score = models.FloatField(verbose_name='成绩', null=True)
```
我们需要把这个表结构，真实的写入到数据库中。也就是create table的过程。django称为migrate。打开 mysite的settings.py，在  INSTALLED_APPS 这里增加一个 edu_admin[官方添加方法](https://docs.djangoproject.com/en/3.0/intro/tutorial02/#activating-models)，表示 edu_admin 这个是我们这个site的一个app，之前startapp命令只是创建了app，必须要把app写入到这里，这个app才会被纳入到站点功能中。
```
python .\manage.py makemigrations
python .\manage.py migrate
```
效果如下图
![](images/django-migration.png)
然后会出现一个 db.sqlite3文件数据库表结构就建立完成了。Django这里默认使用了sqlite这种简单的文件型数据库。settings里加app,加了才会有刚才建的表.Django这里默认使用了sqlite这种简单的文件型数据库。这种数据库的好处是不用按照，就是一个文件来保存数据的所有信息，适合轻量级小规模的应用。但是效率和容量都有效。一般用在开发调试环境，不用在生产环境。加了app以后，执行makemigrations和migrate。makemigrations成功的标志是在app的目录下有migrations目录。  

为了验证Django真的建立了表，我们去下载一个sqlite的客户端软件，来看一下它的表结构。[Windows的同学，下载sqlite-tools-win32-x86-3310100.zip](https://www.sqlite.org/download.html),Linux的同学直接 apt install sqlite3。把这个exe加入在PATH环境变量，或者放在db.sqlite同一个目录,然后```sqlite3.exe db.sqlite3```进入到sqlite的命令行以后 执行 ```.table```。然后可以看到所有的表,如下图所示。

![](images/sqlite-table.png)

这三个表是我们在models中定义的。其他表是Django自己要用的。然后大家可以执行sql语句，插入一条记录。insert和select可以成功。如下图所示，说明表是好的。

![](images/sqlite-insert.png)

然后我会说sql注入的基本原理。然后再给大家分析一下sql注入和xss在django框架下是如何被解决的。

* web开发作业：  
在不使用Django的情况下，我们可以使用更底层的pyhton的sqlite库来编程操作数据库。'https://docs.python.org/3/library/sqlite3.html'。大家可以在上周的httpserver.py的基础上，继续编写漏洞。写两个页面，一个是教师录入成绩页面，一个是学生查询成绩页面。教师录入成绩页面表单有三个字段，课程id，学生id，成绩。录入提交以后，httpserver调用sqlite库使用sql语句写入数据库。然后是学生查询成绩表单，学生输入学生id，课程id，httpserver使用sql语句查询成绩后返回给用户。这里不需要做登录功能，课程也用直接输入id而不是下拉菜单的方式，或者其他选择的方式，而是直接输入id。为了体验最原始的web的开发过程。  
大家用原始的方式写完了以后，后面我们再讲先进的方式如何写。以及原始的方法有什么问题，先进的方法如何规避了这些问题。

继续讲web程序设计和SQL注入  
使用Django这种框架编程，第一步是定义模型，Django会自动把定义好的模型转化为数据库表结构。这种方式称为 ORM，上节课讲过。使用Django这种框架编程，第一步是定义模型，Django会自动把定义好的模型转化为数据库表结构。这种方式称为 ORM，上节课讲过。  
下面，我们来写view。views是Django功能实现应用功能的地方。如果你想写一个动态的页面，就在views中定义一个函数。这是最基本的方法。在此基本的方法上还可以有高级的，系统内置的一些高级的数据库增删改查的方法。最基本的views函数，是收到一个HttpRequest类型的参数，需要返回一个HTTPResponse类型的返回值。和http协议对应。在edu_admin中的views.py写入以下内容。
```
from django.http import HttpResponse
def index(request):
    return HttpResponse('<html><body>OK</body></html>')
```
这个函数就是一个基本的 “处理请求，返回响应”。写好了以后，还没有结束。我们还需要把这个views，对应到一个路径上。也就是客户端如何调用这个views函数对应的功能。因为一个实用的web app只有一个views是不够的，可能有很多很多views。然后我们需要把这些views对应到不同的url上。这样客户端才能访问。这个工作，是urls.py来完成的。下面我们在urls.py中写如下内容。  
在 edu_admin中建一个urls.py文件，写入如下内容。
```
from django.urls import path
from .views import *

urlpatterns = [
     path('index', index),
]
```
然后需要再主urls.py，也就是 mysite的urls.py中包括这个url配置。
```
from django.contrib import admin
from django.urls import path
from django.conf.urls import include

urlpatterns = [
    path('edu/', include('edu_admin.urls')),
    path('admin/', admin.site.urls),
]
```
* edu_admin中的urls.py对应的是views.py中的函数，mysite/urls中对应的是各个app中的urls.py
这是为了适应，可能有多个Django app共同工作的情况。比如我们这里的edu_admin是一个app，polls又是一个app.有了以上修改，我们就可以运行我们的网站，看看效果了。

运行:
1. ```pyhton manage.py runserver```命令。
2. 也可以在vscode中调试运行，让在调试页面，生成一个launch.json选python Django。默认配置，然后就可以调试了。
* launch.json的作用:告诉vscode，程序启动程序运行,任何一种编程语言和平台，都会有launch.json.
![](images/launchjason.png)
Run之后访问 127.0.0.1:8000,结果如下图。
![](images/edu404.png)
这个是404页面。说明我们访问的url路径并不存在,只有edu/和admin/两个路径存在,正如我们在mysite/urls.py中配置的一样。访问‘http://127.0.0.1:8000/edu/index’，如果这一步有，就说明我们 urls和views配合工作成功。用户在浏览器中输入路径，django把这个url对应到一个views函数上。views函数处理HttpRequest。返回HttpResponse。这个工作流程跑通,以后我们要开发一个大型的网站，也是这么写.
* 把url对应到一个views函数的过程，专业术语叫“路由”。注意，这个路由不是路由器的那个路由。路由器的路由是IP层的IP数据包分发功能。web框架的路由只是借用了这个概念，引申为 web程序中url和处理函数的对应关系。
注意一个url是可以带参数的。views函数也是可以带参数的。比如
![](images/edu-urls.png)
![](images/edu-views.png)
这么改一下,比如'http://127.0.0.1:8000/edu/index/100'就可以有一个动态的效果,如图所示。
![](images/dynamic-index.png)
pk就是参数，而且只允许整数。路由系统会把这个参数赋值传递给views函数的pk参数。下一步，如何在views中访问数据库。
![](images/edu-viewspy.png)
这里我讲一下，如何在views函数中访问数据库。关键在第5行和第14行。先从models中导入模型类.然后调用这个模型类的objects的filter方法，就完成了一次sql select。filter函数的参数是就相当于查询的过滤条件。我们要查询的是 student为当前登录用户的Score表的记录。```Score.objects.filter(student=request.user)```就这么写就完成了，非常的方便。Django中，当前登录用户，就在request对象的user属性中。views写了还不够。我们还需要修改一下模型。Django是有默认的用户管理系统的。用户相关的数据库表结构其实Django已经建立好了。但是我们这里用了student表来作为系统用户。所以我们要告诉Django不要用系统默认的用户模型了，用Student。首先在 models.py中导入```from django.contrib.auth.models import AbstractUser```这个是Django默认的用户管理的数据库模型,然后继承修改之，如图所示。
![](images/change-studentmodels.png)
AbstractUser已经有很多数据库字段了，比如密码肯定是需要以某种方式保存到数据库中的。这些字段 AbstractUser都有了。我们在AbstractUser的基础上，扩充几个我们要用的字段就可以了。Student继承了AbstractUser后。告诉Django用Student作为系统用户管理的数据库模型。在mysite settings.py也就是整个站点的配置文件中，增加一条。```AUTH_USER_MODEL =  'edu_admin.Student'```
![](images/change-mysite-setting.png)
告诉Django，使用 edu_admin 的Student作为用户管理登录授权的模型。代码修改完以后。这里是不是涉及到数据库修改啊。所有要进行数据库表结构的migrate.
* 迁移过程中，可能会要求输入一些默认值。全部都输入为空字符串就可以 ''
```
# 生成迁移文件
python manage.py makemigrations 
# 实施迁移
python manage.py migrate
```
下面，我们尝试在数据库中写入一些数据。然后测试看看Django的数据库访问是否正常。最原始的方法就是在sqlite.exe中用sql语句插入。但是这个方法容易把数据搞乱了。而且比如用户密码这样的东西，不是明文保存的。所有插入会有问题。我们用Django的方式，先建立一个超级用户.
```
python manage.py createsuperuser
```
建立一个管理员账户。建立好了以后，用sqlite3.exe可以看到Student表多了一条记录。然后我们可以用Django的admin功能，用超级管理员录入数据。Django的admin相当于一个数据管理的超级权限后台。可以直接操作数据库。在admin.py中录入以下内容。
![](images/edu-admin.png)
这样直接就可以生成一个管理数据库的后台页面，访问 http://127.0.0.1:8000/admin/ 刚才新建的用户 登录后看到这个页面。如下图所示。
![](images/edu-web.png)
可以录入一些课程，学生和成绩.
![](images/insert-grades.png)
为了在admin管理的时候，直接显示课程名称，可以给course模型增加一个 __str__方法。这样所有course对象的str ，返回的是name字段。界面显示就是课程名称了。当数据库有了值以后。我们就可以在view中验证，我们查询自己成绩的功能是否正常了。views中的@login_required表示需要登录。我们这里已经用超级管理员登录了，所以是可以访问的。
![](images/edu-newmodels.png)
然后说一下views.py中的render函数。
![](images/viewspy-render.png)
render是一个Django内置的函数。用于在模板文件的基础上，通过渲染得到动态的网页效果。其中score.html是模板,后面的{}dict是参数,render必须传参reqeust,然后render函数就会在模板html文件的基础上，生成一个html,并返回 HTTPResponse,所以可以直接作为 views函数的返回。
那么还需要一个score.html，在templates目录下。
![](images/edu-scorehtml.png)
这里的result 就是 render传入的result,对每一个result 显示其课程名和分数,大家看到这里的多表查询 (course表中的name）直接. 就调用出来了。模板语言的语法 {{ 变量名 }}。写了新的views函数，需要在edu_admin/urls.py中增加函数
![](images/addurls.png)
然后访问得到结果，如下图所示。
![](images/edu-scorehtml-show.png)
这就完成了当前登录用户（超级管理员 admin 同学）的成绩查询。注意，这里我们偷了一个懒，实际情况，并不是每个用户都是超级管理员。需要有普通用户登录注册页面。这个页面需要自己写，我们这里时间关系，先不实现普通用户的登录，先用超级管理员用户验证一下查询功能。实际情况下普通用户是不能访问 127.0.0.1:8000/admin页面的。
* 大概想了想：管理员与用户，进行判断，然后跳转不同的页面，只有正确的管理员用户名和密码才能进入后台，网上有例子可学。
* 对数据库又更理解了一些
* [cookie与session](https://www.zhihu.com/question/19786827)
把权限赋予到Group。然后用户可以加入到若干不同的Group，就可以有不同的权限。而且也可以通过数据库加字段的方式来控制权限。比如给Course增加一个field，任课老师。只有任课老师才能录入成绩。程序可以控制。编程实现。
* [django权限管理](https://cloud.tencent.com/developer/article/1155184)
大型数据库可不是一个文件这么简单。不同的数据库都有不同的直接访问数据的客户端软件。然后所有的数据库又都支持sql。

* 作业：学习sql_injection.py
#### 通过sql_injection.py学习sql注入
>file/mysite/sql_injection.py
* 在vscode中调试，我们需要增加launch.json的配置。在调试界面，点击 “增加配置”，选python 当前文件.然后在 launch.json中，会怎么一个配置项。用这种方式可以调试sql_injection。然后点击sql_inejction文件，使其成为编辑器的当前文件，就可以调试了。
运行以后，是一个编辑框，输入学生ID，查询对应的成绩。输入1，得到如下图结果。返回了用户id=1的成绩
![](images/sql-.png)
如果输入1 OR 1=1,得到如下图的结果。查出了当前系统中所有用户的成绩。相当于获得了整个数据库。
![](images/sql2.png)
问题在代码的43行，我们直接把用户输入的数据，作为sql语句中的查询条件。最后的 sql语句为：```SELECT edu_admin_course.name, edu_admin_score.score FROM edu_admin_score INNER JOIN edu_admin_course ON edu_admin_score.course_id=edu_admin_course.id WHERE student_id = 1 OR 1=1```
查询条件变成了 student_id = 1 OR 1=1,1=1恒为真， 任何数OR真值，也是真。所以，相当于 SELECT edu_admin_course.name, edu_admin_score.score FROM edu_admin_score INNER JOIN edu_admin_course ON edu_admin_score.course_id=edu_admin_course.id WHERE true;或者没有WHERE,变成了无条件查询。于是显示出了数据中的所有记录。  
还是我们之前说的，在软件安全中，有一个原则，所有用户的输入都是不可信的。因此，我们必须对用户输入进行过滤。进行严格的限制。那么这里提个问，如何打补丁？如何修改才能避免这个漏洞？我提示一下。既然输入的为ID，那么ID是不是只能是整数。使用参数化查询语句:不将用户的输入作为SQL指令的一部分处理，而是在完成SQL指令的编译后，才套用参数执行。  
两种方法，一种很简单。就是对用户输入进行过滤，比如这里。我们可以判断一下input_data是否数字就可以，用python内置函数 isdigit就可以判断。在这个具体的漏洞可以采用这种方法。但是对于大型的系统，会有很多sql语句拼接和执行的地方。每一个都去过滤，编程效率很低，而且不一定能保证你写的过滤就是对的。实际系统的业务远比我们这里输入ID要复杂。这里就在说回到Django，这就是框架ORM的意义了。ORM完全避免了程序员直接接触sql语言，所有的sql语句都在模型管理器中有框架进行拼接。程序员在编程时，只需要使用模型管理器提供的方法进行查询，创建等，就可以了。比如，我们之前写的Django代码。```result = Score.objects.filter(student=request.user)```底层在进行sql的拼接,就避免了这种情况。这里我说明一下，Django的模型管理器中，主要有filter get等获取数据的方法。这些方法返回的数据类型是QuerySet数据类型。这个数据类型是一个数据库访问的接口。在调用filter时，实际上还未查询数据库，只是初步完成了数据库sql语句的拼接。实际的查询是在render中进行的。Django会根据render时需要的具体数据，来精确优化查询语句，所有这里的result，并不是真正的查询结果。而是一个查询对象。在模板 score.html 我们用到了 数据 {{ i.course.name }}，course是 socre表的一个外键，course.name实际是在course表中。所以这里其实是一个跨表查询。这种两个表的跨表查询，我们自己写的sql语言已经比较复杂了。真实系统往往会有多个表的联合跨表查询，sql语句会非常复杂。但是Django处理后，查询数据库的操作就变得非常简单，把数据中的值得访问，编程了python对象的属性访问。所以，建议大家，使用框架。  
但是，从学习的角度，我们需要知道Django内部是怎么做的，也就是我也需要一些底层的http server的开发原理，比如request response模式，html sql语言，数据库表结构等。底层知识要了解。这有助于我们理解Django的工作原理，学习起来就很快。对一些高级的操作也能比较深入理解。但是，具体做工程的时候，就尽量不要直接使用原始的方法了。这是从方法上给大家的建议。就比如，学过windows GDI，都知道，所有的界面原始都是使用GDI绘制出来的，但是如果我们写一个软件会自己去用GDI来做原始操作吗？不会，因为有上层的软件做好了控件，我们直接调用控件，然后有了html。我们直接编写html可以快速的构建软件界面，比自己写GDI，效率高一万倍。别人已经做好了的事情，我要了解原理，但是没有必要自己再去做一遍。这就是那句名言，不要重复发明轮子的意思。web安全介绍了xss和sql注入，我做的只是一个入门的工作。这点知识是远远不够的，实际还需要大家化大量的学习更多更深入的内容。  
* 补充两点 ，student_id = 1; DROP TABLE xxx，这种注入方式，可以获得任意表的数据。在sqlite中，大家做实验室的时候，可以用PRAGMA table_info(table_name);取得表项的名字。因为表项的名字是Django建的，我们要知道表项的名字可以用这种方法。

### 二进制安全
复习缓冲区溢出漏洞：   
在使用C C++编写的原生应用程序。CPU在执行是，会有一个栈的结构。这个栈的结构是程序执行过程中，与程序当前运行的所处的位置密切相关的。因此程序基本的组织单元是函数，函数的调用、返回等等是基本的操作。一个大型的程序，会形成一个函数调用关系图。栈，实际上是CPU用来记录当前执行到哪个函数的这个一个数据结构。所有每次函数调用会入栈一些东西，函数调用返回会出栈一些东西。由于栈和函数的这种一一对应，所以在设计的时候，直接把与函数密切相关的局部变量和参数也顺便保存在了栈中。如果局部变量的写入超过了预先分配的长度，就会覆盖其他数据。栈中的数据有与执行流程相关的，例如函数执行返回地址等。如果覆盖，就会造成执行流程的异常。
* 这个实验上学期已做通
#### 内存管理
* 以4MB（页）作为基本管理单元的虚拟内存管理。
* 虚拟内存管理是一套虚拟地址和物理地址对应的机制。
* 程序访问的内存都是虚拟内存地址，由CPU自动根据系统内核区中的地址对应关系表（分页表）来进行虚拟内存和物理内存地址的对应。
* 每个进程都有一个分页表。
* 每个进程都有一个完整的虚拟内存地址空间，x86情况下为4GB（0x00000000-0xffffffff）
* 但不是每个地址都可以使用（虚拟内存地址没有对应的物理内存）
* 使用VirtualAlloc API可以分配虚拟内存（以页为单位）、使用VirtualFree释放内存分页。
* 使用VirtualProtect 修改内存也保护属性（可读可写可执行）
* 数据执行保护（DEP）的基本原理
* malloc和free等C函数（也包括HeapAlloc和HeapFree等）管理的是堆内存，堆内存区只是全部内存区的一个部分。
* 堆内存管理是建立在虚拟内存管理的机制上的二次分配。
* 真正的地址有效还是无效是以分页为单位的。
* 内存分页可以直接映射到磁盘文件（FileMapping）、系统内核有内存分页是映射物理内存还是映射磁盘文件的内存交换机制。
* 完成内存分页管理的相关实验
```
int main() 
{
 char* a = malloc(100);
 a[101] = 'a';
}
```
我们分配了100个字节的内存单位。但是写入的时候写到时候，超出了两个字节。大家可以实验一下，这段代码，在执行的时候。不会有异常情况。程序能够正常退出。原因在于，操作系统对内存的管理，也是有开销的。系统本身需要在一块单独的系统内存中记录那些内存是可用的，那些内存是不可用的。如果记录内存是否可用这个信息太细，那么记录所有的内存开销就很高。比如，如果我们记录详细到每一个bit是否可用。如果系统的内存有1GB，记录内存是否可用的内存也需要1GB。这个开销有点太大了。所以，实际上，没有记录到这么细。在Windows系统中，通常是以4MB为单位进行管理的。也就是要么这4KB都可用，要么都不可用。这样，所需要的管理数据就小得多。    
malloc还不是最底层的内存管理方式。malloc我们称为堆内存管理。malloc可以分配任意大小的数据，但是，malloc并不管理一块数据是否有效的问题。而是由更底层的虚拟内存管理来进行的。一个4MB的内存管理单元，我们称为一个内存分页。当malloc在内存分配时，如果已经可用的分页中，还有剩余的空间足够用，那么malloc就在这个可用的分页中拿出需要的内存空间，返回地址。如果已经可用的分页不够用，再去分配新的分页。然后返回可用的地址。所以，malloc分配可以比较灵活，但是系统内部，不会把内存搞得特别细碎。都是分块的。  
并且，在任务管理器中，大家切换到详细信息页面。看看每个进程的内存占用都是4KB的倍数。这两个小实验，证明了，系统确实以4KB作为单元在管理内存，要么4KB全部有效，要么全部无效。虽然我们只分配了100个字节。但是这100个字节所在的整个4KB的内存全部是可用的。    
然后，我们每个4KB的内存分页，其实有三个属性，可读可写可执行。所以，我们甚至可以分配一块readonly的内存。那么如何改变一块内存的访问属性呢？用VirtualProtect 函数。虚拟内管管理，系统也提供了一些的函数来让应用程序可以自己管理。分配内存是用 VirtualAlloc，释放使用VirtualFree，修改属性使用 VirtualProtec大家记住这三个函数。只要是VirtualAlloc分配的内存，就可以使用。VirtualAlloc甚至可以指定希望将内存分配在哪个地址上。malloc函数底层也会调用VirtualAlloc函数。当没有足够的整页的的内存可用时，malloc会调用VirtualAlloc。所以，实际的内存分配，没有那么频繁。
* 作业：1、阅读VirtualAlloc、VirtualFree、VirtualProtect等函数的官方文档。2、编程使用malloc分配一段内存，测试是否这段内存所在的整个4KB都可以写入读取。3、使用VirtualAlloc分配一段，可读可写的内存，写入内存，然后将这段内存改为只读，再读数据和写数据，看是否会有异常情况。然后VirtualFree这段内存，再测试对这段内存的读写释放正常。
[virtualalloc](https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualalloc)、[virtualprotect](https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualprotect)、[virtualfree](https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualfree)、[memory-protection-constants](https://docs.microsoft.com/zh-cn/windows/win32/memory/memory-protection-constants)