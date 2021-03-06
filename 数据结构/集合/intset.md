## 集合

## 整数集合
今天首先介绍一个比较神奇的集合，叫做整数集合。
Redis对于只包含整数的集合，形成了一种新的数据结构，叫做整数集合，用于保存int16_t\int32_t\int64_t的整数值。
当然它也保证了集合的性质，即集合中不会出现重复元素。

来看看它的代码定义：
```c
typedef struct intset{
    uint32_t encoding;//编码方式，包含int16_t,int32_t,int64_t
    uint32_t length;//集合长度
    int8_t contents[];//元素数组
}
```
其他都好理解，多说两句contents。contents就是整数集合的具体存储了，整数集合每一个元素，都是contents数组中的一项。
不知道大家有没有发现有个比较矛盾的地方，这个数据结构，既可以规定编码方式，它的存储又是int8_t的，什么鬼？
那如果我们编码成都定义成int16_t的，那元素到底是int8_t还是int16_t？

redis设计与实现给了解答：虽然人家这么定义，但实际上，数组类型还是取决于encoding属性值。

那intset修改查找操作怎么做呢？

- 对于修改，intset保持其一段空间有序。由于intset占用一段连续内存，所以每次修改数据需要重新申请空间，比如增加就是扩容，删除就是缩容。
- 对于查找，由于inset一段空间有序，因此可以执行二分查找算法。

看懂了这个数据结构，咱们迈入下一环。来看个面试题:
### 了解整数集合的升级吗？
当然了解。这名词虽然看的人很蒙圈，但是还是比较简单的。
通俗点说：原来元素都是int16_t，现在突然要添加一个int32_t的。
为了方便操作，老的用int16_t存的，重新转化成int32_t。

所以，在添加新元素，并且新元素的类型比现有整数集合类型长时，会触发升级，即把原始整数集合存放数据，转化成新元素类型。

## 普通集合
当用户往集合添加的元素，是str类型，或者确实是数字，但超过inset能存储的数字类型了，这时inset会转化为普通集合hashset。

既然提到了hashset，背过java八股文的同学肯定不陌生，java 的hashset其实就是hashmap，只不过就用了key项,value项就随便搞了个object对象。

不会redis也这么搞吧？

被你猜中了。

redis的hashset底层也由redis的hashmap实现。所以面试官如果想详细问redis的普通集合实现，大可把这篇文章背给他听，只不过最后多说一句，set也是按这方法实现的。

如果面试官问起普通集合，就按着这篇回答就OK了！




