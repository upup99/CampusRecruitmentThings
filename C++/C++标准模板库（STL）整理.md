## c++标准模板库（STL）整理

### 1. vector

>  **“长度根据需要而自动改变的数组”**

- 定义：`vector<typename> name; vector<vector<int>> name2;`
- 容器内的访问：
  - 通过下标
  - 通过迭代器访问：`for(auto it = v.begin(); it != v.end(); it++ ) printf("%d", *it);`
- vector常用函数：
  - **push_back()**：在vector后面添加一个元素x，时间复杂度为o(1)。
  - **pop_back()**：删除vector的尾元素，时间复杂度为o(1)。
  - **size()**：获得vector中元素的个数，时间复杂度为o(1)。
  - **clear()**：用来清空vector中的所有元素，时间复杂度为o(N)。
  - **insert()**：insert(it, x)用来向vector的任意迭代器it（这是一个指针变量）处插入一个元素x，时间复杂度为o(N)。
  - **erase()**：它有两种用法：
    - 删除单个元素，erase(it)即删除迭代器为it处的元素；
    - 删除一段区间内的所有元素，erase(first, last)即删除[first, last)的所有元素，参数也是指针变量。
- vector常见用途：
  - 储存数据：在一些元素不确定的情况下可以很好节省空间，还有在一些场合需要根据条件把部分数据输出在同一行，最后不要空格，可以用vector数组先记录，然后一次性输出。
  - 用邻接表存储图

### 2. set

> **”内部自动有序且不含重复元素的容器“**

- 定义：`set<typename> name;`   如果typename是一个容器，注意在定义时记得在>>符号之间加上空格，有些编译器会把它视为移位操作。
- set容器内元素的访问：只能通过迭代器`for(auto it = v.begin(); it != v.end(); it++ ) printf("%d", *it);`
- set常用函数：
  - **insert()**：insert(x)可将x插入set容器中，自动实现排序和去重，o(logN)。
  - **find()**：find(value)，返回set中对应值为value的容器，o(logN)。
  - **erase()**：它有两种用法：
    - 删除单个元素，st.erase(it)即删除迭代器为it处的元素，*st.erase(value)，value为要删除的元素*；
    - 删除一段区间内的所有元素，erase(first, last)即删除[first, last)的所有元素，参数也是指针变量。
  - **size()**：获得vector中元素的个数，时间复杂度为o(1)。
  - **clear()**：用来清空set中的所有元素，时间复杂度为o(N)。
- set的常见用途：最主要的作用就是自动去重并按升序排序。

### 3. string

> **“对字符串常用需求进行了封装”**

- 定义：`string str; string str1 = "abcdef";` 要读入输出string类，只能使用cin，cout。
- 容器内的访问：
  - 通过下标
  - 通过迭代器访问：`for(auto it = v.begin(); it != v.end(); it++ ) printf("%d", *it);`
- string常用函数：
  - **operator +=**：可以直接用+号把两个string拼接起来。
  - **compare operator**：两个string类型可以直接使用==、!=、<、等比较大小，比较规则是字典序。
  - **length()/size()**：返回长度。
  - **insert()**：string的insert函数有很多写法
    - **insert(pos, string)**：在pos号位置插入字符串string。
    - **insert(it, it2, it3)**：it为原字符串的欲插入迭代器位置，it2和it3为待插入字符串的首尾迭代器，用来表示串[it2, it3)将被插入在it的位置上。
  - **erase()**：它有两种用法：
    - 删除单个元素，str.erase(it)即删除迭代器为it处的元素；
    - 删除一段区间内的所有元素，str.erase(first, last)即删除[first, last)的所有元素，参数也是指针变量。**str.erase(pos, length)，在起始位置pos删除length个字符**。
  - **clear()**：用来清空string中的所有元素，时间复杂度为o(N)。
  - **substr()**：`str.substr(pos, len)`返回从pos号位开始，长度为len的子串。
  - **find()**：
    - `str.find(str2)`，当str2是str的子串时，返回其在str中第一次出现的位置；如果不是，返回string::npos。
    - `str.find(str2, pos)`，从str的pos位开始匹配str2，返回值同上。o(nm)。
  - **replace()**：
    - `str.replace(pos, len, str2)`把str从pos位开始、长度为len的子串替换为str2。
    - `str.replace(it1, it2, str2)`把str迭代器[it1, it2)的子串替换为str2。

### 4. map

> **“可以将任何基本类型映射到任何基本类型”**

- 定义：`map<typename1, typename2> mp;`
- 容器内的访问：
  - 通过下标`map[typename1] == typename2`
  - 通过迭代器访问:it->first 键，it->second 值
- map常用函数：
  - **find()**：`find(key)`返回键为key的映射的*迭代器*。
  - **erase()**：它有两种用法：
    - `mp.erase(it)`即删除迭代器为it处的元素。
    - `mp.erase(key)`，key为欲删除的映射的键。
    - 删除一段区间内的所有元素，mp.erase(first, last)即删除[first, last)的所有元素，参数也是指针变量。
  - **size()**：用来获得map中映射的对数。
  - **clear()**：用来清空map中的所有元素，时间复杂度为o(N)。
- map的常见用途：
  - 需要建立字符（或字符串）与整数之间的映射，可以用map减少代码量。
  - 判断大整数或者其他类型数据是否存在，可以把map当bool用。
  - 字符串与字符串的映射。

### 5. queue

> **“有队列的常用操作封装”**

- 定义：`queue<typename> name;`
- 容器内的访问：只能通过front()，back()访问队首，队尾元素。
- queue常用函数：
  - **push()**：入队。
  - **pop()**：出队。
  - **empty()**：检测queue是否为空，返回true则空，false则非空。
  - **size()**：返回queue内元素的个数。
- queue的常见用途：广度优先搜索。

### 6. stack

> **“有栈的常用操作封装”**

- 定义：`stack<typename> name;`
- 容器内的访问：只能通过top()访问栈顶元素。
- stack常见函数：
  - **push()**：入栈。
  - **top()**：取栈顶。
  - **pop()**：出栈，弹出栈顶元素。
  - **empty()**：检测stack是否为空，返回true则空，false则非空。
  - **size()**：返回stack内元素的个数。
- stack的常见用途：模拟实现一些递归，例如DFS。

### 7. algorithm头文件下的常用函数

- **max()、min()、abs()**：前两个函数参数是两个。
- **swap()**：交换两个数的值。
- **reverse()**：reverse(it, it2)可以将数组指针在[it, it2)之间的元素或容器的迭代器在[it, it2)范围内的元素进行反转。
- **fill()**：和memset不同，这里的赋值可以是数组类型对应范围中的任意值。
- **sort()**：sort(首元素地址，尾地址的下一个元素，比较函数)。对于结构体的排序要记住。
- **lower_bound()和upper_bound()**：需要用在一个有序数组或容器中。
  - `lower_bound(first, last, val)`用来寻找数组或容器的[first, last)范围内**第一个值大于等于val的元素的值**，如果是数组，则返回该位置的指针，若是容器，则返回该位置的迭代器。
  - `upper_bound(first, last, val)`用来寻找数组或容器的[first, last)范围内**第一个值大于val的元素的值**，如果是数组，则返回该位置的指针，若是容器，则返回该位置的迭代器。

