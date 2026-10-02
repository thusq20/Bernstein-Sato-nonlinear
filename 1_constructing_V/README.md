# 1. 构造 V

主文件 `1_constructing_V.sing` 直接包含构造算法。
`weyl_ring.sing` 建立系数域为 Q(q) 的真实 Weyl 代数。

代码只做三件事：留数递推、按第四章的配对公式构造 V、保存输出。
直接保留第四章固定的泛函所给出的系数，不再按 Dy^9 项作额外归一化。
已删除 V 非零/项数/系数断言、参考表达式比较及调试打印；不再读取 reference_V.sing。
正负整数 rising factorial 的分支属于数学计算，仍保留。
末尾只打印一条完成标记，供批量运行器识别。

在本目录运行：

```sh
Singular --no-rc -q 1_constructing_V.sing
```

输出写入当前工作目录：

- `V.sing`：第四章直接构造的 `poly V=...;`，加载时需先建立相同的 Weyl 环。

通过根目录 `python3 run_all.py --section 1` 运行时，输出在新建运行目录的
`1_constructing_V/` 下，不覆盖源目录中的保存结果。

本步骤不检验 fV 或 B_iV；它们由第 2 步独立验证。
递推来源仍为原包 `certificates/construction/build_curve_witness.sing`。
