[toc]

# 父类子类调用顺序

​	静态变量和静态语句块优先于实例变量和普通语句块，静态变量和静态语句块的初始化顺序取决于它们在代码中的顺序。

​	存在继承的情况下，初始化顺序为：

- 父类（静态变量、静态语句块）
- 子类（静态变量、静态语句块）
- 父类（实例变量、普通语句块）
- 父类（构造函数）
- 子类（实例变量、普通语句块）
- 子类（构造函数）

# 序列化

​	序列化：把对象转换为字节序列的过程称为对象的序列化。

​	反序列化：把字节序列恢复为对象的过程称为对象的反序列化。

​	*serialVersionUI*：进行反序列化时，*JVM* 会把传来的字节流中的 *serialVersionUID* 于本地相应实体类的 *serialVersionUID* 进行比较。如果相同说明是一致的，可以进行反序列化，否则会出现反序列化版本一致的异常，即是 *InvalidCastException*。

## 实现序列化

1. 让类实现 *Serializable*接口,该接口是一个标志性接口,标注该类对象是可被序列

2. 然后使用一个输出流来构造一个对象输出流并通过*writeObect*(*Obejct*)方法就可以将实现对象写出

3. 如果需要反序列化,则可以用一个输入流建立对象输入流,然后通过*readObeject*方法从流中读取对象

## 作用

1. 序列化就是一种用来处理对象流的机制,所谓对象流也就是将对象的内容进行流化,可以对流化后的对象进行读写操作,也可以将流化后的对象传输与网络之间;
2. 为了解决对象流读写操作时可能引发的问题(如果不进行序列化,可能会存在数据乱序的问题)；
3. 序列化除了能够实现对象的持久化之外，还能够用于对象的深度克隆；

**被 *transient* 修饰的字段不会被序列化**

# *final*

​    *final* 修饰类 该类不能被继承；

​    *final* 修饰方法 该方法不能被重写；

​    *final* 修饰变量  *final* 修饰基本类型，该变量值不能变，*final* 修饰引用类型变量，该变量不能指向其他对象（但是其内部的值可以改变）;

# *Java*异常

​	所有的异常都是从 *Throwable* 继承而来的，是所有异常的共同祖先。

​	*Throwable* 有两个子类，*Error* 和 *Exception*。

​	其中 *Error* 是错误，这些错误表示故障发生于虚拟机自身、或者发生在虚拟机试图执行应用时。这些错误是不可查的，因为它们在应用程序的控制和处理能力之外，如*StackOverFlowError*、*OutOfMemoryError*。

​	*Exception*，是另外一个非常重要的异常子类。它规定的异常是程序本身可以处理的异常。 *Exception* 分为 *IOException* 和 *RunTimeException*。

1. 运行时异常(不受检异常)：*RuntimeException* 类极其子类表示*JVM*在运行期间可能出现的错误。比如说试图使用空值对象的引用（*NullPointerException*）、数组下标越界（*ArrayIndexOutBoundException*）。此类异常属于不可查异常，一般是由程序逻辑错误引起的，在程序中可以选择捕获处理，也可以不处理。

2. 编译异常(受检异常)：*Exception* 中除 *RuntimeException* 极其子类之外的异常，如 *IOException*。如果程序中出现此类异常，必须对该异常进行处理，否则编译不通过。在程序中，通常不会自定义该类异常，而是直接使用系统提供的异常类。

# 抽象类和接口

## 抽象类

> + 可以有构造方法
> + 可以没有抽象方法
> + 包含抽象方法的类一定是抽象类
> + 如果一个类继承于一个抽象类，则子类必须实现父类的抽象方法；如果没有实现，则必须将子类也定义为抽象类。

## 接口

> + 可以包含变量，但都是 *final static* 类型
> + 接口里的方法都是抽象方法

# *Equals*和*HashCode*

*Java*对象的 *eqauls* 方法和 *hashCode* 方法是这样规定的：

+ 相等（相同）的对象必须具有相等的 *hashCode*。

  + 如果两个对象的 *hashCode* 相同，它们并不一定相同。

# 重写与重载

> 重载：一个类中，方法名一样，但是参数列表不同
>
> 重写：子类重写父类方法

# 字符串不可变的好处

1. *String* *Pool* 的需要

   如果一个 *String* 对象已经被创建过了，那么就会从 *String* *Pool* 中取得引用。只有 *String* 是不可变的，才可能使用 *String* *Pool*。

2. 安全性

   *String* 经常作为参数，*String* 不可变性可以保证参数不可变。例如在作为网络连接参数的情况下如果 *String* 是可变的，那么在网络连接过程中，*String* 被改变，改变 *String* 的那一方以为现在连接的是其它主机，而实际情况却不一定是。

3. 线程安全

   *String* 不可变性天生具备线程安全，可以在多个线程中安全地使用。

# *String*赋值的两种方式

1. *String str = "test"*

   以这种方式赋值时，*JVM* 会先从字符串实例池中查询是否存在"*test*"这个对象，

   如果存在，直接把实例池中"*test*"的地址返回给 *str*。如果不存在，则会在实例池中创建"*test*"对象，并把该对象的地址返回给*str*。

2. *String str = new String("test")*

   以这种方式赋值时，*JVM* 会先从字符串实例池中查询是否存在"*test*"这个对象，

   若不存在则会在实例池中创建"*test*"对象，同时在堆中创建"*test*"这个对象，然后将**堆中的这个对象的地址**返回赋给引用 *str*。

   若实例池存在"*test*"对象，则直接在堆中创建"*test*"这个对象，然后将堆中的这个对象的地址返回赋给引用 *str*。

# *String*、*StringBuilder* 和 *StringBuffer*

**1. 可变性**

- *String* 不可变
- *StringBuffer* 和 *StringBuilder* 可变

**2. 线程安全**

- *String* 不可变，因此是线程安全的
- *StringBuilder* 不是线程安全的
- *StringBuffer* 是线程安全的，内部使用 *synchronized* 进行同步

 # *Volatile*

​	被 *volatile* 修饰的变量能够保证每个线程能获取到该变量的最新值。（*Java*内存模型告诉我们，各个线程会将共享变量从主内存中拷贝到工作内存，然后执行引擎会基于工作内存中的数据进行操作处理，之后在写到主存中）。

## 作用

1. 保证内存可见性

   当一个变量被 *volatile* 修饰时，任何线程对它的写操作都会立即刷新到主内存中，并且会强制让缓存了该变量的线程从主内存重新读取最新数据。

2. 禁止指令重排序

   对 *volatile* 变量的操作指令都不会被重排序，因为如果重排序，又可能产生可见性问题。

## 使用场景

1. *volatile* 是轻量级同步机制。在访问 *volatile* 变量时不会执行加锁操作，因此也就不会使执行线程阻塞，是一种比 *synchronized* 关键字更轻量级的同步机制。

2. *volatile* 无法保证原子性，加锁机制既可以确保可见性又可以确保原子性。

3. *volatile* 不能修饰写入操作依赖当前值的变量。声明为 *volatile* 的简单变量如果当前值与该变量以前的值相关，那么 *volatile* 关键字不起作用，也就是说如下的表达式都不是原子操作：“*count*++”、“*count* = *count*+1”。

4. 当要访问的变量已在 *synchronized* 代码块中，或者为常量时，没必要使用 *volatile*；

5. *volatile* 屏蔽掉了 *JVM* 中必要的代码优化，所以在效率上比较低，因此一定在必要时才使用此关键字。

# 线程与并发

## *wait* 方法底层原理

*wait* 方法将当前线程放入 *wait* *set*，等待被唤醒，并放弃*lock*对象上的所有同步声明。

1. 将当前线程封装成 *ObjectWaiter* 对象 *node*；

2. 通过 *AddWaiter* 方法将 *node* 添加到 *WaitSet* 列表中；

3. 通过 *exit* 方法释放当前的 *ObjectMonitor* 对象，这样其它竞争线程就可以获取该 *ObjectMonitor* 对象。

4. 最终底层的 *park* 方法挂起线程；

（最后与之对应的 *notify* 方法）会随机唤醒 *WaitSet* 中随机一个线程

## *CAS*

​	即*compare* *and* *swap*（比较与交换），是一种有名的无锁算法。无锁编程，即不使用锁的情况下实现多线程之间的变量同步，也就是在没有线程被阻塞的情况下实现变量的同步，所以也叫非阻塞同步（*Non*-*blocking* *Synchronization*）。*CAS*算法涉及到三个操作数

​	需要读写的内存值 *V*、进行比较的值 *A*、拟写入的新值 *B*。当且仅当 *V* 的值等于 *A* 时，*CAS* 通过原子方式用新值 *B* 来更新 *V* 的值，否则不会执行任何操作（比较和替换是一个原子操作）。一般情况下是一个自旋操作，即不断的重试。

### *ABA* 问题如何解决

加入版本号机制

## 线程中断

### 线程中断的作用

​	**线程中断可以使一个线程从等待状态变成就绪状态**，如果中断的线程正处于运行状态，那么这个中断是不会有任何作用的（表面上不会影响正在运行的线程），线程恢复到就绪状态后，可以继续执行逻辑代码。

> 想要让一个线程从等待状态中恢复过来有三种发送：
>
> 1. 等待超时 
> 2. 得到一个通知
> 3. 使用中断

注意：使用线程中断，并不是要把线程给终止或是杀死，而是让线程不再继续等待，线程可以继续往下执行代码。

### 线程中断的原理

​	*Thread.interrupt()* 方法仅仅将当前线程的中断标志修改为 *true*，并没有真正的停止线程。如果在此基础上进入阻塞状态（*sleep()*,*wait()*,*join()*）,马上就会抛出一个 *InterruptedException*，且中断标志被清除，重新设置为*false*。抛出异常是为了线程从阻塞状态醒过来，并在结束线程前让程序员有足够的时间来处理中断请求。

+ *interrupt()* 中断线程，将会设置该线程的中断状态位，即设置为*true*
+ *isInterrupted()* 判断某个线程是否已被发送过中断请求
+ *interrupted()* 判断某个线程是否已被发送过中断请求，并且将中断状态重新置为*false*

举例：线程*A*获得了锁进入了同步代码块中，但由于条件不足调用 *wait()* 方法阻塞了。这个时候，线程*B*执行 *threadA.interrupt()* 请求中断线程*A*，此时线程A就会抛出 *InterruptedException*，我们就可以在 *catch* 中捕获到这个异常并进行相应处理。

## 线程状态

1. 新建（*NEW*）

   创建后尚未启动。

2. 可运行（*RUNABLE*）

   正在 *Java* 虚拟机中运行。但是在操作系统层面，它可能处于运行状态，也可能等待资源调度（例如处理器资源），资源调度完成就进入运行状态。所以该状态的可运行是指可以被运行，具体有没有运行要看底层操作系统的资源调度。

3. 阻塞（*BLOCKED*）

   请求获取 *monitor* *lock* 从而进入 *synchronized* 函数或者代码块，但是其它线程已经占用了该 *monitor lock*，所以处于阻塞状态。要结束该状态进入 *runnable* 需要其他线程释放 *monitor lock*。

4. 无限期等待（*WAITING*）

    等待其它线程显式地唤醒。

    阻塞和等待的区别在于，阻塞是被动的，它是在等待获取 *monitor lock*。而等待是主动的，通过调用 *Object.wait()* 等方法进入。

5. 限期等待（*TIMED_WAITING*）

   无需等待其它线程显式地唤醒，在一定时间之后会被系统自动唤醒。

6. 死亡（*TERMINATED*）

   可以是线程结束任务之后自己结束，或者产生了异常而结束。

## 创建线程的三种方式

1. 继承 *Thread* 类，重写 *run* 方法

2. 实现 *Runnable* 接口，重写 *run* 方法

3. 实现 *callable* 接口，重写 *call* 方法

## 线程不 *start*，直接 *run* 可以吗

​    可以，但是直接使用 *run* 的话起不到多线程的效果，就相当于平时直接调用类中的某个方法；*start*() 才是真正启动了一个线程，这时线程就进入就绪状态，等待 *CPU* 资源，而且不需要等待 *run* 方法执行完，可以直接去执行下一句代码，这才是真正实现了多线程。

## *CountDownLatch*

1. 创建一个 *CountDownLatch*，并赋予一个数值 *state*，这个值表示需要计数的次数，每次 *countDown* 算一次。
2. 在主线程调用 *await* 方法，表示 *state* 为0之前都不会继续运行。*await* 方法的内部实现依赖于内部的 *AQS*，调用 *await* 方法的时候会尝试去获取资源，成功条件是 *state*=0，也就是说除非 *countDown* 了 *state* 次之后，才能成功，失败的话当前线程进行休眠。
3. 在子线程调用 *countDown* 方法，每次调用都会使内部的 *state*-1，*state* 为0的时候资源释放，*await* 方法不再阻塞(即使再次调用也是)。

## *CyclicBarrier*

1. 创建 *CyclicBarrier*，并赋值 *parties*，每个子线程完成任务后调用 *await* 方法会将该值-1并等待其他线程，等所有子线程均完成任务之后，该值为0，然后重置该值为一开始的值，然后子线程又开始执行任务，重复以上过程，一直到 *run* 方法里执行结束。

# 线程池

队列作为一个缓冲的工具，当没有足够的线程去处理任务时，可以将任务放进队列中，以队列先进先出的特性来执行工作任务

1. *new CachedThreadPool*

   创建一个可缓存线程池，无线程上限，来了一个任务，若有空闲线程则交由其执行，若无空闲线程则新建一个线程来执行，线程空闲时间超过60s会自动销毁。

2. *new FixedThreadPool*

   创建一个指定工作线程数量的线程池。每当提交一个任务就创建一个工作线程，如果工作线程数量达到线程池核心线程数，则将提交的任务存入到池队列中。（其核心线程数和最大线程数是一样的）

3. *new SingleThreadExecutor*

   创建一个单线程化的*Executor*，即只创建唯一的工作者线程来执行任务，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序执行。如果这个线程异常结束，会有另一个取代它，保证顺序执行。

4. *new ScheduleThreadPool*

   创建一个定长的线程池，而且支持定时的以及周期性的任务执行，支持定时及周期性任务执行。

5. *new workingStealingPool*

   创建一个拥有多任务队列的线程池，创建当前cpu核数的线程来并行执行任务

线程数一开始为0，此时来任务则会创建一个新线程来执行。如果线程数量达到了核心线程数，则会向任务队列里添加任务，如果任务队列已满，不能再向任务队列中添加任务时，这时会检查线程池是否达到了最大线程数，如果未达到，则创建新线程，然后从工作队列的头部取出一个任务交由新线程来处理，而将刚提交的任务放入工作队列尾部，否则，执行拒绝策略。

## 线程池的作用

1. 减少了创建和销毁线程的次数，每个工作线程都可以被重复利用，可执行多个任务。

2. 可以根据系统的承受能力，调整线程池中工作线程的数目，防止因为消耗过多的内存，而把服务器累趴下(每个线程需要大约*1MB*内存，线程开的越多，消耗的内存也就越大，最后死机)。

## 线程池初始化参数的含义

1. *corePoolSize*——线程池核心线程大小

   线程池中会维护一个最小的线程数量，**即使这些线程处理空闲状态，他们也不会被销毁**，除非设置了 *allowCoreThreadTimeOut*，这里的最小线程数量即是 *corePoolSize*。

2. *maximumPoolSize*——线程池最大线程数量

   线程池不会无限制的去创建新线程，它会有一个最大线程数量的限制，这个数量即由*maximunPoolSize*指定。

3. *keepAliveTime*——空闲线程存活时间

   一个线程如果处于空闲状态，并且当前的线程数量大于 *corePoolSize*，那么在指定时间后，这个空闲线程会被销毁，这里的指定时间由 *keepAliveTime* 来设定

4. *unit*——空闲线程存活时间单位

   *keepAliveTime*的计量单位

5. [*workQueue*——工作队列](#工作队列)

6. *threadFactory*——线程工厂

   创建一个新线程时使用的工厂，可以用来设定线程名、是否为 *daemon* 线程等等

7. [*handler*——拒绝策略](#线程池四种拒绝策略)

## 工作队列

### *SynchronousQueue*

> *SynchronousQueue* 没有容量，是一个容量为1的阻塞队列
>
> 提交的任务不会被保存在队列中，而是直接将任务交给消费者，必须等队列中的元素被线程取走后才能继续添加新的元素

### *ArrayBlockingQueue*

> 创建队列时，指定队列的最大容量
>
> 若有新的任务要执行，如果线程池中的线程数小于*corePoolSize*，则会优先创建新的线程。若大于*corePoolSize*，则会将新任务加入到等待队列中
>
> 若等待队列已满，无法加入。如果总线程数不大于线程数最大值*maximumPoolSize*，则创建新的线程执行任务。若大于*maximumPoolSize*，则执行拒绝策略

### *LinkedBlockingQueue*

> 队列大小为 *Integer.MAX_VALUE*
>
> 由于其容量，所以使用无界队列将导致在所有 *corePoolSize* 线程都忙时，所有后来的任务都会在队列中等待。这样，创建的线程就不会超过 *corePoolSize*（因此，*maximumPoolSize* 的值也就无效了）。

### *PriorityBlockingQueue*

> 带有执行优先级的队列。是一个特殊的无界队列。
>
> *ArrayBlockingQueue* 和 *LinkedBlockingQueue* 都是按照先进先出算法来处理任务。而*PriorityBlockingQueue* 可根据任务自身的优先级顺序先后执行，总是确保高优先级的任务先执行

## 线程池四种拒绝策略

1. *AbortPolicy* 丢弃任务并抛出异常

2. *DiscardPolicy* 丢弃任务，不抛出异常

3. *DiscardOldestPolicy* 丢弃队列最前面的任务，然后重新提交被拒绝的任务

4. *CallerRunsPolicy* 由提交任务的线程处理该任务

## *sleep*()和*wait*()的区别以及*notify*()

1. *sleep*()来自*Thread*类，*wait*(),*notify*()来自*Object*类。

2. 当执行*sleep*的时候没有释放锁，而*wait*方法必须释放锁，使得其他线程可以使用同步代码块或方法

3. *wait*释放锁用*notify*唤醒；*sleep*不出让系统资源，*wait*出让系统资源，在线程等待池中等待。

4. *wait*，*notify*，*notifyAll*只能用在同步控制（同步控制方法或同步控制块），而*sleep*可以在任何地方使用。

（*Thread.sleep(0)*是让系统重新进行一次*CPU*的竞争，*sleep*到时间之后返回就绪态） 

## *Lock* 和 *Synchorized*

1. 来源：*lock* 是一个接口，而 *synchronized* 是 *Java* 的一个关键字，*synchronized* 是内置的语言实现；
2. 异常是否释放锁：*synchronized* 在发生异常时候会自动释放占有的锁，因此不会出现死锁；而*lock* 发生异常时候，不会主动释放占有的锁，必须手动 *unlock* 来释放锁，可能引起死锁的发生。
3. 是否响应中断：*lock*等待锁过程中可以用 *interrupt* 来响应中断，而 *synchronized* 只能等待锁的释放，不能响应中断；
4. 是否知道获取锁：*lock* 可以通过 *trylock* 来知道有没有获取锁，而 *synchronized* 不能；*Lock* 可以提高多个线程进行读操作的效率。(可以通过 *readwritelock* 实现读写分离)
5. 在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时 *lock* 的性能要远远优于 *synchronized*。所以说，在具体使用时要根据适当情况选择。
6. *synchronized* 使用*Object*对象本身的 *wait* 、*notify*、*notifyAll* 调度机制，而 *lock* 可以使用*Condition* 进行线程之间的调度
7. 对于 *synchronized*，一个对象拥有一个同步队列和等待队列（用来保存被阻塞的线程），而并发包中的*Lock*拥有一个同步队列和多个等待队列；*AQS* 的等待队列是有多个，因为 *AQS* 实现排他锁（*ReentrantLock*）和非排他锁（*ReentrantReadWriteLock*——读写锁），读写锁就是一个需要多个等待队列的锁。因为读写锁是一对锁，所以需要两个等待队列来分别保存被阻塞的读锁和被阻塞的写锁。

8. *synchronized* 采用的是[悲观锁](##悲观锁)，而 *lock* 用的是[乐观锁](##乐观锁)。

## *ReentrantLock* 和 *Synchorized*

1. *ReentrantLock* 和 *Synchorized* 均为可重入锁（**同一线程在不释放锁的情况下可再次获得锁且不死锁**）、独占锁（**同一时刻只能由一个线程持有**）。

2. *ReentrantLock* 分为公平锁和非公平锁（**默认为非公平锁**），公平锁保证等待时间最长的线程将优先获得锁，而非公平锁并不会保证多个线程获得锁的顺序，但是非公平锁的并发性能表现更好。

3. *ReentrantLock* 提供了一个 *Condition* 类，用来实现分组唤醒需要唤醒的线程们，而不是像 *synchronized* 要么随机唤醒一个线程要么唤醒全部线程

# 乐观锁和悲观锁

## 悲观锁

​	总是假设最坏的情况，即每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。*Java* 中 *Synchronized* 和*ReentrantLock* 等独占锁就是悲观锁思想的实现。

## 乐观锁

​	总是假设最好的情况，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间数据有没有更新（可以使用版本号机制和 *CAS* 算法实现），如果因为冲突失败就重试，直到成功为止。乐观锁适用于多读的应用类型，这样可以提高吞吐量。

### 乐观锁常见的两种实现方式

乐观锁一般会使用版本号机制或*CAS*算法实现。

1. 版本号机制

   一般是在数据表中加上一个数据版本号*version*字段，表示数据被修改的次数，当数据被修改时，*version*值会加一。当线程*A*要更新数据值时，在读取数据的同时也会读取*version*值，在提交更新时，若刚才读取到的*version*值为当前数据库中的*version*值相等时才更新，否则重试更新操作，直到更新成功。

2. [*CAS*算法](#CAS)

## 两种锁的使用场景

​	乐观锁适用于读比较多的情况下，即冲突很少发生的时候，这样可以省去了锁的开销，加大了系统的整个吞吐量。但如果是多写的情况，一般会经常产生冲突，这就会导致上层应用会不断的进行*retry*，这样反倒是降低了性能，所以一般多写的场景下用悲观锁就比较合适。

## 对象锁

​	*synchronized* 修饰非静态方法或代码块 *synchronized(this)*。

​	当一个对象中有 *synchronized* 方法或 *synchronized* 代码块的时候，调用此对象的同步方法或进入同步块时，就必须先获得对象锁，如果此对象的对象锁已被其他调用者占用，则需要等待此锁被释放。

​	这种机制确保了同一时刻对于每一个类的实例，其所有声明为 *synchronized* 的成员函数中之多只有一个处于可执行状态，从而有效避免了类成员变量的访问冲突。

​	任何一个对象都有一个 *Monitor* 与之关联，当且一个 *Monitor* 被持有后，它将处于锁定状态。*synchronized* 在 *JVM* 里的实现都是基于进入和退出 *Monitor* 对象来实现方法同步和代码块同步。

​	*MonitorEnter* 指令：插入在同步代码块的开始位置，当代码执行到该指令时，将会尝试获取该对象 *Monitor* 的所有权，即尝试获得该对象的锁；

​	*MonitorExit* 指令：插入在方法结束处和异常处，*JVM* 保证每个 *MonitorEnter* 必须有对应的*MonitorExit*；

​	在 *Java* 的设计中 ，每一个 *Java* 对象自打娘胎里出来就带了一把看不见的锁，它叫做内部锁或者*Monitor* 锁。

​	*Monitor* 对象存在于每个 *Java* 对象的对象头 *Mark* *Word* 中（存储的指针的指向），*synchronized* 锁便是通过这种方式获取锁的，也是为什么 *Java* 中任意对象可以作为锁的原因，同时 *notify*/*notifyAll*/*wait* 等方法会使用到 *Monitor* 锁对象，所以必须在同步代码块中使用。

## 类锁

​	*synchronized* 修饰静态方法(*static synchronized*)或者代码块 *syncronized(xxx.class)* 或者锁住的是*static* 静态变量。

​	由于一个 *class* 不论被实例化多少次，其中的静态方法和静态变量在内存中都只有一份。所以，一旦一个静态的方法被声明为 *synchronized*。此类所有的实例对象在调用此方法，共用的是同一把锁。

## 自旋锁

​	许多应用上，共享数据的锁定状态只会持续很短的一段时间，为了这段时间去挂起和恢复线程并不值得。如果机器有一个以上的处理器，能让两个或以上的线程同时并行执行，我们就可以让后面请求锁的那个线程“稍等一下“，但不放弃处理器的执行时间，看看持有锁的线程是否很快就会释放锁。为了让线程等待，我们只需让线程执行一个忙循环（自旋），这项技术就是所谓的自旋锁。 

 ## 锁的升级过程

​	偏向锁会偏向于第一个获得它的线程，如果在接下来的执行过程中，该锁没有被其他的线程获取，则持有偏向锁的线程将永远不需要同步。大多数情况下，锁不仅不存在多线程竞争，而且总是由同一线程多次获得，为了让线程获得锁的代价更低而引入了偏向锁。当有另外一个线程去尝试获取这个锁时，偏向模式就宣告结束。根据锁对象目前是否处于被锁定的状态，撤销偏向锁后恢复到未锁定或轻量级锁定状态。

​	一个对象刚开始实例化的时候，没有任何线程来访问它的时候。它是可偏向的，意思是它现在认为只可能有一个线程来访问它，所以当第一个线程来访问它的时候，它会偏向这个线程，此时为偏向锁。这个线程在修改对象头成为偏向锁的时候使用 *CAS* 操作，并将对象头中的 *ThreadID* 改成自己的 *ID*，之后再次访问这个对象时，只需要对比 *ID*，不需要再使用 *CAS* 在进行操作。（对象头里保存了*hashcode*，锁信息等）

​	偏向锁不会主动释放，一旦有第二个线程访问这个对象，这时表明在这个对象上已经存在竞争了，检查原来持有该对象锁的线程是否依然存活，如果不存活，对象重置为无锁状态，重新偏向新的线程；如果原来的线程依然存活，则检查对象锁的使用情况，如果不使用，则可以将对象恢复成无锁状态，然后重新偏向；如果仍然需要持有偏向锁，则偏向锁升级为轻量级锁。

​	轻量级锁认为竞争存在，但是竞争的程度很轻，一般两个线程对于同一个锁的操作都会错开，或者说自旋一下（忙循环，不会放弃 *CPU* 避免切换线程带来的开销）另一个线程就会释放锁。 但是当自旋超过一定的次数，或者一个线程在持有锁，一个在自旋，又有第三个来访时，轻量级锁膨胀为重量级锁，重量级锁使除了拥有锁的线程以外的线程都阻塞，防止 *CPU* 空转。

> 轻量级锁提升程序同步性能的依据是：对于绝大部分的锁，在整个同步周期内都是**不存在锁竞争**的（区别于偏向锁），这是一个经验数据。如果没有竞争，轻量级锁使用*CAS*操作避免了使用互斥量的开销，但如果存在锁竞争，除了互斥量的开销外，还额外发生了*CAS*操作，因此在有竞争的情况下，轻量级锁比传统的重量级锁更慢。

 ## 上下文切换

​	*CPU* 切换前把当前任务的状态保存下来，以便下次切换回这个任务时可以再次加载这个任务的状态，然后加载下一任务的状态并执行。任务的状态保存及再加载, 这段过程就叫做上下文切换。进程上下文切换与线程上下文切换最主要的区别就是线程的切换虚拟空间内存是相同的（因为都是属于自己的进程），但是进程切换的虚拟空间内存则是不同的。

# *happens*-*before*

​	*happens*-*before*关系向程序员提供跨线程的内存可见性保证

1. 如果一个操作 *happens*-*before* 另一个操作，那么第一个操作的执行结果将对第二个操作可见，而且第一个操作的执行顺序排在第二个操作之前。

2. 两个操作之间存在 *happens*-*before* 关系，并不意味着 *Java* 平台的具体实现必须要按照*happens*-*before* 关系指定的顺序来执行。如果重排序之后的执行结果，与按 *happens*-*before* 关系来执行的结果一致，那么这种重排序并不非法

## 具体规则

1. 程序顺序规则：一个线程中的每个操作，*happens*-*before* 于该线程中的任意后续操作。
2. 监视器锁规则：对一个锁的解锁，*happens*-*before* 于随后对这个锁的加锁。
3. *volatile*变量规则：对一个 *volatile* 域的写，*happens*-*before* 于任意后续对这个 *volatile* 域的读。
4. 传递性：如果 *A* *happens*-*before* *B*，且 *B* *happens*-*before* *C*，那么 *A* *happens*-*before* *C*。
5. *start*() 规则：如果线程 *A* 执行操作 *ThreadB*.*start*()（启动线程*B*），那么 *A* 线程的*ThreadB*.*start*() 操作 *happens*-*before* 于线程 *B* 中的任意操作。
6. *Join*() 规则：如果线程*A*执行操作 *ThreadB*.*join*() 并成功返回，那么线程 *B* 中的任意操作*happens*-*before* 于线程 *A* 从 *ThreadB*.*join*() 操作成功返回。
7. 程序中断规则：对线程 *interrupted*() 方法的调用先行于被中断线程的代码检测到中断时间的发生。
8. 对象 *finalize* 规则：一个对象的初始化完成（构造函数执行结束）先行于发生它的 *finalize*() 方法的开始。

# 反射机制  

1. *Java* 反射机制的核心是在程序运行时动态加载类并获取类的详细信息，从而操作类或对象的属性和方法。本质是 *JVM* 得到 *class* 对象之后，再通过 *class* 对象进行反编译，从而获取对象的各种信息。

2. *Java* 属于先编译再运行的语言，程序中对象的类型在编译期就确定下来了，而当程序在运行时可能需要动态加载某些类，这些类因为之前用不到，所以没有被加载到 *JVM*。通过反射，可以在运行时动态地创建对象并调用其属性，不需要提前在编译期知道运行的对象是谁。

 ## 获得*Class*字节码的三种方法

1. *Class class = new xxx().getClass();*

2. 任何数据类型（包括基本的数据类型）都有一个“静态”的*class*属性，*Class class = xxx.class*

3. 通过*class*类的静态方法：*forName*(*String* *className*)

   *Class class = Class.forName*(“类完整名”);（最常用）

## 如何通过反射创建对象

+ 方法1：通过类对象调用*newInstance*()方法，例如：*String*.*class*.*newInstance*()

+ 方法2：通过类对象的*getConstructor*()或*getDeclaredConstructor*()方法获得构造器（*Constructor*）对象并调用其*newInstance*()方法创建对象，例如：*String*.*class*.*getConstructor*(*String*.*class*).*newInstance*("*Hello*");

## 反射机制的优缺点

​	优点：可以动态执行，在运行期间根据业务功能动态执行方法、访问属性，最大限度发挥了 *java*的灵活性。

​	缺点：对性能有影响，这类操作总是慢于直接执行 *java* 代码

## 哪里用到反射机制

1. *JDBC* 中，利用反射动态加载了数据库驱动程序。

2. *Web* 服务器中利用反射调用了 *Sevlet* 的服务方法。

3. *Eclispe* 等开发工具利用反射动态刨析对象的类型与结构，动态提示对象的属性和方法。

4. 很多框架都用到反射机制，注入属性，调用方法，如 *Spring* 中用 *IoC* 来注入和组装 *bean*、自定义的注解。

# 幂等性

## 定义

​	一次和多次请求某一个资源对于资源本身应该具有同样的结果（网络超时等问题除外）。

​	用户在 *APP*上连续点击了多次提交订单，后台应该只产生一个订单；

​	向支付宝发起支付请求，由于网络问题或系统 *BUG* 重发，支付宝应该只扣一次钱。

##  保证幂等性策略

​	幂等需要通过唯一的业务单号来保证。也就是说相同的业务单号，认为是同一笔业务。使用这个唯一的业务单号来确保，后面多次的相同的业务单号的处理逻辑和执行效果是一致的。

​	下面以支付为例，在不考虑并发的情况下，实现幂等很简单：①先查询一下订单是否已经支付过，②如果已经支付过，则返回支付成功；如果没有支付，进行支付流程，修改订单状态为‘已支付’。

## 防重复提交策略

​	上述的保证幂等方案是分成两步的，第②步依赖第①步的查询结果，无法保证原子性的。在高并发下就会出现下面的情况：第二次请求在第一次请求第②步订单状态还没有修改为‘已支付状态’的情况下到来。既然得出了这个结论，余下的问题也就变得简单：把查询和变更状态操作加锁，将并行操作改为串行操作。

1. 乐观锁

   如果只是更新已有的数据，没有必要对业务进行加锁，设计表结构时使用乐观锁，一般通过*version*来做乐观锁，这样既能保证执行效率，又能保证幂等。例如：

   ```sql
   update table1 set col1 = 1,version = version + 1 where version=#{version}
   ```

   不过，乐观锁存在失效的情况，就是常说的ABA问题，不过如果version版本一直是自增的就不会出现ABA的情况。（从网上找了一张图片很能说明乐观锁，引用过来，出自Mybatis对乐观锁的支持）

2. 防重表

   使用订单号作为去重表的唯一索引，每次请求都根据订单号向去重表中插入一条数据。当前请求首先查询订单支付状态，没有支付则进行支付操作，执行完后更新订单状态，最后删除去重表中的数据。请求过程中如果有其它订单则会因为表中唯一索引而插入失败，则返回操作失败，直到当前请求完成。可以看出防重表作用是加锁的功能。

3. 分布式锁

   防重表可以使用分布式锁代替，比如 *Redis*。订单发起支付请求，支付系统会去 *Redis* 缓存中查询是否存在该订单号的 *key*，如果不存在，则向 *Redis* 增加 *key* 为订单号。查询订单支付已经支付，如果没有则进行支付，支付完成后删除该订单号的 *key*。通过 *Redis* 做到了分布式锁，只有这次订单支付请求完成，下次请求才能进来。相比去重表，将放并发做到了缓存中，较为高效。思路相同，同一时间只能完成一次支付请求。

# *ThreadLocal*

   *ThreadLocal* 为每个线程创建一个单独的变量副本，该变量只有这个线程能访问到，其他线程无法访问。

​    *ThreadLocal* 有个静态类 *ThreadLocalMap*，*key* 为当前 *ThreadLocal*，*value* 是对应的变量副本。

​	***ThreadLocal*不能解决并发问题，**因为根本不存在多线程竞争。

# *Unsafe*

​    *Unsafe*类拥有直接操作变量空间的能力

```java
public native long objectFieldOffset(Field f);
```

​	通过此方法，可以获取对象中某个属性的内存偏移地址，然后可根据偏移地址直接对属性进行修改。 

# *JUC*包

## *AtomicInteger*

   *IncrementAndGet* 调用了 *unsafe* 类的 *getAndAddInt* 函数，*unsafe* 类拥有直接操作内存空间的能力，*AtomicInteger* 初始化的时候会调用 *unsafe* 类，得到 *value* 的偏移地址，*getAndAddInt* 会根据偏移地址找到变量，然后对其进行操作。*getAndAddInt* 函数使用的是*CAS*操作。

 # 泛型

- < ? *super* *E*> 用于灵活写入或比较，使得对象可以写入父类型的容器，使得父类型的比较方法可以应用于子类对象。

- < ? *extends E*> 用于灵活读取，使得方法可以读取 *E* 或 *E* 的任意子类型的容器对象。

  因此使用通配符的基本原则：

  + 如果参数化类型表示一个 *T* 的生产者，使用 < ? *extends T*>;

  - 如果它表示一个 *T* 的消费者，就使用 < ? *super T*>；
  - 如果既是生产又是消费，那使用通配符就没什么意义了，因为你需要的是精确的参数类型。

  总结：

  - *T* 的生产者的意思就是结果会返回 *T*，这就要求返回一个具体的类型，必须有上限才够具体；
  - *T* 的消费者的意思是要操作 *T*，这就要求操作的容器要够大，所以容器需要是 *T* 的父类，即 *super T*；

## 类型擦除

​	只会在编译时强化类型，运行时对类型进行擦除，用 *Object* 代替，具体方法里进行强制转换。如要满足多态性，则会自动生成桥接方法

## 作用与目的

1. 类型安全

   编译时期就可以检查出由 *Java* 类型导致的错误，提高  *Java* 程序的类型安全

2. 消除强制类型转换

# 布隆过滤器

1. 首先需要 *k* 个 *hash* 函数，每个函数可以把 *key* 散列成为1个整数
2. 初始化时，需要一个长度为 *n* 比特的数组，每个比特位初始化为0
3. 某个 *key* 加入集合时，用 *k* 个 *hash* 函数计算出 *k* 个散列值，并把数组中对应的比特位置为1
4. 判断某个 *key* 是否在集合时，用 *k* 个 *hash* 函数计算出 *k* 个散列值，并查询数组中对应的比特位，如果所有的比特位都是1，认为在集合中。

优点：不需要存储 *key*，节省空间

缺点：

1. 存在误判情况
2. 无法删除*key*

## 应用场景

1. 垃圾邮件、非法网站检测
2. 解决 *redis* 缓存穿透问题
