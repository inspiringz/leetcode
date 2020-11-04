# Python CheetSheet

## [Python 标准库](https://docs.python.org/zh-cn/3/library/index.html#library-index)

### 内置函数



6 个标准的数据类型：Number、String、List、Tuple、Set、Dictionary


## Fluent Python

### 1. Python 数据模型

#### 1.1 一摞有序的纸牌

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

#### 1.2 如何使用特殊方法

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


#### 1.3 特殊方法一览

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

### 2. 序列构成的数组

#### 2.1 内置序列类型概览

- 容器序列：list、tuple 和 collections.deque 这些序列能存放不同类型的数据。

- 扁平序列：str、bytes、bytearray、memoryview 和 array.array，这类序列只能容纳一种类型。

- 可变序列：list、bytearray、array.array、collections.deque 和 memoryview。

- 不可变序列：tuple、str 和 bytes。

![](https://raw.githubusercontent.com/inspiringz/leetcode/main/image/MutableSequence.png)

#### 2.2 列表推导和生成器表达式

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

#### 2.3 元组不仅仅是不可变的列表

元组其实是对数据的记录：元组中的每个元素都存放了记录中一个字段的数据，外加这个字段的位置。正是这个位置信息给数据赋予了意义。

```python
lax_coordinates = (33.9425, -118.408056)
latitude, longitude = lax_coordinates # 元组拆包
t = (20, 8) # * 运算符把一个可迭代对象拆开作为函数的参数
quotient, remainder = divmod(*t) # (2, 4)
```

在元组拆包中使用 `*` 也可以帮助我们把注意力集中在元组的部分元素上：

```py
>>> a, *body, c, d = range(5)
>>> a, body, c, d
(0, [1, 2], 3, 4)
>>> *head, b, c, d = range(5)
>>> head, b, c, d
([0, 1], 2, 3, 4)
```

嵌套元组拆包：

```py
metro_areas = [
 ('Tokyo','JP',36.933,(35.689722,139.691667)),
 ('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)),
 ('Mexico City', 'MX', 20.142, (19.433333, -99.133333)),
 ('New York-Newark', 'US', 20.104, (40.808611, -74.020386)),
 ('Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)),
]
print('{:15} | {:^9} | {:^9}'.format('', 'lat.', 'long.'))
fmt = '{:15} | {:9.4f} | {:9.4f}'
for name, cc, pop, (latitude, longitude) in metro_areas:
    if longitude <= 0:
    print(fmt.format(name, latitude, longitude))
```
```
                | lat.     | long.
Mexico City     | 19.4333  | -99.1333
New York-Newark | 40.8086  | -74.0204
Sao Paul        | -23.5478 | -46.6358
```

#### 2.4 具名元组

`collections.namedtuple` 是一个工厂函数，它可以用来构建一个带字段名的元组和一个有名字的类——这个带名字的类对调试程序有很大帮助。

创建一个具名元组需要两个参数，一个是类名，另一个是类的各个字段的名字。后者可以是由数个字符串组成的可迭代对象，或者是由空格分隔开的字段名组成的字符串。

```py
>>> from collections import namedtuple
>>> City = namedtuple('City', 'name country population coordinates')
>>> tokyo = City('Tokyo', 'JP', 36.933, (35.689722, 139.691667))
>>> tokyo
City(name='Tokyo', country='JP', population=36.933, coordinates=(35.689722,
139.691667))
>>> tokyo.population
36.933
>>> tokyo.coordinates
(35.689722, 139.691667)
>>> tokyo[1]
'JP'
```

除了从普通元组那里继承来的属性之外，具名元组还有一些自己专有的属性:

- `_fields` 属性是一个包含这个类所有字段名称的元组。
- 用 `_make()` 通过接受一个可迭代对象来生成这个类的一个实例，它的作用跟 City(*delhi_data) 是一样的。
- `_asdict()` 把具名元组以 collections.OrderedDict 的形式返回，我们可以利用它来把元组里的信息友好地呈现出来。

#### 2.5 作为不可变列表的元组

除了跟增减元素相关的方法之外，元组支持列表的其他所有方法。还有一个例外，元组没有 `__reversed__` 方法，但是这个方法只是个优化而已，reversed(my_tuple) 这个用法在没有 `__reversed__` 的情况下也是合法的。

列表或元组的方法和属性：

| | 列表 | 元组 ||
|:-:|:--:|:----:|:----:|
|s.\_\_add\_\_(s2)|√|√|s + s2，拼接|
|s.\_\_iadd\_\_(s2)|√||s += s2，就地拼接|
|s.append(e)|√||在尾部添加一个新元素|
|s.clear()|√||删除所有元素|
|s.\_\_contains\_\_(e)|√|√|s 是否包含 e|
|s.copy()|√||列表的浅复制|
|s.count(e)|√|√| e 在 s 中出现的次数|
|s.\_\_delitem\_\_(p)|√||把位于 p 的元素删除|
|s.extend(it)|√||把可迭代对象 it 追加给 s|
|s.\_\_getitem\_\_(p) |√|√| s\[p\]，获取位置 p 的元素|
|s.\_\_getnewargs\_\_()||√| 在 pickle 中支持更加优化的序列化|
|s.index(e) |√|√| 在 s 中找到元素 e 第一次出现的位置|
|s.insert(p, e)|√||在位置 p 之前插入元素e|
|s.\_\_iter\_\_() |√|√| 获取 s 的迭代器|
|s.\_\_len\_\_() |√|√| len(s)，元素的数量|
|s.\_\_mul\_\_(n) |√|√| s * n，n 个 s 的重复拼接|
|s.\_\_imul\_\_(n)|√||s *= n，就地重复拼接|
|s.\_\_rmul\_\_(n) |√|√| n * s，反向拼接 *|
|s.pop([p])|√||删除最后或者是（可选的）位于 p 的元素，并返回它的值|
|s.remove(e)|√||删除 s 中的第一次出现的 e|
|s.reverse()|√||就地把 s 的元素倒序排列|
|s.\_\_reversed\_\_()|√||返回 s 的倒序迭代器|
|s.\_\_setitem\_\_(p, e)|√||s[p] = e，把元素 e 放在位置p，替代已经在那个位置的元素|
|s.sort(\[key\],\[reverse\])|√||就地对 s 中的元素进行排序，可选的参数有键（key）和是否倒序（reverse）|

#### 2.6 切片


在切片和区间操作里不包含区间范围的最后一个元素是 Python 的风格，这个习惯符合Python、C 和其他语言里以 0 作为起始下标的传统。

`s[a:b:c]`: 对 s 在 a 和 b 之间以 c 为间隔取值。c 的值还可以为负，负值意味着反向取值。

切片赋值：如果赋值的对象是一个切片，那么赋值语句的右侧必须是个可迭代对象。即便只有单独一个值，也要把它转换成可迭代的序列。

```python
>>> l = list(range(10))
>>> l
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> l[2:5] = [20, 30]
>>> l
[0, 1, 20, 30, 5, 6, 7, 8, 9]
>>> del l[5:7]
>>> l
[0, 1, 20, 30, 5, 8, 9]
>>> l[3::2] = [11, 22]
>>> l
[0, 1, 20, 11, 5, 22, 9]
```

- Python Tutor（[http://www.pythontutor.com](http://www.pythontutor.com)）是一个对 Python 运行原理进行可视化分析的工具。

#### 2.7 list.sort 方法和内置函数 sorted

list.sort 方法会就地排序列表，也就是说不会把原列表复制一份。这也是这个方法的返回值是 None 的原因，提醒你本方法不会新建一个列表。在这种情况下返回 None 其实
是 Python 的一个惯例：如果一个函数或者方法对对象进行的是就地改动，那它就应该返回 None，好让调用者知道传入的参数发生了变动，而且并未产生新的对象。例如，random.shuffle 函数也遵守了这个惯例。

与 list.sort 相反的是内置函数 `sorted`，它会新建一个列表作为返回值。这个方法可以接受任何形式的可迭代对象作为参数，甚至包括不可变序列或生成器（见第 14 章）。而不管 sorted 接受的是怎样的参数，它最后都会返回一个列表。

不管是 list.sort 方法还是 sorted 函数，都有两个可选的关键字参数。

- reverse

    如果被设定为 True，被排序的序列里的元素会以降序输出（也就是说把最大值当作最小值来排序）。这个参数的默认值是 False。

- key

    一个只有一个参数的函数，这个函数会被用在序列里的每一个元素上，所产生的结果将是排序算法依赖的对比关键字。比如说，在对一些字符串排序时，可以用 key=str.lower 来实现忽略大小写的排序，或者是用 key=len 进行基于字符串长度的排序。这个参数的默认值是恒等函数（identity function），也就是默认用元素自己的值来排序。

#### 2.8 用 bisect 来管理已排序的序列

[bisect 模块](https://docs.python.org/zh-cn/3/library/bisect.html)包含两个主要函数，`bisect` 和 `insort`，两个函数都利用二分查找算法来在有序序列中查找或插入元素。

**bisect(haystack, needle)** 在 haystack（干草垛）里搜索 needle（针）的位置，该位置满足的条件是，把 needle 插入这个位置之后，haystack 还能保持升序。也就是在
说这个函数返回的位置前面的值，都小于或等于 needle 的值。其中 haystack 必须是一个有序的序列。你可以先用 bisect(haystack, needle) 查找位置 index，再用 haystack.insert(index, needle) 来插入新值。但你也可用 insort 来一步到位，并且后者的速度更快一些。

```py
import bisect
import sys

HAYSTACK = [1, 4, 5, 6, 8, 12, 15, 20, 21, 23, 23, 26, 29, 30]
NEEDLES = [0, 1, 2, 5, 8, 10, 22, 23, 29, 30, 31]
ROW_FMT = '{0:2d} @ {1:2d} {2}{0:<2d}'

def demo(bisect_fn):
    for needle in reversed(NEEDLES):
        position = bisect_fn(HAYSTACK, needle)
        offset = position * ' |'
        print(ROW_FMT.format(needle, position, offset))

if __name__ == '__main__':
    if sys.argv[-1] == 'left':
        bisect_fn = bisect.bisect_left
    else:
        bisect_fn = bisect.bisect
    print('DEMO:', bisect_fn.__name__)
    print('haystack ->', ' '.join('%2d' % n for n in HAYSTACK))
    demo(bisect_fn)
```

```
DEMO: bisect_right
haystack ->  1  4  5  6  8 12 15 20 21 23 23 26 29 30
31 @ 14  | | | | | | | | | | | | | |31
30 @ 14  | | | | | | | | | | | | | |30
29 @ 13  | | | | | | | | | | | | |29
23 @ 11  | | | | | | | | | | |23
22 @  9  | | | | | | | | |22
10 @  5  | | | | |10
 8 @  5  | | | | |8 
 5 @  3  | | |5 
 2 @  1  |2 
 1 @  1  |1 
 0 @  0 0 
```

根据一个分数找出对应的成绩：

```py
>>> def grade(score, breakpoints=[60, 70, 80, 90], grades='FDCBA'):
...     i = bisect.bisect(breakpoints, score)
...     return grades[i]
...
>>> [grade(score) for score in [33, 99, 77, 70, 89, 90, 100]]
['F', 'A', 'C', 'C', 'B', 'A', 'A']
```

排序很耗时，因此在得到一个有序序列之后，我们最好能够保持它的有序。 bisect.insort 就是为了这个而存在的。

**insort(seq, item)** 把变量 item 插入到序列 seq 中，并能保持 seq 的升序顺序。

```py
import bisect
import random

SIZE=7
random.seed(1729)

my_list = []
for i in range(SIZE):
    new_item = random.randrange(SIZE*2)
    bisect.insort(my_list, new_item)
    print('%2d ->' % new_item, my_list)
```

```
10 -> [10]
 0 -> [0, 10]
 6 -> [0, 6, 10]
 8 -> [0, 6, 8, 10]
 7 -> [0, 6, 7, 8, 10]
 2 -> [0, 2, 6, 7, 8, 10]
10 -> [0, 2, 6, 7, 8, 10, 10]
```

#### 2.9 当列表不是首选时

##### 2.9.1 数组

如果我们需要一个只包含数字的列表，那么 array.array 比 list 更高效。数组支持所有跟可变序列有关的操作，包括 `.pop`、`.insert` 和 `.extend`。另外，数组还提供从文件读取和存入文件的更快的方法，如 `.frombytes` 和 `.tofile`。

```py
>>> from array import array
>>> from random import random
>>> floats = array('d', (random() for i in range(10**7)))
>>> floats[-1]
0.07802343889111107
>>> fp = open('floats.bin', 'wb')
>>> floats.tofile(fp)
>>> fp.close()
>>> floats2 = array('d')
>>> fp = open('floats.bin', 'rb')
>>> floats2.fromfile(fp, 10**7)
>>> fp.close()
>>> floats2[-1]
0.07802343889111107
>>> floats2 == floats
True
```

列表和数组的属性和方法：

||列表|数组||
|:-:|:-:|:-:|:-:|
|s.\_\_add(s2)\_\_|√|√|s + s2，拼接|
|s.\_\_iadd(s2)\_\_|√|√|s += s2，就地拼接|
|s.append(e)|√|√|在尾部添加一个元素|
|s.byteswap||√|翻转数组内每个元素的字节序列，转换字节序|
|s.clear()|√||删除所有元素|
|s.\_\_contains\_\_(e)|√|√|s 是否含有 e|
|s.copy()|√||对列表浅复制|
|s.\_\_copy\_\_()||√|对 copy.copy 的支持|
|s.count(e)|√|√|s 中 e 出现的次数|
|s.\_\_deepcopy\_\_()||√|对 copy.deepcopy 的支持|
|s.\_\_delitem\_\_(p)|√|√|删除位置 p 的元素|
|s.extend(it)|√|√|将可迭代对象 it 里的元素添加到尾部|
|s.frombytes(b)||√|将压缩成机器值的字节序列读出来添加到尾部|
|s.fromfile(f, n)||√|将二进制文件 f 内含有机器值读出来添加到尾部，最多|添|加 n 项|
|s.fromlist(l)||√|将列表里的元素添加到尾部，如果其中任何一个元素导致了 TypeError 异常，那么所有的添加都会取消|
|s.\_\_getitem\_\_(p)|√|√|s[p]，读取位置 p 的元素|
|s.index(e)|√|√|找到 e 在序列中第一次出现的位置|
|s.insert(p, e)|√|√|在位于 p 的元素之前插入元素 e|
|s.itemsize||√|数组中每个元素的长度是几个字节|
|s.\_\_iter\_\_()|√|√|返回迭代器|
|s.\_\_len\_\_()|√|√|len(s)，序列的长度|
|s.\_\_mul\_\_(n)|√|√|s * n，重复拼接|
|s.\_\_imul\_\_(n)|√|√|s *= n，就地重复拼接|
|s.\_\_rmul\_\_(n)|√|√|n * s，反向重复拼接*|
|s.pop(\[p\])|√|√|删除位于 p 的值并返回这个值，p 的默认值是最后一个元素|的|位置|
|s.remove(e)|√|√|删除序列里第一次出现的 e 元素|
|s.reverse()|√|√|就地调转序列中元素的位置|
|s.\_\_reversed\_\_()|√||返回一个从尾部开始扫描元素的迭代器|
|s.\_\_setitem\_\_(p,e)|√|√|s\[p\] = e，把位于 p 位置的元素替换成 e|
|s.sort(\[key\],\[revers\])|√||就地排序序列，可选参数有 key 和 reverse|
|s.tobytes()||√|把所有元素的机器值用 bytes 对象的形式返回|
|s.tofile(f)||√|把所有元素以机器值的形式写入一个文件|
|s.tolist()||√|把数组转换成列表，列表里的元素类型是数字对象|
|s.typecode||√|返回只有一个字符的字符串，代表数组元素在 C 语言中的类型|

从 Python 3.4 开始，数组类型不再支持诸如 list.sort() 这种就地排序方法。要给数组排序的话，得用 sorted 函数新建一个数组：

```py
a = array.array(a.typecode, sorted(a))
```

想要在不打乱次序的情况下为数组添加新的元素，bisect.insort 还是能派上用场。

##### 2.9.2 双向队列和其他形式的队列

利用 `.append` 和 `.pop` 方法，我们可以把列表当作栈或者队列来用（比如，把 `.append` 和 `.pop(0)` 合起来用，就能模拟栈的 “先进先出” 的特点）。但是删除列表的第一个元素（亦或是在第一个元素之前添加一个元素）之类的操作是很耗时的，因为这些操作会牵扯到移动列表里的所有元素。

`collections.deque` 类（双向队列）是一个线程安全、可以快速从两端添加或者删除元素的数据类型。而且如果想要有一种数据类型来存放 **最近用到的几个元素**，deque 也是一个很好的选择。这是因为在新建一个双向队列的时候，你可以指定这个队列的大小，如果这个队列满员了，还可以从反向端删除过期的元素，然后在尾端添加新的元素。

```py
>>> from collections import deque
>>> dq = deque(range(10), maxlen=10)
>>> dq
deque([0, 1, 2, 3, 4, 5, 6, 7, 8, 9], maxlen=10)
>>> dq.rotate(3) # 旋转操作 n > 0 右边 n 个元素移动到左边
>>> dq
deque([7, 8, 9, 0, 1, 2, 3, 4, 5, 6], maxlen=10)
>>> dq.rotate(-4) # n < 0 左边 n 个元素移动到右边
>>> dq
deque([1, 2, 3, 4, 5, 6, 7, 8, 9, 0], maxlen=10)
>>> dq.appendleft(-1) # len(d) == d.maxlen 删除头部元素
>>> dq
deque([-1, 1, 2, 3, 4, 5, 6, 7, 8, 9], maxlen=10)
>>> dq.extend([11, 22, 33]) # 挤掉头部的元素
>>> dq
deque([3, 4, 5, 6, 7, 8, 9, 11, 22, 33], maxlen=10)
>>> dq.extendleft([10, 20, 30, 40]) # extendleft(iter) 方法会把迭代器里的元素逐个添加到双向队列的左边，，因此迭代器里的元素会逆序出现在队列里。
>>> dq
deque([40, 30, 20, 10, 3, 4, 5, 6, 7, 8], maxlen=10)
```
**append 和 popleft 都是原子操作，也就说是 deque 可以在多线程程序中安全地当作先进先出的栈使用，而使用者不需要担心资源锁的问题。**

列表和双向队列的方法：

||列表|双端队列||
|:-:|:-:|:-:|:-:|
|s.\_\_add\_\_(s2) |√|| s + s2，拼接|
|s.\_\_iadd\_\_(s2) |√|√| s += s2，就地拼接|
|s.append(e) |√|√| 添加一个元素到最右侧（到最后一个元素之后）|
|s.appendleft(e)||√| 添加一个元素到最左侧（到第一个元素之前）|
|s.clear() |√|√| 删除所有元素|
|s.\_\_contains\_\_(e) |√|| s 是否含有 e|
|s.copy() |√|| 对列表浅复制|
|s.\_\_copy\_\_() ||√| 对 copy.copy（浅复制）的支持|
|s.count(e) |√|√| s 中 e 出现的次数|
|s.\_\_delitem\_\_(p) |√|√| 把位置 p 的元素移除|
|s.extend(i) |√|√| 将可迭代对象 i 中的元素添加到尾部|
|s.extendleft(i) ||√| 将可迭代对象 i 中的元素添加到头部|
|s.\_\_getitem\_\_(p) |√|√| s\[p\]，读取位置 p 的元素|
|s.index(e) |√|| 找到 e 在序列中第一次出现的位置|
|s.insert(p, e) |√|| 在位于 p 的元素之前插入元素 e|
|s.\_\_iter\_\_() |√|√| 返回迭代器|
|s.\_\_len\_\_() |√|√| len(s)，序列的长度|
|s.\_\_mul\_\_(n) |√|| s * n，重复拼接|
|s.\_\_imul\_\_(n) |√|| s *= n，就地重复拼接|
|s.\_\_rmul\_\_(n) |√|| n * s，反向重复拼接*|
|s.pop() |√|√| 移除最后一个元素并返回它的值#|
|s.popleft() ||√| 移除第一个元素并返回它的值|
|s.remove(e) |√|√| 移除序列里第一次出现的 e 元素|
|s.reverse() |√|√| 调转序列中元素的位置|
|s.\_\_reversed\_\_() |√|√| 返回一个从尾部开始扫描元素的迭代器|
|s.rotate(n) ||√| 把 n 个元素从队列的一端移到另一端|
|s.\_\_setitem\_\_(p, e) |√|√| s\[p\] = e，把位于 p 位置的元素替换成 e|
|s.sort(\[key\], \[revers\]) |√|| 就地排序序列，可选参数有 key 和 reverse|

**\#** a_list.pop(p) 这个操作只能用于列表，双向队列的这个方法不接收参数。

除了 deque 之外，还有些其他的 Python 标准库也有对队列的实现。

- queue

    提供了同步（线程安全）类 Queue、LifoQueue 和 PriorityQueue，不同的线程可以利用这些数据类型来交换信息。这三个类的构造方法都有一个可选参数 maxsize，它接收正整数作为输入值，用来限定队列的大小。但是在满员的时候，这些类不会扔掉旧的元素来腾出位置。相反，如果队列满了，它就会被锁住，直到另外的线程移除了某个元素而腾出了位置。这一特性让这些类很适合用来控制活跃线程的数量。

- multiprocessing

    这个包实现了自己的 Queue，它跟 queue.Queue 类似，是设计给进程间通信用的。同时还有一个专门的 multiprocessing.JoinableQueue 类型，可以让任务管理变得更方便。

- asyncio

    Python 3.4 新提供的包，里面有 Queue、LifoQueue、PriorityQueue 和
JoinableQueue，这些类受到 queue 和 multiprocessing 模块的影响，但是为异步编程里的任务管理提供了专门的便利。

- heapq

    跟上面三个模块不同的是，heapq 没有队列类，而是提供了 heappush 和 heappop方法，让用户可以把可变序列当作堆队列或者优先队列来使用。


### 3. 字典和集合