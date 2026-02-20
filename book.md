
[__SOURCE](1-intro/README.md)
# 1. 概述

{% hint style="info" %}
支持从 V60.26-00 开始。
{% endhint %}

此功能允许配置为 R1 轴或夹具轴的轴在软件软限制之外旋转。它主要有三个用途：

1. 指定相对于机器人 JOB 程序中位置的旋转次数。通过设置旋转计数并运行，指定轴将旋转确切的转数。

2. 将旋转超过 ±180° 的无限轴转换为 ±180° 内的等效角度。例如，旋转到 360° 的轴在物理上等同于 0°。无限重置功能很方便，因为它在将轴移动到 0° 位置时避免了反向旋转。

3. 将无限旋转轴设置为 0°。无限零功能将当前位置信息设置为 0°，而不考虑轴的绝对位置。它类似于无限重置功能。然而，它不是保存物理轴的绝对位置，而是将当前位置信息更改为零。


- 特点

    (1) 易于指定无限旋转计数（支持专用功能）
    (2) 当 R1 轴执行无限旋转时支持线性插值（工具 X/Y 内部设置为 0）
    (3) 允许超出软限制范围的旋转
    (4) 达到步骤或停止时自动重置
    (5) 专用重置功能将旋转转换为一次旋转内的角度

![](../_assets/image_1.png)
[__SOURCE](2-system-setting/README.md)
# 2. 系统设置

1. 在`[F2: 系统] - 5：初始化 - 机制设置 ([F2: System] - Initialize - Mechanism Settings)`中，配置无尽轴。检查该轴以启用其无尽操作。请注意，并非所有轴都可以根据轴规格设置为无尽。

2. 如果轴类型为“机器人”，则R1轴可以设置为无尽轴。对于额外轴，设置无尽时，在轴类型为“夹具”或“定位器”时启用。

3. 设置完成后，按下确认键。<br>
![](../_assets/image_2.png)

4. 重启控制器以应用无尽轴设置。

<br>

{% hint style="info" %}
1. 当控制器重启时，无尽轴位置会自动转换为-180~180°范围内的值。
2. 如果从备份的项目文件恢复控制器，则无尽轴的物理位置无法恢复。请重新配置编码器偏移和轴校准值。

{% endhint %}
[__SOURCE](3-endless/README.md)
# 3. 无尽的功能
[__SOURCE](3-endless/3-1-command.md)
# 3.1 无尽命令

### 描述
- 在移动到下一个步骤时，按指定的旋转次数旋转配置为无尽的轴。
- 在保持轴的物理位置的同时，将无尽轴的位置转换为 -180~180° 之间的角度。
- 将当前位置设置为 0° 或指定角度，忽略轴的物理位置。

### 语法

```python
endless turn,axis=<axis number>,count=<rotation count>
endless change,axis=<axis number>,value=<axis angle>
endless reset
endless zero
```

### 参数
<table>
<thead>
    <tr>
    <th style="text-align:left">参数</th>
    <th style="text-align:left">描述</th>
    <th style="text-align:left">备注</th>
    </tr>
</thead>
<tbody>
    <tr>
    <td style="text-align:left">行动</td>
    <td style="text-align:left">
        - turn: 在移动到下一个步骤时，按指定的旋转次数旋转无尽轴<br>
        - change: 将无尽轴的当前位置设置为指定角度<br>
        - reset: 将无尽轴的当前位置转换为 -180~180° 之间的角度<br>
        - zero: 将无尽轴的当前位置设置为 0°
    </td>
    <td style="text-align:left">字符串</td>
    </tr>
</tbody>
<tbody>
    <tr>
    <td style="text-align:left">轴号</td>
    <td style="text-align:left">
                应用无尽功能的轴号
    </td>
    <td style="text-align:left">变量</td>
    </tr>
</tbody>
<tbody>
    <tr>
    <td style="text-align:left">旋转次数</td>
    <td style="text-align:left">
```
无限轴旋转的圈数
    </td>
    <td style="text-align:left">变量  (-10000~10000)</td>
    </tr>
</tbody>
    <tbody>
    <tr>
    <td style="text-align:left">轴角</td>
    <td style="text-align:left">
        设置为无限轴当前的位置的角度
    </td>
    <td style="text-align:left">变量</td>
    </tr>
</tbody>
</table>

{% hint style="info" %} 

旋转计数指定所选轴在步进运动期间将旋转多少圈（-10,000 到 10,000 圈）。
允许的范围取决于轴的减速比。通常，设置 R1 为 1000 圈是可以的。如果设置超过这个值， 无限指令在程序运行时可能会引发 `E0173 Endless rotation overflow`。在这种情况下，请减少指定的计数。

{% endhint %}



### 示例
```python
S1  move P,spd=100%,accu=1,tool=1 
S2  move P,spd=30%,accu=5,tool=1  
    endless turn,axis=6,count=10        # 指定轴 6 旋转 10 圈
S3  move L,spd=30%,accu=1,tool=1        # 在绕轴 6 旋转 10 圈的同时移动到 S3
    endless change,axis=6,value=750     # 将轴 6 设置为 750°
S4  move L,spd=30%,accu=1,tool=1  
    endless reset                       # 将所有无限轴转换为 -180~180° 内的角度
S5  move L,spd=30%,accu=1,tool=1  
    endless zero                        # 将所有无限轴设置为 0°
S6  move L,spd=30%,accu=1,tool=1  
    end
```
[__SOURCE](3-endless/3-2-rcode/README.md)
# 3.2 R 代码

无限特性支持的 R 代码功能。有关 R 代码的基本用法，请参阅以下链接：

[基本 R 代码用法](https://hrbook-hrc.web.app/#/view/doc-hi6-operation/en-tp630/8-r-code/1-use-r-code?cont_model=${cont_model})
[__SOURCE](3-endless/3-2-rcode/1-r350-manual-reset.md)
# 3.2.1 R350 手动重置无限轴
使用 R350 代码的手动重置用于机器人停止时，用户希望进行重置而不是执行程序命令 (`endless reset`)。它可以在手动或自动模式下使用。

| **R 代码** | **参数** | **描述** |
| :--------: | :-----------: | :------------- |
| R350       | 0             | 重置所有轴 |
| R350       | 无限轴编号 | 重置指定轴 |
[__SOURCE](3-endless/3-2-rcode/2-r354-manual-zero.md)
# 3.2.2 R354 执行无尽归零
使用 R354 代码的手动归零用于机器人停止时，用户希望将轴位置设置为 0° 而不是执行程序命令（`endless zero`）。它可以在手动或自动模式下使用。

| **R 代码** | **参数** | **描述** |
| :--------: | :-----------: | :------------- |
| R354       | 0             | 所有轴归零 |
| R354       | 无尽轴编号 | 归零指定轴 |
[__SOURCE](3-endless/3-3-error-code.md)
# 3.3 错误代码

| **错误** | **信息** | **描述** |
| :------: | :---------: | :------------- |
| E0108 | (轴 0) 编码器错误：需要重置编码器 | 编码器超出可用范围。请纠正编码器偏移并重试。 |
| E0172 | (轴 0) 无限旋转位置错误 | 当备份的编码器位置与开机时读取的绝对编码器值之间的差异大于 0x20000 时，会发生此错误。如果发生此错误，请重新校准该轴的编码器偏移。 |
| E0173 | 无限旋转溢出 | 指定的旋转量超过软件的有效数字。对于大减速比，即使是少于 1000 的旋转计数也可能无法一次完成。减少无限命令中指定的旋转计数。 |
| E0193 | (轴 0) 不支持的无限编码器类型 | 仅支持每转电机 1024、2048、4096 或 8192 脉冲的编码器。其他编码器类型不受支持。 |