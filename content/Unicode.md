>Joel on Software

>#The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)

#所有软件开发者绝对，一定，必须，至少要知道的Unicode和字符集（没有任何借口） 

>#####by Joel Spolsky Wednesday, October 08, 2003

---

>Ever wonder about that mysterious Content-Type tag? You know, the one you're supposed to put in HTML and you never quite know what it should be?


有没有好奇那个神奇的Content-Type标签？你知道那个标签要放到HTML页面里面去，但是你却从来没搞清楚要放什么


>Did you ever get an email from your friends in Bulgaria with the subject line "???? ?????? ??? ????"?


有没有收到过保加利亚朋友寄来的电子邮件，标题栏写着“？？？？？？？？？？？？？？”？


>I've been dismayed to discover just how many software developers aren't really completely up to speed on the mysterious world of character sets, encodings, Unicode, all that stuff. A couple of years ago, a beta tester for FogBUGZ was wondering whether it could handle incoming email in Japanese. Japanese? They have email in Japanese? I had no idea. When I looked closely at the commercial ActiveX control we were using to parse MIME email messages, we discovered it was doing exactly the wrong thing with character sets, so we actually had to write heroic code to undo the wrong conversion it had done and redo it correctly. When I looked into another commercial library, it, too, had a completely broken character code implementation. I corresponded with the developer of that package and he sort of thought they "couldn't do anything about it." Like many programmers, he just wished it would all blow over somehow.


我很失望的发现原来那么多的软件开发者对于神奇的字符集，编码，Unicode等等东西的了解真的并不是那么到位。几年前，FogBUGZ的beta测试员很好奇能不能处理进来的日文电子邮件。 日文？他们还用日文写电子邮件？ 完全没概念。 当我仔细的分析了我们使用的用来解析MIME电子邮件消息的商业ActiveX控件之后，我们发现它对于字符集的处理完全是错误的。所以我们只好英勇地先撤销它做的错误转换然乎再正确的转换一遍。我看了另外一个商业代码库，发现它的字符集实现也是完全错误的。我联系了该代码包的开发者，他似乎有种观念“他们也无能为力。”就像很多程序员一样，他就希望那是一定程度上能混过去的。


>But it won't. When I discovered that the popular web development tool PHP has almost complete ignorance of character encoding issues, blithely using 8 bits for characters, making it darn near impossible to develop good international web applications, I thought, enough is enough.


但不行，我发现流行的网页开发工具PHP几乎完全忽略了字符集编码问题，草率的就用8比特来表示字符，结果几乎无法开发出好的国际化网页应用程序来，我觉得，受够了。


>So I have an announcement to make: if you are a programmer working in 2003 and you don't know the basics of characters, character sets, encodings, and Unicode, and I catch you, I'm going to punish you by making you peel onions for 6 months in a submarine. I swear I will.


所以我要发布一个声明：如果你是在2003年工作的程序员，而你不知道字符，字符集，编码和Unicode基础，如果让我逮到了，我要逼你在潜水艇里剥洋葱以示惩戒，我发誓我一定会这么做的 。


>And one more thing:


还有一件事情： 


>###IT'S NOT THAT HARD.

###这其实没那么难


>In this article I'll fill you in on exactly what every working programmer should know. All that stuff about "plain text = ascii = characters are 8 bits" is not only wrong, it's hopelessly wrong, and if you're still programming that way, you're not much better than a medical doctor who doesn't believe in germs. Please do not write another line of code until you finish reading this article.


在这片文章里我将向所有正在工作的程序员灌输这些基本概念。 所有那些说法“普通文本=ascii=8比特字符”不仅是错的，而且错的非常离谱，如果你还要继续那么做，那么你比电视购物里那些连细菌都不懂的媒体医生也好不到哪儿去了。在读完这篇文章之前，请不要写代码。


>Before I get started, I should warn you that if you are one of those rare people who knows about internationalization, you are going to find my entire discussion a little bit oversimplified. I'm really just trying to set a minimum bar here so that everyone can understand what's going on and can write code that has a hope of working with text in any language other than the subset of English that doesn't include words with accents. And I should warn you that character handling is only a tiny portion of what it takes to create software that works internationally, but I can only write about one thing at a time so today it's character sets.


在我开始之前，我应该提醒一下，如果你是那种罕见的懂国际化的开发者的话，你会发现我的整个讨论有一点过分简化了。我真的只是想设定一个最低标准，这样所有人就能够知道到底是怎么回事，然后有希望写出能够处理除了英语和英语子集不包含口音之外的语言。 还要提醒的是字符集处理只是编写国际化软件中非常小的一部分， 不过我一次只能说一样东西，所以今天只说字符集处理。


>###A Historical Perspective

###历史观点


>The easiest way to understand this stuff is to go chronologically.


理解这件事情的最佳途径就是按时间顺序了


>You probably think I'm going to talk about very old character sets like EBCDIC here. Well, I won't. EBCDIC is not relevant to your life. We don't have to go that far back in time.


你也许觉得我要跟你们讲非常古老的扩展二进式十进制交换码了。不，我不会说那个， 扩展二进式十进制交换码跟你的生活不相干。我们没必要时间上回溯那么远。 


>Back in the semi-olden days, when Unix was being invented and K&R were writing The C Programming Language, everything was very simple. EBCDIC was on its way out. The only characters that mattered were good old unaccented English letters, and we had a code for them called ASCIIwhich was able to represent every character using a number between 32 and 127. Space was 32, the letter "A" was 65, etc. This could conveniently be stored in 7 bits. Most computers in those days were using 8-bit bytes, so not only could you store every possible ASCII character, but you had a whole bit to spare, which, if you were wicked, you could use for your own devious purposes: the dim bulbs at WordStar actually turned on the high bit to indicate the last letter in a word, condemning WordStar to English text only. Codes below 32 were called unprintable and were used for cussing. Just kidding. They were used for control characters, like 7 which made your computer beep and 12 which caused the current page of paper to go flying out of the printer and a new one to be fed in.


远在古老的分号时代，Unix刚刚发明，K&R还在写“C编程语言”，一切都很简单，扩展式二进式十进制交换码慢慢被淘汰。唯一重要的字符集就是古老的不带口音的英语字符集。我们使用叫做ASCII的编码来表示这种字符集，这种编码使用32-127间的数字来编码任意字符。 空格是32，字母“A”是65等等。这种编码可以仅用7个比特存储。当时大部分的计算机都使用8比特表示一个字节，所以不仅可以存储所有的ASCII字符，你还有整整一个比特可以拿来，如果你很顽皮，可以用来做些狡猾的用途：WordStar 的昏暗灯泡通常会把单词里最后一个字母的高比特位打开，宣判WordStar只能用于英文文字处理。代码小于32的被称为是非打印字符，只能用来诅咒。开个玩笑，他们被用作控制字符，就像7为让你的电脑发出蜂鸣声的控制字符，12是控制当前打印页飞出打印机填入新打印纸的控制字符。


>And all was good, assuming you were an English speaker.


假如你是说英语的，这些都很美好。


>Because bytes have room for up to eight bits, lots of people got to thinking, "gosh, we can use the codes 128-255 for our own purposes." The trouble was, lots of people had this idea at the same time, and they had their own ideas of what should go where in the space from 128 to 255. The IBM-PC had something that came to be known as the OEM character set which provided some accented characters for European languages and a bunch of line drawing characters... horizontal bars, vertical bars, horizontal bars with little dingle-dangles dangling off the right side, etc., and you could use these line drawing characters to make spiffy boxes and lines on the screen, which you can still see running on the 8088 computer at your dry cleaners'. In fact  as soon as people started buying PCs outside of America all kinds of different OEM character sets were dreamed up, which all used the top 128 characters for their own purposes. For example on some PCs the character code 130 would display as é, but on computers sold in Israel it was the Hebrew letter Gimel ( ), so when Americans would send their résumés to Israel they would arrive asr sum s. In many cases, such as Russian, there were lots of different ideas of what to do with the upper-128 characters, so you couldn't even reliably interchange Russian documents.


因为一个字节有8比特空间，所以很多人就会想说“天，我们可以ASCII代码128-255拿来自己用啊。”问题就在于很多人都会同时想到这个主意，而且他们对于128-255该放什么都有自己的看法。 IBM-PC就有一堆被称为是代工厂商字符集的东西。这种字符集为欧洲语言提供了带重音的字符，以及一系列的制表符…水平线，竖直线，水平线带右撇等等…你可以使用这些制表符在屏幕上画出一些巧妙的格子和线段。你还能看到这些东西在你的干洗机的8088计算机上运行着。 实际上一旦美国之外的人们开始购买PC，各种各样的代工厂商字符就被想象了出来，他们都是基于自己的考量用了顶上的128个字符。例如有些PC使用代码130显示é，但是在以色列出售的计算机，它对应的是希伯来字母 Gimel( ) ，所以当美国人把他们的résumés发到以色列的时候，对方收到的就是asr sum s。 在很多情况下，例如俄国，有一大堆拿上128位做处理的想法，所以你甚至不能很可靠地交换俄国文档。


>Eventually this OEM free-for-all got codified in the ANSI standard. In the ANSI standard, everybody agreed on what to do below 128, which was pretty much the same as ASCII, but there were lots of different ways to handle the characters from 128 and on up, depending on where you lived. These different systems were called code pages. So for example in Israel DOS used a code page called 862, while Greek users used 737. They were the same below 128 but different from 128 up, where all the funny letters resided. The national versions of MS-DOS had dozens of these code pages, handling everything from English to Icelandic and they even had a few "multilingual" code pages that could do Esperanto and Galician on the same computer! Wow! But getting, say, Hebrew and Greek on the same computer was a complete impossibility unless you wrote your own custom program that displayed everything using bitmapped graphics, because Hebrew and Greek required different code pages with different interpretations of the high numbers.


最后，这种OEM随便处理的方法进入了ANSI标准，ANSI标准规定，所有人都同意小于128的代码如何处理，基本和ASCII相同，但是有如此多的方案处理128以上的代码，根据你的居住地不同，这些不同的处理方法被称为代码页。所以例如在以色列，DOS使用了叫做862的代码页，希腊用户用的是737代码页，128以下的代码处理起来都是一样的，但128往上的就不尽相同，各种各样好玩的字母都有。 各个国家的MS-DOS版本有一打这样的代码页，处理从英语到冰岛语之类的各种语言，甚至还有一种“多语言”代码页 能够在一台计算机上同时处理世界语和嘉西利亚语，喔，不过要想让希伯莱和希腊字符在同一台电脑上显示就完全不可能了，除非你能开发出你自己的程序用比特映射的图形来显示所有的字符，因为希伯莱语和希腊字符在处理高位代码的时候用到了不同的代码页。


>Meanwhile, in Asia, even more crazy things were going on to take into account the fact that Asian alphabets have thousands of letters, which were never going to fit into 8 bits. This was usually solved by the messy system called DBCS, the "double byte character set" in whichsome letters were stored in one byte and others took two. It was easy to move forward in a string, but dang near impossible to move backwards. Programmers were encouraged not to use s++ and s-- to move backwards and forwards, but instead to call functions such as Windows' AnsiNext and AnsiPrev which knew how to deal with the whole mess.


同时，在亚洲，要考虑的更疯狂的事情能够是亚洲字母包含了成千上万的字符，完全不可能用8比特表示。这通常使用很糟糕的DBCS（Double byte character set双字节字符集）系统处理的，这种系统中要在字符串中往前移动是可行的，不过几乎没办法后退移动。程序员被鼓励不要用S++和S—来在字符串中移动，相反应该调用Windows的AnsiNext和AnsiPrev函数，这两个函数知道如何处理这团糟糕的东西。


>But still, most people just pretended that a byte was a character and a character was 8 bits and as long as you never moved a string from one computer to another, or spoke more than one language, it would sort of always work. But of course, as soon as the Internet happened, it became quite commonplace to move strings from one computer to another, and the whole mess came tumbling down. Luckily, Unicode had been invented.


但仍然大部分人家装一个字符就是一个字节以及8个比特就是一个字符，只要你不把字符串从一台电脑搬到另一台，只要你不说超过一种语言，听起来似乎还是可行的。但显然，因特网一发生，把字符串从一台电脑搬到另一台就变得稀松平常了，然后这团乱麻就砸了下来。 幸运的是人们发明了Unicode。


>###Unicode


>Unicode was a brave effort to create a single character set that included every reasonable writing system on the planet and some make-believe ones like Klingon, too. Some people are under the misconception that Unicode is simply a 16-bit code where each character takes 16 bits and therefore there are 65,536 possible characters. This is not, actually, correct. It is the single most common myth about Unicode, so if you thought that, don't feel bad.


Unicode是一项创建单一字符集来包含地球上所有合理书写系统字符的大胆尝试，有人甚至觉得也可以包含“克林贡语 ”。 有些人有些错误的观念认为Unicode就是16比特的代码，每个字符可以占用16比特，所以一共有65，536种可能字符。 这实际上是不对的。 这也是Unicode最普遍也最神秘的地方，所以如果你就是那么想的，也别难过。


>In fact, Unicode has a different way of thinking about characters, and you have to understand the Unicode way of thinking of things or nothing will make sense.


实际上，Unicode有不同的处理字符的方法，你必须理解Unicode处理事情的方式，要不然所有的东西都说不通。


>Until now, we've assumed that a letter maps to some bits which you can store on disk or in memory:


到目前为止，我们假设字符能够映射为可以存储在磁盘或内存中的比特：


     A -> 0100 0001


>In Unicode, a letter maps to something called a code point which is still just a theoretical concept. How that code point is represented in memory or on disk is a whole nuther story.


在Unicode里，一个字符可以映射为一个“代码点”，这还只是个理论概念。至于“代码点”在磁盘或内存里如何表示，那就完全是另一个故事了。


>In Unicode, the letter A is a platonic ideal. It's just floating in heaven:


在Unicode里，字母A是一个柏拉图式理想的存在。 就像漂浮在天堂里那么理想。


    A


>This platonic A is different than B, and different from a, but the same as A and A and A. The idea that A in a Times New Roman font is the same character as the A in a Helvetica font, but different from "a" in lower case, does not seem very controversial, but in some languages just figuring out what a letter is can cause controversy. Is the German letter ß a real letter or just a fancy way of writing ss? If a letter's shape changes at the end of the word, is that a different letter? Hebrew says yes, Arabic says no. Anyway, the smart people at the Unicode consortium have been figuring this out for the last decade or so, accompanied by a great deal of highly political debate, and you don't have to worry about it. They've figured it all out already.


这个柏拉图式的A和B不一样，和小写a不一样，但是和粗体A，粗斜体A,A是一样的。想法是TimesNewRoman字体下的A和Helvetica字体下的A是一样的，但是和小写的字母“a”不一样，听起来矛盾么？不过在有些语言里要搞清楚字母到底是什么就很矛盾了。德语字母B究竟是真的字母呢？还是只是花写的ss而已？如果一个放在末尾的字母形状发生改变，这是个不同的字母么？希伯莱文会这样认为，阿拉伯文不是这样。总之Unicode协会的聪明家伙们在过去的十年一直在思考这些事情，当然也有一些很政治化的争论，不过你不用担心，他们已经搞定了。


>Every platonic letter in every alphabet is assigned a magic number by the Unicode consortium which is written like this: U+0639.  This magic number is called a code point. The U+ means "Unicode" and the numbers are hexadecimal. U+0639 is the Arabic letter Ain. The English letter A would be U+0041. You can find them all using thecharmap utility on Windows 2000/XP or visiting the Unicode web site.


Unicode协会给字母表里的每一个字母都分配了一个特定的数字，如：U+0639。这个数字被称为代码点。U+指“Unicode”，数字是16进制的。U+0639是阿拉伯字母Ain。英语字母A的代码点为U+0041。你可以使用Windows2000/Xp的字符映射工具或者登陆Unicode网站来查找这些代码点。


>There is no real limit on the number of letters that Unicode can define and in fact they have gone beyond 65,536 so not every unicode letter can really be squeezed into two bytes, but that was a myth anyway.


实际上Unicode能定义的字母数没有任何限制，实际上已经超过了65，536个，所以不是所有的Unicode字符都能塞进两字节，不过那也是特殊情况了。


>OK, so say we have a string:


好，假定我们有这么一个字符串：


>Hello


>which, in Unicode, corresponds to these five code points:


在Unicode里面，对应了这5个代码点：


>U+0048 U+0065 U+006C U+006C U+006F.


>Just a bunch of code points. Numbers, really. We haven't yet said anything about how to store this in memory or represent it in an email message.


其实也就是一对代码点，数字。我们还没有讨论到如何在内存中存储这些数字，或者如何在电子邮件里存储这些数字.


>###Encodings

###编码


>That's where encodings come in.


然后就说到编码了。


>The earliest idea for Unicode encoding, which led to the myth about the two bytes, was, hey, let's just store those numbers in two bytes each. So Hello becomes


>Unicode最早的编码思想，也就是那个神奇的两个字节是怎么来的，即：我们就把那些数字分别塞进两个字节吧。然后Hello就变成了

```
00 48 00 65 00 6C 00 6C 00 6F
```

>Right? Not so fast! Couldn't it also be:


对不对？没这么快！难道不能写成这样么：

```
48 00 65 00 6C 00 6C 00 6F 00 ?
```

>Well, technically, yes, I do believe it could, and, in fact, early implementors wanted to be able to store their Unicode code points in high-endian or low-endian mode, whichever their particular CPU was fastest at, and lo, it was evening and it was morning and there were already two ways to store Unicode. So the people were forced to come up with the bizarre convention of storing a FE FF at the beginning of every Unicode string; this is called a Unicode Byte Order Mark and if you are swapping your high and low bytes it will look like a FF FE and the person reading your string will know that they have to swap every other byte. Phew. Not every Unicode string in the wild has a byte order mark at the beginning.

技术角度来讲，是的，我觉得确实可以，事实上，早期的实现者确实想根据特定CPU的快速模式用大端模式和小端模式来存储Unicode代码点。结果一早一晚 就已经有两种方式来存储Unicode了。所以人们被迫想出了在Unicode字符串开头存储FE FF的奇怪惯例；这被称为Unicode字节序标志而且如果你交换了你的高字节和低字节使字节序标志成为FF FE，读取你字符串的人就知道他们也需要交换所有其他的字符。但是。也不是世上所有的Unicode字符串在开头都有个字符序标志。

>For a while it seemed like that might be good enough, but programmers were complaining. "Look at all those zeros!" they said, since they were Americans and they were looking at English text which rarely used code points above U+00FF. Also they were liberal hippies in California who wanted to conserve (sneer). If they were Texans they wouldn't have minded guzzling twice the number of bytes. But those Californian wimps couldn't bear the idea of doubling the amount of storage it took for strings, and anyway, there were already all these doggone documents out there using various ANSI and DBCS character sets and who's going to convert them all? Moi ? For this reason alone most people decided to ignore Unicode for several years and in the meantime things got worse.


一段时间，这看上去已经足够好了。但程序员们在那儿抱怨。“看看那些0！”他们说，因为他们是美国人而且他们看的英文所以基本不会用到U+00FF以上的，而且他们是他们是加利佛尼亚自由嬉皮士更倾向于保守(冷笑)。如果他们是德州人他们就不会介意存了两个字节的耗费。不过那些加州嬉皮佬无法忍受要花两倍的实际消耗来存储字符串，再说，已经有一大堆用各种ANSI，DBCS字符集的该死的文档了。谁去负责转换那些文档？我？就因为这个原因，很多年来，很多人决定忽略Unicode，与此同时事情越来越糟。


>Thus was invented the brilliant concept of UTF-8. UTF-8 was another system for storing your string of Unicode code points, those magic U+ numbers, in memory using 8 bit bytes. In UTF-8, every code point from 0-127 is stored in a single byte. Only code points 128 and above are stored using 2, 3, in fact, up to 6 bytes.


然后，UTF-8这样的绝妙的概念就被发明了。UTF-I是用来存储你的Unicode字符串的代码点的另一套系统，那些奇特的U+数字，在内存里存储的时候只用了8比特。在UTF-8里，每一个从0-127的代码点都仅用一个字节存储。只有128以及128以上的代码点才会占到2，3字节，实际最大到6字节。

>This has the neat side effect that English text looks exactly the same in UTF-8 as it did in ASCII, so Americans don't even notice anything wrong. Only the rest of the world has to jump through hoops. Specifically, Hello, which was U+0048 U+0065 U+006C U+006C U+006F, will be stored as 48 65 6C 6C 6F, which, behold! is the same as it was stored in ASCII, and ANSI, and every OEM character set on the planet. Now, if you are so bold as to use accented letters or Greek letters or Klingon letters, you'll have to use several bytes to store a single code point, but the Americans will never notice. (UTF-8 also has the nice property that ignorant old string-processing code that wants to use a single 0 byte as the null-terminator will not truncate strings).


这样做好的地方是，UTF-8表示的英文和ASCII表示的看起来完全相同，所以美国人根本就不会注意到不对的地方。只有世界上其他地方的人要在圈子里绕来绕去。特别是，HELLO，也就是U+0048 U+0065 U+006C U+006C U+006F,会被存储为48 65 6C 6C 6F，注意，这跟ASCII的存储也是一样的，跟ANSI以及地球上所有其他的OEM字符集也是一样的。现在，如果你足够勇敢想要去使用带口音的字母或是希腊字母或是克林贡字母，你就得花几个字节来存储一个代码点，不过美国人永远不会注意到。（UTF-8还有一个很好的特性在于如果那些无知的旧字符处理代码想要用一个0字节来表示NULL终结符也不会因此勿舍弃掉字符串）。


>So far I've told you three ways of encoding Unicode. The traditional store-it-in-two-byte methods are called UCS-2 (because it has two bytes) or UTF-16 (because it has 16 bits), and you still have to figure out if it's high-endian UCS-2 or low-endian UCS-2. And there's the popular new UTF-8 standard which has the nice property of also working respectably if you have the happy coincidence of English text and braindead programs that are completely unaware that there is anything other than ASCII.


目前为止，我已经讲授了3种Unicode编码方式，传统的双字节编码存储法被称为UCS-2（因为使用两字节进行存储）或者UTF-16（因为使用了16比特），不过你还是得搞清楚它是大端的UCS-2呢还是小端的UCS-2. 当然也还有很流行的UTF-8标准，UTF-8的优点是：不论你是碰巧遇到英文还是那些完全不知道除了ASCII外其他字符的无脑程序，它都可以工作。


>There are actually a bunch of other ways of encoding Unicode. There's something called UTF-7, which is a lot like UTF-8 but guarantees that the high bit will always be zero, so that if you have to pass Unicode through some kind of draconian police-state email system that thinks 7 bits are quite enough, thank you it can still squeeze through unscathed. There's UCS-4, which stores each code point in 4 bytes, which has the nice property that every single code point can be stored in the same number of bytes, but, golly, even the Texans wouldn't be so bold as to waste that much memory.


实际上还有一堆Unicode编码方式。 有一种编码方式叫做UTF-7，跟UTF-8很像不过保证最高位始终为0，所以如果你要确保Unicode能够通过某些严格的州立警署电子邮件系统，这些系统认为7个比特就够了，谢天谢地你还是可以好发无损的挤出那个比特。 还有一种叫做UCS-4的编码方案，每个代码点用4个字节存储，好处是每个代码点都以相同的长度存储，不过，我发誓，即使是德州人也不会这么大胆的去浪费那么多的内存空间。


>And in fact now that you're thinking of things in terms of platonic ideal letters which are represented by Unicode code points, those unicode code points can be encoded in any old-school encoding scheme, too! For example, you could encode the Unicode string for Hello (U+0048 U+0065 U+006C U+006C U+006F) in ASCII, or the old OEM Greek Encoding, or the Hebrew ANSI Encoding, or any of several hundred encodings that have been invented so far, with one catch: some of the letters might not show up! If there's no equivalent for the Unicode code point you're trying to represent in the encoding you're trying to represent it in, you usually get a little question mark: ? or, if you'rereally good, a box. Which did you get? -> �


实际上，既然你现在已经能够开始用Unicode代码点表示的柏拉图式理想字符来考虑问题， 那些unicode代码点也可以使用任何老式编码方案来编码！例如，你可以使用ASCII来编码HELLO（U+0048 U+0065 U+006C U+006C U+006F），或者使用古老的OEM希腊文编码或者是希伯莱ANSI编码或者是到目前已经发明的数百种编码方法中的任意一种编码，只有一个问题：有些字符可能无法显示！如果在编码方案中无法找到你要表示的代码点等同编码，你通常会得到一个小问号：？或者如果你确实干得“不错”，一个方块，你得到过么？-> �


>There are hundreds of traditional encodings which can only store somecode points correctly and change all the other code points into question marks. Some popular encodings of English text are Windows-1252 (the Windows 9x standard for Western European languages) and ISO-8859-1, aka Latin-1 (also useful for any Western European language). But try to store Russian or Hebrew letters in these encodings and you get a bunch of question marks. UTF 7, 8, 16, and 32 all have the nice property of being able to store any code point correctly.


有上百种的传统编码方案只能正确的表示一部分的代码点，然后把其他的代码点表示为问号。一些流行的英文编码如：Windows-1252（西欧语言的Windows9x标准）和ISO-8859-1，还有Latin-1（同样对西欧语言很有用）。但如果你尝试用这些编码存储俄文和希伯莱文字符你就会得到一堆的问号。UTF7，8，16和32都具有能够正确存储任意代码点的优点。


>The Single Most Important Fact About Encodings


关于编码的最重要一点事实


>If you completely forget everything I just explained, please remember one extremely important fact. It does not make sense to have a string without knowing what encoding it uses. You can no longer stick your head in the sand and pretend that "plain" text is ASCII.


如果你完全忘记了我刚刚说的所有东西，请记住一点最重要的事实。如果不知道编码是什么那么字符串毫无意义。你再也不能把头扎进沙堆里然后假装“普通文本就是ASCII”


>There Ain't No Such Thing As Plain Text.


没有所谓的普通文本


>If you have a string, in memory, in a file, or in an email message, you have to know what encoding it is in or you cannot interpret it or display it to users correctly.


如果你在内存，文件，电子邮件信息里有一个字符串，你必须知道那是什么编码否则你无法正确的解码或者是正确的显示给用户。


>Almost every stupid "my website looks like gibberish" or "she can't read my emails when I use accents" problem comes down to one naive programmer who didn't understand the simple fact that if you don't tell me whether a particular string is encoded using UTF-8 or ASCII or ISO 8859-1 (Latin 1) or Windows 1252 (Western European), you simply cannot display it correctly or even figure out where it ends. There are over a hundred encodings and above code point 127, all bets are off.


几乎所有的愚蠢问题，像“我的网站看起来像胡言乱语”或者“当我使用方言的时候她就没办法理解我的电子邮件了”全部能够追溯到一个幼稚的程序员，他不懂这个简单的事实：如果你不告诉我一个字符串使用UTF-8还是ASCII还是ISO8859-1（Latin 1）还是Windows1252（西欧）编码的，你就没办法正确的显示它，甚至弄清楚它在哪儿终结。 在127以上的代码点里有着上百种的编码方案，所有的猜测全部是徒劳。


>How do we preserve this information about what encoding a string uses? Well, there are standard ways to do this. For an email message, you are expected to have a string in the header of the form


我们应该如何保存信息使用的编码呢？额，有标准的方法来做这件事情。对于电子邮件，你应该在邮件头里放上这种形式的字符串：


>Content-Type: text/plain; charset="UTF-8"


>For a web page, the original idea was that the web server would return a similar Content-Type http header along with the web page itself -- not in the HTML itself, but as one of the response headers that are sent before the HTML page. 


对于一个网页，最初的想法是网页服务器应该和Web页面一起返回一个类似的Content-Type http头 – 不是在HTML里面，而是和响应头 一起在HTML页面之前返回。


>This causes problems. Suppose you have a big web server with lots of sites and hundreds of pages contributed by lots of people in lots of different languages and all using whatever encoding their copy of Microsoft FrontPage saw fit to generate. The web server itself wouldn't really know what encoding each file was written in, so it couldn't send the Content-Type header.


这会导致很多问题，假设你有个很大的网页服务器，服务器上有很多站点和上百的网页。这些网页由许多说各种语言的人使用Microsoft FrontPage选择的相应合适编码产生。 Web服务器本身不知道每个文件是用什么编码产生的，所以它也无法发送正确的Content-Type头部。


>It would be convenient if you could put the Content-Type of the HTML file right in the HTML file itself, using some kind of special tag. Of course this drove purists crazy... how can you read the HTML file until you know what encoding it's in?! Luckily, almost every encoding in common use does the same thing with characters between 32 and 127, so you can always get this far on the HTML page without starting to use funny letters:


最好是能够用某种特殊的标签，把HTML页面的Content-Type信息放到HTML页面文件里。当然这就会让完美主义者抓狂…你怎么在不知道页面编码的前提下去解析HTML页面文件？幸运的是，几乎所有的编码都有一个共同点，在32-127之间的字符处理都是相同的，所以在HTML页面里你总是能够得到这些信息，而不是奇怪的字符：

```
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
```

>But that meta tag really has to be the very first thing in the <head> section because as soon as the web browser sees this tag it's going to stop parsing the page and start over after reinterpreting the whole page using the encoding you specified.


但这个meta标签必须是出现在<head>标签部分里的第一个，因为浏览器一看见这个标签就会停止解析页面并使用你指定的编码重新解析整个页面。


>What do web browsers do if they don't find any Content-Type, either in the http headers or the meta tag? Internet Explorer actually does something quite interesting: it tries to guess, based on the frequency in which various bytes appear in typical text in typical encodings of various languages, what language and encoding was used. Because the various old 8 bit code pages tended to put their national letters in different ranges between 128 and 255, and because every human language has a different characteristic histogram of letter usage, this actually has a chance of working. It's truly weird, but it does seem to work often enough that naïve web-page writers who never knew they needed a Content-Type header look at their page in a web browser and it looks ok, until one day, they write something that doesn't exactly conform to the letter-frequency-distribution of their native language, and Internet Explorer decides it's Korean and displays it thusly, proving, I think, the point that Postel's Law about being "conservative in what you emit and liberal in what you accept" is quite frankly not a good engineering principle. Anyway, what does the poor reader of this website, which was written in Bulgarian but appears to be Korean (and not even cohesive Korean), do? He uses the View | Encoding menu and tries a bunch of different encodings (there are at least a dozen for Eastern European languages) until the picture comes in clearer. If he knew to do that, which most people don't.


如果浏览器没有在http头部或者Meta里找到Content-Type标签会怎么做呢？InternetExplorer实际上会做些非常有意思的事情：它会根据各种字节出现在各种语言编码的频率，用过的语言和编码来猜测字符编码。 因为各种旧的8比特代码页将他们的母语字符放在128-255之前不同的区域里，因为每一种人类语言都有不同的字母使用频率柱状图，所以这实际上是有一定概率是能够成功的。听起来很奇怪，但实际上还是蛮可靠的以至于那些幼稚的不知道要写Content-Type的网页作者在浏览器里打开网页一看，看起来不错。直到有一天他们写出了一些不遵循他们母语字母使用频率分布的东西，然后IE浏览器就决定这是韩文，然后就那样显示。我觉得这证明了 Postel法则“在你给出的时候要保守，而接受的时候则要激进”不是什么靠谱的工程原则。 不管怎么样，可怜的读者看着这个用保加利亚语写成的但是显示成韩文（甚至不是连贯的韩文）的网站会怎么做呢？如果他懂怎么处理的话，他会使用 查看|编码 菜单然后尝试一堆不同的编码（至少有一打的西欧语言编码）直到画面变得清楚许多。实际上很多人并不知道这些。

>For the latest version of CityDesk, the web site management software published by my company, we decided to do everything internally in UCS-2 (two byte) Unicode, which is what Visual Basic, COM, and Windows NT/2000/XP use as their native string type. In C++ code we just declare strings as wchar_t ("wide char") instead of char and use the wcs functions instead of the str functions (for example wcscatand wcslen instead of strcat and strlen). To create a literal UCS-2 string in C code you just put an L before it as so: L"Hello".


我们公司发布的最新的网站管理软件CityDesk的最新版本，我们决定内部采用UCS-2（双字节）Unicode，则也是Visual Basic，COM,和Windows NT/2000/XP字符采用的类型，在C++代码里面我们决定将字符串声明为wchar_t（“宽字符”）类型而不是char，并且我们会是用wcs函数而不是str函数（例如 wcscat和wcslen而不是strcat和strlen）。要创建一个UCS-2常量字符，在C语言里你只要在字符前加一个L，就像这样L”Hello”


>When CityDesk publishes the web page, it converts it to UTF-8 encoding, which has been well supported by web browsers for many years. That's the way all 29 language versions of Joel on Software are encoded and I have not yet heard a single person who has had any trouble viewing them.


当CityDesk发布网页的时候，他会把它转换成UTF-8编码，浏览器已经很好的支持这种编码很多年了。这就是Joel on software的29种语言所使用的编码。 到目前为止我还没有听说有人阅读这些有任何问题。


>This article is getting rather long, and I can't possibly cover everything there is to know about character encodings and Unicode, but I hope that if you've read this far, you know enough to go back to programming, using antibiotics instead of leeches and spells, a task to which I will leave you now.


这篇文章变得很长很长，而且我也无法涵盖到所有关于字符编码，Unicode的方方面面，不过既然你已经读了这么多，我希望你已经了解了足够多的东西回去编程，用抗生素而不是水蛭或是咒语，接下来就看你的了。


