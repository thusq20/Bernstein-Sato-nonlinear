# 2. 验证 fV=0、B_iV=0

主代码：`2_fBiV_zero.sing`。这是曲线侧的检验，系数域为精确的 Q(q)。

参数代入为

```
s1 = 4*q^2 - 2
s2 = -(8*q^2 + 8*q + 15)/6
s3 = q - 1
f  = y*(z*y-x^2)*(z^2+x^2*y+z*y^2+x^3)
```

`ann_generators.sing` 是原来 13 个算子的表达式，次序不变，只将空间变量
a、Da 改为 z、Dz。`annfs.sing` 额外保留完整的、已改名的 Weyl 环声明与
13 个生成元，便于单独打开核对。两者表达式相同，主程序仅加载前者，避免换环。

`V.sing` 是第四章直接构造、未按 Dy^9 系数重新归一化的 84 项见证表达式。顶层 `run_all.py` 全部运行时，会改用第 1 节
刚刚重新构造出的 V，而不是仅检验预存文件；单独运行本节时使用此预存 V。

## 真正的非交换运算

主程序在 `nc_algebra(1,comm)` 建立的 Weyl 环中，使用

```
ideal pointIdeal=x,y,z;
ideal pointGB=std(pointIdeal);
reduce(f*V,pointGB);
reduce(B[i]*V,pointGB);
```

这是左模 Delta = D/(D*x+D*y+D*z) 的正规形，不是把 x,y,z 设成零的交换环代入。
代码只保留数学上的核心检验：V 在 Delta 中非零，且 fV 及全部 13 个 B_iV 的
余式为零。已删除交换关系、左右作用、参数、项数、次数、归一化等辅助自检和负对照。
全部通过的标记为 `FULL13_NONCOMMUTATIVE_WEYL_WITNESS_VERIFIED`。

## 运行

从整包目录，在 Linux/WSL 中执行：

```sh
python3 run_all.py --section 2
```

或在本目录直接运行 `Singular --no-rc -q 2_fBiV_zero.sing`。
本节复用已保存的 annihilator，不重新计算其完备性；检验的内容是它们的实际作用。
