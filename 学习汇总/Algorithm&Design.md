### 手写线程安全的单例模式？
```cpp
DCL 双重锁定检查
class Singleton{
private:
    static Singleton* instance;
    static std::mutex mtx;
public:
    Singleton(){
        std::cout << "Newing" << std::endl;
    }
    static Singleton* getInstance(){
        if(instance == nullptr){
            mtx.lock();
            if(instance == nullptr){
                instance = new Singleton();
                mtx.unlock();
                return instance;
            }
            mtx.unlock();
        }
        return instance;
    }
};
Singleton* Singleton::instance = nullptr;
std::mutex Singleton::mtx = std::mutex();
```
### 实现一个vector？是1.5还是2倍，各有什么优缺点
vector简单实现仅供参考
```cpp
template <typename T>
class Vector {
public:
    Vector() : m_data(nullptr), m_size(0), m_capacity(0) {}
    explicit Vector(size_t size) : m_data(new T[size]), m_size(size), m_capacity(size) {}
    Vector(size_t size, const T& value) : m_data(new T[size]), m_size(size), m_capacity(size) {
        for (size_t i = 0; i < m_size; ++i) {
            m_data[i] = value;
        }
    }
    Vector(const Vector& other) : m_data(new T[other.m_size]), m_size(other.m_size), m_capacity(other.m_capacity) {
        for (size_t i = 0; i < m_size; ++i) {
            m_data[i] = other.m_data[i];
        }
    }
    Vector& operator=(const Vector& other) {
        if (this != &other) {
            delete[] m_data;
            m_data = new T[other.m_size];
            m_size = other.m_size;
            m_capacity = other.m_capacity;
            for (size_t i = 0; i < m_size; ++i) {
                m_data[i] = other.m_data[i];
            }
        }
        return *this;
    }
    ~Vector() {
        delete[] m_data;
    }
    void push_back(const T& value) {
        if (m_size >= m_capacity) {
            reserve(m_capacity == 0 ? 1 : m_capacity * 2);
        }
        m_data[m_size++] = value;
    }
    void pop_back() {
        if (m_size > 0) {
            --m_size;
        }
    }
    T& operator[](size_t index) {
        return m_data[index];
    }
    const T& operator[](size_t index) const {
        return m_data[index];
    }
    size_t size() const {
        return m_size;
    }
    bool empty() const {
        return m_size == 0;
    }
    size_t capacity() const {
        return m_capacity;
    }
    void reserve(size_t capacity) {
        if (capacity > m_capacity) {
            T* new_data = new T[capacity];
            for (size_t i = 0; i < m_size; ++i) {
                new_data[i] = m_data[i];
            }
            delete[] m_data;
            m_data = new_data;
            m_capacity = capacity;
        }
    }
private:
    T* m_data;
    size_t m_size;
    size_t m_capacity;
};
```
1.5倍内存利用率高，但是对于频繁插入，需要多次重复申请内存，性能可能更低  
2倍性能更好，但是内存利用率低，可能因为超出实际所需要的内存，导致内存浪费
### 红黑树的特征是什么？
1. 属于二叉树
2. 根节点必是黑色
3. 叶子节点(NIL)必是黑色
4. 不存在两个连续的红色节点相连
5. 从任意一个节点到它所有可达的叶子节点所经过的黑色节点数目必相同
### 红黑树和B+,B-的区别？
1. 结构形态：红黑树是一种二叉搜索树，每个节点最多有两个子节点；而B+树和B-树是多叉树，每个节点可以有多个子节点。
2. 存储方式：红黑树的节点包含键值对和指向左右子树的指针；B+树和B-树的节点只包含键值对，子节点的指针存储在父节点中。
3. 查找效率：红黑树和B+树的查找效率都是O(log n)，但是B+树的查找效率更稳定，因为它的非叶子节点只包含键值而不包含数据，可以容纳更多的节点，减少了I/O操作次数；而B-树的查找效率可能会更高，因为它的非叶子节点包含了数据，可以减少I/O操作的次数。
4. 插入和删除效率：红黑树的插入和删除操作效率较高，但是需要保持平衡性质，有一定的复杂度；B+树的插入和删除操作也比较高效，但是需要维护叶子节点的链表指针，增加了一些细节；B-树的插入和删除操作比较复杂，需要对节点进行合并和分裂。
5. 应用场景：红黑树适用于需要频繁插入和删除的场景，如操作系统的内存管理等；B+树适用于需要支持范围查询和排序的场景，如数据库索引等；B-树适用于需要支持高并发和大规模访问的场景，如分布式文件系统等。
### 手写strcpy,memcpy,memmove函数？
```cpp
char *strcpy(char *dest, const char *src){
    size_t i;
    for(i = 0; src[i] != '\0'; i++){
        dest[i] = src[i];
    }
    return dest;
}
void *memcpy(void *dest, const void *src, size_t n){
    size_t i;
    for(i = 0; i < n; i++){
        *((char *)dest + i) = *((char *)src + i);
    }
    return dest;
}
void *memmove(void *dest, const void *src, size_t n){
    ssize_t i;
    if(dest < src){
        for(i = 0; i < (ssize_t)n; i++){
            *((char *)dest + i) = *((char *)src + i);
        }
    }
    else if(dest > src){
        for(i = n - 1; i >= 0; i--){
            *((char *)dest + i) = *((char *)src + i);
        }
    }
    return dest;
}
```
### do{}while(0)的用法有哪些？
1. 在宏中使用可以将一段代码封装在一个块中
```cpp
#define FUNC() do{\
func1();\
func2();\
}while(0)\

#define FUNC2() \
func1();\
func2()\

if(...)
    FUNC();
//转变为
if(...)
    do{
        func1();
        func2();
    }while(0);

//而FUNC2会导致歧义
if(...)
    func1();
func2();//func2跳出if控制块
```
2. 配合break可以作为出错处理的跳转指令，代替goto使用
```cpp
do{
    //...
    if(func() == -1)
        break;
    //...
}while(0);
```
### 线程池的作用是什么？
1. 提高程序的并发性：线程池可以管理和调度多个线程，使得程序可以同时执行多个任务，提高了程序的并发性和吞吐量。
2. 优化资源的利用：线程池可以在程序启动时预先创建一定数量的线程，并在需要时分配线程执行任务，避免了频繁创建和销毁线程的开销，优化了系统资源的利用。
3. 提高代码的可维护性：线程池可以将任务的执行和线程的管理分离开来，使得代码结构更加清晰和易于维护。
4. 提高程序的响应速度：由于线程池中的线程已经创建好，当任务到达时，可以直接分配线程执行任务，从而减少了线程创建和销毁的开销，提高了程序的响应速度。
### 解决hash冲突的方法？ 
1. 链地址法
在C++ STL中，unordered_set对于hash值一样但是元素不同的元素，会将其元素串成链表进行存储（链地址法），当链表长度超过一定的阈值，那么就会将链表转变为红黑树进行存储，当红黑树中存储的元素小于一定的阈值的时候，那么就会将他重新转变为链表  
1. 开放地址法
   1. 线性探测法  冲突了就选择下一位，再冲突就选择下下位，以此类推
   2. 二次探测法  冲突了就选择平方取余后的位置，再冲突就继续计算
   3. 双重散列法  冲突了就选择其他hash算法继续运算，再冲突就继续计算
   链地址法虽然简单易于实现，但是需要额外的空间来存储  
   开放地址法不需要额外空间来解决冲突，但是容易产生聚集，导致哈希性能下降  
### unordered_set中所采用的hash方法是什么
在C++ STL中，unordered_set使用的是std::hash函数，它是一种通用的哈希函数，可以处理任何类型的元素。std::hash函数的实现是基于一种称为Fowler-Noll-Vo（FNV）哈希的算法。它通过将元素的字节按给定的方式组合，再进行一些基本的位运算和异或操作，生成一个哈希值。FNV哈希算法简单、快速，而且具有良好的分布特性，因此被广泛应用于哈希表的实现中。
### 什么是RAII资源管理？
RAII(Resource Acquisition Is Initialization)是一种C++编程技术，用于在程序中管理资源的获取和释放。  
这种技术的核心思想是将资源的获取和释放绑定在一个对象的生命周期中，即资源在对象构造时被获取，在对象析构时被释放。  
这样可以确保资源的正确获取和释放，避免资源泄漏和悬挂指针等问题。
### 如何判断两个浮点数相等？
虽然他们的分数表现形式是一致的，但是会在运算过程中丢失精度，所以两个理论上一致的浮点数在使用==运算符时返回false  
可以使用fabs(float_a - float_b) < 0.00001来判断是否相等，而后面的数字越小所需要的精度越大，根据实际需求来判断
### 手写一个有可变参数的函数？
```cpp
#include <stdarg.h>
int sum(int len, ...){
    //定义可变参数列表
    va_list vlist;
    int sum = 0;
    //设置可变参数起始位置，len为最后一个变量
    va_start(vlist, len);
    for(int i = 0; i < len; i++){
        //从可变参数列表中获取数据，第二个参数为参数类型
        sum += va_arg(vlist, int);
    }
    //清理
    va_end(vlist);
    return sum;
}
```
