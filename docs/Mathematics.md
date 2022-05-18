# Mathematics

## Linear Algebra

- 行列式（Determinant）： 行列式是对矩阵表按一定规则进行运算之后所得到的一个数值。

- 迹（Trace）：矩阵主对角元素的和，且迹与特征值的和相等。

- 特征值（Eigenvalue）：假设我们有一个n阶的矩阵A以及一个实数λ，使得一个非零向量x,满足Ax=λx，如果存在，则称λ为矩阵A的特征值，向量则为矩阵A的特征向量。所有特征值的和等于矩阵A主对角元之和trace(A)所有特征值的积等于矩阵A行列式的值det(A)。

- 反对称矩阵（Symmetric Matrix）：向量的叉乘可以用反对称矩阵表示，用矩阵的乘法来表示叉乘。

- SVD分解：将矩阵A(m×n)拆成一个U(m×m)S(m×n)V(n×n)三个矩阵，其中S为奇异值矩阵，奇异值是特征值的平方根，V是特征向量组成的矩阵的转置。

- 相似矩阵：设A，B为数域P上两个n阶矩阵，如果可以找到数域P上的n阶可逆矩阵X，使得

  ![img](https://bkimg.cdn.bcebos.com/formula/6ef3f6735efa701c37c85618105d896d.svg)

   则称A相似于B，记作A~B。
  
- 雅可比矩阵（Jacobian Matrix）：在向量微积分中，雅可比矩阵是一阶偏导数以一定方式排列成的矩阵，其行列式称为雅可比行列式。

- 协方差（Covariance）：Cov(X, Y)=E(X-E(x))×E(Y-E(Y))

- 协方差矩阵（Covariance Matrix）:Xn和Yn之间的两两协方差构成的对称矩阵为协方差矩阵。
