

[TOC]

# Cpp

算法基础部分参考：https://www.hello-algo.com/chapter_hello_algo/



## 刷题注意！！

刷题刷完leetcode之后需要刷一些ACM题，这些leetcode问题跟实际面试问题的输入输出不一样！！！ 笔试的时候需要考虑这些问题的输入与输出（比如这些问题都需要自己手动切割出来自己需要的数据，比如用getline什么的或者考虑输入数据有空格什么的）





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





## 向量与链表

### vector向量

初始化的一个小区别

vector<vector<int>> res  = {};     这个二维数组是一个空数组

vector<vector<int>> res = {{}};   这个二维数组的size = 1，但是其第一个元素却是一个空的一维数组

vector<vector<int>> arr(3, vector<int>(4, 0));  二维数组的初始化方案



**关于vector清空当前元素快速方法**

生成一个与当前vector大小相同的vector，然后交换两个向量之间的元素(使用swap) | 因为vector中内存在整个数据没有被释放的时候是不会减小自己的内存的，也就是clear只能清理掉当前的元素，即改变size()取值，但是对于整个vector的capcity，其是无法清除的





**reserve | resize | assign**

- reserve处理的是capcity本身，与当前vector的size没有关系
- resize是调整size的大小，可能会截断或者增加size的大小
- assign是改变当前vector中的元素的值，如果当前元素的数量不够(size大小不够)，其也会改变size的大小应应对需求

```cpp
// resize影响的是一个vector中的size, 于captity无关
now_level.resize(path[0].size());
// assign会vector中的部分分配值，并不需要手动resize大小 | assign如果captity不够会自动分配新的空间
now_level.assign(path[0].size(),0);
```



void resize(size_type n, value_type val = value_type());

- 如果n<当前容器的size，则将元素减少到前n个，移除多余的元素(并销毁）
- 如果n>当前容器的size，则在容器中追加元素，如果val指定了，则追加的元素为val的拷贝，否则，默认初始化
- 如果n>当前容器容量，内存会自动重新分配


void reserve(size_type n)

- 如果n>容器的当前capacity，该函数会使得容器重新分配内存使capacity达到n

- 任何其他情况，该函数调用不会导致内存重新分配，并且容器的capacity不会改变

- 该函数不会影响向量的size而且不会改变任何元素

  

**insert**

- insert部分在使用的时候为指定插入位置后直接插入元素,但是其可能导致后续元素都往后移动一位, 即O(n)操作, 以及vector中的capcity需要扩容（这种操作比较消耗时间，谨慎使用）



### List 列表

list的优势主要在于插入以及删除元素比较方法，但是想找一个元素的时候，需要使用按值索引的for循环或者是迭代器进行搜索。**一定要注意其在删除元素或者插入元素的时候，迭代器是否还能正常工作** | 一定注意List并不是没有办法进行迭代！！！

- 删除元素

```cpp
list<int> lst = {1, 2, 3, 4, 5};
// 正确做法：使用erase的返回值
for (auto it = lst.begin(); it != lst.end();) {
    if (*it == 3) {
        it = lst.erase(it);  // erase返回下一个有效位置的迭代器
    } else {
        ++it;
    }
}
```

- 插入元素

```CPP
list<int> lst = {1, 2, 3, 4, 5};
// 正确做法：插入时保存下一个位置
for (auto it = lst.begin(); it != lst.end();) {
    if (*it == 3) {
        it = lst.insert(it, 10);  // 在3前面插入10
        ++it;  // 移动到3的位置
        ++it;  // 移动到下一个位置
    } else {
        ++it;
    }
}
```

- 同时插入以及删除

```cpp
list<int> lst = {1, 2, 3, 4, 5};
for (auto it = lst.begin(); it != lst.end();) {
    if (*it == 3) {
        // 删除3，并在该位置插入6和7
        it = lst.erase(it);  // 删除3，返回下一个位置
        it = lst.insert(it, 7);  // 插入7
        it = lst.insert(it, 6);  // 插入6
        advance(it, 2);  // 移动迭代器跳过新插入的元素
    } else {
        ++it;
    }
}
```





## 栈与队列

- 栈一般不认为是一种基础的数据结构，**栈是以底层容器完成其所有的工作，对外提供统一的接口，底层容器是可插拔的（也就是说我们可以控制使用哪种容器来实现栈的功能）。**stack栈可以由双向队列deque或者vector来实现。

- 队列同样如此，其也是一种容器适配器，一般STL中实现queue队列使用的容器类型也是deque双端队列，当然也可以在创建queue的时候人为指定构造类型。
    - 对于双端队列deque，其是一个C++ STL中的容器类型。既然是双端队列，其对应的元素就应该是
    
        

**还需要注意: 在栈与队列中相邻数据的存储位置不一定是连续的，这是由于deque的实现方式，其对应的内存就不是连续分布的，所以按照deque实现的栈以及队列，其中存放的数据对应的内存就不是连续分布的**

- 所以对于栈与队列，其都不会提供遍历的方法




容器

- 序列式容器（Sequence containers），此为可序群集，其中每个元素均有固定位置—取决于插入时机和地点，和元素值无关。如果你以追加方式对一个群集插入六个元素，它们的排列次序将和插入次序一致。STL提供了三个序列式容器：向量（vector）、双端队列（deque）、列表（list），此外你也可以把 string 和 array 当做一种序列式容器。
- 关联式容器（Associative containers），此为已序群集，元素位置取决于特定的排序准则以及元素值，和插入次序无关。如果你将六个元素置入这样的群集中，它们的位置取决于元素值，和插入次序无关。STL提供了四个关联式容器：集合（set）、多重集合（multiset）、映射（map）和多重映射（multimap）。



### 单调队列

- 单调队列解决就是按照方便去一个区间的最大/小值的问题，
  - 对于n个元素的数组，如果求解连续k个数中的最大值，所使用的时间复杂度为O(n*k)，即n个元素基本上每一个元素都得与其后k-1个元素进行比较（近似计算）







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







## 回溯

- 回溯就是使用递归方法，用于解决组合、切割、排列、棋盘等问题。回溯问题本质上就是一种暴力地穷举方法，其对应地就是不断地更换取值，直接获取符合题目条件的方法。
- 回溯算法可以抽象成为树形结构进行分析（n叉树）
- 既然回溯是一种递归操作，所以记得一些操作需要进行退回操作

```cpp
/* 函数模板(也存在有返回值的回溯函数, 需要看整个算法是需要将所有的情况全部分析一遍, 还是找到其中一种情况就可以返回)
	对于找到一种情况就可以返回的情况，其只需要将函数提前返回，不断退回直接退出整个递归
*/
void backtracking(参数) {
    if (终止条件) {
        存放结果;
        return;
    }

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销处理结果
    }
}
```



**后续部分分别讨论针对不同问题的套路**

### 组合

- 组合由于不能重复，需要给定一个start_index参数，让下一次递归不能使用前面使用过的参数。并且对于一些不太方便的选取问题，可以使用排序的方法简化流程 | **使用start_index主要是让后续选择数只能往大不能取小 (排列中前后不一致是新情况，但是组合不是，相当于要少了一半的取值可能)**
- 对于去重问题的结果：如果设计到重复的可能性，基本上有两种去重方式 
  - sort排序 —— 这样如果当前元素于前一个元素相同，就可以跳过，实现同一层元素的去重
  - 不能改变元素顺序，可以设计unordered_set，确定当前这一层中已经使用了那些元素，unordered_set中能find到这个变量，则跳过 

```cpp
/**给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。
示例: 输入: n = 4, k = 2 输出: [ [2,4], [3,4], [2,3], [1,2], [1,3], [1,4], ] **/

// 使用回溯处理(回溯等效成为二叉树,输入参数中加入path,记录当前路径) | 处理组合逻辑并不能在时间复杂度上获取到提升，只是在使用逻辑上获取到优化(只可能在一些处理中加入剪枝操作)
vector<vector<int>> res = {};
vector<int> path = {};
void backtracking(int n, int k, int start_index)
{
    // 判断终止条件
    if(path.size() == k)
    {
        res.push_back(path);
        return;
    }
    // 从start_index开始，遍历到n | 增加剪枝操作 —— 这样整个算法的时间复杂度为 k* (C_n)^k 与需要遍历二叉树的深度有关(k也是整个问题中定义的k值)
    for(int i = start_index; i <= n; ++i)
    {
        // 判断 当前剩余元素 + 当前元素量(也就是1) + 已元素量(path.size()) 要大于等于k, 才说明后面还必要继续去遍历元素
        if( (n - i + path.size() + 1) >= k )
        {
            path.push_back(i);
            backtracking(n,k,i+1);
            path.pop_back();    // 增加这里部分的主要作用就是满足下一次循环开始的时候, path去除上一次的影响
        }
    }
}
```



### 分割

对于分割问题，经常遇到的就是分割字符串，则需要每一层中从start_index处不断改变切割字符串的长度，实现对所有切割方法的遍历

```cpp
    /*** 这里复杂的部分主要有:
     *   1. 遍历切割方案(使用回溯) —— 这里的切割方法相当于对每次输入进来的字符串都判断到底切割几个字符(从少到多所有的切割方案都确定一下)
     *   2. 关于string的使用 —— 如何使用string中的substr方法
     *   3. 判断回溯
     * ***/
    vector<vector<string>> res = {};
    vector<string> path = {};
//    string temp = {};           // 保存临时结果

    bool isValid(string& s)
    {
        if(s.empty()) return false;
        int left = 0;
        int right = s.size()-1;
        while(left <= right)
        {
            if(s[left] != s[right])
                return false;
            left++;
            right--;
        }
        // 说明遍历完成, 都是正常
        if(left > right) return true;
        return false;
    }

    /** 我一开始的思路是将整个字符串无脑切割了,然后从切割结果中判断其是不是回文串然后返回 —— 但这个实际使用应该要处理每一套切割方案中的所有字符串, 要求其必要全部都是子字符串 **/
    // substr() 两个参数 开始位置以及截止长度
    void backtracking(string& s, int start_index)
    {
        // 全部字符串都已经判断完成了, 这里才会将结果放置进去
        if(start_index >= s.size())
        {
            res.push_back(path);
            return;
        }

        for(int i = 0; (start_index+i) < s.size() ;i++)
        {
            // start_index为本次字符串开始的位置 | i+1表示开始copy从start_index位置的字符(包含start_index)
            auto b = s.substr(start_index,i+1);
            if(isValid(b))
            {
                path.push_back(b);
                backtracking(s, start_index+i+1);
                path.pop_back();
            }
        }
    }
```





### 子集

子集问题与组合问题很类似，组合问题可能需要遍历到n叉树的叶子节点上，但是对于子集问题需要在每一层上都进行结果输出

- 对于集合中有相同元素处理手段与组合问题相同 （1）unordered_set （2）sort排序

```cpp
    vector<vector<int>> res = {};
    vector<int> path ={};
    /*** 1.个人方法(与随想录中的方法类似), 之前忽略的一点是 —— 由于这里不能将原始数组进行排序，这就导致了之前的去重方法不能再次使用，现在使用的去重方法只能对每一层设置一个数组或者哈希表来处理
     *  PS: {1,2,3,4,5,6,7,8,9,10,1,1,1,1,1} 之前就是再这个数组上出了错
     *
     *
     * ***/
    void backtracking(vector<int>& nums, int start_index)
    {
        unordered_set<int> used = {};
        for(int i = start_index; i<nums.size() ;++i)
        {
            // 这里没有想到可以写的这么简洁，只需要path中的前一个永远小于新来的数就可以了 | 而且只需要这个nums[i]数据没有进入path, 那么路径中其余与nums[i]相同的元素也不需要放进来
            if((path.size() != 0 && path.back() > nums[i]) || used.find(nums[i]) != used.end()) continue;
            else
            {
                used.insert(nums[i]);
                path.push_back(nums[i]);
                if(path.size() >= 2) res.push_back(path);
                backtracking(nums, i+1);
                path.pop_back();
            }
        }
    }
```



### 排列

排列问题不需要start_index，毕竟start_index只是一种防止往前取值导致结果重复的手段。**这里出现了另一种去重需求，同一层上不能相同，同一列上也不能相同，需要使用两个unordered_set或者一个vector一个unordered_set等等的这种方式来进行去重**

```cpp
// 这里需要同层之间也进行去重 | 在46(nums中没有重复元素的情况中), used数组可以表示整条路线上有什么数据被使用
vector<vector<int>> res = {};
vector<int> used = {};
vector<int> path = {};

/** 这里的解决思路就是设置两个used,用于分析同层的相同性以及同一树枝上的取值 **/
void backtracking(vector<int>& nums)
{
    if(path.size() == nums.size())
    {
        res.push_back(path);
        return;
    }

    unordered_set<int> level_use = {};
    for(int i = 0; i < nums.size(); ++i)
    {
        // used 判断当前位置其是否已经被使用
        if( used[i] == 0 )
        {
            used[i] = 1;
            // 判断同一层中是否有相同元素
            if(level_use.find(nums[i]) == level_use.end())
            {
                path.push_back(nums[i]);
                level_use.insert(nums[i]);
                backtracking(nums);
                path.pop_back();
            }
            used[i] = 0;
        }
    }

}
```



### 棋盘问题

都是先确定一行，再去确定列。

- N皇后就是常规的递归，第一行确定皇后Q的位置，再去找下一行的Q的位置，不断递归。
- 解数独为二维递归，每一行都需要调用递归，处理完一行的取值再去确定下一行的递归。











## 贪心

- 贪心即局部最优，按此逻辑进行整个问题求解，实现一种自定义的全局最优 | 这里具体是不是最优并不需要严格证明，另外求解最优问题的方法即动态规划。
  - **贪心算法相比于二叉树、回溯算法并没有明确的模板进行套用，需要具体条件具体分析**







## 动态规划

- 背包问题 | 打家截舍 | 股票问题 | 子序列问题等等一般都是属于动态规划问题。**动态规划问题中的下一步是需要基于上一步中的状态实现出来的，即需要确定递推公式，DP数组以及DP数组的初始化方法**
    - DP数组debug的时候一般需要将整个DP数组打印出来



**动态规划中的处理模板**

```
1. 确定dp数组（dp table）以及下标的含义
2. 确定递推公式
3. dp数组如何初始化
4. 确定遍历顺序
5. 举例推导dp数组
```



### 背包问题

#### 01背包

背包问题中主要分析的是01背包，完全背包，对于更困难的多重背包问题，leetcode上面还没有类似的问题。背包问题都是需要自行分析题目确实是背包问题才可以继续处理。



#### 完全背包

完全背包就是元素的个数可以是无限的，可以重复选择同一个元素。**主要是修改了遍历背包容量的时候，将倒叙遍历转换成为正序遍历**

```cpp
// n为物体种类 v背包的总容量
for(int i = 0; i < n; ++i)
{
    for(int j = weight[i]; j <= v; ++j)
    {
        dp[j] = max(dp[j], dp[j-weight[i]] + value[i]);
    }
}
```



**遍历顺序**

- 主要是看问题转换成为背包问题之后，是求解一种组合还是一种排列。如果是组合，就可以先遍历物体，再遍历背包。对于排序问题的话，就需要先去背包再去遍历物体



### 买卖股票问题

- 需要分析是否有买卖次数的限制

****



### 子序列问题 

- 个人感觉在分析子序列问题中，递推的感觉只要初始状态正确，递推分析只需要符合逻辑，就可以直接得到结果。比如分析子序列问题中，虽然是提供删除字符串中的可能，但是只需要手动去执行这种推导过程，就可以不去直接分析各种字符串可能出现的情况。





## 图论

图论这部分主要是深度优先以及广度优先两种解决方法



### 并查集问题

浅显来看，并查集就是一种使用简单的结构，可以用于将两个元素放到同一个集合中或者查询两个元素能否在同一个集合中。**更神奇的，无论是对有向图还是无向图结构，都能利用并查集表示元素是否在同一个集合中，join()函数创建边的时候与其并没有关系。并查集本身就是有方向的多层的多叉树结构，转换成为了单层的多叉树结构，故其在描述有向图时并不需要单独操作。**

- 并查集中保存的结构并不是图结构，调用join()之后，其只能保证元素在一个集合中，原始的图结构被破坏了。

- 个人感觉如果两个元素可以在同一个连通图中，就可以认为其在同一个集合中，利用并查集中提供的方法就可以直接查找到。

- 后续对应着整个并查集的基本原理，其中利用find()函数进行递归操作，最后将father[i]对应根节点，根节点的father为自己本身。不在该集合中的节点的father对应自己，所以isSame()函数就直接判断是否是同一个根节点。并且find()函数的返回值还是直接使用C++赋值语句返回左值，即赋值左侧的元素。

```Cpp
int n = 1005; // n根据题目中节点数量而定，一般比节点数量大一点就好
vector<int> father = vector<int> (n, 0); // C++里的一种数组结构

// 并查集初始化
void init() {
    for (int i = 0; i < n; ++i) {
        father[i] = i;
    }
}
// 并查集里寻根的过程
int find(int u) {
    return u == father[u] ? u : father[u] = find(father[u]); // 路径压缩
}

// 判断 u 和 v是否找到同一个根
bool isSame(int u, int v) {
    u = find(u);
    v = find(v);
    return u == v;
}

// 将v->u 这条边加入并查集
void join(int u, int v) {
    u = find(u); // 寻找u的根
    v = find(v); // 寻找v的根
    if (u == v) return ; // 如果发现根相同，则说明在一个集合，不用两个节点相连直接返回
    father[v] = u;
}
```







### 最小生成树

- 最小生成树的方法主要有prim以及Kruskal方法。前者主要是从点的角度，后者是从边的角度。其与最短路径的区别是**最小生成树**要求是整个树的边的权值和最小，而最短路径是要求从start到end的距离最小。

#### prim

- 其主要是维护**每一个点到当前最小生成树的距离**，所以实际计算时只需要比较到当前点的边权值



### 最短路径

- 主要使用的方法为Dijkstra与Bellman_ford，其都是分析单出发点的方法，针对其计算性能/是否出现负权回路/是否对经过的顶点数量有限制，都有改进版本。

#### Dijstra

- 其解决的是非负权值图中的最短路径问题，主要是维护一个**从start开始到其余各个顶点**的距离值数组minDistance[] 

#### Bellman_ford

- 其可以解决非负权值的问题，并且是从边的角度来处理问题
- **"松弛"**的概念主要是分析每一个点从出发点到当前点的距离，不断地取最小值

- 该方法本身就可以解决**负权值**问题，但如果其出现负权值回环的话，可以通过增加松弛次数的方法来判断

- 对于限制经过的顶点次数的问题，可以通过严格限制收敛次数的方法来解决



#### floyd

- 主要用于解决多开始点的分析，其利用动态规划的思想，借助来一个dp [i] [j] [k]的三维数组表示从i到j中间可能经历1...k点后对应的最短路径。



#### Astar

- 使用一个启发函数，计算当前到起始点已经走过的距离以及到终点的预估距离。借助优先级队列优先读取启发函数中得分较低的元素作为下一步的可能出发点。**需要注意并不是从队列中弹出一个元素其对应的路径就+1，而是从一个出发点对应的所有方向上的可能到达的下一个位置都属于是+1的路径位置**



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







## 常用函数

### **sort() 函数**

- 关于sort排序算法的使用 (有时候会针对自己想使用的数据结构，提供一种排序方案) | 自定义比较函数中比较的是a 与 b的位置先后，true(a的位置在b前面)  | false 则为b的位置在a前面

```cpp
// 自定义比较函数
bool compareByAbsoluteValue(int a, int b) {
    // 按绝对值降序排序
    return std::abs(a) > std::abs(b);
}
std::sort(numbers.begin(), numbers.end(), compareByAbsoluteValue);
```

- 如果在类中定义类似的compare函数，需要将这个compare函数定义成static类型，要么就是定义在class之外，否则提示Reference to non-static member function must be called.

```cpp
class Solution 
{
public:
    static bool compare(vector<int>& a, vector<int>& b)
    {
        if(a[0] > b[0]) return true;  // 进行比较(降序排列)
    }

    vector<vector<int>> reconstructQueue(vector<vector<int>>& people)
    {
        sort(people.begin(), people.end(), compare);
    }
};
```



### **find() 函数**

对于vector容器进行元素查找，查到之后返回该元素的迭代器，否者返回end迭代器













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





