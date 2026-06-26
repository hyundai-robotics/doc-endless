
[__SOURCE](README.md)
# ${cont_model} 机器人控制器功能手册 - 无限
[__SOURCE](0-about-this-manual/README.md)
# 关于手册
[__SOURCE](0-about-this-manual/precautions.md)
# 注意事项

{% include file="zh/precautions.md" %}
[__SOURCE](0-about-this-manual/safety-notice.md)
# 安全注意事项

{% include file="zh/safety-notice.md" %}
[__SOURCE](1-intro/README.md)
# 1. 概述

{% hint style="info" %}
支持从 V60.26-00。
{% endhint %}

此功能允许配置为 R1 轴或夹具轴的轴在软件软限制之外旋转。它主要有三个用途：

1. 在机器人 JOB 程序中相对于某个位置指定旋转次数。通过设置旋转计数并运行，指定的轴将旋转确切的次数。

2. 将旋转超过 ±180° 的无限轴转换为 ±180° 内的等效角度。例如，旋转到 360° 的轴在物理上相当于 0°。无限重置功能非常方便，因为它避免了在将轴移动到 0° 位置时的反向旋转。

3. 将无限旋转轴设置为 0°。无限零功能将当前 posición 设置为 0°，无论轴的绝对位置如何。它类似于无限重置功能。但是，它不会保留物理轴的绝对位置，而是将当前位置更改为零。

- 特点

    (1) 易于指定无限旋转计数（支持专用功能）
    (2) 当 R1 轴执行无限旋转时支持线性插值（工具 X/Y 内部设置为 0）
    (3) 允许超出软限制范围的旋转
    (4) 到达步骤时或停止时自动重置
    (5) 专用重置功能将转换为一次旋转内的角度

![](../_assets/image_1.png)
[__SOURCE](2-system-setting/README.md)
# 2. 系统设置

1. 在 `[F2: 系统] - 初始化 - 机械设置 ([F2: System] - Initialize - Mechanism Settings)` 中，配置无限轴。检查轴以启用其无限操作。请注意，并非所有轴都可以根据轴规格设置为无限。

2. 如果轴类型为“机器人”，则 R1 轴可以设置为无限轴。对于额外的轴，当轴类型为“夹具”或“定位器”时，设置无限为启用。

3. 完成设置后，按下 OK 键。<br>
![](../_assets/image_2.png)

4. 重启控制器以应用无限轴设置。

<br>

{% hint style="info" %}
1. 当控制器重启时，无限轴的位置会自动转换为 -180~180° 之间的值。
2. 如果从已备份的项目文件中恢复控制器，则无限轴的物理位置无法恢复。请重新配置编码器偏移量和轴校准值。

{% endhint %}
[__SOURCE](3-endless/README.md)
# 3. 无尽的特性
[__SOURCE](3-endless/3-1-command.md)
# 3.1 无尽命令

### 描述
- 在移动到下一个步骤时，按指定的旋转次数旋转配置为无尽的轴。
- 将无尽轴的位置转换为-180~180°之间的角度，同时保留轴的物理位置。
- 将当前位置设置为0°或指定角度，忽略轴的物理位置。

### 语法

```python
endless turn,axis=<轴号>,count=<旋转次数>
endless change,axis=<轴号>,value=<轴角度>
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
    <td style="text-align:left">Action</td>
    <td style="text-align:left">
        - turn: 在移动到下一个步骤时，按指定的旋转次数旋转无尽轴<br>
        - change: 将无尽轴的当前位置设置为指定角度<br>
        - reset: 将无尽轴的当前位置转换为-180~180°之间的角度<br>
        - zero: 将无尽轴的当前位置设置为0°
    </td>
    <td style="text-align:left">string</td>
    </tr>
</tbody>
<tbody>
    <tr>
    <td style="text-align:left">Axis number</td>
    <td style="text-align:left">
                应用无尽功能的轴号
    </td>
    <td style="text-align:left">variable</td>
    </tr>
</tbody>
<tbody>
    <tr>
    <td style="text-align:left">Rotation count</td>
    <td style="text-align:left">
        旋转无尽轴的旋转次数
    </td>
    <td style="text-align:left">variable  (-10000~10000)</td>
    </tr>
</tbody>
    <tbody>
    <tr>
    <td style="text-align:left">Axis angle</td>
    <td style="text-align:left">
        设置为无尽轴当前的位置的角度
    </td>
    <td style="text-align:left">variable</td>
    </tr>
</tbody>
</table> 

### 描述

#### 无尽旋转功能（endless turn）
- 在移动到下一个步骤时，设置为“无尽”的轴可以按指定的旋转次数旋转。

{% hint style="info" %}  

1. **旋转次数:** 这指的是在移动通过步骤时，指定的无尽轴将执行的旋转次数。可配置的旋转范围因轴的减速比而异。通常，为R1轴设置1,000次旋转是没有问题的。然而，若旋转量在程序启动期间超过单次执行的允许限制，可能会出现错误 **“E0173 无尽旋转量溢出”**。
2. 该功能仅在 `endless` 函数记录后的**第一个步骤**有效。对于后续步骤，必须再次指定。
3. 步骤的目标位置计算为：**[记录位置 + (旋转次数 x 360°)]**。
4. 如果对同一轴指定多个旋转次数，则只有**最后发出的命令**有效。
5. 一旦达到无尽步骤的目标位置，轴的位置会**自动重置**。如果在无尽旋转期间停止操作，当前轴位置不会自动重置；重新启动步骤时，轴将完成剩余的旋转量。

{% endhint %}  
  
#### 无尽轴角度转换（endless zero, endless change）
- 此功能允许您忽略无尽轴的物理位置，并将当前位置设置为**0度**或**特定指定的轴角度**。

#### 无尽轴重置（endless reset）
- 此功能将轴位置转换为**-180到180度（一个旋转）**之间的角度，同时保留无尽轴的物理位置。

{% endhint %} 

1. 如果旋转量尚未重置（例如，在无尽旋转期间由于停止而导致），则在移动到下一个步骤时，轴可能会执行不必要的反向旋转。使用 **无尽轴重置** 功能可以防止这种行为。

{% endhint %}  
  

### 示例
```python
S1  move P,spd=100%,accu=1,tool=1 
S2  move P,spd=30%,accu=5,tool=1  
    endless turn,axis=6,count=10        # 为轴6指定10次旋转
S3  move L,spd=30%,accu=1,tool=1        # 在旋转轴6 10次的同时移动到S3
    endless change,axis=6,value=750     # 将轴6设置为750°
S4  move L,spd=30%,accu=1,tool=1  
    endless reset                       # 将所有无尽轴转换为-180~180°之间的角度
S5  move L,spd=30%,accu=1,tool=1  
    endless zero                        # 将所有无尽轴设置为0°
S6  move L,spd=30%,accu=1,tool=1  
    end
```
[__SOURCE](3-endless/3-2-rcode/README.md)
# 3.2 R 代码

无限特性支持的 R 代码函数。有关 R 代码的基本用法，请参阅以下链接：

[基本 R 代码用法](https://hrbook-hrc.web.app/#/view/doc-hi6-operation/zh-tp630/8-r-code/1-use-r-code?cont_model=${cont_model})
[__SOURCE](3-endless/3-2-rcode/1-r350-manual-reset.md)
# 3.2.1 R350 手动复位无尽轴
使用 R350 代码的手动复位适用于机器人停止时，用户希望重置而不是执行程序命令（`endless reset`）。它可以在手动或自动模式下使用。

| **R 代码** | **参数** | **描述** |
| :--------: | :-----------: | :------------- |
| R350       | 0             | 复位所有轴 |
| R350       | endless axis number | 复位指定轴 |
[__SOURCE](3-endless/3-2-rcode/2-r354-manual-zero.md)
# 3.2.2 R354 执行无限归零
使用 R354 代码的手动归零在机器人停止时使用，用户希望将轴位置设置为 0° 而不是执行程序命令（`endless zero`）。它可以在手动或自动模式下使用。

| **R 代码** | **参数** | **描述** |
| :--------: | :-----------: | :------------- |
| R354       | 0             | 归零所有轴 |
| R354       | 无限轴号     | 归零指定轴 |
[__SOURCE](3-endless/3-3-error-code.md)
# 3.3 错误代码

| **错误** | **消息** | **描述** |
| :------: | :---------: | :------------- |
| E0108 | (轴 0) 编码器错误：需要重置编码器 | 编码器超出可用范围。请修正编码器偏移量并重试。 |
| E0172 | (轴 0) 无限旋转位置错误 | 此错误在初始化期间发生，当备份的编码器位置与开机读取的绝对编码器值之间的差异大于 0x20000 时。如果发生此错误，请为该轴重新校准编码器偏移量。 |
| E0173 | 无限旋转溢出 | 指定的旋转量超过了软件的有效数字。对于大减速比，即使是小于 1000 的旋转计数也可能无法一次性执行。减少无限命令中指定的旋转计数。 |
| E0193 | (轴 0) 不支持无限的编码器类型 | 仅支持每转动电机 1024、2048、4096 或 8192 脉冲的编码器用于无限功能。不支持其他类型的编码器。 |