# Python CheetSheet

## [Python 标准库](https://docs.python.org/zh-cn/3/library/index.html#library-index)

### 内置函数



6 个标准的数据类型：Number、String、List、Tuple、Set、Dictionary


## Fluent Python

### Python 数据模型

#### 一摞有序的纸牌

```python
import collections

Card = collections.namedtuple('Card', ['rank', 'suit'])

class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JKQA')
    suits = 'spades diamonds clubs hearts'.split()
    
    def __init__(self):
        self._cards = [Card(rank, suit) for suit in suits
                                        for rank in ranks]
    
    def __len__(self):
        return len(self._cards)

    def __getitem__(self, position):
        return self._cards[position]

suit_values = dict(spades=3, hearts=2, diamonds=1, clubs=0)
def spades_high(card):
    rank_value = FrenchDeck.ranks.index(card.rank)
    return rank_value * len(suit_values) + suit_values[card.suit]

```

#### 如何使用特殊方法

```python
from math import hypot

class Vector:
    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

    def __repr__(self):
        return 'Vector(%r, %r)' % (self.x, self.y)

    def __abs__(self):
        return hypot(self.x, self.y)

    def __bool__(self):
        return bool(abs(self))

    def __add__(self, other):
        x = self.x + other.x
        y = self.y + other.y
        return Vector(x, y)

    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)
```


#### 特殊方法一览

> [Python 语言参考 - 数据模型](https://docs.python.org/zh-cn/3/reference/datamodel.html#)

跟运算符无关的特殊方法：

|类别|方法名|
|:---:|:----:|
|字符串 / 字节序列表示形式| \_\_repr\_\_、\_\_str\_\_、\_\_format\_\_、\_\_bytes\_\_|
|数值转换| \_\_abs\_\_、\_\_bool\_\_、\_\_complex\_\_、\_\_int\_\_、\_\_float\_\_、\_\_hash\_\_、\_\_index\_\_|
|集合模拟| \_\_len\_\_、\_\_getitem\_\_、\_\_setitem\_\_、\_\_delitem\_\_、\_\_contains\_\_|
|迭代枚举| \_\_iter\_\_、\_\_reversed\_\_、\_\_next\_\_|
|可调用模拟| \_\_call\_\_|
|上下文管理| \_\_enter\_\_、\_\_exit\_\_|
|实例创建和销毁| \_\_new\_\_、\_\_init\_\_、\_\_del\_\_|
|属性管理| \_\_getattr\_\_、\_\_getattribute\_\_、\_\_setattr\_\_、\_\_delattr\_\_、\_\_dir\_\_|
|属性描述符| \_\_get\_\_、\_\_set\_\_、\_\_delete\_\_|
|跟类相关的服务| \_\_prepare\_\_、\_\_instancecheck\_\_、\_\_subclasscheck\_\_|

跟运算符相关的特殊方法：

|类别|方法名和对应的运算符|
|:--:|:----:|
|一元运算符| \_\_neg\_\_ -、\_\_pos\_\_ +、\_\_abs\_\_ abs()|
|众多比较运算符| \_\_lt\_\_ <、\_\_le\_\_ <=、\_\_eq\_\_ ==、\_\_ne\_\_ !=、\_\_gt\_\_ >、\_\_ge\_\_ >=|
|算术运算符| \_\_add\_\_ +、\_\_sub\_\_ -、\_\_mul\_\_ *、\_\_truediv\_\_ /、\_\_floordiv\_\_ //、\_\_mod\_\_ %、\_\_divmod\_\_ divmod()、\_\_pow\_\_ ** 或pow()、\_\_round\_\_ round()|
|反向算术运算符| \_\_radd\_\_、\_\_rsub\_\_、\_\_rmul\_\_、\_\_rtruediv\_\_、\_\_rfloordiv\_\_、\_\_rmod\_\_、\_\_rdivmod\_\_、\_\_rpow\_\_|
|增量赋值算术运算符| \_\_iadd\_\_、\_\_isub\_\_、\_\_imul\_\_、\_\_itruediv\_\_、\_\_ifloordiv\_\_、\_\_imod\_\_、\_\_ipow\_\_|
|位运算符|\_\_invert\_\_ ~、\_\_lshift\_\_ <<、\_\_rshift\_\_ >>、\_\_and\_\_ &、\_\_or\_\_ \|、\_\_xor\_\_ ^|
|反向位运算符| \_\_rlshift\_\_、\_\_rrshift\_\_、\_\_rand\_\_、\_\_rxor\_\_、\_\_ror\_\_|
|增量赋值位运算符|\_\_ilshift\_\_、\_\_irshift\_\_、\_\_iand\_\_、\_\_ixor\_\_、\_\_ior\_\_|

### 序列构成的数组

#### 内置序列类型概览

- 容器序列：list、tuple 和 collections.deque 这些序列能存放不同类型的数据。

- 扁平序列：str、bytes、bytearray、memoryview 和 array.array，这类序列只能容纳一种类型。

- 可变序列：list、bytearray、array.array、collections.deque 和 memoryview。

- 不可变序列：tuple、str 和 bytes。

![](https://raw.githubusercontent.com/inspiringz/leetcode/main/image/MutableSequence.png)

#### 列表推导和生成器表达式

虽然也可以用列表推导来初始化元组、数组或其他序列类型，但是生成器表达式是更好的选择。这是因为生成器表达式背后遵守了迭代器协议，可以逐个地产出元素，而不是先建立一个完整的列表，然后再把这个列表传递到某个构造函数里。这种方式显然能够节省内存。


```python
>>> symbols = '$¢£¥€¤'
>>> beyond_ascii = [ord(s) for s in symbols if ord(s) > 127] # 列表推导
>>> beyond_ascii
[162, 163, 165, 8364, 164]
>>> beyond_ascii = list(filter(lambda c: c > 127, map(ord, symbols))) # filter map
>>> beyond_ascii
[162, 163, 165, 8364, 164]
>>> tuple(ord(symbol) for symbol in symbols) # 生成器表达式
(36, 162, 163, 165, 8364, 164)
>>> import array
>>> array.array('I', (ord(symbol) for symbol in symbols)) 
array('I', [36, 162, 163, 165, 8364, 164])
```

#### 元组不仅仅是不可变的列表

元组其实是对数据的记录：元组中的每个元素都存放了记录中一个字段的数据，外加这个字段的位置。正是这个位置信息给数据赋予了意义。



