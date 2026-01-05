
## 与C的基本区别

- C风格的字符串是`char*`或`char[]` 而C++的string类型是一个类。

## 声明与赋值

```cpp
string str("hello");
str = "world";
```

## append 与`+` 运算符拼接

- 将字符串添加到字符串末尾
```cpp
str.append(",hello");
//str: world,hello
cout<<"hello,"+"world"<<endl;

string s = "hello,"
s += "world";
cout<<s<<endl;
```
## size

- 返回字符串的长度
```cpp
int size = str.size(); 
```

## empty

- 返回字符串是否为空
```cpp
bool isempty = str.empty();
```

## 下标访问成员字符,修改

```cpp
char ch1 = str[2];
char ch2 = str.at(2);
ch1 == ch2

//可以修改字符串内容
str2[2] = 'a';
str2.at(2) = 'b';
```
## 获取子字符串

- 使用substr函数，接受两个参数a，b
- 意思是获取从第a个字符开始的b个字符
```c++
string sub = str.substr(0,5); 
//sun : world
```

## find&rfind

- 查找字符串的内容
- find是从左往右找，rfind是从右往左找。
- 找到返回其第一次出现的下标，未出现则返回-1。
- 参数含义
	- 接受要查找的子字符串c，从下标pos处开始查找n个字符
```cpp
int find(const char* c,int pos=0,int n)
```

## 比较

- compare：当两个字符串相同时返回0
```cpp
string str1 = "abcd";
string str2 = "abcd";
if(str1.compare(str2) == 0){
	cout<<"str1 == str2";
}
```


## 替换

- replace，及参数含义
	- 将字符串的cong第i个字符开始的n个字符替换成str
```cpp
string& replace(int i,int n,const char* str);
string str = "abcdef";
std::cout<< str.replace(1,3,"111111");
//[output] a111111ef
```

## 插入与删除

 - 插入：insert
 - 删除：erase
 - 清空：clear
```cpp
string& insert(int pos,const char* str);
//在第pos个字符之前插入str

string& erase(int i,int n);
//删除第i个字符开始的n个字符

string str = "helloworld";
cout<<str.insert(5,",");
// "hello,world"
cout<<str.erase(6,5);
// "hello,"
str.clear()
// ""
```


## 转换成c风格字符串

```cpp
//string -> cstring
StringName.c_str();
```
