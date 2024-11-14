# Java Basic Grammar

```markdown
**1. Java数据类型有哪些？**

- 基本数据类型
  - 整数值型：byte(1个字节)，short(2个字节)，int(4个字节)，long(8个字节)
  - 字符型：char(2个字节)
  - 浮点类型：float(4个字节)，double (8个字节)
  - 布尔型：boolean(没有明确说明)
- 引用数据类型
类，接口，数组

**2. 什么是自动拆装箱？**

Java语言的特性，指的是基本数据类型和引用数据类型可以自动互相转换。

**3. Object类有哪些方法？**
- hashCode():返回对象的哈希码
- getClass(): 返回对象运行时的类
- equals()
- toString()

**4. == 和 equals() 的区别**

== 比较的是值，对于基本数据类型比较值，对于引用数据类型比较内存地址
equals比较的是内容，本质上是对引用数据类型比较内存地址的修正
比如String比较的是具体的字符串而不是内存地址

**5. hashCode是什么？**

每个对象都可以计算出一个hashCode, 它是对象的唯一ID

**6. hashCode有什么作用？**

提高集合的查找效率，通过计算对象hashCode然后快速查找

**7. 关于hash冲突**
两个不同的输入值经过函数计算后得到相同的值。
修正：用equals方法做二次确认

**8. 为什么重写equals必须重写hashCode**

保持一致性，不能equals相等，hashCode不一样

**9. String 能被继承吗 为什么用final 修饰**

- final修饰，不能被继承
- 安全性考虑，String有native修饰的方法，直接调用操作系统，开放继承权限的话有一定安全隐患。

**10. String, StringBuffer, StringBuilder区别**

- 可变性
String不可变，数组用final修饰，另外两个是可变的

- 线程安全
String final修饰所以线程安全
StringBuffer 对方法加了同步锁，所以线程安全
StringBuilder是线程不安全

**11. String equals和Object equals区别**

String的equals是被重写过的，比较值，后者比较引用地址

**12. 字符串常量池 String abc = new String("abc"); 创建几个对象**

● 确认常量池有没有，没有就会创建
● 然后创建一个字符串对象

**13. &和&&区别？|和||区别？**

&&，|| ：左右条件均需参加运算
& ，| ：短路，一方false或true结束运算

**14. 值传递和引用传递**

值传递：传递副本，函数无法改变原始对象
引用传递：直接传递地址，函数可以改变原始对象

Java是值传递，对于对象来说，传递的是对象的栈上的复制地址，原地址和新地址都指向堆的同一个对象

**15. final、finally、finalize区别**
final: 不可变。用于修饰类，变量，方法。修饰后禁止继承和重写
finally: try catch一起使用，即使发生异常，依然执行finally中的代码
finalize: Object类中的方法，允许对象在被垃圾回收之前执行一些清理操作（如资源释放之类）

**16. 遇到过哪些运行时异常**
- 空指针
- 非法参数
- 类型转换错误
- 内存溢出

**17. 关于序列化和反序列化**
对象和字节流之间的转换

目的：用于对象持久化和跨网络传输。（rpc远程调用）

**18. Get和Post区别**

- 数据量Post更大
- 语义，Get表示数据获取，Post表示数据的修改和删除

**19. Cookie和Session区别**
- Cookie存在客户端的，Session存在服务端
- Session可以存一些更加敏感的数据，Cookie通常存一些用户可见的东西（权限）
```