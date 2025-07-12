---
title: 符号运算
description: sympy
date: 2025-05-20
weight: 50
viz: true
---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>


- https://docs.sympy.org/latest/modules/geometry/index.html





## install

```bash
pip install sympy
pip3 install sympy

```

## 配置

```python
from sympy import init_printing
init_printing()

```


## symbols

可以通过将假设传递给 symbols() 来为符号指定不同的假设。在本节的其余部分，我们将假设 x 和 y 为正数，并且 a 和 b 为实数。我们将保留 z、t 和 c 作为任意复数符号，以演示在这种情况下会发生什么。

```python
from sympy.abc import x,y,z
symbols('a:5')
ymbols('mark(1:4)')

a, b = symbols('a b', real=True)
x, y = symbols('x y', positive=True)
z, t, c = symbols('z t c')

x1 = Symbol('x1', positive=True, real=True)

xpos = Symbol('xpos', positive=True)
xneg = Symbol('xneg', negative=True)
x = Symbol('x')
print(xpos.is_positive)
print(xneg.is_positive)
print(x.is_positive)

```

- infinite 无穷大
- finite 有限值
- complex 复数
- real 实数
- imaginary 虚数
- rational 有理数
- irrational 无理数
- integer 整数
- noninteger 非整数
- even 奇数
- odd 偶数
- prime 质数
- composite 合数
- zero 零
- nonzero 非零
- positive 正数
- nonnegative 非负数
- negative 负数
- nonpositive 非正数





## 字符串转换为`SymPy`表达式: `sympify`
```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

str_expr = "x**2 + 3*x - 1/2"
expr = sympify(str_expr)

```




## 替换: `subs`

首先，它返回一个新的表达式。SymPy 对象是不可变的。这意味着 subs 不会就地修改它。事实上，由于 SymPy 表达式是不可变的，因此任何函数都不会就地更改它们。所有函数都会返回新的表达式。

要一次执行多个替换，请将 (old, new) 对的列表传递给 subs。

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

expr = cos(x) + 1
expr.subs(x, y)
expr.subs(x, 0)

expr = x**3 + 4*x*y - z
expr.subs([(x, 2), (y, 4), (z, 0)])

expr = x**4 - 4*x**3 + 4*x**2 - 2*x + 3
replacements = [(x**i, y**i) for i in range(5) if i % 2 == 0]
expr.subs(replacements)

```


## 替换: `evalf`

要将数值表达式评估为浮点数，请使用 evalf。

首先，它返回一个新的表达式。SymPy 对象是不可变的。这意味着 subs 不会就地修改它。事实上，由于 SymPy 表达式是不可变的，因此任何函数都不会就地更改它们。所有函数都会返回新的表达式。

要一次执行多个替换，请将 (old, new) 对的列表传递给 subs。

要以数值方式评估具有符号的表达式，我们可以使用 subs 后跟 evalf，但更有效且数值上更稳定的是使用 subs 标志将替换传递给 evalf，该标志接受 Symbol: point 对的字典。

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

expr = sqrt(8)
expr.evalf()
pi.evalf(100)

expr = cos(2*x)
expr.evalf(subs={x: 2.4})

```

## 替换: `lambdify`

lambdify 使用 eval。不要将其用于未经清理的输入。

将 SymPy 表达式转换为可进行数值评估的表达式最简单的方法是使用 lambdify 函数。 lambdify 的作用类似于 lambda 函数，只是它将 SymPy 名称转换为给定数值库（通常是 NumPy）的名称。

您可以使用 NumPy 以外的其他库。例如，要使用标准库数学模块，请使用 "math"。

```python
import numpy
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

a = numpy.arange(10)
expr = sin(x)
f = lambdify(x, expr, "numpy")
f = lambdify(x, expr, "math")


```



## 化简


#### simplify

我们可以看到 simplify() 能够处理一大类表达式。

但是 simplify() 存在一个缺陷。它只是应用了 SymPy 中所有主要的简化操作，并使用启发式方法来确定最简单的结果。但“最简单”这个词语并没有一个明确的定义。

使用 simplify() 的另一个陷阱是，它可能不必要地慢，因为它会在选择最佳方案之前尝试多种简化方法。如果你已经知道要进行哪种简化，最好使用应用这些简化的特定简化函数。

当你不确定表达式的形式，需要一个万能函数来简化它时，它也很有用。

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

simplify(sin(x)**2 + cos(x)**2)
simplify((x**3 + x**2 - x - 1)/(x**2 + 2*x + 1))
simplify(gamma(x)/gamma(x - 2))
simplify(x**2 + 2*x + 1)

```

#### 展开: `expand`

多项式/有理函数简化展开

expand() 可能听起来不像一个简化函数。毕竟，顾名思义，它使表达式更大，而不是更小。通常情况下是这样的，但通常情况下，表达式在调用 expand() 后会变小，因为发生了抵消。

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

expand((x + 1)**2)
expand((x + 1)*(x - 2) - (x - 1)*x)

expand((cos(x) + sin(x))**2)

```

#### 因式分解: `factor`

factor() 接受一个多项式，并将其分解成有理数上的不可约因子。

factor() 使用有理数上的完全多元分解算法，这意味着由 factor() 返回的每个因子都保证是不可约的。

如果你对因子本身感兴趣，factor_list 会返回一个结构更清晰的输出。

请注意，factor 和 expand 的输入不一定是严格意义上的多项式。它们将智能地分解或扩展任何类型的表达式（但请注意，如果输入不再是有理数上的多项式，则因子可能不会是不可约的）。

请注意，由于 factor() 将完全分解表达式的分子和分母，因此它也可以用来做同样的事情

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

factor(x**3 - x**2 + x - 1)
factor(x**2*z + 4*x*y*z + 4*y**2*z)
factor_list(x**2*z + 4*x*y*z + 4*y**2*z)

factor(cos(x)**2 + 2*cos(x)*sin(x) + sin(x)**2)

```

#### 合并同类项: `collect`

`collect() `收集表达式中项的相同幂。

`collect()` 与 `.coeff()` 方法结合使用特别有用。` expr.coeff(x, n)` 给出了 `expr` 中 `x**n` 的系数

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

expr = x*y + x - 3 + 2*x**2 - z*x**2 + x**3
collected_expr = collect(expr, x)
collected_expr.coeff(x, 2)

```

#### 合并同类项: `cancel`

cancel() 将接受任何有理函数，并将其转换为标准规范形式，，其中 和 是没有公因子的扩展多项式，并且 和 的最高系数没有分母（即为整数）。

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

cancel((x**2 + 2*x + 1)/(x**2 + x))

expr = 1/x + (3*x/2 - 2)/(x - 4)
cancel(expr)

expr = (x*y**2 - 2*x*y*z + x*z**2 + y**2 - 2*y*z + z**2)/(x**2 - 1)
cancel(expr)

```

#### 部分分式分解: `apart`

apart() 对有理函数执行 部分分式分解。

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

expr = (4*x**3 + 21*x**2 + 10*x + 12)/(x**4 + 5*x**3 + 5*x**2 + 4*x)
apart(expr)

```


#### 三角恒等式简化合并: `trigsimp`

要使用三角恒等式简化表达式，请使用 trigsimp()。trigsimp() 也适用于双曲三角函数。

与 simplify() 类似，trigsimp() 将各种三角恒等式应用于输入表达式，然后使用启发式方法返回“最佳”表达式。

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

trigsimp(sin(x)**2 + cos(x)**2)
trigsimp(sin(x)**4 - 2*cos(x)**2*sin(x)**2 + cos(x)**4)
trigsimp(sin(x)*tan(x)/sec(x))

trigsimp(cosh(x)**2 + sinh(x)**2)
trigsimp(sinh(x)/tanh(x))
trigsimp(sin(x)*cos(y) + sin(y)*cos(x))

```

#### 三角函数展开: `expand_trig`

要扩展三角函数，即应用和角或倍角恒等式，请使用 expand_trig()。

由于 eexpand_trig() 往往会使三角表达式变大，而 trigsimp() 往往会使它们变小，因此可以使用 trigsimp() 反向应用这些恒等式

- 三角函数的和差倍半

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

expand_trig(sin(x + y))
expand_trig(tan(2*x))

```





## 导数

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)
diff(cos(x), x)
diff(x**4, x, x, x)
diff(x**4, x, 3)

expr = exp(x*y*z)
diff(expr, x, y, y, z, z, z, z)
expr.diff(x, y, y, z, 4)

deriv = Derivative(expr, x, y, y, z, 4)
deriv.doit()



```

## 积分

如果 integrate 无法计算积分，它将返回一个未计算的 Integral 对象。



- 要计算不定积分，即反导数或原函数，只需在表达式后传递变量。

```python
integrate(cos(x), x)



```


- 要计算定积分，请传递参数 `(integration_variable, lower_limit, upper_limit)`。

```python
integrate(exp(-x), (x, 0, oo))



```


- 与不定积分一样，你可以传递多个极限元组来执行多重积分。


```python
integrate(exp(-x**2 - y**2), (x, -oo, oo), (y, -oo, oo))



```


- 与 Derivative 一样，你可以使用 Integral 创建未计算的积分。

```python
expr = Integral(log(x)**2, x)
expr.doit()


```

## 数值积分

- 当符号积分不切实际或不可能时，数值积分成为一种可行的方法。这种方法允许通过数值技术计算积分，即使在处理无限区间或被积函数时也是如此。

```python
from sympy import Integral, Symbol, sqrt
x = Symbol('x')
integral = Integral(sqrt(2)*x, (x, 0, 1))
integral.evalf()
integral.evalf(50)


Integral(exp(-(x ** 2)), (x, -oo, oo)).evalf()
Integral(1 / sqrt(x), (x, 0, 1)).evalf()

```


## 极限

- `limit(f(x), x, x0)`
- 只要评估点是奇点，就应该使用 `limit` 而不是 `subs`,即使 SymPy 有对象来表示 ，使用它们进行评估也不可靠，因为它们不跟踪诸如增长率之类的东西。此外，诸如 和 之类的东西返回 （非数字）。

```python
limit(sin(x)/x, x, 0)


expr = x**2/exp(x)
expr.subs(x, oo)
limit(expr, x, oo)

```

- 要仅在一侧评估极限，请将 '+' 或 '-' 作为第四个参数传递给 limit。例如，要计算

```python
limit(1/x, x, 0, '-')
limit(1/x, x, 0, '+')

```

- 与 Derivative 和 Integral 一样，limit 有一个未计算的对应项，即 Limit。要计算它，请使用 doit。

```python
expr = Limit((cos(x) - 1)/x, x, 0)
expr.doit()

```

## 级数展开

SymPy 可以计算函数在某一点周围的渐近级数展开式。要计算在点附近以阶展开的表达式，请使用 `f(x).series(x, x0, n)`。可以省略 `x0`和 `n`，在这种情况下，将使用默认值 `x0=0` 和 `n=6`。

```python
expr = exp(sin(x))
expr.series(x, 0, 4)

```

末尾的 项表示 处的 Landau 阶项（不要与计算机科学中使用的大 O 符号混淆，后者通常表示 处的 Landau 阶项，其中 ）。这意味着所有 x 项的幂大于或等于 都被省略了。可以在 series 之外创建和操作阶项。它们会自动吸收高阶项。


- 如果你不想要阶项，请使用 removeO 方法。
```python
expr = exp(sin(x))
expr.series(x, 0, 4).removeO()

exp(x - 6).series(x, x0=6)

```


## 解方程

SymPy 中的符号方程不是由` =` 或 `== `表示的，而是由 `Eq` 表示的.

然而，还有一个更简单的方法。在 `SymPy` 中，任何不在 `Eq `中的表达式都会被求解函数自动认为等于 `0`。由于 当且仅当 ，这意味着您可以使用 `x - y` 代替 `x == y`。

求解代数方程的主要函数是 `solveset`,请注意，还有一个名为 `solve` 的函数也可以用来求解方程。语法是 `solve(equations, variables)`，但建议使用 `solveset` 代替。

`solveset(equation, variable=None, domain=S.Complexes)`
- 当求解单个方程时，solveset 的输出是一个 `FiniteSet` 或 `Interval` 或 `ImageSet`，包含解。
- 如果没有解，则返回 `EmptySet`
- 如果无法找到解，则返回 `ConditionSet`
- solveset 只报告每个解一次。要获取多项式的解，包括重数，请使用 roots。
- 当前 solveset 无法求解以下类型的方程，可通过 LambertW（超越方程求解器）求解的方程。solve 可用于此类情况


#### 单个方程: `solveset`

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)
Eq(x, y)

solveset(Eq(x**2, 1), x)
solveset(Eq(x**2 - 1, 0), x)
solveset(x**2 - 1, x)

solveset(x**2 - x, x)
solveset(x - x, x, domain=S.Reals)
solveset(sin(x) - 1, x, domain=S.Reals)


solveset(x**3 - 6*x**2 + 9*x, x)
roots(x**3 - 6*x**2 + 9*x, x)

```

#### 线性方程组: `linsolve`

在 solveset 模块中，线性方程组使用 linsolve 求解。将来我们能够直接从 solveset 中使用 linsolve。以下是 linsolve 语法的示例。


```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)
Eq(x, y)

linsolve([x + y + z - 1, x + y + 2*z - 3 ], (x, y, z))

linsolve(Matrix(([1, 1, 1, 1], [1, 1, 2, 3])), (x, y, z))

M = Matrix(((1, 1, 1, 1), (1, 1, 2, 3)))
system = A, b = M[:, :-1], M[:, -1]
linsolve(system, x, y, z)

```
- 解的顺序对应于给定符号的顺序。

#### 非线性方程组: `nonlinsolve`

在 solveset 模块中，非线性方程组使用 nonlinsolve 求解。以下是 nonlinsolve 的示例。

解的顺序对应于给定符号的顺序。

当前 nonlinsolve 不会以 LambertW 的形式返回解（如果存在 LambertW 形式的解）。solve 可用于此类情况

当前 nonlinsolve 无法正确求解包含三角函数的方程组。solve 可用于此类情况（但不会给出所有解）


- 只有实数解存在时
```python
from sympy import *
a, b, c, d = symbols('a, b, c, d', real=True)
nonlinsolve([a**2 + a, a - b], [a, b])
nonlinsolve([x*y - 1, x - 2], x, y)

```



- 只有复数解存在时
```python
from sympy import *
a, b, c, d = symbols('a, b, c, d', real=True)
nonlinsolve([x**2 + 1, y**2 + 1], [x, y])

```

- 实数解和复数解都存在时
```python
from sympy import sqrt
system = [x**2 - 2*y**2 -2, x*y - 2]
vars = [x, y]
nonlinsolve(system, vars)

system = [exp(x) - sin(y), 1/y - 3]
nonlinsolve(system, vars)

```

- 当系统是正维系统（有无穷多解）时
```python
from sympy import *
nonlinsolve([x*y, x*y - x], [x, y])
system = [a**2 + a*c, a - b]
nonlinsolve(system, [a, b])

```

#### 求解微分方程: `dsolve`

通常情况下，微分方程的解无法显式地求解函数。

```python
from sympy import *
f, g = symbols('f g', cls=Function)
f(x).diff(x)
diffeq = Eq(f(x).diff(x, x) - 2*f(x).diff(x) + f(x), sin(x))
dsolve(diffeq, f(x))

dsolve(f(x).diff(x)*(1 - sin(f(x))) - 1, f(x))

```


#### 排列组合: `factorial`、`binomial`

通常情况下，微分方程的解无法显式地求解函数。

```python
from sympy import *
x, y, z = symbols('x y z')
init_printing(use_unicode=True)

factorial(n)
binomial(n, k)
gamma(z)

```

## 绘图


#### 二维线图: `plot`


```python
from sympy import symbols
from sympy.plotting import plot

x = symbols('x')
plot(x**2, (x, -5, 5))

# 具有单一范围的多个图
plot(x, x**2, x**3, (x, -5, 5))

# 具有不同范围的多个图
plot((x**2, (x, -6, 6)), (x, (x, -5, 5)))

plot(x**2, adaptive=True, n=400)

```

`sympy.plot ting.plot.plot（* args， show = True， ** kwargs）`

- 第一个参数是表示要绘制的单变量函数的表达式。

- 最后一个参数是一个三元组，表示自由变量的范围。例如(x, 0, 5)


**选项**

- show 默认值设置为True。将 show 设置为False，函数将不会显示绘图。
- size 大小，一个 (width, height) 形式的元组，以英寸为单位，用于指定图形的整体尺寸。默认值设置为None，表示尺寸将由默认后端设置。
- adaptive 默认无自适应采样。如果需要自适应采样，请设置adaptive=True。
- markers 指定标记所需类型的字典列表。
- annotations 指定所需注释类型的字典列表。
- line_color 指定绘图的颜色
- axis_center 表示中心坐标的两个浮点数的元组或 {'center', 'auto'}
- title 绘图标题。如果绘图只有一个表达式，则将其设置为表达式的乳胶表示
- label 绘图中表达式的标签。调用时会用到legend。默认为表达式的名称。
- xlabel  x轴的标签
- ylabel y轴的标签
- xscale 设置 x 轴的缩放比例，'linear'或'log'
- yscale 设置 y 轴的缩放比例，'linear'或'log'
- xlim 表示 x 轴范围
- ylim 表示 y 轴范围
- rectangles 指定要绘制的矩形尺寸的字典列表。





#### 二维参数图: `plot_parametric`



```python
from sympy import plot_parametric, symbols, cos, sin
from sympy import pi

u = symbols('u')
plot_parametric((cos(u), sin(u)), (u, -5, 5))

plot_parametric((cos(u), sin(u)), (u, cos(u)), (u, -10, 10))

plot_parametric((cos(u), sin(u), (u, -5, 5)), (cos(u), u, (u, -5, 5)))


expr1 = (u, cos(2*pi*u)/2 + 1/2)
expr2 = (u, sin(2*pi*u)/2 + 1/2)
p = plot_parametric(expr1, expr2, (u, 0, 1), line_color='blue')
p[0].line_color = 'red'
p.show()

```

#### 二维隐式图和区域图: `plot_implicit`

绘图模块能够绘制一些二维几何体，例如线、圆和椭圆。不直接支持绘制多边形（多边形、正多边形、三角形）。类似地，plot_implicit()可用于根据其隐式方程绘制任何二维几何结构。

```python
from sympy import plot_implicit, symbols, Eq, And

x, y = symbols('x y')

plot_implicit(Eq(x**2+y**2, 4))

p1 = plot_implicit(Eq(x**2 + y**2, 5))

p2 = plot_implicit(Eq(x**2 + y**2, 3), (x, -3, 3), (y, -3, 3))

# 绘图区域
p6 = plot_implicit(y > x**2)

# 使用布尔连接进行绘图
p7 = plot_implicit(And(y > x, y > -x))

# 绘制单个变量（例如 y - 1）的表达式时，请明确指定 x 或 y 变量
p8 = plot_implicit(y - 1, y_var=y)
p9 = plot_implicit(x - 1, x_var=x)


```


#### 3D图线图: `plot3d`


```python
from sympy import symbols
from sympy.plotting import plot3d

x, y = symbols('x y')

plot3d(x*y, (x, -5, 5), (y, -5, 5))

plot3d(x*y, -x*y, (x, -5, 5), (y, -5, 5))

plot3d((x**2 + y**2, (x, -5, 5), (y, -5, 5)), (x*y, (x, -3, 3), (y, -3, 3)))

```


#### 参数定义的3D线图: `plot3d_parametric_line`

```python
from sympy import symbols, cos, sin
from sympy.plotting import plot3d_parametric_line

u = symbols('u')

plot3d_parametric_line(cos(u), sin(u), u, (u, -5, 5))

plot3d_parametric_line((cos(u), sin(u), u, (u, -5, 5)), (sin(u), u**2, u, (u, -5, 5)))

```


#### 参数定义的3D曲面图: `plot3d_parametric_surface`


```python
from sympy import symbols, cos, sin
from sympy.plotting import plot3d_parametric_surface

u, v = symbols('u v')
plot3d_parametric_surface(cos(u + v), sin(u - v), u - v, (u, -5, 5), (v, -5, 5))


```


#### 子图



```python
from sympy import symbols
from sympy.plotting import plot, plot3d, PlotGrid

x, y = symbols('x, y')

p1 = plot(x, x**2, x**3, (x, -5, 5))
p2 = plot((x**2, (x, -6, 6)), (x, (x, -5, 5)))
p3 = plot(x**3, (x, -5, 5))
p4 = plot3d(x*y, (x, -5, 5), (y, -5, 5))

# 在单行中垂直绘图
PlotGrid(2, 1, p1, p2)

# 在单行中水平绘图
PlotGrid(1, 3, p2, p3, p4)

# 以网格形式绘图
PlotGrid(2, 2, p1, p2, p3, p4)

```












