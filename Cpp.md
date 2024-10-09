

[TOC]

# Cpp

算法基础部分参考：https://www.hello-algo.com/chapter_hello_algo/



## 初始化

C++中的对应的变量最好还是给一个初始值最好 (无论其是一些内置的数据类型 int/double/float等，还是class或者struct等这些人为定义的部分)

- 对于内置类型 
  - 在定义成全局变量 其值会进行默认初始化
  - 在函数体中定义 其值不会进行初始化

```cpp
    int res; 		// 全局变量按照0初始化
	if(...)
    {
        int res;	// 局部变量, 不进行初始化(随机值)
    }
```





## 栈与队列

- 栈一般不认为是一种基础的数据结构，**栈是以底层容器完成其所有的工作，对外提供统一的接口，底层容器是可插拔的（也就是说我们可以控制使用哪种容器来实现栈的功能）。**stack栈可以由双向队列deque或者vector来实现。

- 队列同样如此，其也是一种容器适配器，一般STL中实现queue队列使用的容器类型也是deque双端队列，当然也可以在创建queue的时候人为指定构造类型。
    - 对于双端队列deque，其是一个C++ STL中的容器类型
    
        

**还需要注意: 在栈与队列中相邻数据的存储位置不一定是连续的，这是由于deque的实现方式，其对应的内存就不是连续分布的，所以按照deque实现的栈以及队列，其中存放的数据对应的内存就不是连续分布的**

- 所以对于栈与队列，其都不会提供遍历的方法




容器

- 序列式容器（Sequence containers），此为可序群集，其中每个元素均有固定位置—取决于插入时机和地点，和元素值无关。如果你以追加方式对一个群集插入六个元素，它们的排列次序将和插入次序一致。STL提供了三个序列式容器：向量（vector）、双端队列（deque）、列表（list），此外你也可以把 string 和 array 当做一种序列式容器。
- 关联式容器（Associative containers），此为已序群集，元素位置取决于特定的排序准则以及元素值，和插入次序无关。如果你将六个元素置入这样的群集中，它们的位置取决于元素值，和插入次序无关。STL提供了四个关联式容器：集合（set）、多重集合（multiset）、映射（map）和多重映射（multimap）。



### 单调队列





## 二叉树

- 满二叉树(也可以称之为完美二叉树) | 完全二叉树 ： 满就是所有左右子树都有（除了底层，并且底层也是满的），完全二叉树除了底层都是满的，底层从左到右是来连续的。

![image-20240916210506436](figure/image-20240916210506436.png)

- 二叉搜索树 | 平衡二叉搜索树
    - 二叉搜索树：二叉树中的左子树的值小于中间节点，右子树的值大于中间节点。但是对于数据；排列非常不平衡的情况下，即虽然是二叉树但是数据过于特殊，将这个二叉树退化成了一个链表形状 —— 其对应的搜索效率就从 O(log n)退化成 O(n)
    - 平衡二叉搜索树：解决上述二叉搜索树中的左右子树差距过大的情况而产生的数据结构，可以保证搜索数据基本可以维持在O(log n) —— **C++中的set、map、multi-set、multi-map都是类似的方式 **

- 高度 | 深度 (但是在leetcode中节点的深度与高度都是从1开始的)
    - 节点的深度（depth）：从根节点到该节点所经过的边的数量。
    - 节点的高度（height）：从距离该节点最远的叶节点到该节点所经过的边的数量

- 度
  - 节点的度（degree）：节点的子节点的数量。在二叉树中，度的取值范围是 0、1、2 



### 存储方式

- 线性存储 | 顺序存储 ： 使用数组储存表中元素
- 链性存储 : 用指针方式实现

```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};
```



### 遍历方式

这里的遍历方式与图论中的遍历方式是类似的，深度优先就是按照一个方向遍历完再去遍历其他方向，对于广度优先搜索就是一层一层搜索。**对于二叉树中的左中右三种遍历方式，其都属于是深度优先搜索方式, 并且这里说的前中后的意思都是针对中结点而言的**

- 深度优先搜索：左中右说的是 左子树 + 中间节点 + 右子树。可以使用**迭代**以及**递归**方式实现，不过主要使用的方式还是递归方式 —— leetcode上面也有题目是基于递归方法实现
- 广度优先搜索：主要使用迭代方式实现



**递归方法相比于迭代方法的好处在于：通过函数不断返回值可以获取到二叉树中上一层的数据，迭代方法需要自行保存这个数据(比如使用stack或者queue来快速的实现迭代遍历)**



### 使用

一般使用二叉树的时候需要先确定返回值，确定输入参数以及函数中执行的操作

**递归法**

- 二叉树在实现递归的时候更像是一种分而治之的思路，将一个二叉树中的数据不断地进行分解，一直分解到左/右子树中一个不能再继续分解到部分开始实现计算，然后不断地在进行返回处理(返回处理的顺序就看使用的是前序/中序/后序哪一种了)。

**二叉搜索树**

- 二叉搜索树中不会有相同元素
- 二叉搜索树按照中序遍历的方式，将遍历到的节点都直接表示在数组中，获取到的数组是一个升序排列的数组。

**平衡二叉搜索树**

- 这个部分属于是比较困难点 —— 在其上面无论是插入节点还是删除节点都是比较复杂的部分

**刷题**

- 代码随想录中给出来的方法一般都是在递归开始的时候确定这个节点是不是为nullptr(应对node为叶子节点的左右子树) | 这种表示方法在一定程度上是比在进入递归的时候先去判断node->left或者node->right是不是为空要更方便一些(至少在代码上是简化的)





****

## 多线程与mutex

std::atomic 原子类型 适用于多线程的情况，可以不使用显式的互斥🔓。

- 其实就是定义的一个变量，多线程的时候，每一个线程都能去使用这个变量值，读数据或者更改数据的时候还不用加锁 | 或者是主线程中更改这个原子值，让其他子线程停止while循环，退出。

PS: 使用的时候需要注意链接库文件Threads::Threads以及find_package(Threads REQUIRED)

```cpp
#include <iostream>
#include <thread>
#include <atomic>
#include <vector>

std::atomic<int> counter(0);

void increment_counter(int num_iterations) {
    for (int i = 0; i < num_iterations; ++i) {
        ++counter; // 原子操作，线程安全
    }
}

int main() {
    const int num_threads = 10;
    const int num_iterations = 1000;

    std::vector<std::thread> threads;
    for (int i = 0; i < num_threads; ++i) {
        threads.emplace_back(increment_counter, num_iterations);
    }

    for (auto& thread : threads) {
        thread.join();
    }

    std::cout << "Final counter value: " << counter << std::endl;

    return 0;
}
```



#### mutex

​	多线程还需要注意的一个部分就是上锁 - 即对于需要重复读取的数据需要上锁进行限制(防止出现多个线程同时修改变量)。

​	我发现有时候锁是定为全局变量,有时候定义为类的成员变量。 **但是不论怎么定义,对于同一个数据,其对应的锁就只能是同一个(即对应的mutex的变量是同一个)**。 对于类中的成员函数，其中可能使用多线程来并行加速计算，这样就会使用定义在类中的锁进行控制。



#### condition variable

​	条件变量就是多线程中进行控制线程执行的变量，通过notify_one()以及notify_all()来让当前正在被wait()的函数继续执行。在wait()状态中的线程会释放其占用的锁

- cv为条件变量，所有的wait() 以及 notify_one() notify_all() 都是针对这个条件变量操作的

- cv.wait(lck, []{ return ready; }); 相当于是一种双重验证 即cv被通知之后，线程被唤醒之后就会取判断ready变量的值，只有这里返回true之后才会正常让线程工作，否则还是休眠状态

```cpp
#include <iostream>
#include <thread>
#include <mutex>
#include <condition_variable>

std::mutex mtx;
std::condition_variable cv;
bool ready = false;

void print_id(int id) {
    std::unique_lock<std::mutex> lck(mtx);
    // 当ready为false时，wait将阻塞当前线程并释放锁，直到ready变为true
    cv.wait(lck, []{ return ready; });
    std::cout << "Thread " << id << '\n';
}

void go() {
    std::unique_lock<std::mutex> lck(mtx);
    ready = true;
    cv.notify_all();  // 通知所有等待的线程
}

int main() {
    std::thread threads[10];
    for (int i = 0; i < 10; ++i)
        threads[i] = std::thread(print_id, i);

    std::cout << "10 threads ready to race...\n";
    std::this_thread::sleep_for(std::chrono::seconds(1));
    go();  // 开始通知

    for (auto& th : threads) th.join();
    return 0;
}
```









PS: 关于ros中的使用

​	**现在有一个疑惑，如果不使用多线程的时候，ros中在处理数据的时候遇到了数据的写入怎么办 | 或者是ros中多个回调函数都会对同一个数据执行写入操作怎么办**

​	在ros中调用回调函数的部分是使用ros::spinOnce()或者ros::spin()进行处理的，即只有执行到这里才会去执行回调函数，其他时间都可以在进行数据的处理，所以不会出现同时读取与同时写入数据的烦恼。ros自己肯定会回调函数的执行顺序进行整理，不会出现同时写入写出的情况。

- 如果使用ros::spin()的就是一直在这里使用回调函数处理数据，调用其他线程来继续处理数据





多个线程访问同一块shared_ptr的时候,需要上锁. 并且现在在使用智能指针的时候, 如果这个指针本身会作为一个实参传递给另一个函数的形参.这种操作本身就会延长这个智能指针的生命周期(相当于如果在执行函数的时候又部分指向了这个智能指针指向的内存,即会导致引用计数不会1, 不会删除这个内存部分)

https://blog.csdn.net/weixin_43850474/article/details/106328641

关于互斥锁的颗粒度的问题 - 以及互斥锁是如何保护线程资源的 —— 总感觉我现在设置的锁完全是不应该上锁的时候上锁，该上锁的部分又没有设置上锁



感觉这种lock的方式并不能很好的保护资源,至少在颗粒度上是不够细的



关于锁对应的是一种机制，即当前持有这个锁的线程在操作的时候，其余线程是不能修改这个锁保护的资源的







### 线程池

为一些任务提前创建好线程，避免使用的时候重复创建以及销毁线程。一个线程池里面包含着提前创建好的线程，必然是为了执行多种任务来创建的(为了一种任务创建多线程显然不是那么划算)，所以线程池的封装比较好(一个比较简单的多线程的操作 : https://github.com/progschj/ThreadPool )







### 异步操作

异步操作是一种目的，C++中的多线程就是一种异步操作，还有异步操作的方法就是使用std::future<>来实现。

https://yilingui.xyz/wiki/c++/cxx11_multi_thread_and_async.html



- future(即unique future)对象就相当于是一种异步操作，其会与async以及pormise一起使用，async就是会直接启动一个函数，返回一个future对象，使用这个future对象就可以将这个函数的返回值在另一个线程直接读取到，不需要使用全局变量。promise的作用也是类似的(因为async会隐式创建一个future对象)，promise主要用于设置future的返回值，future对象本身可以使用wait或者get, 让其他线程(只能是一个)等待这个线程的执行结果

- shared_future就是future对象的升级版，可以让多个线程获取或者等待这个线程的执行结果。



因为在使用promise对象的时候，如果这个promise的生命周期结束了，future对象还调用get函数来获取函数返回值就会出错。geth( )或者wait( )都会堵塞其他线程(future堵塞一个, shared_future堵塞多个线程的执行)

https://www.limerence2017.com/2023/09/17/concpp07/



opencv自己也有一个tbb进行多线程的计算

我感觉这里对应的tbb库中lambda表达式对应形参应该就是前面指定的范围 即对应每一个RGB_Voxel

```cpp
tbb::parallel_for_each( voxels_recent_visited->begin(), voxels_recent_visited->end(), [ & ]( const std::shared_ptr< RGB_Voxel > &voxel )
```



****

## 全局变量 | main | 其他源文件

全局变量的初始化是要在main函数执行之前处理的 (无论是什么cpp文件中的全局变量，分为静态初始化与动态初始化)

- 静态初始化： 用常量来对全局变量进行初始化
- 动态初始化： 对于需要使用构造函数的类等变量进行初始化

初始化一般在main函数执行之前进行。并且全局变量说的是在所有cpp文件中的全局变量，并且不同文件中的全局变量的初始化执行顺序是不一定的。想使用其他文件中定义的全局变量只要extern就行。但是要注意如果全局变量要是有依赖关系的话，需要写在同一个cpp文件中，这样可以保证初始化的顺序(同一个文件中的全局变量执行起来是按照写的顺序执行的)





**.h文件中extern变量进行导入 | 在cpp中对变量进行定义**

- 但是如果变量定义在.h文件中，如果其他部分都include这个.h文件，这样就会导致变量的重复定义。所以一般定义在一个cpp文件中，这样就不会导致重复定义。在这个.h中对应的函数中按顺序使用这些变量即可。

- .hpp在使用上与.h文件没有大的区别



- main函数

![image-20240615151700037](figure/image-20240615151700037.png)

- .h函数 中extern获取这些变量定义，在自己的成员函数中使用。 相当于在extern中只会实现

<img src="figure/image-20240615152244362.png" alt="image-20240615152244362" style="zoom: 80%;" />











**inline** 

其可以用于修饰一个函数，这样可以让这个函数定义放在头文件中，但不用担心其违背一次定义原则 | 编译器会自动将多次的定义转换成一次









****





## string

- 注意在C++中对于一个string数据进行遍历，遍历得到的数据实际上是char类型的数据
- 字符串转换成int类型的数据可以使用 stoi以及stoll，分别是转换成为int或者long long int类型的数据

**回文字符串**: 对应的是正读反读都是一样的字符串





## 智能指针

全局变量的生命周期是 从程序开始到程序结束(但是其他文件想使用这个变量需要extern)



在C++中，全局变量既不是保存在堆上，也不是保存在栈上。全局变量是存储在静态存储区（有时也称为数据段或BSS段）中的。具体来说，C++中的变量可以根据它们的存储持续时间（storage duration）分类为以下几种类型：

- 自动存储持续时间（Automatic Storage Duration）：通常是局部变量，保存在栈上。
- 静态存储持续时间（Static Storage Duration）：包括全局变量、静态局部变量以及在文件范围内声明的静态变量。它们在程序的生命周期内存在，保存在静态存储区。
- 动态存储持续时间（Dynamic Storage Duration）：由程序员在堆上动态分配，使用new或malloc等函数。



智能指针在生命周期结束的时候会自动释放自身占据的空间(shared_ptr最后一个结束的时候释放) | 一般要是在函数中使用的话，函数结束之后就会停止使用。



在使用共享指针的时候，会使用make_shared<>这个函数, 其对应的效果就是创建一个对象，后面括号里面的就是新对象需要的一些基本信息。**要注意的是一块内存，如果在创建对象的时候使用的智能指针来创建的，那么就不要使用new/delete 这种操作再操作这块内存了** https://www.cnblogs.com/secondtonone1/p/15818945.html

```cpp
m_render_thread = std::make_shared< std::shared_future< void > >( m_thread_pool_ptr->commit_task(render_pts_in_voxels_mp, img_pose, &m_map_rgb_pts.m_voxels_recent_visited, img_pose->m_timestamp ) );
```



unqiue_ptr与互斥锁一起使用: 可以防止使用者忘记开锁，这个函数执行完之后，unique_str指针自己进行析构的时候就可以结束锁 ( 注意其他部分想使用这部分的数据的时候也需要使用同一个mutex_image_callback变量来设置锁 )。

```cpp
std::mutex mutex_image_callback;
void R3LIVE::image_callback( const sensor_msgs::ImageConstPtr &msg )
{
    std::unique_lock< std::mutex > lock( mutex_image_callback );
    if ( sub_image_typed == 2 )
    {
        return; // Avoid subscribe the same image twice.
    }
    sub_image_typed = 1;

    if ( g_flag_if_first_rec_img )
    {
        g_flag_if_first_rec_img = 0;
        m_thread_pool_ptr->commit_task( &R3LIVE::service_process_img_buffer, this );
    }

    cv::Mat temp_img = cv_bridge::toCvCopy( msg, sensor_msgs::image_encodings::BGR8 )->image.clone();
    process_image( temp_img, msg->header.stamp.toSec() );
}
```



## Eigen

Eigen本身就是一个C++的矩阵库，但eigen库中竟然是按列存储的方式。

C++数据转换到Eigen时(Eigen::Map)，要注意，Eigen默认按列优先存储。列优先因为存储位置相邻会比行优先更快些。注意：当用输入运算符<<时，都是一行一行输入，不管该矩阵是否是指定的行优先还是列优先.





## 补充

### 逆波兰表达式

一般我们使用的(1+2)*(3+4)这种表达式属于是中缀表达式，波兰表达式与逆波兰表达式是去掉括号也不会引起歧义的表达式，尤其是对于逆波兰表达式来说，特别适合栈来处理(碰到数就入栈，碰到运算符就弹出两个数进行计算)。

https://www.zhihu.com/question/41103160

- 逆波兰表达式对于计算机来说是方便的，处理起来巨方便。



## .hpp文件

.hpp文件在cmakelists.txt中只需要手动include文件目录即可，不需要手动使用add_library的形式将.h文件对应的.cpp文件进行打包(不需要)





而且我还看到了在hpp文件里面对类的成员函数进行定义(但是这里在使用上应该与.hpp还是.h文件都没有关系)

1是编译器的唯一命名规则，就是inline函数,class和模板类函数被多次包含的情况下，在编译的时候，编译器会自动把他们认为是同一个函数，不会发生二次定义的问题。前提是他们一模一样。

2是编译器会把class里面定义的函数当做inline函数，所以直接在类里面实现函数的定义没有关系。由上面的说明，他不会发生二次定义的问题。

3一般函数的声明和实现分开，在编译的时候，声明可以无数次，但是定义只能一份，只会生成一份函数的.obj，所以有函数调用的地方，编译器必须在调用的地方先保持现场，然后在花点时间去调用函数，然后回来，恢复现场。所以函数在头文件中实现，如果被包含二次。函数的实现就被编译了2次，如果单独写在一个.cpp中间，自然就编译成为一份.obj，不会产生二义性的问题。

3.inline函数在编译的时候直接复制在有该函数的地方，在空间上有消耗，但是在省去了时间上的消耗，是一个模板函数。也就是说在有这些函数的地方都不需要去调用函数，也就不涉及有2种函数可以调用产生的二义性问题。























## hash

**C++中封装好的hash**

- C++中的unorder_map就是基于hash表实现的，map是基于红黑树实现的(也就是二叉搜索树)

1. 对于unorder_map

```cpp
    map<string, int> dict;
    // 插入数据的三种方式
    dict.insert(pair<string,int>("apple",2));
    dict.insert(map<string, int>::value_type("orange",3));
    dict["banana"] = 6;
```



2. 对于map初始化 (由于其底层使用的是红黑树，其实际对应的数据保存的时候可以实现有顺序的保存)

```cpp
	unordered_map<string, int>  dict; // 声明unordered_map对象
	// 插入数据的三种方式
	dict.insert(pair<string,int>("apple",2));
	dict.insert(unordered_map<string, int>::value_type("orange",3));
	dict["banana"] = 6;
```





hash表使用的一个数据结构就是unorder_map，给定一个键值对，找到对应位置存放的数据。如果键值要使用自定义的数据类型的时候，就需要手动实现 

- 数据类型的定义
- hash函数的定义



```CPP
class VOXEL_LOC
{
  public:
    int64_t x, y, z;
    VOXEL_LOC( int64_t vx = 0, int64_t vy = 0, int64_t vz = 0 ) : x( vx ), y( vy ), z( vz ) {}
    bool operator==( const VOXEL_LOC &other ) const { return ( x == other.x && y == other.y && z == other.z ); }
};

// Hash value
namespace std
{
template <>
struct hash< VOXEL_LOC >
{
    int64_t operator()( const VOXEL_LOC &s ) const
    {
        using std::hash;
        using std::size_t;
        return ( ( ( ( s.z ) * HASH_P ) % MAX_N + ( s.y ) ) * HASH_P ) % MAX_N + ( s.x );
    }
};
} // namespace std
```

但是我现在觉得这种使用起来比较神奇 : Hash_map_3d在这里是一个模板类，int以及Triangle_ptr都是指定模板类型，下面的int完全不是hash表里面的key值, 真正的hash表是这个类自己的成员变量

Hash_map_3d< int, Triangle_ptr >        m_triangle_hash;











多线程里面如果每个线程里面都会使用ros中的回调函数接受数据，其对应的多个线程应该都要使用ros::spin()或者ros::spinOnce()来处理数据

























关于ros句柄

- ros::NodeHandle 是访问 ROS 参数服务器的接口。
- 任何有效的 ros::NodeHandle 实例都可以读取和设置参数(这里可能就是说一个整个程序中可能有多个ros句柄或者ros节点——虽然可以设置多个，但是正常的设置一般都是只使用ros进行数据读取以及话题发布，其余部分都是最好不使用ros)。
- 参数的命名空间和节点的命名空间会影响参数的解析。







指针常量以及常量指针

const T* ptr = &a 这种是常量指针 指向的对象a的数值不可以被修改ia

T* const ptr = &a 指针常量 a的数值是可以修改的, 但是指向的对象不能被修改









## lambda表达式

lambda表达式又可以称为lambda函数，获取作用域中变量的匿名函数。

lambda表达化自动获取函数中的变量，或者是自动定义形参，然后在函数体中完成计算返回 

```cpp
[函数对象参数] (操作符重载函数参数) mutable 或 exception 声明 -> 返回值类型 {函数体}
```

- 获取函数对象参数有两种方法 1. 值传递(相当于重新copy一份，类似于C++函数中常规定义的形参与实参之间的关系) 2. 引用传递( 就是引用 )



https://www.cnblogs.com/jimodetiantang/p/9016826.html

https://blog.csdn.net/gongjianbo1992/article/details/105128849







## explicit | static

其在class中使用会出现之前没有意料到的情况

- explicit
- static 





## 其他

1. 匿名命名空间

​	匿名空间主要是为了防止在多个cpp文件中设置全局变量之后，不同cpp中的全局变量可能命名相同，导致命名冲突。匿名空间相当于是在整个cpp中定义了一个只属于这个cpp中的变量/函数/结构体等等。这种方法比直接使用static的效果要更好一些。



2. boost库

    boost库的主要作用就是为C++标准库开发更多的功能，也有很多在boost的内容不断地被选到c++标准库中，所以只需要查看一些boost的内容添加到c++标准库中即可。

    https://blog.csdn.net/qq_41868108/article/details/105778022









