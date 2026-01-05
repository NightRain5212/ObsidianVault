# strlen[[C/函数]]

- 用于统计[[字符串]]长度。
```
stlen(string);
```
# strcat[[C/函数]]

- 用于拼接[[字符串]]。
- 接受两个[[字符串]]作为参数，把第二个[[字符串]]的备份加在第一个的末尾，再返回第一个参数。
- strcat()[[C/函数]]无法检查第1个[[C++/基础/数组|数组]]是否能容纳第2个[[字符串]]。如果分配给第1个[[C++/基础/数组|数组]]的空间不够大，多出来的字符溢出到相邻[[存储]]单元时就会出问题。
```
char * str1 = "hello";
char * str2 = "world";
strcat(str1,str2);
str1 == "helloworld";
str2 == "world"
```
# strncat[[C/函数]]

- 该[[C/函数]]的第3个参数指定了最大添加字符数。
```
strncat(str1,str2,3);
str1 == "hellowor";
str2 == "world";
```
# strcmp[[C/函数]]

- 用于[[字符串]]的比较（[[字符串]]的内容）
- strcmp()会依次比较每个字符，直到发现第1对不同的字符为止。然后，返回相应的值。
```
char * str1 = "apple";
char * str2 = "apples";
char * str3 = "apple";

strcmp(str1,str3) == 0; //字符串相同返回0，否则返回非0值。
strcmp("a","b") == -1; //在字母表中，第一个字符串在第二个字符串前面，返回负数
strcmp("b","a") == 1; //在字母表中，第一个字符串在第二个字符串后面，返回正数
strcmp(str1,str2) == -115;
```

# strncmp函数

- 只比较第三个参数限定的字符数
```
char * str1 = "applejuice";
char * str2 = "apples";
char * str3 = "applepie";
strncmp(str1,str3,5) == 0;
```


# strcpy与strncpy函数

- strcpy[[拷贝]]字符串
```
//错误使用：使用未初始化的指针。（字符串可能被拷贝到任意地方。）
char * target = "hi";
//第1个是目标字符串，第2个是源字符串。
//确保目标数组有足够的空间容纳源字符串的副本。
strcpy(target,"hello,world");

//正确使用
char target[40]; 
strcpy(target,"hello,world");
target == "hello,world";

//strcpy返回类型为char* ,返回第一个参数的地址。
const char * orig = "beast";
char copy[40] = "be the best that you can be";
char * ps;
ps = strcpy (copy + 7,orig);
copy == "be the beast";
ps == "beast";
//注意，strcpy()把源字符串中的空字符也拷贝在内.
/*
在此例子中，空字符\0覆盖了copy数组中的that的第一个t
*/
```

- 更谨慎的strncpy
- strcpy()和strcat()都有同样的问题，它们都不能检査目标空间是否能容纳源字符串的副本。
- strncpy()更安全，该函数的第3个参数指明可拷贝的最大字符数。
```
char str1[6];
char str2 = "hello,world";
strncpy(str1,str2,5);
str1[5] = '\0';
puts(str1);
/*
strncpy()拷贝字符串的长度不会超过 n，如果拷贝到第n个字符时还未拷贝完整个源字符串,
就不会拷贝空字符。鉴于此，该程序把n设置为比目标数组大小少1，然后把数组最后一个元素设
置为空字符。这样做确保储存的是一个字符串。
*/
程序输出： hello
```

# sprintf 函数

- sprintf()函数声明在stdio.h中，而不是string.h
- 该函数可以把多个元素组合成一个字符串。sprintf()的第1个参数是目标字符串的地址。其余参数和printf()相同，即格式字符串和待写入项的列表。
```
char first[20] = "Annie";
char last[20] = "Smith";
char formal[50];
double prize = 25000;
sprintf(formal,"%s,%-19s:$%6.2f\n",last,first,prize);
puts(formal);
输出：Smith,Annie                 :$25000.00
```
- 注意,sprintf()的用法和 printf()相同，只不过sprintf()把组合后的[[字符串]]储存在[[C++/基础/数组|数组]] formal 中而不是显示在屏幕上。


# 等等等等
