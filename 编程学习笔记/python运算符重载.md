# Python

## 运算符重载
&emsp; 所谓**重载运算符(operator overloading)**，指的是在类中定义并实现一个与运算符对应的处理方法，这样当类对象在进行运算符操作是，系统就会条用类中相应的方法来处理。
&emsp; 也就是说，可以通过重载运算符来改变内置对象的行为，使得这些对象可以支持自定义的运算符操作，比如对类对象的+、-、×、÷等。
&emsp; python中可以重载运算符如下：
**算术运算符：+、-、*、/、//、%、** **
* **add**(self, other)：定义 + 运算符的行为
* **sub**(self, other)：定义 – 运算符的行为
* **mul**(self, other)：定义 * 运算符的行为
* **truediv**(self, other)：定义 / 运算符的行为
* **floordiv**(self, other)：定义 // 运算符的行为
* **mod**(self, other)：定义 % 运算符的行为
* **pow**(self, other)：定义 ** 运算符的行为

**位运算符：&、|、^、~、<<、>>**
* **and**(self, other)：定义 & 运算符的行为
* **or**(self, other)：定义 | 运算符的行为
* **xor**(self, other)：定义 ^ 运算符的行为
* **invert**(self)：定义 ~ 运算符的行为
* **lshift**(self, other)：定义 << 运算符的行为
* **rshift**(self, other)：定义 >> 运算符的行为

**比较运算符：<、<=、>、>=、==、!=**
* **lt**(self, other)：定义 < 运算符的行为
* **le**(self, other)：定义 <= 运算符的行为
* **gt**(self, other)：定义 > 运算符的行为
* **ge**(self, other)：定义 >= 运算符的行为
* **eq**(self, other)：定义 == 运算符的行为
* **ne**(self, other)：定义 != 运算符的行为

**逻辑运算符：and、or、not**
* **and**(self, other)：定义 and 运算符的行为
* **or**(self, other)：定义 or 运算符的行为
* **not**(self)：定义 not 运算符的行为

**赋值运算符：=**
* **eq**(self, other)：定义 = 运算符的行为