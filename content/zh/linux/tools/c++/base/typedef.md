---
title: typedef
description: typedef
date: 2023-11-17
weight: 30000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>
{{< alert >}}

类型定义的语法可以归结为一句话：只要在变量定义前面加上typedef，就成了类型定义。这儿的原本应该是变量的东西，就成为了类型。

在编程中使用typedef目的一般有两个:
- 一个是给变量一个易记且意义明确的新名字
- 另一个是简化一些比较复杂的类型声明。

`typedef   类型     定义名;`

{{< /alert >}}


{{<alert>}}


{{</alert>}}

## 定义函数指针别名






```c++
int(*p)(int, int);  // 那么问题来了，定义一个变量，他的类型是什么呢，这里把变量名去掉，就是他的类型：int(*)(int,int);
typedef char (*p)(int); // 是不是看着有点懵，因为没有用一般的 typedef 源类型 字面类型名; 的表达方式。 要定义的类型是void (*)(int)，即参数一个int，什么也不返回的函数指针，定义的别名是p。


typedef int (*commit_t)(handlerton *hton, THD *thd, bool all);

typedef int (*rollback_t)(handlerton *hton, THD *thd, bool all);

typedef int (*prepare_t)(handlerton *hton, THD *thd, bool all);

typedef int (*recover_t)(handlerton *hton, XA_recover_txn *xid_list, uint len,
                         MEM_ROOT *mem_root);
```





## 定义简单类型别名

```c++
typedef unsigned char byte;

int integer;     //整型变量
int *pointer;   //整型指针变量
int array [5]; //整型数组变量
int *p_array [5]; //整型指针的数组的变量
int (*array_pointer) [5];//整型数组的指针的变量
int function (int param);//函数定义，也可将函数名看作函数的变量
int *function (int param);//仍然是函数，但返回值是整型指针
int (*function) (int param);//现在就是指向函数的指针了

// 若要定义相应类型，即为类型来起名字，就是下面的形式：
typedef int integer_t;                      //整型类型
typedef int *pointer_t;     //整型指针类型
typedef int array_t [5]; //整型数组类型
typedef int *p_array_t [5];    //整型指针的数组的类型
typedef int (*array_pointer_t) [5]; //整型数组的指针的类型
typedef int function_t (int param);     //函数类型
typedef int *function_t (int param);    //函数类型
typedef int (*function_t) (int param); //指向函数的指针的类型

//注意：上面的函数类型在C中可能会出错，因为C中并没有函数类型，它的函数变量会自动退化成函数指针；在C++中好像是可以的。在这里主要说明的是形式上的相似性.
```

## 定义结构体别名


```c++
typedef struct Books
{
   char  title[50];
   char  author[50];
   char  subject[100];
   int   book_id;
} Book;
```


- typedef 仅限于为类型定义符号名称，#define 不仅可以为类型定义别名，也能为数值定义别名，比如您可以定义 1 为 ONE。
- typedef 是由编译器执行解释的，#define 语句是由预编译器进行处理的。





一句话理解
我不知道你是不是已经完全理解了前面的场景，无论理解与否，这句话都能很好的帮助你再次理解前面的内容：
typedef中声明的类型在变量名的位置出现。

什么意思呢，我们回头来看。我们是怎么声明int类型变量的？

```c++
int Typename;
```
像上面这样，对不对？那么用typedef之后呢？把变量名的位置替换为别名：

```c++
typedef int Typename;

```
好了，你现在已经把为int取别名为Typename。

再来看结构体，声明普通结构体变量：

```c++

struct info
{
    char name[128];
    int length;
};
struct info Typename;

```
用typedef取别名，别名取代变量名的位置：

```c++

struct info
{
    char name[128];
    int length;
};
typedef struct info Typename;

```
好了，你现在已经为struct info取别名为Typename。
当然这可能我们平常通常使用下面这种写法：

```c++

typedef struct info
{
    char name[128];
    int length;
}Typename;

```
再来看函数指针类型，我们平常是如何声明函数的？

```c++

void *function(int param);

```
那么使用typedef取别名呢？用别名取代函数名的位置即可：

```c++

void *(*Fun)(int param);

typedef void * (*function_t) (int param);
```
不过这里需要注意用括号将这个别名括起来，并在前面加*号。


函数体声明
```c++
typedef int func(void);
func f1; //等价于int f1(void)
```








---
&#128311;【】举例说明

```c++

#define DECLARE_MYSQL_SYSVAR_SIMPLE(name, type) \
  struct {                                      \
    MYSQL_PLUGIN_VAR_HEADER;                    \
    type *value;                                \
    type def_val;                               \
    type min_val;                               \
    type max_val;                               \
    type blk_sz;                                \
  } MYSQL_SYSVAR_NAME(name)



typedef DECLARE_MYSQL_SYSVAR_SIMPLE(sysvar_int_t, int);






















```


