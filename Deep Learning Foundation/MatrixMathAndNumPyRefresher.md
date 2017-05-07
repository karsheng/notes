# Matrix Math and NumPy Refresher
## Data Dimension
1. Scalars - have zero dimension
1. Vectors - have one dimension (row vector or column vector)
1. Matrices - have two dimensions
1. Tensors - have more than 2 i.e. n-dimensions
1. Bear in mind math indices usually start counting from 1, but programming from 0.

## Data in Numpy
### Scalar
1. `ndarray` objects can have any number of dimension and support fast math operation and can be used to represent any data types i.e. scalars, vectors, matrices, and tensors.
1. Numpy array that holds a scalar:
```
s = np.array(5)
```
### Vector
1. To create vector, pass a Python list to the `array` function:
```
v = np.array([1,2,3])
```
1. In this case `v.shape` would return `(3,)`
1. Python doesn’t understand (3) as a tuple with one item, so it requires the comma.
1. `x` equals to 2  when `x = v[1]`
1. `v[1:]` returns `[2, 3]`
### Matrices
1. To create matrices, do `m = np.array([[1,2,3], [4,5,6], [7,8,9]])`
1. `shape` would return `(3,3)`
1. `m[1][2]` returns 6
### Tensors
1. To create a 3 x 3 x 2 x 1 tensor,
```
t = np.array([[[[1],[2]],[[3],[4]],[[5],[6]]],[[[7],[8]],\
    [[9],[10]],[[11],[12]]],[[[13],[14]],[[15],[16]],[[17],[17]]]])
```
1. `t.shape` would return `(3,3,2,1)`
1. `t[2][1][1][0]` would return 16.
## Element-wise Matrix Operations
1. Treat items in the matrix individually and perform the same operation on each one
## Element-wise Operations in NumPy
1. For example:
```
values = [1,2,3,4,5]
values = np.array(values) + 5

# now values is an ndrray that holds [6,7,8,9,10]
```  
1. NumPy has functions for things like adding, multiplying, etc. But it also supports using the standard math operations:
```
x = np.multiply(some_array, 5)
x = some_array * 5

# Both are equivalent
```
1. Setting all values to zero for an `ndarrays`:
```
m *= 0

# now every element in m is zero, no matter how many dimensions it has
```
1. Make sure items being perform the operation on have compatible shapes.
```
a = np.array([[1,3],[5,7]])
a
# displays the following result:
# array([[1, 3],
#        [5, 7]])

b = np.array([[2,4],[6,8]])
b
# displays the following result:
# array([[2, 4],
#        [6, 8]])

a + b
# displays the following result
#      array([[ 3,  7],
#             [11, 15]])
```
## Matrix Multiplication
1. You will get error when shapes are incompatible: `ValueError: operands could not be broadcast together with shapes (2,2) (3,3)`
1. Matrix multiplication <> Matrix dot product.
1. Matrix dot product: Number of rows on the right matrix must be equal to number of columns on the right matrix
1. Order matters in matrix dot multiplication: A.B <> B.A
1. Standard multiplication is commutative i.e. order doesn't matter.
1. The answer matrix always has the same number of rows as the left matrix and the same number of columns as the right matrix.
1. Data in the left matrix should be arranged as rows., while data in the right matrix should be arranged as columns.
## NumPy Matrix Multiplication
1. Matrix product - NumPy's [`matmul`](https://docs.scipy.org/doc/numpy/reference/generated/numpy.matmul.html#numpy.matmul) is very similar to [`dot`](https://docs.scipy.org/doc/numpy/reference/generated/numpy.dot.html).
```
a = np.array([[1,2,3,4],[5,6,7,8]])
a
# displays the following result:
# array([[1, 2, 3, 4],
#        [5, 6, 7, 8]])
a.shape
# displays the following result:
# (2, 4)

b = np.array([[1,2,3],[4,5,6],[7,8,9],[10,11,12]])
b
# displays the following result:
# array([[ 1,  2,  3],
#        [ 4,  5,  6],
#        [ 7,  8,  9],
#        [10, 11, 12]])
b.shape
# displays the following result:
# (4, 3)

c = np.matmul(a, b)
c
# displays the following result:
# array([[ 70,  80,  90],
#        [158, 184, 210]])
c.shape
# displays the following result:
# (2, 3)
```
```
a = np.array([[1,2],[3,4]])
a
# displays the following result:
# array([[1, 2],
#        [3, 4]])

np.dot(a,a)
# displays the following result:
# array([[ 7, 10],
#        [15, 22]])

a.dot(a)  # you can call `dot` directly on the `ndarray`
# displays the following result:
# array([[ 7, 10],
#        [15, 22]])

np.matmul(a,a)
# array([[ 7, 10],
#        [15, 22]])
```
## Matrix Transposes
1. Matrix transpose - a matrix with the same values as the original, but it has the rows and columns switched.
1. Depending on how you store data in rows and columns, you might need to use transpose sometimes to get the right answer.
1. The only time you can safely use a transpose in a matrix multiplication is if the data in both of your original matrices is arranged as rows
## Transposes in NumPy
1. use `T` or `transpose()` to transpose an array in NumPy
1. NumPy tranposes without actually moving any data in memory - it simply changes the way it indexes the original matrix - so it’s quite efficient.
1. However, that also means you need to be careful with how you modify objects, because they are sharing the same data. For example, with the same matrix m from above, let's make a new variable m_t that stores m's transpose. Then look what happens if we modify a value in m_t:
```
m_t = m.T
m_t[3][1] = 200
m_t
# displays the following result:
# array([[ 1,   5, 9],
#        [ 2,   6, 10],
#        [ 3,   7, 11],
#        [ 4, 200, 12]])

m
# displays the following result:
# array([[ 1,  2,  3,   4],
#        [ 5,  6,  7, 200],
#        [ 9, 10, 11,  12]])
```
Notice how it modified both the transpose and the original matrix, too! That's because they are sharing the same copy of data. So remember to consider the transpose just as a different view of your matrix, rather than a different matrix entirely.
1. 
