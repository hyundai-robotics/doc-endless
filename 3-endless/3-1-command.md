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