
- Deque是双端队列的数据结构，是一个动态数组，没有固定长度，在两端添加删除元素十分快速。

## 定义

- 别忘了模板参数列表，指定你要储存的数据类型
```cpp
deque<int> d1;
deque<double> d2;
```
- 构造函数
```cpp
//构造大小为n的deque
deque(size_type n);
//构造n个值为value的deque
deque(size_type n,const T& value);
```

## 下标访问

- 可以通过`[i]` 和 `.at(i)` 访问元素
```cpp
deque<int> d {1,2,3};
d[1] == d.at(1); //2
```

## 在两端添删元素

```cpp
pushfront(const T& value)  //在前端添加元素
pushback(const T& value)   //在后端添加元素
popfront()                 //删除首端元素
popback()                  //删除末端元素
```

## 引用首尾元素及迭代器的使用

```cpp
front();    //返回首端元素的引用
back();     //返回末端元素的引用
begin()     //返回指向第一个元素的迭代器
end()       //返回指向末尾元素后一个位置的迭代器
rbegin()    //返回指向最后一个元素的逆向迭代器
rend()      //返回指向第一个元素前一个位置的逆向迭代器
```

## 其他操作

- 用法与其他容器相似
```cpp
clear()    //清空
size()     //大小
empty()    //是否为空
insert()   //插入
ersae()    //删除
resize()   //更改容器大小
```
