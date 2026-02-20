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