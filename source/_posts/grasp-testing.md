---
title: 你了解软件测试吗？
date: 2017-11-02 07:32:42
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/test0.png
thumbnailImagePosition: left
tags: 
- testing
- 测试
comments: false
metaAlignment: center
categories: 技术博文 
---
日常开发中，程序员可能对 Bug 并不陌生，如果仅仅依赖测试人员给出反馈，无疑隔靴挠痒。如何才能保证高质量代码，这就需要我们了解测试，而不是只会人肉测试。
<!-- more -->
#### 一、软件都有多少种分类?
{% alert info %}
根据功能的不同，电脑软件可以粗略地分成四个层次：
{% endalert %}
1.最贴近电脑硬件的是一些小巧的软件。它们实现一些最基本的功能，通常“固化”在只读存储器芯片中，因此称为固件。
2.系统软件包括操作系统和编译器软件等。系统软件和硬件一起提供一个“平台”。它们管理和优化电脑硬件资源的使用。
3.支持软件。包括图形用户界面、软件开发工具、软件评测工具、数据库管理系统、中间件等。
4.应用软件种类最多，包括办公软件、电子商务软件、通信软件、行业软件，游戏软件等等。

#### 二、集成测试通常都有那些策略？
- 大爆炸集成
- 自顶向下集成
- 自底向上集成
- 三明治集成适应于大部分软件开发项目
- 基干集成
- 分层集成
- 基于功能的集成
- 基于消息的集成
- 基于风险的集成
- 基于进度的集成

#### 三、请你分别画出 OSI 的七层网络结构图和 TCP/IP 的四层结构图
OSI 七层网络结构图，由上至下：
应用层-表示层-会话层-传输层-网络层-数据链路层-物理层

TCP/IP 的四层结构图
应用层-传输层-互联层-链路层

#### 四、关于自动化测试工具
- 功能测试工具：QTP；
- 性能测试工具：LoadRunner；
- 测试管理工具：QC Mantis缺陷跟踪工具。 

#### 五、软件测试类型都有哪些？
按测试策略分类：
- 静态与动态测试；
- 黑盒与白盒测试；
- 手工和自动测试；
- 冒烟测试；
- 回归测试；

按测试阶段分类：
- 单元测试；
- 集成测试；
- 系统测试；

其他常见测试方法：
- 功能测试；
- 性能测试；
- 压力测试；
- 负载测试；
- 易用性测试；
- 安装测试；
- 界面测试；
- 配置测试；
- 文档测试；
- 兼容性测试；
- 安全性测试；
- 恢复测试；

#### 六、系统测试的策略有
系统测试的策略有很多种的，有性能测试、负载测试、强度测试、易用性测试、安全测试、配置测试、安装测试、文档测试、故障恢复测试、用户界面测试、恢复测试、分布测试、可用性测试等15 种方法。

#### 七、设计测试用例时应该考虑哪些方面，即不同的测试用例针对那些方面进行测试
设计测试用例时需要注意的是，除了对整体流程及功能注意外，还要注意强度测试、性能测试、压力测试、边界值测试、稳定性测试、安全性测试等多方面。（测试用例需要考虑的四个基本要素是输入、输出、操作和测试环境；另外，测试用例需要考虑的是测试类型（功能、性能、安全……），这部分可以参照TP做答。此外，还需要考虑用例的重要性和优先级）

#### 八、软件验收测试的合格通过准则
A.软件需求分析说明书中定义的所有功能已全部实现，性能指标全部达到要求。 
B.所有测试项没有残余一级、二级和三级错误。 
C.立项审批表、需求分析文档、设计文档和编码实现一致。

#### 九、什么是软件测试？软件测试的目的与原则？
使用人工或自动手段，来运行或测试某个系统的过程。其目的在于检验它是否满足规定的需求或弄清预期结果与实际结果之间的差别。

目的：
- 测试是程序的执行过程，目的在于发现错误；
- 一个成功的测试用例在于发现至今未发现的错误；
- 一个成功的测试是发现了至今未发现的错误的测试；
- 确保产品完成了它所承诺或公布的功能，并且用户可以访问到的功能都有明确的书面说明；
- 确保产品满足性能和效率的要求；
- 确保产品是健壮的和适应用户环境的；

软件测试的原则：
- 软件测试应尽早执行，并贯穿于整个软件生命周期
- 软件测试应追溯需求
- 测试应由第三方来构造
- 穷举测试是不可能的,要遵循 `Good-enough` 原则
- 必须确定预期输出（或结果）
- 必须彻底检查每个测试结果
- 充分注意测试中的群集现象
- 缺陷的二八定理 
- 严格执行测试计划，排除测试的随意性
- 注意合法合理的输入，也要注意非法的非预期的输入 
- 检查程序是否是否做了不该做的 
- 测试应从“小规模”开始，逐步转向“大规模” 
- 反复使用同样的测试会使软件具有抵抗力 
- 关注缺陷的修复

程序员应该避免检查自己的程序，测试工作应该由独立的专业的软件测试机构来完成。
- 设计测试用例时，应该考虑到合法的输入和不合法的输入，以及各种边界条件，特殊情况下要制造极端状态和意外状态，比如网络异常中断、电源断电等情况。
- 一定要注意测试中的错误集中发生现象，这和程序员的编程水平和习惯有很大的关系。
- 对测试错误结果一定要有一个确认的过程。一般有 A 测试出来的错误，一定要有一个 B 来确认，严重的错误可以召开评审会进行讨论和分析。
- 制定严格的测试计划，并把测试时间安排得尽量宽松，不要希望在极短的时间内完成一个高水平的测试。
- 回归测试的关联性一定要引起充分的注意，修改一个错误而引起更多错误出现的现象并不少见。
- 妥善保存一切测试过程文档，意义是不言而喻的，测试的重现性往往要靠测试文档

#### 十、一套完整的测试应该由哪些阶段组成
可行性分析、需求分析、概要设计、详细设计、编码、单元测试、集成测试、系统测试、验收测试

#### 十一、测试用例通常包括那些内容？
不同结构的用例包括的不一样。（版本、编号、项目、设计人员、设计日期、输入、预期输出……）。

软件测试用例的基本要素包括测试用例编号、测试标题、重要级别、测试输入、操作步骤、预期结果：
- 用例编号： 测试用例的编号有一定的规则，比如系统测试用例的编号这样定义规则： PROJECT1-ST-001 ，命名规则是项目名称＋测试阶段类型（系统测试阶段）＋编号。定义测试用例编号，便于查找测试用例，便于测试用例的跟踪。
- 测试标题： 对测试用例的描述，测试用例标题应该清楚表达测试用例的用途。比如 “ 测试用户登录时输入错误密码时，软件的响应情况 ” 。
- 重要级别： 定义测试用例的优先级别，可以笼统的分为 “ 高 ” 和 “ 低 ” 两个级别。一般来说，如果软件需求的优先级为 “ 高 ” ，那么针对该需求的测试用例优先级也为 “ 高 ” ；反之亦然，一般而言，是5级划分。
- 测试输入： 提供测试执行中的各种输入条件。根据需求中的输入条件，确定测试用例的输入。测试用例的输入对软件需求当中的输入有很大的依赖性，如果软件需求中没有很好的定义需求的输入，那么测试用例设计中会遇到很大的障碍。 
- 操作步骤： 提供测试执行过程的步骤。对于复杂的测试用例，测试用例的输入需要分为几个步骤完成，这部分内容在操作步骤中详细列出。 
- 预期结果： 提供测试执行的预期结果，预期结果应该根据软件需求中的输出得出。如果在实际测试过程中，得到的实际测试结果与预期结果不符，那么测试不通过；反之则测试通过。

#### 十二、黑盒测试、白盒测试以及单元测试、集成测试、系统测试、验收测试的区别与联系
{% alert info %}
这些测试的范围正好是逐步递增的关系，但是测试的人员角色是不同的。
{% endalert %}
黑盒测试、白盒测试、单元测试：开发人员分在不同的开发阶段要做的事情。
黑盒测试、集成测试、系统测试：测试人员在测试周期内级层做的工作。
验收测试：一般是在用户方做的工作。

#### 十三、手工测试与自动测试有哪些区别？
- 手工测试是传统的测试方法，由测试人员手工编写测试用例，缺点在于测试工作量大，重复多，回归测试难以实现；自动化测试利用软件测试工具自动实现全部或者部分测试工作：管理、设计、执行和报告，自动化测试节省大量的测试开销，并能够完成一些手工测试无法实现的测试。
- 自动化测试是对手工测试的一种补充，自动化测试不可能完全替代手工测试，因为很多数据的正确性、界面是否美观、业务逻辑的满足程度等都离不开测试人员的人工判断。而仅仅依赖手工测试的话，则会让测试过于低效，尤其是回归测试的重复工作量对测试人员造成了巨大的压力。

{% alert info %}
自动化测试仅仅是某些条件下手工测试的一种补充，而无法全面取代手工测试。
{% endalert %}

#### 十四、做好测试计划的关键是什么？
1.明确测试的目标，增强测试计划的实用性---测试计划中的测试范围必须高度覆盖功能需求，测试方法必须切实可行，测试工具具有较高的实用性，便于使用，生成的测试结果直观准确。
2.坚持 “5W” 规则，明确内容与过程 “5W” 规则指：what,why,when,where,how ;用例5W规则创建软件测试计划，可帮助测试团队理解测试目的，明确测试范围和内容，确定测试开始和结束日期，指出测试的方法和工具，给出测试文档和软件存放位置
3.采用评审和更新机制，保证测试计划满足实际需求。

#### 十五、测试生命周期，测试过程分为几个阶段，以及各阶段的含义及使用的方法？
测试生命周期：①对测试人员进行业务培训，②测试需求分析，③编写测试计划，④编写测试用例，⑤测试执行（包括缺陷跟踪），⑥编写测试报告。

①	测试需求分析：
测试需求是整个测试过程的基础；确定测试对象以及测试工作的范围和作用。用来确定整个测试工作（如安排时间表、测试设计等）并作为测试覆盖的基础。而且被确定的测试需求项必须是可核实的。
- 测试需求是制订测试计划的基本依据，确定了测试需求能够为测试计划提供客观依据； 
- 测试需求是设计测试用例的指导，确定了要测什么、测哪些方面后才能有针对性的设计测试用例； 
- 测试需求是计算测试覆盖的分母，没有测试需求就无法有效地进行测试覆盖；

②	测试过程设计：包括测试计划,测试策略制定，测试时间安排，测试用例编写等；
③	测试实现：环境配置好了，新的版本也收到了，人员也都培训好了等等；
④	测试实施：已经按照测试计划进行展开了，比如手工测试，自动化测试等；
⑤	测试评价：对版本测试覆盖率，测试质量，人员测试工作以及前期的一些工作制定情况进行评价，评估；
⑥	测试维护：对测试用例库，测试脚本，`bug` 库等进行维护，保证延续性等。

#### 十六、LoadRunner 分为哪三个模块？
- Virtual User Generator：用于录制脚步
- Mercury LoadRunner Controller：用于创建、运行和监控场景
- Mercury LoadRunner Analysis：用于分析测试结果

#### 十七、网络七层协仪具体
- 应用层：与其他计算机进行通讯的一个应用，他是应对应用程序的通信服务。示例：NFS,SMTP
- 表示层：主要功能是定义数据格式及加密。示例：加密。ASCII 等.
- 会话层：它定义了如何开始，控制和结束一个会话，包括对多个双向消息的控制和管理。示例：RPC，SQL
- 传输层：功能包括是否选择差错恢复协议还是无差错恢复协议，及在同一主机上对不同应用的数据流的输入进行复用。
- 网络层：对端到端的包传输进行定义，它定义了能够表示所有结点的逻辑地址，还定义了路由实现的方式和学习的方式。
- 数据链路层：定义了在单个链路上如何传输数据。
- 物理层：常用多个规范完成对所有细节的定义。

#### 十八、BIOS, Fat, IDE, Sata, SCSI, Ntfs windows NT
- BIOS 基本输入输出系统
- Fat 文件配置表
- IDE 集成开发环境
- Sata 串行高级技术附件
- SCSI 小型计算机系统接口
- Ntfs windows NT 环境的文件系统

#### 十九、负载测试、容量测试、强度测试的区别
- 负载测试：通过在被测系统上不断增加压力，直到性能指标达到极根。
- 容量测试：可以看作系统性能指标中一个特定环境下的一个特定性能指标，即设定的界限或极限值。
- 强度测试：主要是为了检查程序对异常情况的抵抗能力。

#### 二十、测试用例与测试脚本
- 测试脚本是用某一计算机高级语言记录用户对软件操作的步骤。主要用作自动测试。
- 测试用例主要用作手工测试。在实际的测试工作中，两者是互补的关系。

#### 二十一、谈谈你对 `cmm` 和 `is9000` 的认识
两者都是质量管理体系，ISO是通用的质量管理体系，全而不专，CMM是专业的软件质量体系。

#### 二十二、什么是α测试，β测试？
α、β、λ 常用来表示软件测试过程中的三个阶段：
α 是第一阶段，一般只供内部测试使用；
β 是第二个阶段，已经消除了软件中大部分的不完善之处，但仍有可能还存在缺陷和漏洞，一般只提供给特定的用户群来测试使用；
λ 是第三个阶段，此时产品已经相当成熟，只需在个别地方再做进一步的优化处理即可上市发行。

α   测试(alpha测试)：在开发小组内部进行，测试的方法也较多，黑盒、白盒、  压力、应力等等；
β　测试（beta测试）：有选择地请一些最终用户实际使用，将发现的问题反馈回来再进行修改。

#### 二十三、性能测试工作
性能测试类型包括负载测试，强度测试，容量测试等。
- 负载测试：负载测试是一种性能测试指数据在超负荷环境中运行，程序是否能够承担。 
- 强度测试： 强度测试是一种性能测试，他在系统资源特别低的情况下软件系统运行情况 
- 容量测试：确定系统可处理同时在线的最大用户数 

在网站流量逐渐加大的情况下，开始考虑做性能测试了，首先要写好性能测试计划，根据运营数据得出流量最大的页面（如果是第一次的话，一般是首页，下载页，个人帐户页流量最大，而且以某种百分比）。

#### 二十四、Web服务器指标指标

* Avg Rps: 平均每秒钟响应次数＝总请求时间 / 秒数； 
* Successful Rounds：成功的请求； 
* Failed Rounds ：失败的请求； 
* Successful Hits ：成功的点击次数； 
* Failed Hits ：失败的点击次数； 
* Hits Per Second ：每秒点击次数； 
* Successful Hits Per Second ：每秒成功的点击次数； 
* Failed Hits Per Second ：每秒失败的点击次数； 
* Attempted Connections ：尝试链接数。

#### 二十五、I P协议、RARP协议、ICMP协议与ARP协议的功能是什么？
- IP 协议（Internet Protocol，因特网协议），属于 OSI7 层参考模型中的网络层协议。它提供两个基本功能：寻址和分段。寻址即常说的路由功能；分段是指对数据包的大小进行重新组装，以适应不同网络对包大小的要求。
- ARP 协议（Address Resolution Protocol，地址解析协议），属于 IPv4 协议簇，工作在数据链路层。其功能是将IP地址解析为对应的MAC地址。
- RARP 协议（Reverse ARP，反向ARP协议），其功能是将MAC地址解析为对应的IP地址。
- ICMP协议（Internet Control Message Protocol，Internet控制消息协议），它的功能是报告无法传送的数据包的错误，并帮助对这些错误进行疑难解答。

#### 二十六、在频繁的版本发布中，如何回归测试？
回归测试是指修改了旧代码后，重新进行测试以确认修改没有引入新的错误或导致其他代码产生错误。

自动回归测试将大幅降低系统测试、维护升级等阶段的成本。回归测试作为软件生命周期的一个组成部分，在整个软件测试过程中占有很大的工作量比重，软件开发的各个阶段都会进行多次回归测试。

在渐进和快速迭代开发中，新版本的连续发布使回归测试进行的更加频繁，而在极端编程方法中，更是要求每天都进行若干次回归测试。因此，通过选择正确的回归测试策略来改进回归测试的效率和有效性是非常有意义的。

（1）回归测试是指重复以前的全部或部分的相同测试。
（2）新加入测试的模组，可能对其他模组产生副作用，故须进行某些程度的回归测试。
（3）回归测试的重心，以关键性模组为核心。


#### 二十七、对 RUP,CMM,CMMI,XP,PSP,TSP 的认识
- RUP（Rational Unified Process，统一软件开发过程）是一个面向对象且基于网络的程序开发方法论。
- CMM（Capability Maturity Model）指能力成熟度模型。
- CMMI（Capability Maturity Model Integration）指能力成熟度模型集成。
- XP 电脑操作系统。
- PSP（PlayStation Portable）掌上游戏机。
- TSP(Team Software Process)团队软件过程，是为开发软件产品的开发团队提供指导，侧重于帮助开发团队其质量和生产效率，以使其更好的满足成本及进度的目标

#### 二十八、缺陷等级应如何划分
{% alert info %}
不同公司对缺陷的等级划分有所区别，一般遵循以下原则：
{% endalert %}
极高：在测试过程中出现死机，系统崩溃、数据丢失，功能没有实现等此类缺陷的级别。
高：此类缺陷导致系统功能不稳定，或功能实现错误，流程错误等。
中：校验错误，罕见故障，错别字等不会影响系统功能，但会影响用户易用性等的错误。
低：对系统没有影响的一些小问题。

#### 二十九、什么是Negative测试？
负面测试(Negative Testing)：用于验证软件不执行其不应该完成的工作。这一步骤主要依赖于错误猜测，需要依靠测试设计者的经验判断可能出现问题的位置。 

适合的技术有： 错误猜测 、边界值分析 、内部边界值测试 、状态转换测试。

#### 三十、验收测试包括哪三种类型
Alpha测试  Beta测试  UAT--User Acceptance Testing（用户可接受性测试）

#### 三十一、软件测试的过程的V模型，说出它的缺点？
V模型：RAD(Rap Application Development，快速应用开发)模型是软件开发过程中的一个重要模型，由于其模型构图形似字母V，所以又称软件开发的V模型。它通过开发和测试同时进行的方式来缩短开发周期，提高开发效率。V模型大体可以划分为以下几个不同的阶段步骤:需求分析、概要设计、详细设计、软件编码、单元测试、集成测试、系统测试、验收测试。

- 优点：
（1）既有底层测试又有高层测试。底层：单元测试。高层：系统测试。
（2）将开发阶段清楚的表现出来，便于控制开发的过程。当所有阶段都结束时，软件开发就结束了。
- 缺点：
（1）容易让人误解为测试是在开发完成之后的一个阶段。
（2）由于它的顺序性，当编码完成之后，正式进入测试时，这时发现的一些bug可能不容易找到其根源，并且代码修改起来很困难。
（3）实际中，由于需求变更较大，导致要重复变更需求、设计、编码、测试。返工量大。

#### 三十二、单元测试的策略有哪些？
逻辑覆盖、循环覆盖、同行评审、桌前检查、代码走查、代码评审、景泰数据流分析

#### 三十三、什么是数据的对立性，有几个层次？
数据库管理系统(Database Management System，DBMS)是在文件管理系统基础上发展起来的数据管理技术，它建立在操作系统的基础上，对数据操作语句进行统一的管理和控制，并维护数据库的安全性和完整性，是数据库系统的核心组成部分。

它是位于用户与操作系统之间的一层数据管理软件，帮助企业开发、使用、维护组织的数据库。它既能将所有数据集成在数据库中，又允许不同的用户应用程序方便地存取相同的数据库。

#### 三十四、面向对象的测试用例设计有几种方法，如何实现？
{% alert info %}
软件测试分为黑盒测试和白盒测试。
{% endalert %}
- 黑盒测试称驱动测试或功能测试，主要有:等价类划法,边界值划分法,错误推断法,因果图法,场景法.
- 白盒测试主要有:桌面走查,代码走查,代码审查.

#### 三十五、什么是回归测试？
回归测试是指修改了旧代码后，重新进行测试以确认没有引入新的错误或导致其他代码产生错误。

#### 三十六、什么是冒烟测试？
冒烟测试是对软件的基本功能进行测试，测试的对象是每一个新编译的需要正式测试的软件版本，目的是确认软件基本的功能正常，保证软件系统能跑得起来，可以进行后续的正式测试工作，如果最基本的测试都有问题，那么其他测试也就没办法进行

#### 三十七、QA 和测试的区别
QA 关注的重点不仅仅是质量，而且是整个软件过程，保证的首先是过程和体系。而测试通过一系列活动，给 QA 人员提供尽可能有效的信息和数据，是他们能够发现过程上的异常或者制度上的不妥之处。

- 共同之处：QA 和测试的目的一样，都是尽可能的保证最终发布的产品更符合客户的需求，尽可能的没有 bug。
- 不同之处：QA 关注的是整个软件过程，测试人员则是通过设计、执行用例等方法去发现错误，关注的是最终质量。

#### 三十八、网络攻击常用的手段
- 口令入侵
- 放置特洛伊木马程序
- WWW 欺骗技术
- 电子邮件攻击
- 通过一个节点攻击其他节点
- 网络监听
- 利用黑客软件攻击
- 利用安全漏洞攻击
- 端口扫描攻击

#### 三十九、防火墙如何保证安全？
（一）内部网络和外部网络之间的所有网络数据流都必须经过防火墙；
（二）只有符合安全策略的数据流才能通过防火墙；
（三）防火墙自身应具有非常强的抗攻击免疫力；

