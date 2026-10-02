# 3. 三个左 Weyl 组合、initial part 与次数上界 18

本目录从原 `build_weighted_bound.sing` 的有界最低权消元过程重建三个算子，
不是仅抄录最终有理数。原文件保持不变；本目录独立输入将空间变量
`a, Da` 改名为 `z, Dz`。此处不涉及乘积算子；外层统一记乘积为 `f`。

在本目录作为工作目录执行：

```sh
Singular --no-rc -q 3_initial_term18.sing
Singular --no-rc -q check_paper_formulas.sing
Singular -q check_degree_bound.sing
```

依赖为 Singular/Plural 和 `dmodideal.lib`，以及本目录的
`inputs/annfs.sing`。主文件已直接包含 `weightInitial`、`initialWeight`，
不再引用 `weight_tools.sing`；后者仍供独立的 `check_degree_bound.sing` 使用。
不再读取预存算子作对照。
第一条命令生成或覆盖 `operators.sing`，后两条命令在新的进程中读取该生成结果。
`check_paper_formulas.sing` 逐字录入论文 Section 3 的有理数公式，检查三个展开及
全部左组合系数与重新构造的结果相等；不只检查预存算子自己与自己相等。
可把整个目录复制到独立运行目录后执行；没有绝对路径依赖。
不要并发写同一个运行目录的 `operators.sing`。

## 构造与独立检查

`3_initial_term18.sing` 在真实 Weyl 代数中维护二元组
`(P, (C_j))`，每次同步执行 `P <- P - lambda*T*P'` 和
`C_j <- C_j - lambda*T*C'_j`，所有系数均从左边相乘。
有界消元只是发现算法，不宣称计算出权 Gröbner 基。
随后精确验证 `P_i = sum_j C_ij*B_j` 及 initial part。
输出保存三个算子、三个左系数行和第三个最低权部分。
已删除交换关系、生成元数量、预存结果对照等辅助自检，以及消元步数、项数等调试打印。
消元分支和停止条件仍保留，它们属于构造算法。

全部算子计算发生在 `nc_algebra` 构造的真实 Weyl 环；为构造该环而声明的
交换基环不执行任何算子运算。输入包含 13 个原生成元，但本目录不重新证明
这些输入属于湮灭理想或构成完整生成系。本包将原先计算出的 annihilator 作为输入，
不重算其完备性；第 2 节检验的是这些输入算子对 V 的作用。

使用有符号权重
`3*(alpha_x-beta_x)+2*(alpha_y-beta_y)+4*(alpha_z-beta_z)`，参数权重为零。
初始部分指所有最小权重项之和，不是微分最高阶主符号。
在未特化参数的 Weyl 环中验证：

- 三个最小权重分别是 `2, 0, -6`；
- `in(P1)=y*(E-S)/3`；
- `in(P2)=(z*Dz+1)*(E-S)/3`；
- `E=3*x*Dx+2*y*Dy+4*z*Dz`，`S=2*s1+6*s2+8*s3`。

## 泛平面上的完整次数论证

先设 `s1=-27/2-3*b-4*c, s2=b, s3=c`，于是 `S=-27`。
`b,c` 是代数独立参数，底域是 `Q(b,c)`；没有额外施加任何参数曲线方程。
Delta 模是左模 `D/(D*x+D*y+D*z)`，记其循环元为 `delta`。

若非零共同核向量的最高加权次数为 `d>18`，最高齐次部分为 `v_d`，则
`E*v_d=-(d+9)*v_d`，所以 `(E-S)*v_d=(18-d)*v_d`。
由 `in(P1)*v_d=0`，`v_d` 不含 `Dy`；再由 `in(P2)*v_d=0`，
利用 `z*Dz+1=-Dz*d/dDz`，得到 `v_d` 不含 `Dz`。
因此 `v_d=c0*Dx^m*delta`，其中 `c0!=0`、`3*m=d`、`m>=7`。

`check_degree_bound.sing` 在真实 Weyl 环 `Q(b,c,m)<x,y,z,Dx,Dy,Dz>`
内按 PBW 正规序的下降阶乘公式精确验证

```text
in(P3)*Dx^m*delta = 3*m*(m-1)*(m-10-b-c)*Dx^(m-2)*Dz^3*delta   (m>=3).
```

符号 `m` 只出现在标量下降阶乘里；用统一偏移量 10 编码导数指数，绝不在
交换多项式环里相乘算子。符号系数恒等式是对所有 `m` 的证明，而不是随机采样。
程序还验证空间 `x` 指数至多为 3。
已删除交换关系、平面代入、作用符号等辅助自检及四个特定次数的交叉检查；
保留上述关于所有 m 的符号恒等式检验。

由于 `m>=7` 且 `b,c` 独立，上式系数在 `Q(b,c)` 中非零，产生矛盾。
故所有被全部 13 个 `B_i` 同时消掉的 Delta 向量加权次数至多 18。
这一步不要求这些算子保持次数不超过 18 的子空间，也不单独完成共同核为零的证明；
剩下的有限维秩检查单独放在 `4_kerfBi_zero` 目录。

从整包目录执行 `python3 run_all.py --section 3`，可在新运行目录中完成上述三个脚本。

## 成功标记

构造入口最后输出 `CONSTRUCT_OPERATORS_EXACT_PASS`；中间还有
`LEFT_PBW_IDENTITY_1_EXACT_PASS`（以及 2、3）、
`INITIAL_WEIGHTS_2_0_MINUS6_EXACT_PASS` 和
`ONE_HTEN_INITIAL_EULER_FACTORS_EXACT_PASS`。

次数入口最后输出 `WEIGHTED_DEGREE_18_BOUND_CERTIFICATE_PASSED`；
符号全次数检查输出 `PURE_DX_ACTION_ALL_M_SYMBOLIC_EXACT_PASS`。
运行器还必须拒绝任何 Singular 错误信息，不能仅依据进程退出码判断成功。

## 来源

- 输入生成元：原包 `certificates/audit/annfs.sing`。
- 独立保存展开：原包 `certificates/audit/bound_operators.sing`。
- 构造算法：原包 `certificates/construction/build_weighted_bound.sing`。
- 数学说明：原包 `source/exclusion.tex`、`source/p3-initial.tex`。

输入保留原始系数、生成元顺序与作者来源；仅统一空间变量名和 LF 换行。
