# 第二章-进程的描述与控制

## 1.什么是前趋图?为什么要引入前趋图?
所谓前趋图(Precedence Graph)，是指一个有向无环图，可记为DAG(Directed Acyclic Graph)，它用于描述进程之间执行的先后顺序。前趋图的存在可以更好地帮助我们描述程序的顺序和并发执行情况。


2.试画出下面四条语句的前趋图:

S1: a = x+y;

S2: b = z+1;

S3: c = a-b;

S4: w = c+1;

![alt text](image-1.png)

## 3.为什么程序并发执行会产生间断性特征?
程序在并发执行时，由于它们共享系统资源，以及为完成同一项任务面相互合作，致使在这些并发执行的程序之间形成了相互制约的关系。

## 4.程序并发执行时为什么会失去封闭性和可再现性?
**失去封闭性**：当系统中存在着多个可以并发执行的程序时，系统中的各种资源将为它们所共享，而这些资源的状态也由这些程序来改变，致使其中任一程序在运行时，其环境都必然会受到其它程序的影响。

**不可再现性**：程序在并发执行时，由于失去了封闭性，其计算结果必将与并发程序的执行速度有关，从而使程序的执行失去了可再现性。换而言之，程序经过多次执行后，虽然它们执行时的环境和初始条件相同，但得到的结果却各不相同。

## 5.在操作系统中为什么要引入进程的概念?它会产生什么样的影响?
在早期未配置OS的系统和单道批处理系统中，程序的执行方式是顺序执行，即在内存中仅装入一道用户程序，由它独占系统中的所有资源,只有在一个用户程序执行完成后才允许装入另一个程序并执行。可见，这种方式浪费资源、系统运行效率低等缺点。而在名道程序系统中，由于内存中可以同时装入多个程序，使它们共享系统资源，并发执行显然可以克服上述缺点。而为了实现对并发执行的程序加以描述和控制，人们引入了“进程”的概念。

## 6.试从动态性、并发性和独立性上比较进程和程序。
进程和程序是两个截然不同的概念，除了进程具有程序所没有的PCB结构外，还具有下面一些特征:
(1)动态性。进程的实质是进程实体的执行过程，因此，动态性就是进程的最基本的特征。动态性还表现在:“它由创建而产生，由调度而执行，由消而消亡。”可见，程实体有一定的生命期，而程序则只是一组有序指令的集合，并存放于某种介质上，其本身并不具有活动的含义，因而是静态的。

(2)并发性。是指多个进程实体同存于内存中，且能在一段时间内同时运行。引入进程的目的也正是为了使其进程实体能和其它进程实体并发执行。因此，并发性是进程的一重要特征，同时也成为OS的重要特征。而程序(没有建立PCB)是不能参与并发执行的。

(3)独立性，在传统的OS中，独立性是指进程实体是一个能独立运行、独立获得资和独立接受调度的基本单位。凡未建立PCB的程序都不能作为一个独立的单位参与运行。

## 7.试说明 PCB 的作用具体表现在哪几个方面,为什么说 PCB 是进程存在的唯一标志

PCB 的作用是使一个在多道程序环境下不能独立运行的程序(含数据)成为一个能独立运行的基本单位,一个能与其他进程并发执行的进程。

(1)作为独立运行基本单位的标志。当一个程序(含数据)配置了PCB后，就表示它是一个能在多道程序环境下独立运行的、合法的基本单位，也就具有取得OS服务的权利如打开文件系统中的文件，请求获得系统中的IO设备，以及与其它相关进程进行通信因此，当系统创建一个新进程时，就为它建立了一个PCB。进程结束时又回收其PCB，程于是也随之消亡。**系统是通过PCB感知进程的存在的。事实上，PCB已成为进程存在的唯一标志。**

> 外部标识符:方便用户（进程）对进程的访问。
> 
> 内部标识符:方便系统对进程的使用。

(2)能实现间断性运行方式。

(3)提供进程管理所需要的信息。

(4)提供进程调度所需要的信息。

(5)实现与其它进程的同步与通信。

## 8.PCB 提供了进程管理和进程调度所需要的哪些信息?

1. 只能根据该进程PCB 中记录的程序和数据在内存或外存中的始址指针-L>找到相应的程序和数据

2. PCB中的信息->访问文件系统中的文件或I/O设备;

3. PCB中的资源清单->了解到该进程所需的全部资源等。

## 9.进程控制块的组织方式有哪几种?

在一个系统中，通常可拥有数十个、数百个乃至数千个PCB。为了能对它们加以有效的管理，应该用适当的方式将这些PCB 组织起来。目前常用的组织方式有以下三种。

(1)**线性方式**，即将系统中所有的PCB都组织在一张线性表中，将该表的首址存放在内存的一个专用区域中。该方式实现简单、开销小，但每次查找时都需要扫描整张表，因此适合进程数目不多的系统。
![alt text](image-2.png)

(2)**链接方式**，即把具有相同状态进程的PCB 分别通过PCB中的链接字链接成一个队列。这样，可以形成就绪队列，若干个阻塞队列和空白队列。对就绪队列而言，往往按进程的优先级将PCB从高到低进行排列，将优先级高的进程PCB排在队列的前面。同样，也可把处于阻状态进程的 PCB 根据其阻塞原因的不同，排成多个阻塞队列，如等待 I/O操作完成的队列和等待内存分配的队列。
![alt text](image-3.png)

(3)**索引方式**，即系统根据所有进程状态的不同，建立几张索引表，例如，就绪索引表、阻塞索引表等，并把各索引表在内存的首地址记录在内存的一些专用单元中。在每个索引表的表目中，记录具有相应状态的某个PCB在PCB表中的地址。
![alt text](image-4.png)

## 10.何谓操作系统内核?内核的主要功能是什么?

通常将一些**与硬件紧密相关的模块(如中断处理程序等)**、**各种常用设备的驱动程序**以及**运行频率较高的模块(如时钟管理、进程调度和许多模块所公用的一些基本操作)**，都安排在紧靠硬件的软件层次中，将它们常驻内存，即通常被称为的S内核这

## 11.试说明进程在三个基本状态之间转换的典型原因:

进程在运行过程中会经常发生状态的转换。例如，处于就绪状态的进程，在调度程序为之分配了处理机之后便可执行，相应地，其状态就由就绪态转变为执行态;正在执行的进程就绪(当前进程)如果因分配给它的时间片已完而被剥夺处理机暂停执行时,其状态便由执行转为就绪;如果因发生某事件，致使当前进程的执行受阻(例如进程访问某临界资源，而该资源正被其它进程访时)，使之无法继续执行，则该进程状态将由执行转变为阻塞。

![alt text](image-5.png)

## 12.为什么要引入挂起状态?该状态有哪些性质?

**挂起**意味着此时该进程处于静止状态。如果进程正在执行，它将暂停执行若原本处于就绪状态，则该进程此时暂不接受调度。与挂起操作对应的操作是激活操作。

引入挂起操作的原因，是基于系统和用户的如下需要:

(1)终端用户的需要。当终端用户在自己的程序运行期间发现有可疑问题，希望暂自己的程序的运行，使之停止下来，以便用户研究其执行情况或对程序进行修改。

(2)父进程请求。有时父进程希望挂起自己的某个子进程，以便考查和修改该子进程或者协调各子进程间的活动。

(3)负荷调节的需要。当实时系统中的工作负荷较重，已可能影响到对实时任务的控制时，可由系统把一些不重要的进程挂起，以保证系统能正常运行。

(4)操作系统的需要。操作系统有时希望挂起某些进程，以便检查运行中的资源使用情况或进行记账。

## 13.在进行进程切换时，所要保存的处理机状态信息有哪些?

它必须保留自己运行时的CPU现场信息，再被调度运行时，还需要恢复其CPU现场信息。在有了PCB后，系统就可将CPU现场信保存在被中断进程的PCB中，供该进程再次被调度执行时恢复CPU现场时使用。

## 14.试说明引起进程创建的主要事件。

首先由进程申请一个空白PCB，并向PCB中填写用于控制和管理进程的信息，系统有能为该进程分配运行时所必须的足够的资源。

## 15.试说明引起进程被撤消的主要事件

当一个进程到达了自然结束点，或是出现了无法克服的错误，或是被操作系统所终结，或是被其他有终止权的进程所终结，它将进入终止状态。

## 16.在创建一个进程时所要完成的主要工作是什么?

首先由进程申请一个空白PCB，并向PCB 中填写用于控制和管理进程的信息:然后为该进程分配运行时所必须的资源;最后，把该进程转入就绪状态并插入就绪队列之中但如果进程所需的资源尚不能得到满足，比如系统尚无足够的内存使进程无法装入其中，此时创建工作尚未完成，进程不能被调度运行。

## 17.在撤消一个进程时所要完成的主要工作是什么?

进入终止态的进程以后不能再执行，但在操作系统中依然就绪保留一个记录，其中保存状态码和一些计时统计数据，供其他进程收集。一旦其他进程完成了对其信息的提取之后，操作系统就删除该进程，即将PCB清零。

## 18.试说明引起进程阻塞或被唤醒的主要事件是什么?

有下述几类事件会引起进程阻塞或被唤醒:

(1)向系统请求共享资源失败。进程在向系统请求共享资源时，由于系统已无足够的资源分配给它，此时进程因不能继续运行而转变为阻塞状态。例如，一进程请求使用打印机，由于系统已将打印机分配给其它进程，已无可以再可分配的打印机，这时请求者进程只能被阻塞，仅在其它进程释放出打印机时，请求进程才被唤醒。

(2)等待某种操作的完成。当进程启动某种操作后，如果该进程必须在该操作完成之后能继续执行，则应先将该进程阻塞起来，以等待操作完成。例如，进程启动了某I/O设备如果只有在 I/O 设备完成了指定的 I/O 操作任务后进程才能继续执行，则该进程在启动了I/0设备后便应自动进入阻塞状态去等待。在I/O操作完成后，再由中断处理程序将该进程唤醒、

(3)新数据尚未到达。对于相互合作的进程，如果一个进程需要先获得另一进程提供的数据后才能对该数据进行处理，只要其所需数据尚未到达，进程便只有阻塞。例如，有两个进程，进程A用于输入数据，进程B对输入数据进行加工。假如A尚未将数据输入完毕，则进程B将因没有所需处理的数据而阻塞: 一旦进程A把数据输入完毕，便可去唤醒进程B。

(4)等待新任务的到达。在某些系统中，特别是在网络环境下的OS，往往设置一些特定的系统进程，每当这种进程完成任务后便把自己阻塞起来，等待新任务的到来。例如在网络环境中的发送进程，其主要任务是发送数据包，若已有的数据包已全部发送完成而又无新的数据包发送，这时发送进程将把自己阻塞起来;仅当有新的数据包到达时，将发送进程唤醒。

## 19.为什么要在OS中引入线程?

在20世纪60年代中期，人们在设计多道程序S时，引入了进程的概念，从而解决了在单处理机环境下的程序并发执行问题。此后在长达20年的时间里，在多道程序OS中一直是以进程作为能拥有资源和独立调度(运行)的基本单位的。直到80年代中期，人们又提出了比进程更小的基本单位--线程的概念，试图用它来提高程序并发执行的程度，进一步改善系统的服务质量。特别是在进入20世纪90年代后，多处理机系统得到迅速发展，由于线程能更好地提高程序的并行执行程度，因而近几年推出的多处理机OS无一例外地都引入了线程，用以改善OS的性能。

如果说，在OS中引入进程的目的是为了使多个程序能并发执行，以提高资源利用率和系统吞吐量，那么，**在操作系统中再引入线程，则是为了减少程序在并发执行时所付出的时空开销，使 OS 具有更好的并发性。**

## 20.试说明线程具有哪些属性?

(1)进程是一个可拥有资源的基本单位。在多线程OS中，进程仍是作为系统资源分的基本单位，任一进程所拥有的资源都包括:用户的地址空间、实现进程(线程)间同步和通信的机制、已打开的文件和已申请到的IO设备，以及一张由核心进程维护的地址映射表，该表用于实现用户程序的逻辑地址到其内存物理地址的映射。

(2)多个线程可并发执行。通常一个进程都含有若干个相对独立的线程，其数目可多可少，但至少要有一个线程。由进程为这些(个)线程提供资源及运行环境，使它们能并发执行。在OS中的所有线程都只能属于某一个特定进程。实际上，现在把传统进程的执行方法称为单线程方法。如传统的UNIX系统能支持多用户进程，但只支持单线程方法。反之，将每个进程支持多个线程执行的方法称为多线程方法。如Java的运行环境是单进程多线程的，Windows2000、Solaris、Mach等采用的则是多进程多线程的方法。

(3)进程已不是可执行的实体。在多线程QS中、是把线程作为独立运行(或称调度)的基本单位。此时的进程已不再是一个基本的可执行实体。虽然如此，进程仍具有与执行相关的状态。例如，所谓进程处于“执行”状态，实际上是指该进程中的某线程正有执行。茨外，对进程所施加的与进程状态有关的操传也对其线程起作用。例如，在把执程此分，该进程中的所有线程也都将被挂起;又如，在把某进程激活时，局于进企进程线程也都将被激活。

## 21.试从调度性、并发性、拥有资源及系统开销方面对进程和线程进行比较

1.调度的基本单位

在传统的 OS中，进程是作为独立调度和分派的基本单位，因而进程是能独立运行的基本单位。在每次被调度时，都需要进行上下文切换，开销较大。而在引入线程的OS中已把线程作为调度和分派的基本单位，因而线程是能独立运行的基本单位。当线程切换时仅需保存和设置少量寄存器内容，切换代价远低于进程。在同一进程中，线程的切换不引起进程的切换，但从一个进程中的线程切换到另一个进程中的线程时，必然就会引起造程的切换。

2.并发性

在引入线程的 OS中，不仅进程之间可以并发执行，而且在一个进程中的多个线程间亦可并发执行，甚至还允许在一个进程中的所有线程都能并发执行。同样，不同进程的线程也能并发执行，这使得OS具有更好的并发性，从而能更加有效地提高系统资源的利用率和系统的吞吐量。例如，在文字处理器中可以设置三个线程:第一个线程用于显文字和图形，第二个线程从键盘读入数据，第三个线程在后台进行拼写和语法检查。又如在网页浏览器中，可以设置一个线程来显示图像或文本，再设置一个线程用于从网络中收数据。

此外，有的应用程序需要执行多个相似的任务。例如，一个网络服务器经常会接到许多客户的请求，如果仍采用传统的单线程的进程来执行该任务，则每次只能为一个客户服务。但如果在一个进程中可以设置多个线程，将其中的一个专用于监听客户的请求，则当有一个客户请求时，便立即创建一个线程来处理该客户的请求。

3.拥有资源

进程可以拥有资源，并作为系统中拥有资源的一个基本单位。然而，线程本身并不拥有系统资源，而是仅有一点必不可少的、能保证独立运行的资源。比如，在每个线程中都应具有一个用于控制线程运行的线程控制块TCB、用于指示被执行指令序列的程序计数器保留局部变量、少数状态参数和返回地址等的一组寄存器和堆栈。

线程除了拥有自己的少量资源外，还允许多个线程共享该进程所拥有的资源。这首先表现在:属于同一进程的所有线程都具有相同的地址空间，这意味着，线程可以访问该地址空间中的每一个虚地址;此外，还可以访问进程所拥有的资源，如已打开的文件、定时器、信号量机构等的内存空间和它所申请到的I/O 设备等。

4.独立性

在同一进程中的不同线程之间的独立性要比不同进程之间的独立性低得多。这是因为为防止进程之间彼此干扰和破坏，每个进程都拥有一个独立的地址空间和其它资源，除了共享全局变量外，不允许其它进程的访问。但是同一进程中的不同线程往往是为了提高并发性以及进行相互之间的合作而创建的，它们共享进程的内存地址空间和资源，如每个线程都可以访问它们所属进程地址空间中的所有地址,如一个线程的堆栈可以被其它线程读、写，甚至完全清除。由一个线程打开的文件可以供其它线程读、写。

5.系统开销

在创建或撤消进程时，系统都要为之分配和回收进程控制块、分配或回收其它资源，如内存空间和JO设备等。OS为此所付出的开销，明显大于线程创建或撤消时所付出的开销。类似地，在进程切换时，涉及到进程上下文的切换，而线程的切换代价也远低于进程的。例如，在Solaris20s中，线程的创建要比进程的创建快30倍，而线程上下文切换要比进程上下文的切换快5倍。此外，由于一个进程中的多个线程具有相同的地址空间，线程之间的同步和通信也比进程的简单。因此，在一些OS中，线程的切换、同步和通信都无需操作系统内核的干预。

6.支持多处理机系统

在多处理机系统中，对于传统的进程，即单线程进程，不管有多少处理机，该进程只能运行在一个处理机上。但对于多线程进程，就可以将一个进程中的多个线程分配到多个处理机上，使它们并行执行，这无疑将加速进程的完成。因此，现代多处理机S都无例外地引入了多线程。

## 22.线程控制块 TCB 中包含了哪些内容?

如同每个进程有一将所有用于控制和管理线程的信息记录在线程控制块中。线程控制块通常有这样几项:
①线程标识符，为每个线程赋予一个唯一的线程标识符;

②一组寄存器，包括程序计数器PC、状态寄存器和通用寄存器的内容;

③线程运行状态，用于描述线程正处于何种运行状态；

④优先级，描述线程执行的优先程度；

⑤线程专有存储区，用于线程切换时存放现场保护信息，和与该线程相关的统计信息等；

⑥信号屏蔽，即对某些信号加以屏蔽；

⑦堆栈指针，在线程运行时，经常会进行过程调用，而过程的调用通常会出现多重嵌套的情况，这样，就必须将每次过程调用中所使用的局部变量以及返回地址保存起来。为此，应为每个线程设置一个堆栈，用它来保存局部变量和返回地址。相应地，在TCB中，也须设置两个指向堆栈的指针:指向用户自己堆栈的指针和指向核心栈的指针。前者是指当线程运行在用户态时，使用用户自己的用户栈来保存局部变量和返回地址，后者是指当线程运行核心态时使用系统的核心栈。

## 23.何谓用户级线程和内核支持线程?

1.内核支持线程 KST(Kernel Supported Threads)

在OS中的所有进程，无论是系统进程还是用户进程，都是在操作系统内的支持下运行的，是与内核紧密相关的。而内核支持线程KST同样也是在内核的支持下运行的，它们的创建、阻塞、撤消和切换等，也都是在内核空间实现的。为了对内核线程进行控制和管理，在内核空间也为每一个内核线程设置了一个线程控制块，内核根据该控制块而感知某线程的存在，并对其加以控制。当前大多数OS都支持内核支持线程。

2.用户级线程 ULT(User Level Threads)

用户级线程是在用户空间中实现的。对线程的创建、撤消、同步与通信等功能，都无需内核的支持，即用户级线程是与内核无关的。在一个系统中的用户级线程的数目可以达到数百个至数千个。由于这些线程的任务控制块都是设置在用户空间，而线程所执行的操作也无需内核的帮助，因而内核完全不知道用户级线程的存在。值得说明的是，**对于设置了用户级线程的系统，其调度仍是以进程为单位进行的**。在采用轮转调度算法时，各个进程轮流执行一个片，这对诸进程而言貌似是公平的。但假如在进程A中包含了一个用户级线程，而在另一个进程B中含有100个用户级线程，这样，进程A中线程的运行时间将是进程B中各线程运行时间的100倍:相应地，某速度快上100倍，因此说实质上并不公平。

假如系统中设置的是**内核支持线程，则调度便是以线程为单仪进行的**。在采用轮转法调度时，是各个线程轮流执行一个时间片。同样假定进程A中只有一个内核支持线程，而在进程B中有100个内核支持线程。此时进程B可以获得的CPU时间是进程A的100倍且进程B可使100个系统调用并发工作

## 24.试说明用户级线程的实现方法

用户级线程是在用户空间实现的。所有的用户线程都具有相同的结构，它们都运行在一个中间系统上。当前有两种方式实现中间系统，即运行时系统和内核控制线程。

## 25.试说明内核支持线程的实现方法

内核支持线程可以直接利用系统调用为它服务，故线程的控制相当简单。

## 26.多线程模型有哪几种类型?多对一模型有何优缺点?

主要有多对一模型、一对多模型、多对多模型。

**多对一模型**： 这些用户线程一般属于一个进程，运行在该进程的用户空间，对这些线程的调度和管理也是在该进程的用户空间中完成。仅当用户线程需要访问内核时，才将其映射到一个内核控制线程上，但每次只允许一个线程进行映射。该模型的主要优点是线程管理的开销小，效率高:其主要缺点在子，如果一个线程在访问内核时发生阻塞，则整个进程都会被阻塞、此外，在任一时刻，只有一个线程能够访问内核，多个线程不能同时在多个处理机上运行。