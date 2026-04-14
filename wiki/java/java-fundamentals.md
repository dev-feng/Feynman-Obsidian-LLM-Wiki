# Java 基础
> Updated: 2026-04-10
> Published: Unknown
> Sources: Oracle Official Documentation, 2026
> Raw: ../../raw/java/2026-04-10-java-fundamentals-oracle.md
> See Also:

## 一、概述
Java 基础涵盖了 Java 编程语言的核心组成部分：变量、基本数据类型、操作符、表达式、语句、块以及控制流语句。理解这些基础概念是掌握 Java 编程的必经之路。

## 二、核心知识点

### 1. 变量 (Variables)

#### 定义
变量是存储数据的基本单位。在 Java 中，对象的状态存储在字段中，而"变量"这一术语用于描述各种数据存储位置。

#### 变量命名规则与约定
- 变量名必须以字母（A-Z、a-z）、美元符号（$）或下划线（_）开头
- 变量名不能使用 Java 保留字（如 int、class、public 等）
- 变量名区分大小写
- 建议使用有意义的名称，体现变量的用途
- 若变量名由多个单词组成，推荐使用驼峰命名法（如 firstName、totalCount）

### 2. 基本数据类型

#### 原始类型 (Primitive Types)
Java 提供了 8 种原始数据类型：
- **byte**：8 位有符号整数，范围 -128 到 127
- **short**：16 位有符号整数，范围 -32768 到 32767
- **int**：32 位有符号整数，范围 -2147483648 到 2147483647
- **long**：64 位有符号整数，范围 -9223372036854775808 到 9223372036854775807
- **float**：32 位单精度浮点数
- **double**：64 位双精度浮点数
- **boolean**：布尔类型，值为 true 或 false
- **char**：16 位 Unicode 字符

#### 代码示例
```java
int age = 25;
double salary = 5000.50;
boolean isActive = true;
char grade = 'A';
```

#### 字符串 (Character Strings)
字符串是由字符序列组成的对象，在 Java 中使用 String 类表示。

#### 代码示例
```java
String name = "Hello Java";
String message = "Welcome to Java programming";
```

#### 数组 (Arrays)
数组是相同类型元素的固定有序集合。

#### 代码示例
```java
int[] numbers = new int[5];
numbers[0] = 10;
numbers[1] = 20;

String[] names = {"Alice", "Bob", "Charlie"};
```

### 3. 默认值和字面量

#### 默认值
当变量声明但未初始化时，会被赋予默认值：
- 整数类型（byte、short、int、long）：默认值为 0
- 浮点数类型（float、double）：默认值为 0.0
- 布尔类型（boolean）：默认值为 false
- 字符类型（char）：默认值为 '\u0000'（空字符）
- 引用类型（包括 String）：默认值为 null

#### 字面量
字面量是直接表示固定值的源代码形式：

##### 整数字面量
```java
int decimal = 42;      // 十进制
int hex = 0x2A;         // 十六进制
int binary = 0b101010;  // 二进制（Java 7+）
```

##### 浮点字面量
```java
double pi = 3.14159;
double scientific = 1.5e10;
```

##### 字符和字符串字面量
```java
char letter = 'A';
String greeting = "Hello";
```

##### 布尔字面量
```java
boolean trueValue = true;
boolean falseValue = false;
```

### 4. 操作符 (Operators)

#### 算术操作符
| 操作符 | 描述 | 示例 |
|--------|------|------|
| + | 加法 | a + b |
| - | 减法 | a - b |
| * | 乘法 | a * b |
| / | 除法 | a / b |
| % | 取模 | a % b |
| ++ | 递增 | ++a 或 a++ |
| -- | 递减 | --a 或 a-- |

#### 代码示例
```java
int a = 10;
int b = 3;
System.out.println(a + b);  // 输出: 13
System.out.println(a - b);  // 输出: 7
System.out.println(a * b);  // 输出: 30
System.out.println(a / b);  // 输出: 3（整数除法）
System.out.println(a % b);  // 输出: 1
```

#### 比较操作符
| 操作符 | 描述 |
|--------|------|
| == | 等于 |
| != | 不等于 |
| > | 大于 |
| < | 小于 |
| >= | 大于等于 |
| <= | 小于等于 |

#### 代码示例
```java
int x = 5;
int y = 10;
System.out.println(x == y);  // 输出: false
System.out.println(x < y);    // 输出: true
```

#### 逻辑操作符
| 操作符 | 描述 | 说明 |
|--------|------|------|
| && | 逻辑与 | 短路求值 |
| \|\| | 逻辑或 | 短路求值 |
| ! | 逻辑非 | 取反 |

#### 代码示例
```java
boolean a = true;
boolean b = false;
System.out.println(a && b);  // 输出: false
System.out.println(a || b);  // 输出: true
System.out.println(!a);       // 输出: false
```

### 5. 表达式、语句和块 (Expressions, Statements, and Blocks)

#### 表达式
表达式是由操作符、操作数和变量组合而成的计算式，它产生一个值。

#### 代码示例
```java
int result = a + b * c;  // 这是一个表达式
int x = 5 + 3 * 2;       // x 的值为 11
```

#### 语句
语句是执行特定操作的完整指令，以分号结尾。

#### 代码示例
```java
int a = 10;              // 赋值语句
System.out.println(a);   // 方法调用语句
if (a > 5) { }           // 条件语句
```

#### 块
块是由零个或多个语句组成的代码片段，用大括号括起来。

#### 代码示例
```java
public class Example {
    public static void main(String[] args) {
        int x = 10;
        if (x > 5) {
            System.out.println("x is greater than 5");
            System.out.println("Block statement");
        }
    }
}
```

### 6. 控制流语句 (Control Flow Statements)

#### 决策语句

##### if-else 语句
```java
int score = 85;
if (score >= 90) {
    System.out.println("Grade: A");
} else if (score >= 80) {
    System.out.println("Grade: B");
} else {
    System.out.println("Grade: C");
}
```

##### switch 语句
```java
int day = 3;
switch (day) {
    case 1:
        System.out.println("Monday");
        break;
    case 2:
        System.out.println("Tuesday");
        break;
    case 3:
        System.out.println("Wednesday");
        break;
    default:
        System.out.println("Other day");
}
```

#### 循环语句

##### for 循环
```java
for (int i = 0; i < 5; i++) {
    System.out.println("Iteration: " + i);
}
```

##### while 循环
```java
int count = 0;
while (count < 5) {
    System.out.println("Count: " + count);
    count++;
}
```

##### do-while 循环
```java
int n = 0;
do {
    System.out.println("n = " + n);
    n++;
} while (n < 5);
```

#### 分支语句

##### break 语句
```java
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        break;  // 跳出循环
    }
    System.out.println("i = " + i);
}
```

##### continue 语句
```java
for (int i = 0; i < 5; i++) {
    if (i == 2) {
        continue;  // 跳过本次迭代
    }
    System.out.println("i = " + i);
}
```

## 三、易错点与注意事项

### 1. 变量相关
- 变量使用前必须声明，否则编译错误
- 变量有作用域限制，在作用域外无法访问
- 局部变量不会自动赋予默认值，必须显式初始化

### 2. 数据类型相关
- 浮点数除法与整数除法结果不同：5/2 = 2（整数），5.0/2 = 2.5（浮点数）
- 浮点数比较不应使用 ==，应使用容差比较
- 数组下标从 0 开始，最大下标为 length-1

### 3. 操作符相关
- == 用于比较引用时比较的是地址，不是内容
- 字符串比较应使用 equals() 方法，而非 ==
- 短路操作符 && 和 || 会在第一个条件满足时跳过后续判断

### 4. 控制流相关
- if-else 语句中，else 必须紧跟 if 语句的右大括号
- switch 的 case 必须使用 break 防止贯穿
- 循环中注意避免死循环

## 四、补充说明
本文内容严格基于 Oracle 官方 Java 教程，涵盖 Java 编程语言的基础核心概念，为后续深入学习面向对象编程、异常处理等高级主题奠定基础。

---
> 注：本文由 AI 基于 raw 原始素材编译生成，严格遵循 Karpathy 知识库规范，仅可由 AI 进行更新维护。