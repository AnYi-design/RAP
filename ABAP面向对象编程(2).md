# OO ABAP( 二 ) 深入探讨：ABAP 类详解

> 在之前的文章 [面向对象 ABAP (1)：ABAP 类简介] 中，我们了解了类的定义和实现。本文将进行更深入的探讨。

我们创建了一个新类，并在其中创建了一个方法 `call_abap_class`。参考下面的类：

```
CLASS ycx_abap_calling_a_class DEFINITION
  PUBLIC
  FINAL
  CREATE PUBLIC .

  PUBLIC SECTION.
    METHODS : call_abap_class .
  PROTECTED SECTION.
  PRIVATE SECTION.
ENDCLASS.

CLASS ycx_abap_calling_a_class IMPLEMENTATION.

  METHOD call_abap_class.
    "旧方法
    DATA: lo_abap TYPE REF TO ycx_abap.
    CREATE OBJECT lo_abap.                   "创建对象

    "调用方法
    CALL METHOD lo_abap->instance_method.
    CALL METHOD ycx_abap=>static_method.


    "新方法
    DATA(lo_abap_new) = NEW ycx_abap( ).     "创建对象

    "调用方法
    lo_abap_new->instance_method( ).
    ycx_abap=>static_method( ).
  ENDMETHOD.

ENDCLASS.
```

## 创建对象和调用方法

创建新对象可以使用以下方式之一：

```abap
"旧方式 / 7.40 之前版本
DATA : lo_abap TYPE REF TO ycx_abap.
CREATE OBJECT lo_abap.

"新方式 / 从 7.40 版本开始
DATA(lo_abap_new) = NEW ycx_abap( ).
```

> 调用`实例方法(instance method)`需要类的对象，并使用操作符 `->`。调用`静态方法(static method)`则不需要对象，可以使用类本身通过操作符 `=>` 来调用。
>

```
"调用方法
CALL METHOD lo_abap->instance_method.
CALL METHOD zjp_abap=>static_method.

"另一种调用方法的方式
lo_abap_new->instance_method( ).
zjp_abap=>static_method( ).
```

## 方法参数
方法可以有 `IMPORTING`、`EXPORTING`、`CHANGING` 和 `RETURNING` 参数。具有前三种参数的方法定义如下所示：

```
METHODS
  method_with_parameters
    IMPORTING
      iv_var_a  TYPE i
      iv_var_b  TYPE i
    EXPORTING
      ev_sum    TYPE i
    CHANGING
      cv_result TYPE i.
      
```

调用该方法的方式如下：

```
DATA : lv_sum    TYPE i,
       lv_result TYPE i.

lv_result = 4.

lo_abap_new->method_with_parameters( 
  EXPORTING
    iv_var_a = 10
    iv_var_b = 20
  IMPORTING
    ev_sum = lv_sum
  CHANGING
    cv_result = lv_result
).
```
> 请注意，类似于函数模块调用，方法的导入参数在调用时作为导出参数传递，反之亦然，而更改参数保持不变。
---


> 对于 `RETURNING` 参数，我们需要在调用时使用关键字 `RECEIVING`。此外，一个方法只能有一个返回参数，因此这样的方法可以用更好更简单的方式调用，如下所示：
>
```
"定义
METHODS
  functional_method
    RETURNING VALUE(rv_val) TYPE i.

"调用
DATA(lv_val) = lo_abap_new->functional_method( ).
```
> 请注意，返回参数总是按值传递，并且需要记住的一个重要点是：一个方法只能有一个返回参数。