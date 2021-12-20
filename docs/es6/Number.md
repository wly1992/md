## ES6 数值 <!-- {docsify-ignore} -->

常量

> Number.EPSILON

Number.EPSILON 属性表示 1 与大于 1 的最小浮点数之间的差。

它的值接近于 2.2204460492503130808472633361816E-16，或者 2-52。

测试数值是否在误差范围内:

```
0.1 + 0.2 === 0.3; // false
// 在误差范围内即视为相等
equal = (Math.abs(0.1 - 0.3 + 0.2) < Number.EPSILON); // true
```

### Number 对象新方法

> Number.isFinite()

#### 从全局移植到 Number 对象的方法

> Number.parseInt() Number.parseFloat()  Number.isNaN()

### Math 对象的扩展

1. `Math.cbrt`用于计算一个数的立方根。

2. `Math.hypot`用于计算所有参数的平方和的平方根。

3. `Math.clz32` 用于返回数字的32 位无符号整数形式的前导0的个数。

4. `Math.trunc`用于返回数字的整数部分。

5. `Math.fround`用于获取数字的32位单精度浮点数形式。

6. `Math.sign`判断数字的符号（正、负、0）。

7. `Math.expm1()`用于计算 e 的 x 次方减 1 的结果，即 Math.exp(x) - 1 。

8. `Math.log1p(x)`用于计算1 + x 的自然对数，即 Math.log(1 + x) 。

9. `Math.log10(x)`用于计算以 10 为底的 x 的对数。

10. `Math.log2()`用于计算 2 为底的 x 的对数。

#### 指数运算符

> **

```
// 右结合，从右至左计算
2 ** 2 ** 3; // 256
// **=
let exam = 2;
exam ** = 2; // 4
```