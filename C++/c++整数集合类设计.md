---
title: c++整数集合类
description: 程序设计小作业~
categories:
  - study
  - c++
tags:
  - c++
  - Data Structure
---

- 这是学校c++程序设计课内作业的实现，这里仅提供我的思路（没有复杂度限制随便写了好叭~）

- 基本需求：提供union(并集), intersection(交集), difference(差集) 和 symmetric difference(对称差集)操作和其它必要操作。
- 选择数据表示方法时，使用自由存储区（堆）中的数组。
- 不能使用STL中的容器
- 如：初始化, 赋值, insert(插入一个元素), erase(删除一个元素), isSubset(另一集合是否是其子集), contains(一整数是否属于集合), print(显示所有元素), size(元素个数), capacity(允许的最大容量), empty(是否是空集), full(是否已满), clear(删除所有元素)等

- 总体思路：先设计类的具体功能和成员变量，总结在头文件中，再根据头文件中的声明具体实现即可。直接用数组存储集合元素，用变量记录集合中元素个数和集合最大容量即可。（注意区分使用）

- 具体思路详见代码+注释。

### 附源码

- 头文件接口
```cpp
#iSet.h文件

#include<iostream>
#pragma once
using namespace std;

class iSet {

    private:
    //集合元素个数
    int setsize;
    //集合最大容量
    int setcapacity;
    //指向数组的指针
    int *setlist;
    
    public:
	//各功能汇总
    iSet(int c);

    ~iSet();

    void print();

    int size();

    int capacity();

    void insert(int i);

    iSet setUnion(iSet& other);

    iSet setIntersection(iSet& other);

    iSet setDifference(iSet& other);

    iSet setSymmetricDifference(iSet& other);

    void clear();

    bool empty();

    bool full();

    void erase(int i);

    bool contains(int i);

    bool isSubset(iSet& other);

    iSet& operator=(iSet& other);

	//嵌套类型，抛出错误
    class bad_iSet {
        public:
        int errnum;
        bad_iSet(int n);
    };

};
```

- 具体实现：

```cpp
#iSet.cpp

#include<iostream>
#include "iSet.h"
using namespace std;

//不用STL的排序？直接手搓快排！
//快速排序（当模板记就行了）
void quicksort(int n[],int l,int r) {
    if(l>=r) return;

    int i=l-1;int j=r+1;
    int mid = n[l+r>>1];
    while(i<j) {
        while(n[++i]<mid);
        while(n[--j]>mid);
        if(i<j) {
            int t=n[i];
            n[i]=n[j];
            n[j]=t;
        }
    }
    quicksort(n,l,j);
    quicksort(n,j+1,r);
}

//构造
iSet::iSet(int c) {
    if(c<1) {
        throw iSet::bad_iSet(1);        
    }
    //设定集合最大容量
    setcapacity=c;
    //默认构造空集
    setsize=0;
    //新建对应长度的数组
    setlist = new int[c];
}

//析构
iSet::~iSet() {
    if(setlist==nullptr) return;
    delete[] setlist;
}

void iSet::print() {
    if(setsize==0) {
        cout<<"{}";
        return;
    }
    quicksort(setlist,0,setsize-1);
    cout<<"{";
    for(int i=0;i<setsize;i++) {
        if(i==setsize-1) {
            cout<<setlist[i];
            break;
        }
        cout<<setlist[i]<<",";

    }
    cout<<"}";
}

//访问集合大小
int iSet::size() {
    return setsize;
}

//访问最大容量
int iSet::capacity() {
    return setcapacity;
}

//判断是否包含元素
bool iSet::contains(int n) {
    //直接遍历集合大小次数的数组
    for(int i=0;i<setsize;i++) {
        if(setlist[i]==n) {
            return true;
        }
    }
    return false;
}

//判断空集
bool iSet::empty() {
    return setsize==0;
}

//判断已满
bool iSet::full() {
    return setsize==setcapacity;
}

//清空
void iSet::clear() {
   if(setlist != nullptr) {
        delete[] setlist;
   } 
   setlist=new int[setcapacity];
   setsize=0;
}

//插入
void iSet::insert(int i) {
    //如果已满就抛出错误
    if(this->full()) {
        throw iSet::bad_iSet(2);
    }
    //如果已经包含就跳过
    if(contains(i)) return;
    //插入整数到尾部，并将大小增加1
    setlist[setsize++] = i;
    //setlist[setsize] = i;
    //setsize++;
}

//并集
iSet iSet::setUnion(iSet& other) {
    //创建一个新的集合用于储存并集元素
    iSet unionset(this->setcapacity+other.setcapacity);
    //分别从两个集合获取元素插入到新集合中
    for(int i=0;i<this->setsize;i++) {
        unionset.insert(this->setlist[i]);
    }
    for(int i=0;i<other.setsize;i++) {
        unionset.insert(other.setlist[i]);
    }
    //返回新集合
    return unionset;
}

//交集
iSet iSet::setIntersection(iSet& other) {
    //建立新集合，存储符合条件的元素
    iSet intersectinoset(max(setcapacity,other.setcapacity));
    //筛选共有元素添加到新集合
    for(int i=0;i<setsize;i++) {
        if(other.contains(setlist[i])) {
            intersectinoset.insert(setlist[i]);
        }
    }
    //返回新集合
    return intersectinoset;
}

//差集
iSet iSet::setDifference(iSet& other) {
    //新建
    iSet differenceset(max(setcapacity,other.setcapacity));
    //筛选（在一个集合不在另一个集合的元素）
    for(int i=0;i<setsize;i++) {
        if(!other.contains(setlist[i])) {
            differenceset.insert(setlist[i]);
        }
    }            
    //返回
    return differenceset;
}

//对称差集（两个集合的独有部分的并集）
iSet iSet::setSymmetricDifference(iSet& other) {
    //新建
    iSet symdiffset(max(setcapacity,other.setcapacity));
    //筛选（对两个集合分别筛选，在一个集合不在另一个集合的元素）
    for(int i=0;i<setsize;i++) {
        if(!other.contains(setlist[i])) {
            symdiffset.insert(setlist[i]);
        }
    }
    for(int i=0;i<other.setsize;i++) {
        if(!this->contains(other.setlist[i])) {
            symdiffset.insert(other.setlist[i]);
        }
    }
    //返回
    return symdiffset;
}


//拷贝（赋值运算符重载）
iSet& iSet::operator=(iSet& other) {
	//拷贝基本变量
    setsize=other.setsize;
    setcapacity=other.setcapacity;
    //释放原指针指向的数组
    if(setlist!=nullptr) {
        delete[] setlist;
    }
    //新建数组并拷贝每个元素的值
    setlist = new int[other.setcapacity];
    for(int i=0;i<other.setsize;i++) {
        setlist[i]=other.setlist[i];
    }
    //返回自己用于级联操作
    return *this;
}

//删除元素
void iSet::erase(int n) {
	//如果不包含直接跳过
    if(!contains(n)) return;
    //新集合储存删除后的元素
    int *erased = new int[setcapacity];
    //指向新集合末尾的索引
    int pos=0;
    //遍历原数组元素
    for(int i=0;i<setsize;i++) {
	    //跳过要删除的元素
        if(setlist[i]==n) {
            continue;
        }
        //添加不删除的元素到新集合末尾
        erased[pos++]=setlist[i];
    }
    //释放原指针指向的数组
    if(setlist!=nullptr) {
        delete[] setlist;
    }
    //更改指针指向的数组，使其指向新数组
    setlist = erased;
    //更新元素个数
    setsize--;
}

//判断子集
bool iSet::isSubset(iSet& other) {
	//空集是所有集合的子集
    if(other.empty()) {return true;}
    //遍历每个元素判断
    for(int i=0;i<setsize;i++) {
	    //如果存在不包含在另一个集合的元素就不是子集啦
        if(!other.contains(setlist[i])) {
            return false;
        }
    }
    return true;
}

//坏集合的构造
iSet::bad_iSet::bad_iSet(int i) {
    errnum=i;
}
```

- 测试代码

```cpp
//----------------------------------------------------------------------
//
// TestISet.cpp : Test program for Lab11. 
// Dec,12,2024
//
//----------------------------------------------------------------------

#include <iostream>
using namespace std;

#include "iSet.h"  // your header file for class iSet

int main(int argc, char** argv)
try
  {
    // ***** test for initialization *****

    iSet s1{100};  // an integer set with 100 maximum elements
    iSet s2{110};   // an integer set with 110 maximum elements

    // ***** test for printing *****

    cout << "Set s1 after initialization: " << endl;
    s1.print();     // display set
    cout << endl;

    // ***** test for size *****

    cout << "The number of elements in Set s1: " << s1.size() << endl;
    cout << "The capacity of Set s1: " << s1.capacity() <<endl;

    cout << endl << "Set s2 after initialization: " << endl;
    s2.print();     // display set
    cout << endl;
    cout << "The number of elements in Set s2: " << s2.size() << endl;
    cout << "The capacity of Set s2: " << s2.capacity() << endl;
    cout << endl;

    // ***** test for inserting *****

    for (int i{200};i<=210;i++)   s1.insert(i);        
    // insert 11 elements from 200 to 210
    for (int i{190};i<=215;i++)   s2.insert(i);        
    // insert 16 elements from 190 to 205

    cout << "Set s1 after inserting: " << endl;
    s1.print();   // display set
    cout << endl;
    cout << "The number of elements in Set s1: " << s1.size() << endl;
    cout << "The capacity of Set s1: " << s1.capacity() << endl;
    cout << endl << "Set s2 after inserting: " << endl;
    s2.print();     // display set
    cout << endl;
    cout << "The number of elements in Set s2: " << s2.size() << endl;
    cout << "The capacity of Set s2: " << s2.capacity() << endl;
    cout << endl;

    // ***** test for union *****

    cout << "Union of two integer sets s1 and s2: " << endl;
    s1.print();     // display set
    cout << " �� ";
    s2.print();    // display set
    cout << " = ";
    iSet r1{s1.setUnion(s2)};   // compute union of two integer sets
    r1.print();     // display union result
    cout << endl;
    cout << endl;

    // ***** test for intersection *****

    cout << "Intersection of two integer sets s1 and s2: " << endl;
    s1.print();
    cout << " �� ";
    s2.print();
    cout << " = ";
    iSet r2{s1.setIntersection(s2)};  
    // compute intersection of two integer sets
    r2.print();
    cout << endl;
    cout << endl;

    // ***** test for difference *****

    cout << "Difference of two integer sets s1 and s2: " << endl;
    s1.print();
    cout << " - ";
    s2.print();
    cout << " = ";
    iSet r3{s1.setDifference(s2)};     
    // compute difference of two integer sets
    r3.print();
    cout << endl;
    cout << endl;

    cout << "Difference of two integer sets s2 and s1: " << endl;
    s2.print();
    cout << " - ";
    s1.print();
    cout << " = ";
    iSet r4{s2.setDifference(s1)};     
    // compute difference of two integer sets
    r4.print();
    cout << endl;
    cout << endl;

    // ***** test for symmetric difference *****

    cout << "Symmetric difference of two integer sets s1 and s2: " << endl;
    s1.print();
    cout << " symmetric- ";
    s2.print();
    cout << " = ";
    iSet r5{s1.setSymmetricDifference(s2)};   
    // compute symmetric difference of two integer sets
    r5.print();
    cout << endl;
    cout << endl;

    // ***** test for assignment *****

    cout << "Set s1 after assignment s1=s2: " << endl;
    s1=s2;
    s1.print();
    cout << endl;
    cout << endl;

    // ***** test for "clear" *****

    cout << "Set s2 after erasing all elements: " << endl;
    s2.clear();
    s2.print();
    cout << endl;
    cout << endl;

    // ***** test   "empty" *****

    if (s1.empty()) cout << "Set s1 is empty " << endl;
    else cout << "Set s1 is NOT empty " << endl;
    cout << endl;

    if (s2.empty()) cout << "Set s2 is empty " << endl;
    else cout << "Set s2 is NOT empty " << endl;
    cout << endl;

    // ***** test  "full" *****

    if (s1.full()) cout << "Set s1 is fully " << endl;
    else cout << "Set s1 is NOT full " << endl;
    cout << endl;

    if (s2.full()) cout << "Set s2 is full " << endl;
    else cout << "Set s2 is NOT full " << endl;
    cout << endl;

    // ***** test for "erase" *****

    cout << "Set s1 after erasing an element 202 : " << endl;
    s1.erase(202);
    s1.print();
    cout << endl;
    cout << endl;
    cout << "Set s1 after erasing an element 255 : " << endl;
    s1.erase(255);
    s1.print();
    cout << endl;
    cout << endl;

    // ***** test for "contains" *****
    int e{205};
    if (s1.contains(e)) cout << e << " is a member of s1" << endl;
    else cout << e << " is NOT a member of s1" << endl;
    cout << endl;
    e+=222;
    if (s1.contains(e)) cout << e << " is a member of s1" << endl;
    else cout << e << " is NOT a member of s1" << endl;
    cout << endl;


    // ***** test for "isSubset" *****

    if (s1.isSubset(s2)) cout << "Set s2 is a subset of s1" << endl;
    else cout << "Set s2 is NOT a subset of s1" << endl;
    cout << endl;

    cout << "Insert an element 666 into set s2 : " << endl;
    s2.insert(666);
    cout << "Set s2 : " << endl;
    s2.print();
    cout << endl;

    cout << endl;

    if (s1.isSubset(s2)) cout << "Set s2 is a subset of s1" << endl;
    else cout << "Set s2 is NOT a subset of s1" << endl;
    cout << endl;


    // ***** test for exception throwing *****

    cout << "insert 1000 elements from 1 to 999 into set s1 : " << endl;
    for (int i{1};i<1000;i++)
      s1.insert(i);        // insert 1000 elements from 1 to 999

    return 0;
  }

  // ***** exception handling *****

  catch(iSet::bad_iSet bi)     
  // catch exceptions related to integer set
  { switch (bi.errnum)         // # of exceptions
    { case 1: 
	    cerr << "*** bad iSet: constructor parameter<1 , exit "
	         << endl; // error of constructor parameter
	    break;
      case 2: 
        cerr << "*** bad iSet: maximum elements reached(is Full), exit " 
		      << endl;// overflow of integer set
        break;
    }
    system("pause");
    return 0;
 }
```