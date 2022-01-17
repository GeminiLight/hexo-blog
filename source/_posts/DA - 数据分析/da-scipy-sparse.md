---
title: 【SciPy】Sparse稀疏矩阵主要存储格式总结
author: Gemini向光性
date: 2020-08-18
categories: 
  - DA - 数据分析
tags:
  - Data Science
  - Python
  - SciPy
---

在数据科学和深度学习等领域常会采用矩阵格式来存储数据，但当矩阵较为庞大且非零元素较少时，运算效率和存储有效率并不高。所以，通常我们采用Sparse稀疏矩阵的方式来存储矩阵，提高存储和运算效率。下面将对SciPy中七种常见的存储方式（COO/ CSR/ CSC/ BSR/ DOK/ LIL/ DIA）的概念和用法进行介绍和对比总结。

<!-- more -->

## 稀疏矩阵简介

### 稀疏矩阵

| _numpy.sparse_ | 稀疏矩阵 | sparse matrix |
| --- | --- | --- |
| _numpy.ndarray_ | 密集矩阵 | array matrix |
| _numpy.matrix_ | 密集矩阵 | dense matrix |

- 稀疏矩阵
  - 具有少量非零项的矩阵 - **N**umber of **N**on-**Z**ero ([NNZ](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.spmatrix.getnnz.html)) < 0.5
  - （在矩阵中，若数值0的元素数目远多于非0元素的数目，并且非0元素分布没有规律）

- 矩阵的稠密度
  - 非零元素的总数比上矩阵所有元素的总数为矩阵的稠密度。

![[By Matt](https://matteding.github.io/2019/04/25/sparse-matrices/)](/resource/images/da-scipy-sparse-dense.gif)

### 压缩存储

存储矩阵的一般方法是采用二维数组，其优点是可以随机地访问每一个元素，因而能够容易实现矩阵的各种运算。  
对于稀疏矩阵，它通常具有很大的维度，有时甚大到整个矩阵（零元素）占用了绝大部分内存  
采用二维数组的存储方法既浪费大量的存储单元来存放零元素，又要在运算中浪费大量的时间来进行零元素的无效运算。因此必须考虑对稀疏矩阵进行压缩存储（只存储非零元素）。

```python
from scipy import sparse
help(sparse)

'''
Sparse Matrix Storage Formats
There are seven available sparse matrix types:

        1. csc_matrix: Compressed Sparse Column format
        2. csr_matrix: Compressed Sparse Row format
        3. bsr_matrix: Block Sparse Row format
        4. lil_matrix: List of Lists format
        5. dok_matrix: Dictionary of Keys format
        6. coo_matrix: COOrdinate format (aka IJV, triplet format)
        7. dia_matrix: DIAgonal format
        8. spmatrix: Sparse matrix base clas
'''
```

### 矩阵属性

```python
from scipy.sparse import csr_matrix

### 共有属性
mat.shape  # 矩阵形状
mat.dtype  # 数据类型
mat.ndim  # 矩阵维度
mat.nnz   # 非零个数
mat.data  # 非零值, 一维数组

### COO 特有的
coo.row  # 矩阵行索引
coo.col  # 矩阵列索引

### CSR\CSC\BSR 特有的
bsr.indices    # 索引数组
bsr.indptr     # 指针数组
bsr.has_sorted_indices  # 索引是否排序
bsr.blocksize  # BSR矩阵块大小
```

### 通用方法

```python
import scipy.sparse as sp

### 转换矩阵格式
tobsr()、tocsr()、to_csc()、to_dia()、to_dok()、to_lil()
mat.toarray()  # 转为array
mat.todense()  # 转为dense
# 返回给定格式的稀疏矩阵
mat.asformat(format)
# 返回给定元素格式的稀疏矩阵
mat.astype(t)  

### 检查矩阵格式
issparse、isspmatrix_lil、isspmatrix_csc、isspmatrix_csr
sp.issparse(mat)

### 获取矩阵数据
mat.getcol(j)  # 返回矩阵列j的一个拷贝，作为一个(mx 1) 稀疏矩阵 (列向量)
mat.getrow(i)  # 返回矩阵行i的一个拷贝，作为一个(1 x n)  稀疏矩阵 (行向量)
mat.nonzero()  # 非0元索引
mat.diagonal()   # 返回矩阵主对角元素
mat.max([axis])  # 给定轴的矩阵最大元素

### 矩阵运算
mat += mat     # 加
mat = mat * 5  # 乘
mat.dot(other)  # 坐标点积


resize(self, *shape)
transpose(self[, axes, copy])

```

---

## 稀疏矩阵分类

### COO - [coo_matrix](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.coo_matrix.html)

#### Coordinate Matrix 对角存储矩阵

- 采用三元组`(row, col, data)`(或称为ijv format)的形式来存储矩阵中非零元素的信息
- 三个数组 `row` 、`col` 和 `data` 分别保存非零元素的行下标、列下标与值（一般长度相同）
- 故 `coo[row[k]][col[k]] = data[k]` ，即矩阵的第 `row[k]` 行、第 `col[k]` 列的值为 `data[k]` 

![ ](/resource/images/da-scipy-sparse-coo.gif)

- 当 `row[0] = 1` , `column[0] = 1` 时， `data[0] = 2` ，故 `coo[1][1] = 2`
- 当 `row[3] = 0` , `column[3] = 2` 时， `data[3] = 9` ，故 `coo[0][3] = 9`

#### 适用场景

- 主要用来创建矩阵，因为coo_matrix无法对矩阵的元素进行增删改等操作
- 一旦创建之后，除了将之转换成其它格式的矩阵，几乎无法对其做任何操作和矩阵运算

#### 优缺点

##### ①优点

- 转换成其它存储格式很快捷简便（`tobsr()`、`tocsr()`、`to_csc()`、`to_dia()`、`to_dok()`、`to_lil()`）
- 能与CSR / CSC格式的快速转换
- 允许重复的索引（例如在1行1列处存了值2.0，又在1行1列处存了值3.0，则转换成其它矩阵时就是2.0+3.0=5.0）

##### ②缺点

- 不支持切片和算术运算操作
- 如果稀疏矩阵**仅包含非0元素的对角线**，则对角存储格式(DIA)可以减少非0元素定位的信息量
- 这种存储格式对有限元素或者有限差分离散化的矩阵尤其有效

#### 实例化方法

- `coo_matrix(D)`：D代表密集矩阵；
- `coo_matrix(S)`：S代表其他类型稀疏矩阵
- `coo_matrix((M, N), [dtype])`：构建一个shape为M*N的空矩阵，默认数据类型是`d`，
- `coo_matrix((data, (i, j)), [shape=(M, N)]))`：三元组初始化 
  - `i[:]` : 行索引
  - `j[:]` : 列索引
  - `A[i[k], j[k]]=data[k]`

#### 特殊属性

- `data`：稀疏矩阵存储的值，是一个一维数组
- `row`：与`data`同等长度的一维数组，表征`data`中每个元素的行号
- `col`：与`data`同等长度的一维数组，表征`data`中每个元素的列号

#### 代码示例

```python
# 数据
row = [0, 1, 2, 2]
col = [0, 1, 2, 3]
data = [1, 2, 3, 4]

# 生成coo格式的矩阵
# <class 'scipy.sparse.coo.coo_matrix'>
coo_mat = sparse.coo_matrix((data, (row, col)), shape=(4, 4),  dtype=np.int)

# coordinate-value format
print(coo)
'''
(0, 0)        1
(1, 1)        2
(2, 2)        3
(3, 3)        4
'''

# 查看数据
coo.data
coo.row
coo.col

# 转化array
# <class 'numpy.ndarray'>
coo_mat.toarray()
'''
array([[1, 0, 0, 0],
       [0, 2, 0, 0],
       [0, 0, 3, 4],
       [0, 0, 0, 0]])
'''
```

### CSR - [csr_matrix](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.csr_matrix.html)

#### Compressed Sparse Row Matrix 压缩稀疏行格式

- csr_matrix是按行对矩阵进行压缩的
- 通过 `indices`,  `indptr`，`data` 来确定矩阵。
  - `data` 表示矩阵中的非零数据
  - 对于第 `i` 行而言，该行中非零元素的列索引为 `indices[indptr[i]:indptr[i+1]]`
  - 可以将 `indptr` 理解成利用其自身索引 `i` 来指向第 `i` 行元素的列索引
  - 根据`[indptr[i]:indptr[i+1]]`，我就得到了该行中的非零元素个数，如
    - 若 `index[i] = 3` 且 `index[i+1] = 3` ，则第 `i` 行的没有非零元素
    - 若 `index[j] = 6` 且 `index[j+1] = 7` ，则第 `j` 行的非零元素的列索引为 `indices[6:7]`
  - 得到了行索引、列索引，相应的数据存放在： `data[indptr[i]:indptr[i+1]]`

![ ](/resource/images/da-scipy-sparse-csr.gif)

- 对于矩阵第 `0` 行，我们需要先得到其非零元素列索引
  - 由 `indptr[0] = 0` 和 `indptr[1] = 2` 可知，第 `0` 行有两个非零元素。
  - 它们的列索引为 `indices[0:2] = [0, 2]` ，且存放的数据为 `data[0] = 8` ， `data[1] = 2` 
  - 因此矩阵第 `0` 行的非零元素 `csr[0][0] = 8` 和 `csr[0][2] = 2` 
- 对于矩阵第 `4` 行，同样我们需要先计算其非零元素列索引
  - 由 `indptr[4] = 3` 和 `indptr[5] = 6` 可知，第 `4` 行有3个非零元素。
  - 它们的列索引为 `indices[3:6] = [2, 3，4]` ，且存放的数据为 `data[3] = 7` ，`data[4] = 1` ，`data[5] = 2`
  - 因此矩阵第 `4` 行的非零元素 `csr[4][2] = 7` ， `csr[4][3] = 1` 和 `csr[4][4] = 2`

#### 适用场景

- 常用于读入数据后进行稀疏矩阵计算，运算高效

#### 优缺点

##### ①优点

- 高效的稀疏矩阵算术运算
- 高效的行切片
- 快速地矩阵矢量积运算

##### ②缺点

- 较慢地列切片操作（可以考虑CSC）
- 转换到稀疏结构代价较高（可以考虑LIL，DOK）

#### 实例化

- `csr_matrix(D)`：D代表密集矩阵；
- `csr_matrix(S)`：S代表其他类型稀疏矩阵
- `csr_matrix((M, N), [dtype])`：构建一个shape为M*N的空矩阵，默认数据类型是`d`，
- `csr_matrix((data, (row_ind, col_ind)), [shape=(M, N)]))`
  - 三者关系：`a[row_ind[k], col_ind[k]] = data[k]`
- `csr_matrix((data, indices, indptr), [shape=(M, N)])`
  - 第i行的列索引存储在其中`indices[indptr[i]:indptr[i+1]]`
  - 其对应值存储在中`data[indptr[i]:indptr[i+1]]`

#### 特殊属性

- `data` ：稀疏矩阵存储的值，一维数组
- `indices` ：存储矩阵有有非零值的列索引
- `indptr` ：类似指向列索引的指针数组
- `[has_sorted_indices](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.bsr_matrix.has_sorted_indices.html#scipy.sparse.bsr_matrix.has_sorted_indices)`：索引 `indices` 是否排序

```python
# 生成数据
indptr = np.array([0, 2, 3, 3, 3, 6, 6, 7])
indices = np.array([0, 2, 2, 2, 3, 4, 3])
data = np.array([8, 2, 5, 7, 1, 2, 9])

# 创建矩阵
csr = sparse.csr_matrix((data, indices, indptr))

# 转为array
csr.toarray()
'''
array([[1, 0, 2],
       [0, 0, 3],
       [4, 5, 6]])
'''

# 按row行来压缩
# 对于第i行，非0数据列是indices[indptr[i]:indptr[i+1]] 数据是data[indptr[i]:indptr[i+1]]
# 在本例中
# 第0行，有非0的数据列是indices[indptr[0]:indptr[1]] = indices[0:2] = [0,2]
# 数据是data[indptr[0]:indptr[1]] = data[0:2] = [1,2],所以在第0行第0列是1，第2列是2
# 第1行，有非0的数据列是indices[indptr[1]:indptr[2]] = indices[2:3] = [2]
# 数据是data[indptr[1]:indptr[2] = data[2:3] = [3],所以在第1行第2列是3
# 第2行，有非0的数据列是indices[indptr[2]:indptr[3]] = indices[3:6] = [0,1,2]
# 数据是data[indptr[2]:indptr[3]] = data[3:6] = [4,5,6],所以在第2行第0列是4，第1列是5,第2列是6
```

### CSC - [csc_matrix](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.csc_matrix.html)

#### Compressed Sparse Column Matrix 压缩稀疏列矩阵

- csc_matrix是按列对矩阵进行压缩的
- 通过 `indices`,  `indptr`，`data` 来确定矩阵，可以对比CSR
  - `data` 表示矩阵中的非零数据
  - 对于第 `i` 列而言，该行中非零元素的行索引为`indices[indptr[i]:indptr[i+1]]`
  - 可以将 `indptr` 理解成利用其自身索引 `i` 来指向第 `i` 列元素的列索引
  - 根据`[indptr[i]:indptr[i+1]]`，我就得到了该行中的非零元素个数，如
    - 若 `index[i] = 1` 且 `index[i+1] = 1` ，则第 `i` 列的没有非零元素
    - 若 `index[j] = 4` 且 `index[j+1] = 6` ，则第 `j`列的非零元素的行索引为 `indices[4:6]`
  - 得到了列索引、行索引，相应的数据存放在： `data[indptr[i]:indptr[i+1]]`

![ ](/resource/images/da-scipy-sparse-csc.gif)

- 对于矩阵第 `0` 列，我们需要先得到其非零元素行索引
  - 由 `indptr[0] = 0` 和 `indptr[1] = 1` 可知，第 `0`列行有1个非零元素。
  - 它们的行索引为 `indices[0:1] = [0]` ，且存放的数据为 `data[0] = 8`
  - 因此矩阵第 `0` 行的非零元素 `csc[0][0] = 8`
- 对于矩阵第 `3` 列，同样我们需要先计算其非零元素行索引
  - 由 `indptr[3] = 4` 和 `indptr[4] = 6` 可知，第 `4` 行有2个非零元素。
  - 它们的行索引为 `indices[4:6] = [4, 6]` ，且存放的数据为 `data[4] = 1` ，`data[5] = 9`
  - 因此矩阵第 `i` 行的非零元素 `csr[4][3] = 1` ， `csr[6][3] = 9`

#### 应用场景

参考CSR

#### 优缺点

对比参考CSR

#### 实例化

- `csc_matrix(D)`：D代表密集矩阵；
- `csc_matrix(S)`：S代表其他类型稀疏矩阵
- `csc_matrix((M, N), [dtype])`：构建一个shape为M*N的空矩阵，默认数据类型是`d`，
- `csc_matrix((data, (row_ind, col_ind)), [shape=(M, N)]))`
  - 三者关系：`a[row_ind[k], col_ind[k]] = data[k]`
- `csc_matrix((data, indices, indptr), [shape=(M, N)])`
  - 第i列的列索引存储在其中`indices[indptr[i]:indptr[i+1]]`
  - 其对应值存储在中`data[indptr[i]:indptr[i+1]]`

#### 特殊属性

- `data` ：稀疏矩阵存储的值，一维数组
- `indices` ：存储矩阵有有非零值的行索引
- `indptr` ：类似指向列索引的指针数组
- `[has_sorted_indices](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.bsr_matrix.has_sorted_indices.html#scipy.sparse.bsr_matrix.has_sorted_indices)`：索引 `indices` 是否排序

```python
# 生成数据
row = np.array([0, 2, 2, 0, 1, 2])
col = np.array([0, 0, 1, 2, 2, 2])
data = np.array([1, 2, 3, 4, 5, 6])

# 创建矩阵
csc = sparse.csc_matrix((data, (row, col)), shape=(3, 3)).toarray()

# 转为array
csc.toarray()
'''
array([[1, 0, 4],
       [0, 0, 5],
       [2, 3, 6]], dtype=int64)
'''

# 按col列来压缩
# 对于第i列，非0数据行是indices[indptr[i]:indptr[i+1]] 数据是data[indptr[i]:indptr[i+1]]
# 在本例中
# 第0列，有非0的数据行是indices[indptr[0]:indptr[1]] = indices[0:2] = [0,2]
# 数据是data[indptr[0]:indptr[1]] = data[0:2] = [1,2],所以在第0列第0行是1，第2行是2
# 第1行，有非0的数据行是indices[indptr[1]:indptr[2]] = indices[2:3] = [2]
# 数据是data[indptr[1]:indptr[2] = data[2:3] = [3],所以在第1列第2行是3
# 第2行，有非0的数据行是indices[indptr[2]:indptr[3]] = indices[3:6] = [0,1,2]
# 数据是data[indptr[2]:indptr[3]] = data[3:6] = [4,5,6],所以在第2列第0行是4，第1行是5,第2行是6
```

### BSR - [bsr_matrix](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.bsr_matrix.html)

#### Block Sparse Row Matrix 分块压缩稀疏行格式

- 基于行的块压缩，与csr类似，都是通过`data`，`indices`，`indptr`来确定矩阵
- 与csr相比，只是data中的元数据由0维的数变为了一个矩阵（块），其余完全相同
- 块大小 `blocksize`
  - 块大小 `(R, C)` 必须均匀划分矩阵 `(M, N)` 的形状。
  - R和C必须满足关系：`M % R = 0` 和 `N % C = 0`

#### 应用场景

参考CSR

#### 优缺点

##### ①优点

- 与csr很类似
- 更适合于适用于具有密集子矩阵的稀疏矩阵
- 在某些情况下比csr和csc计算更高效。

#### 实例化

- `bsr_matrix(D)`：D代表密集矩阵；
- `bsr_matrix(S)`：S代表其他类型稀疏矩阵
- `bsr_matrix((M, N), [blocksize =(R,C), [dtype])`：构建一个shape为M*N的空矩阵，默认数据类型是`d`，
- `(data, ij), [blocksize=(R,C), shape=(M, N)]`
  - 两者关系：`a[ij[0,k], ij[1,k]] = data[k]]`
- `bsr_matrix((data, indices, indptr), [shape=(M, N)])`
  - 第i行的块索引存储在其中`indices[indptr[i]:indptr[i+1]]`
  - 其相应块值存储在中`data[indptr[i]:indptr[i+1]]`

#### 特殊**属性**

- `data` ：稀疏矩阵存储的值，一维数组
- `indices` ：存储矩阵有有非零值的列索引
- `indptr` ：类似指向列索引的指针数组
- `blocksize` ：矩阵的块大小
- `[has_sorted_indices](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.bsr_matrix.has_sorted_indices.html#scipy.sparse.bsr_matrix.has_sorted_indices)`：索引 `indices` 是否排序

#### 代码示例

```python
# 生成数据
indptr = np.array([0,2,3,6])
indices = np.array([0,2,2,0,1,2])
data = np.array([1,2,3,4,5,6]).repeat(4).reshape(6,2,2)

# 创建矩阵
bsr = bsr_matrix((data, indices, indptr), shape=(6,6)).todense()

# 转为array
bsr.todense()
matrix([[1, 1, 0, 0, 2, 2],
        [1, 1, 0, 0, 2, 2],
        [0, 0, 0, 0, 3, 3],
        [0, 0, 0, 0, 3, 3],
        [4, 4, 5, 5, 6, 6],
        [4, 4, 5, 5, 6, 6]])
```

### DOK- [dok_matrix](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.dok_matrix.html)

#### Dictionary of Keys Matrix 按键字典矩阵

- 采用字典来记录矩阵中不为0的元素
- 字典的 `key` 存的是记录元素的位置信息的元组， `value` 是记录元素的具体值

#### 适用场景

- 逐渐添加矩阵的元素

#### 优缺点

##### ①优点

- 对于递增的构建稀疏矩阵很高效，比如定义该矩阵后，想进行每行每列更新值，可用该矩阵。
- 可以高效访问单个元素，只需要O(1)

##### ②缺点

- 不允许重复索引（coo中适用），但可以很高效的转换成coo后进行重复索引

#### 实例化方法

- `dok_matrix(D)`：D代表密集矩阵；
- `dok_matrix(S)`：S代表其他类型稀疏矩阵
- `dok_matrix((M, N), [dtype])`：构建一个shape为M*N的空矩阵，默认数据类型是`d`，

#### 代码示例

```python
dok = sparse.dok_matrix((5, 5), dtype=np.float32)
for i in range(5):
    for j in range(5):
        dok[i,j] = i+j    # 更新元素

# zero elements are accessible
dok[(0, 0)]  # = 0

dok.keys()
# {(0, 0), ..., (4, 4)}

dok.toarray()
'''
[[0. 1. 2. 3. 4.]
 [1. 2. 3. 4. 5.]
 [2. 3. 4. 5. 6.]
 [3. 4. 5. 6. 7.]
 [4. 5. 6. 7. 8.]]
 '''
```

### LIL - [lil_matrix](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.lil_matrix.html)

#### Linked List Matrix 链表矩阵

- 使用两个列表存储非0元素data
- rows保存非零元素所在的列
- 可以使用列表赋值来添加元素，如 `lil[(0, 0)] = 8` 

![ ](/resource/images/da-scipy-sparse-lil.gif)

- `lil[(0, -1)] = 4` ：第0行的最后一列元素为4
- `lil[(4, 2)] = 5` ：第4行第2列的元素为5

#### 适用场景

- 适用的场景是逐渐添加矩阵的元素（且能快速获取行相关的数据）
- 需要注意的是，该方法插入一个元素最坏情况下可能导致线性时间的代价，所以要确保对每个元素的索引进行预排序

#### 优缺点

##### ①优点

- 适合递增的构建成矩阵
- 转换成其它存储方式很高效
- 支持灵活的切片

##### ②缺点

- 当矩阵很大时，考虑用coo
- 算术操作，列切片，矩阵向量内积操作慢

#### 实例化方法

- `lil_matrix(D)`：D代表密集矩阵；
- `lil_matrix(S)`：S代表其他类型稀疏矩阵
- `lil_matrix((M, N), [dtype])`：构建一个shape为M*N的空矩阵，默认数据类型是`d`

#### 特殊属性

- **`data`**：存储矩阵中的非零数据
- **`rows`**：存储每个非零元素所在的列（行信息为列表中索引所表示）

#### 代码示例

```python
# 创建矩阵
lil = sparse.lil_matrix((6, 5), dtype=int)

# 设置数值
# set individual point
lil[(0, -1)] = -1
# set two points
lil[3, (0, 4)] = [-2] * 2
# set main diagonal
lil.setdiag(8, k=0)

# set entire column
lil[:, 2] = np.arange(lil.shape[0]).reshape(-1, 1) + 1

# 转为array
lil.toarray()
'''
array([[ 8,  0,  1,  0, -1],
       [ 0,  8,  2,  0,  0],
       [ 0,  0,  3,  0,  0],
       [-2,  0,  4,  8, -2],
       [ 0,  0,  5,  0,  8],
       [ 0,  0,  6,  0,  0]])
'''

# 查看数据
lil.data
'''
array([list([0, 2, 4]), list([1, 2]), list([2]), list([0, 2, 3, 4]),
       list([2, 4]), list([2])], dtype=object)
'''
lil.rows
'''
array([[list([8, 1, -1])],
       [list([8, 2])],
       [list([3])],
       [list([-2, 4, 8, -2])],
       [list([5, 8])],
       [list([6])]], dtype=object)
'''
```

### DIA - [dia_matrix](https://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.dia_matrix.html)

#### Diagonal Matrix 对角存储格式

- dia_matrix通过两个数组确定： `data` 和 `offsets` 
  - `data` ：对角线元素的值
  - `offsets` ：第 `i` 个 `offsets` 是当前第 `i` 个对角线和主对角线的距离
  - `data[k:]` 存储了 `offsets[k]` 对应的对角线的全部元素

![ ](/resource/images/da-scipy-sparse-dia.gif)

- 当 `offsets[0] = 0` 时，表示该对角线即是主对角线，相应的值为 `[1, 2, 3, 4, 5]` 
- 当 `offsets[2] = 2` 时，表示该对角线为主对角线向上偏移2个单位，相应的值为 `[11, 12, 13, 14, 15]`
  - 但该对角线上元素仅有三个 ，于是采用先出现的元素无效的原则
  - 即前两个元素对构造矩阵无效，故该对角线上的元素为 `[13, 14, 15]` 

#### 适用场景

- 最适合对角矩阵的存储方式

#### 实例化方法

- `dia_matrix(D)`：D代表密集矩阵；
- `dia_matrix(S)`：S代表其他类型稀疏矩阵
- `dia_matrix((M, N), [dtype])`：构建一个shape为M*N的空矩阵，默认数据类型是`d`，
- `dia_matrix((data, offsets)), [shape=(M, N)]))`：
  - `data[k,:]` 存储着对角偏移量为 `offset[k]` 的对角值

#### 特殊属性

- `data`：存储DIA对角值的数组
- `offsets`：存储DIA对角偏移量的数组

#### 代码示例

```python
# 生成数据
data = np.array([[1, 2, 3, 4], [5, 6, 0, 0], [0, 7, 8, 9]])
offsets = np.array([0, -2, 1])

# 创建矩阵
dia = sparse.dia_matrix((data, offsets), shape=(4, 4))

# 查看数据
dia.data
'''
array([[[1 2 3 4]
        [5 6 0 0]
        [0 7 8 9]])
'''

# 转为array
dia.toarray()
'''
array([[1 7 0 0]
       [0 2 8 0]
       [5 0 3 9]
       [0 6 0 4]])
'''
```

---

## 矩阵格式对比

|  | **COO** | **DOK** | **LIL** | **CSR** | **CSC** | **BSR** | **DIA** | **Dense** |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **indexing** | no | yes | yes | yes | yes | no† | no | yes |
| **write-only** | yes | yes | yes | no | no | no | no | yes |
| **read-only** | no | no | no | yes | yes | yes | yes | yes |
| **low memory** | yes | no | no | yes | yes | yes | yes | no |
| **PyData sparse** | yes | yes | no | no | no | no | no | n/a |

---

## 稀疏矩阵存取

### 存储 - `save_npz`

```python
# 存储为npz文件
scipy.sparse.save_npz('sparse_matrix.npz', sparse_matrix)
```

### 读取 - `load_npz`

```python
# 从npz文件中读取
mat = sparse.load_npz('./data/npz/test_x.npz')
```

### 存储大小比较

```python
a = np.arange(100000).reshape(1000,100)
a[10: 300] = 0
b = sparse.csr_matrix(a)

# 稀疏矩阵压缩存储到npz文件
sparse.save_npz('b_compressed.npz', b, True)  # 文件大小：100KB

# 稀疏矩阵不压缩存储到npz文件
sparse.save_npz('b_uncompressed.npz', b, False)  # 文件大小：560KB

# 存储到普通的npy文件
np.save('a.npy', a)  # 文件大小：391KB

# 存储到压缩的npz文件
np.savez_compressed('a_compressed.npz', a=a)  # 文件大小：97KB• 1
```

对于存储到npz文件中的CSR格式的稀疏矩阵，内容为：

```shell
data.npy
format.npy
indices.npy
indptr.npy
shape.npy
```

## 参考

> [Sparse matrices (scipy.sparse)](https://docs.scipy.org/doc/scipy/reference/sparse.html)  
> [Sparse Matrices](https://matteding.github.io/2019/04/25/sparse-matrices/)  
> [python稀疏矩阵的存储与表示](https://blog.csdn.net/winycg/article/details/80967112)  
> [python scipy 稀疏矩阵详解](https://blog.csdn.net/jeffery0207/article/details/100064602)  
> [SciPy教程 - 稀疏矩阵库scipy.sparse](https://blog.csdn.net/pipisorry/article/details/41762945)  