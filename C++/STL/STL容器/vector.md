- `std::vector` 是 STL 提供的 **内存连续的**、**可变长度** 的数组（亦称列表）数据结构。能够提供线性复杂度的插入和删除，以及常数复杂度的随机访问。

## 构造函数

```cpp
using namespace std;

//创建空vector
vector<int> v0;

//创建初始空间为3，元素默认值为0的vector
vector<int> v1(3);

//创建初始空间为3，元素默认值为2的vector
vector<int> v2(3,2);

//创建一个v2的拷贝vector v3,其内容元素和v2一样
vector<int> v3(v2);

//创建一个初始空间为3的vector，其元素的默认值是1，并且使用v2的空间配置器
vector<int> v4(3,1,v2.get_allocator());

//移动v2到新创建的vector v5，不发生拷贝
vector<int> v(std::move(v2));
```

## 元素访问

```cpp
vector<int> v;

//使用下表访问
v.at(0)
v[1]
//返回首元素引用
v.front()
//返回末尾元素引用
v.back()
//返回指向第一个元素的指针
v.data()

```

## 迭代器

1. `begin()/cbegin()`
    
    返回指向首元素的迭代器，其中 `*begin = front`。
    
2. `end()/cend()`
    
    返回指向数组尾端占位符的迭代器，注意是没有元素的。
    
3. `rbegin()/crbegin()`
    
    返回指向逆向数组的首元素的逆向迭代器，可以理解为正向容器的末元素。
    
4. `rend()/crend()`
    
    返回指向逆向数组末元素后一位置的迭代器，对应容器首的前一个位置，没有元素。


**与长度相关**：

- `empty()` 返回一个 `bool` 值，即 `v.begin() == v.end()`，`true` 为空，`false` 为非空。
    
- `size()` 返回容器长度（元素数量），即 `std::distance(v.begin(), v.end())`。
    
- `resize()` 改变 `vector` 的长度，多退少补。补充元素可以由参数指定。
    
- `max_size()` 返回容器的最大可能长度。
    
    **与容量相关**：
    
- `reserve()` 使得 `vector` 预留一定的内存空间，避免不必要的内存拷贝。
    
- `capacity()` 返回容器的容量，即不发生拷贝的情况下容器的长度上限。
    
- `shrink_to_fit()` 使得 `vector` 的容量与长度一致，多退但不会少。