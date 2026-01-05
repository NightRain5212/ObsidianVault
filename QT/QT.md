
- **Qt** 是一个跨平台的 **C++ 应用程序开发框架**，由 **Qt Company**（原属诺基亚，后独立）开发并维护。它广泛用于开发 **图形用户界面（GUI）** 程序，但也支持非 GUI 应用（如服务器、命令行工具）。Qt 的主要特点是 **跨平台、模块化、高性能**，并且提供了丰富的工具和库。

- **集成开发工具**
    - **Qt Creator**：官方提供的跨平台 IDE，支持代码编辑、调试、UI 设计。
    - **Qt Designer**：可视化 UI 设计工具，可拖拽生成界面。


## 项目配置文件.pro


```qmake
# 指定 Qt 模块（core、gui、widgets 等）
QT       += core gui widgets

# 如果 Qt 版本 >= 5，需要额外声明
greaterThan(QT_MAJOR_VERSION, 4): QT += widgets

# 项目名称
TARGET = MyFirstQtApp

# 项目类型（app = 应用程序，lib = 库）
TEMPLATE = app

# 源代码文件
SOURCES += main.cpp \
           mywidget.cpp

# 头文件
HEADERS += mywidget.h

# UI 文件（由 Qt Designer 生成）
FORMS += mywidget.ui

# 资源文件（图片、QML 等）
RESOURCES += resources.qrc
```

## main.cpp入口

```cpp
#include <QApplication>
#include "mywidget.h"  // 你的主窗口类

int main(int argc, char *argv[]) {
    QApplication app(argc, argv);  // 初始化 Qt 应用程序

    MyWidget window;  // 创建主窗口对象
    window.show();    // 显示窗口

    return app.exec();  // 进入 Qt 事件循环（等待用户操作）
}
```


## 项目构建流程

### QMAKE

-  **运行 `qmake`，生成 `Makefile`**
-  **运行 `make`（Linux/macOS）或 `nmake`/`mingw32-make`（Windows）**
-  **运行程序**

### CMAKE

-  **运行 `cmake`，生成构建系统（Makefile/Ninja）**,读取 `CMakeLists.txt`，生成 `Makefile` 或 `Ninja` 构建文件。
-  **运行 `cmake --build` 编译**, 调用底层构建工具（如 `make` 或 `ninja`）编译代码
-  **运行程序**


 - 当你使用 **Qt Designer** 设计界面并保存为 `.ui` 文件（如 `mywindow.ui`）后，Qt 的构建工具（`qmake` 或 `CMake`）会在编译时自动调用 **`uic`（Qt 的用户界面编译器）**，将 `.ui` 文件转换成 `ui_mywindow.h`。

- `ui->setupUi(this);` 是 Qt 中非常关键的一行代码，它的作用是将 **你在 Qt Designer 里设计的界面（.ui 文件）加载并应用到当前的窗口（或控件）上**。

## 信号&槽

### **信号(Signal)**

- 当对象的状态发生变化时**发出**的信号
- 类似于"事件通知"
- 例如：按钮被点击时发出`clicked()`信号
### **槽(Slot)**

- 接收信号并**响应**的函数
- 类似于"事件处理函数"
- 例如：点击按钮后执行某个操作
### **连接(Connection)**

- 使用`connect()`函数将信号与槽关联起来
- 当信号发出时，自动调用连接的槽函数
## 连接

### SIGNAL/SLOT

```cpp
connect(sender, SIGNAL(signalSignature), receiver, SLOT(slotSignature));

connect(button, SIGNAL(clicked()), this, SLOT(close()));
```

- **特点**：
    - Qt4 的传统语法
    - 使用字符串表示信号和槽
    - 运行时检查信号和槽是否存在
    - 类型安全较弱，拼写错误只能在运行时发现

### 函数地址连接

```cpp
connect(sender, &SenderClass::signalName, receiver, &ReceiverClass::slotName);

connect(button, &QPushButton::clicked, this, &MainWindow::close);
```

- **特点**：
    - Qt5 引入的新语法
    - 编译时类型检查
    - 更安全，IDE 支持更好
    - 支持函数重载解析


### **[[Lambda]] 表达式作为槽**

```cpp
connect(sender, &SenderClass::signalName, [=](){
    // Lambda 函数体
});

connect(button, &QPushButton::clicked, [=](){
    qDebug() << "Button clicked!";
});
```

- **特点**：
    - 不需要单独定义槽函数
    - 可以捕获局部变量
    - 适合简单的响应逻辑

### **自动连接 (UI 文件)**

- **特点**：
    - 在 Qt Designer 中直接连接信号和槽
    - 自动生成 `ui_*.h` 文件中的连接代码
    - 命名槽函数必须遵循 `on_对象名_信号名` 格式

### 函数地址访问重载函数

- 当函数发生重载时，不能直接通过函数地址访问

```cpp
    Commander c;
    Soldier s;
    void (Commander:: *pgo)() = &Commander::go;
    void (Soldier:: *pfight)() = &Soldier::fight;
    connect(&c,pgo,&s,pfight);
    emit c.go();

    void (Commander:: *pgoa)(QString) = &Commander::go;
    void (Soldier:: *pfighta)(QString) = &Soldier::fight;
    connect(&c,pgoa,&s,pfighta);
    emit c.go("freedom");
```


### 自定义信号槽

#### 声明信号和槽

- 在**继承自`QObject`的类**中声明信号和槽，注意：
- 类声明中**必须包含`Q_OBJECT`宏**
- 信号只需声明，不需实现
- 槽函数需要实现
```cpp
// MyClass.h
#include <QObject>

class MyClass : public QObject
{
    Q_OBJECT  // 必须包含这个宏
    
public:
    explicit MyClass(QObject *parent = nullptr);
    
signals:  // 信号声明区
    void mySignal(int value);  // 信号只需声明，不要实现
    void anotherSignal();
    
public slots:  // 槽函数声明区
    void mySlot(int receivedValue);  // 需要实现
    void anotherSlot();
    
private slots:  // 私有槽
    void privateSlot();
};
```

#### 连接

```cpp
MyClass *sender = new MyClass;
MyClass *receiver = new MyClass;

// 连接信号和槽
connect(sender, &MyClass::mySignal, receiver, &MyClass::mySlot);

// 触发信号
emit sender->mySignal(42);  // 会调用receiver的mySlot(42)
```

### 信号连接信号

```cpp
connect(sender1, &Class1::signal1, sender2, &Class2::signal2);
```
### 断开连接

```cpp
disconnect(sender, &SenderClass::signal, receiver, &ReceiverClass::slot);
disconnect(sender); // 断开所有与sender的连接
```
