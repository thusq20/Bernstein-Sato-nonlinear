# 4. 有限维矩阵与 P3 作用计算

在本目录运行：

```sh
Singular --no-rc -q 4_kerfBi_zero.sing
```

主程序直接读取第 3 步生成的 `operators.sing`，不再重复检查生成元及左组合。
在真实 Weyl 代数中取系数域 Q(b,c)，代入
s1=-27/2-3*b-4*c、s2=b、s3=c；只施加 h=0。

枚举 3i+2j+4k<=18 的全部 84 个基向量，用左理想
`std(ideal(x,y,z))` 约化 f、P1、P2 的作用，逐项提取完整系数。
第 3 步的权重结论保证 f、P1、P2 保持 U18；这里按该基提取系数。
矩阵为完整的 252×84，零行全部保留，直接调用内置 `rank(fullMap)`。

直接计算并输出：

- 内置 rank(fullMap) 得到的实际秩。
- reduce(P3,pointGB) 得到的实际 P3*delta。

没有预设秩、P3*delta 的对照公式或零/非零断言。

全 Delta 模的共同核为零还依赖第 3 步的次数界和左组合恒等式，
本程序不独立重证它们。矩阵第一列记录 f、P1、P2 对 delta 的实际作用。

`phi_matrices.sing` 保存基的指数、基向量、完整矩阵、实际秩及 P3*delta 的正规形。
矩阵的三个 84 行块依次对应 f、P1、P2；不再输出重复的权重或曲线参数数据。

正常结束打印下列字段；维数、行数、秩和 P3*delta 均来自实际计算：

```text
FULL_MAP_SHAPE=...
ALL_..._ROWS_RETAINED
CORE_RANK_PHI=...
P3_DELTA=...
```

从整包目录运行 `python3 run_all.py --section 4` 时，
程序与日志在新运行目录中；运行器自身另有运行状态输出。
运行器只检查执行错误和输出文件是否存在，不将结果与预设的秩或公式比较。
运行成功不等同于自动断言数学结论成立。
