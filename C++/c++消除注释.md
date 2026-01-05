---
title: c++实现消除注释的程序
description: 程序设计小作业~
categories:
  - study
  - c++
tags:
  - c++
---

## 实现要求

- 写一个程序去除一个 C++ 程序中的所有注解
- 读取一个C++源程序文件 file, 去除注解, 写到另一个文件 file
- 运行使用命令行参数 

## 实现思路

- 刚开始的想法：只要一个一个字符的输入，判断注解的开头和结尾不就行了嘛~简单简单
- 后来的想法：转义字符？注释内字符串？字符串内注释？还挺复杂的。。。（分别判定读取的字符处于什么状态就行）
- 大概流程：读取文件字符->判定状态->决定字符取舍->输出文件。（这里我采取边读取边输出的方式）
- 状态判定分类：在行注释里，在块注释里，在字符串里，在字符里，以及转义字符的判定

### 文件操作

- ifstream和ofstream：文件输入流和文件输出流
- 使用方法：声明+打开文件+流的输入输出+关闭文件

### 命令行参数的使用

- 注意main函数的参数列表:两个参数int,char*，用来表示命令行参数的个数，和存储命令行参数的数组
```cpp
int main(int argc,char* argv[]) {
    return 0;
}
```

### 简单例子
```cpp
//文件操作示例：文件复制
#include <iostream>
#include <fstream>
using namespace std;
int main(int argc,char* argv[]) // 传入命令行参数
{   ifstream fin;
    char c;
    ofstream fout;

    fin.open(argv[1]); //使用命令行参数

    if(!fin)
    {   cerr << "can't open file " << argv[1] << endl;
        exit(1);
    }

    fout.open(argv[2]);
    if(!fout)
    {   cerr << "can't open file " << argv[2] << endl;
        exit(1);
    }

    while(fin)
    {   fin.get(c);
        fout << c;
    }
    fin.close();
    fout.close();

    return 0;
}
```

### 源码，仅供参考

- 实现代码

```cpp
#include<fstream>
using namespace std;

int main (int argc,char* argv[]) {
    ifstream ifs;
    ifs.open(argv[1]);
    ofstream ofs;
    ofs.open(argv[2]);

    bool isInString = false;
    bool isInLComment = false;
    bool isInBComment = false;
    bool isInChar = false;
    bool isInComment = isInLComment||isInBComment;
    char c;
    bool flag;
    while(ifs.get(c)){
        if(isInLComment){
            if(c=='\n') {isInLComment=false;ofs.put(c);}
            continue;
        }
        else if(isInBComment) {
            if(c=='*') {
                flag = true;
            } else if(c=='/'&&flag) {
                isInBComment=false;
            } else {
                flag=false;
            }
            continue;
        }
        else if(isInString){
            ofs.put(c);
            if(c=='"'){isInString = false;}
            else if(c=='\\'){
                ifs.get(c);
                ofs.put(c);
            }
        }
        else if(isInChar){
            ofs.put(c);
            if(c=='\''){isInChar = false;}
            else if(c=='\\'){
                ifs.get(c);
                ofs.put(c);
            }
        }
        else{
            if(c=='/'){
                ifs.get(c);
                if(c=='/'){isInLComment = true;}
                else if (c=='*') {isInBComment = true;}
                else{
                    ofs.put('/');
                    ofs.put(c);
                }
            }
            else if(c=='\''){ 
                isInChar = true;
                ofs.put(c);
            }
            else if(c=='"') {
                isInString = true;
                ofs.put(c);
            }
            else {
                ofs.put(c);
            }
        }

    }
    ifs.close();
    ofs.close();
    return 0;
}

```

- 输入文本

```cpp
//This is a Test Input File for Lab9
//Nov. 28, 2024

/*******************************************************************
// Programming problem:  Eight Queens Problem.
//******************************************************************/
#include <iostream> // for cout
#include <fstream>  // for file operations
#include <iomanip>  // for setw
#include <ctime>    // for run time counting
#include <cstring>    // for run time counting

using namespace std;

const int MAXNUM = 12 ;   //NUM <= 12

int NUM ;

const char *qq="/**/";
const char *pp=/**"\"007*/"/**/";
const char *rr=/**'\'007*/"/**/""//";

/****************************************************************/
/*      print layout                                            */
/****************************************************************/
void printBoard(const char [][MAXNUM]);
/****************************************************************/
/*      recursive function to place each queen                  */
/****************************************************************/
void place(char [][MAXNUM],int,int);
/****************************************************************/
/*      rotate layout by 90 degree                              */
/****************************************************************/
void rotatematrix90(int rrow[MAXNUM],int rrow1[MAXNUM]);
/****************************************************************/
/*      decide if different solutuin "//found//"                */
/****************************************************************/
bool canfound(int rrow[MAXNUM], int rowcol[][MAXNUM],int solution);

int rowcol[14200][MAXNUM];
int solution=0;
int diffrowcol[3574][MAXNUM];
int diffsolution=0;

char c='\"';
const char* p1 = "sdjksd\"\\d//fj/*kdhjk\"dsfjl*/dks";

int main()
{

    cout << "//The number of queens (4 -- 12) :// " ;
    cin  >> NUM;

   long startTime=clock();//time(0);

   char board[MAXNUM][MAXNUM];

   for (char y=0;y<NUM;y++)
   {
       memset(board,0,MAXNUM*MAXNUM);
       place(board,0,y);
   }

    long endTime=clock();

    ofstream outFile1("full.txt");
   for ( int i=0;i<solution;i++ )
   {
       outFile1 << setw(3) << i+1 << ":" ;
        for (int j=0;j<NUM;j++)
           outFile1 << "/*(" << rowcol[i][j]/100 << ',' << rowcol[i][j]%100 << "*/";
       outFile1 << endl;
   }

    ofstream outFile2("unique.txt");
   for (int i=0;i<diffsolution;i++ )
   {
       outFile2 << setw(3) << i+1 << ":" ;
        for (int j=0;j<NUM;j++)
           outFile2 << "/*(" << rowcol[i][j]/100 << ',' << rowcol[i][j]%100 << "*/)";
       outFile2 << endl;
   }

   cout << endl
        << "//Total number of solutions after eliminating solutions \n"
           "//that can be represented by rotating chessboard is "
        << diffsolution
        << "// ." << endl;

   cout << endl << "Time Elasped : "
        << 1000*(double)(endTime-startTime) / CLOCKS_PER_SEC
        << " microseconds." << endl;

    {char ch; cout<<"press any key to exit" <<endl; cin.get(ch);cin.get(ch);}
    return 0;
}

void printBoard(const char board[][MAXNUM])
{
    int rrow[MAXNUM],rrow1[MAXNUM],rrow2[MAXNUM],rrow3[MAXNUM];
   for (int row=0; row<NUM; row++)
      for (int col=0; col<NUM; col++)
          if (board[row][col]>0)
              rrow[row]=row*100+col;

// if (!canfound(rrow,rowcol,solution))
   {
       cout << "Solution " << setw(5) << solution+1 << ": ";
       for (int j=0;j<NUM;j++)
       {
           rowcol[solution][j]=rrow[j];
               cout << '(' << rrow[j]/100 <<','<<rrow[j]%100 << ')';
       }
       solution++;
       cout << endl;

           rotatematrix90(rrow,rrow1);
       if (canfound(rrow1,diffrowcol,diffsolution))
           return;
       rotatematrix90(rrow1,rrow2);
       if (canfound(rrow2,diffrowcol,diffsolution))
           return;
       rotatematrix90(rrow2,rrow3);
       if (canfound(rrow3,diffrowcol,diffsolution))
           return;

       for (int j=0;j<NUM;j++)
           diffrowcol[diffsolution][j]=rrow[j];
        diffsolution++;
   }
}

void place(char board[][MAXNUM], int r, int c)
{
   if (r+1==NUM)
   {
       board[r][c] = r+1;
      printBoard(board);
      return;
    }

   for (int idx=0; idx<NUM; idx++)
   {
       board[r][idx] = -9;
       board[idx][c] = -9;
       int t=c+r-idx;
        if ( t>=0 && t<NUM )
           board[idx][t]=-9;
       t = c-r+idx;
        if ( t>=0 && t<NUM )
           board[idx][t]=-9;
   }
   board[r][c] = r+1;

    for (int col=0; col<NUM; col++)
      if (!board[r+1][col])
          {
              char bakboard[MAXNUM][MAXNUM];
              memcpy(bakboard,board,MAXNUM*MAXNUM*sizeof(char));
              place(bakboard,r+1,col);
          }
}

void rotatematrix90(int rrow[MAXNUM],int rrow1[MAXNUM])
{
       for (int i=0;i<NUM;i++)
       rrow1[i]=rrow[i]/100+100*(NUM-1-rrow[i]%100);
   for (int i=0;i<NUM;i++)
   {
       int min = rrow1[i];
       int minidx=i;
       for (int j=i+1;j<NUM;j++)
           if (rrow1[j]<min)
           {
               min=rrow1[j];
               minidx=j;
           }
       rrow1[minidx]=rrow1[i];
       rrow1[i]=min;
   }
}

bool canfound(int rrow[MAXNUM],int rowcol[][MAXNUM],int solution)
{
    bool found = false;
   for (int i=0; i<solution && !found; i++)
/* {
       int j=0;
       while (j<NUM && rrow[j]==rowcol[i][j])
           j++;
       if (j==NUM)
           found=true;
    }
*/
       found = !memcmp(rrow,rowcol[i],NUM*sizeof(int));
   return found;
}

// End of the Program.
// PLEASE check after processing by your program

a //*
//*/ b

/*mdskjdhsdkj*/

```

- 输出文本

```cpp




#include <iostream> 
#include <fstream>  
#include <iomanip>  
#include <ctime>    
#include <cstring>    

using namespace std;

const int MAXNUM = 12 ;   

int NUM ;

const char *qq="/**/";
const char *pp="/**/";
const char *rr="/**/""//";




void printBoard(const char [][MAXNUM]);



void place(char [][MAXNUM],int,int);



void rotatematrix90(int rrow[MAXNUM],int rrow1[MAXNUM]);



bool canfound(int rrow[MAXNUM], int rowcol[][MAXNUM],int solution);

int rowcol[14200][MAXNUM];
int solution=0;
int diffrowcol[3574][MAXNUM];
int diffsolution=0;

char c='\"';
const char* p1 = "sdjksd\"\\d//fj/*kdhjk\"dsfjl*/dks";

int main()
{

    cout << "//The number of queens (4 -- 12) :// " ;
    cin  >> NUM;

   long startTime=clock();

   char board[MAXNUM][MAXNUM];

   for (char y=0;y<NUM;y++)
   {
       memset(board,0,MAXNUM*MAXNUM);
       place(board,0,y);
   }

    long endTime=clock();

    ofstream outFile1("full.txt");
   for ( int i=0;i<solution;i++ )
   {
       outFile1 << setw(3) << i+1 << ":" ;
        for (int j=0;j<NUM;j++)
           outFile1 << "/*(" << rowcol[i][j]/100 << ',' << rowcol[i][j]%100 << "*/";
       outFile1 << endl;
   }

    ofstream outFile2("unique.txt");
   for (int i=0;i<diffsolution;i++ )
   {
       outFile2 << setw(3) << i+1 << ":" ;
        for (int j=0;j<NUM;j++)
           outFile2 << "/*(" << rowcol[i][j]/100 << ',' << rowcol[i][j]%100 << "*/)";
       outFile2 << endl;
   }

   cout << endl
        << "//Total number of solutions after eliminating solutions \n"
           "//that can be represented by rotating chessboard is "
        << diffsolution
        << "// ." << endl;

   cout << endl << "Time Elasped : "
        << 1000*(double)(endTime-startTime) / CLOCKS_PER_SEC
        << " microseconds." << endl;

    {char ch; cout<<"press any key to exit" <<endl; cin.get(ch);cin.get(ch);}
    return 0;
}

void printBoard(const char board[][MAXNUM])
{
    int rrow[MAXNUM],rrow1[MAXNUM],rrow2[MAXNUM],rrow3[MAXNUM];
   for (int row=0; row<NUM; row++)
      for (int col=0; col<NUM; col++)
          if (board[row][col]>0)
              rrow[row]=row*100+col;


   {
       cout << "Solution " << setw(5) << solution+1 << ": ";
       for (int j=0;j<NUM;j++)
       {
           rowcol[solution][j]=rrow[j];
               cout << '(' << rrow[j]/100 <<','<<rrow[j]%100 << ')';
       }
       solution++;
       cout << endl;

           rotatematrix90(rrow,rrow1);
       if (canfound(rrow1,diffrowcol,diffsolution))
           return;
       rotatematrix90(rrow1,rrow2);
       if (canfound(rrow2,diffrowcol,diffsolution))
           return;
       rotatematrix90(rrow2,rrow3);
       if (canfound(rrow3,diffrowcol,diffsolution))
           return;

       for (int j=0;j<NUM;j++)
           diffrowcol[diffsolution][j]=rrow[j];
        diffsolution++;
   }
}

void place(char board[][MAXNUM], int r, int c)
{
   if (r+1==NUM)
   {
       board[r][c] = r+1;
      printBoard(board);
      return;
    }

   for (int idx=0; idx<NUM; idx++)
   {
       board[r][idx] = -9;
       board[idx][c] = -9;
       int t=c+r-idx;
        if ( t>=0 && t<NUM )
           board[idx][t]=-9;
       t = c-r+idx;
        if ( t>=0 && t<NUM )
           board[idx][t]=-9;
   }
   board[r][c] = r+1;

    for (int col=0; col<NUM; col++)
      if (!board[r+1][col])
          {
              char bakboard[MAXNUM][MAXNUM];
              memcpy(bakboard,board,MAXNUM*MAXNUM*sizeof(char));
              place(bakboard,r+1,col);
          }
}

void rotatematrix90(int rrow[MAXNUM],int rrow1[MAXNUM])
{
       for (int i=0;i<NUM;i++)
       rrow1[i]=rrow[i]/100+100*(NUM-1-rrow[i]%100);
   for (int i=0;i<NUM;i++)
   {
       int min = rrow1[i];
       int minidx=i;
       for (int j=i+1;j<NUM;j++)
           if (rrow1[j]<min)
           {
               min=rrow1[j];
               minidx=j;
           }
       rrow1[minidx]=rrow1[i];
       rrow1[i]=min;
   }
}

bool canfound(int rrow[MAXNUM],int rowcol[][MAXNUM],int solution)
{
    bool found = false;
   for (int i=0; i<solution && !found; i++)

       found = !memcmp(rrow,rowcol[i],NUM*sizeof(int));
   return found;
}




a 




```