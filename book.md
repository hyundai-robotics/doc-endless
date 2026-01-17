
[__SOURCE](README.md)
# ${cont_model} Robot Controller Function Manual - Endless

{% hint style="warning" %}
The information in this product manual is the property of HD Hyundai Robotics.

No part of this manual may be reproduced or redistributed without prior written consent from HD Hyundai Robotics. It may not be provided to third parties or used for other purposes.

This manual is subject to change without notice.

**Copyright ⓒ 2024 by HD Hyundai Robotics**
{% endhint %}

```

[__SOURCE](1-intro/README.md)
# 1. Overview

{% hint style="info" %}
Supported from V60.26-00.
{% endhint %}

This function allows an axis configured as an R1 axis or a jig axis to rotate beyond the software soft-limit. It has three main uses:

1. Specify a number of rotations relative to a position in a robot JOB program. By setting the rotation count and running it, the specified axis will rotate the exact number of turns.

2. Convert an endless axis that has rotated beyond ±180° into an equivalent angle within ±180°. For example, an axis rotated to 360° is physically equivalent to 0°. The endless reset function is convenient because it avoids reverse rotation when moving the axis to the 0° position.

3. Set the endless rotation axis to 0°. The endless zero function sets the current position to 0° regardless of the axis's absolute position. It is similar to endless reset function. However, instead of preserving the physical axis's absolute position, it changes the current position to zero.


- Features

    (1) Easy specification of endless rotation count (dedicated function supported)
    (2) Linear interpolation support when R1 axis performs endless rotation (tool X/Y internally set to 0)
    (3) Rotation beyond soft-limit range allowed
    (4) Automatic reset when step is reached or on stop
    (5) Dedicated reset function to convert to an angle within one revolution

![](../_assets/image_1.png)
[__SOURCE](2-system-setting/README.md)
# 2. System Settings

1. In [**System > Initialize > Mechanism Settings**], configure the endless axis. Check the axis to enable it for endless operation. Note that not all axes can be set as endless depending on axis specifications.

2. If the axis type is "Robot", the R1 axis can be set as an endless axis. For additional axes, set endless to enabled when the axis type is "Jig" or "Positioner".

3. After completing settings, press the OK key.<br>
![](../_assets/image_2.png)

4. Reboot the controller to apply the endless axis setting.

<br>

{% hint style="info" %}
1. When the controller reboots, the endless axis positions are automatically converted to values within -180~180°.
2. If you restore the controller from a backed-up project file, the physical positions of endless axes cannot be restored. Reconfigure the encoder offsets and axis calibration values.

{% endhint %}

[__SOURCE](3-endless/README.md)
# 3. Endless Features

[__SOURCE](3-endless/3-1-command.md)
# 3.1 endless command

### Description
- While moving to the next step, rotate the axis configured as endless by the specified number of revolutions.
- Convert the endless axis position to an angle within -180~180° while preserving the axis's physical position.
- Set the current position to 0° or a specified angle, ignoring the axis's physical position.

### Syntax

```python
endless turn,axis=<axis number>,count=<rotation count>
endless change,axis=<axis number>,value=<axis angle>
endless reset
endless zero
```

### Parameters
<table>
<thead>
    <tr>
    <th style="text-align:left">Parameter	</th>
    <th style="text-align:left">Description</th>
    <th style="text-align:left">Remarks</th>
    </tr>
</thead>
<tbody>
    <tr>
    <td style="text-align:left">Action</td>
    <td style="text-align:left">
        - turn: Rotate the endless axis by the specified number of revolutions when moving to the next step<br>
        - change: Set the current position of the endless axis to the specified angle<br>
        - reset: Convert the current position of the endless axis to an angle within -180~180°<br>
        - zero: Set the current position of the endless axis to 0°
    </td>
    <td style="text-align:left">string</td>
    </tr>
</tbody>
<tbody>
    <tr>
    <td style="text-align:left">Axis number</td>
    <td style="text-align:left">
                Axis number to apply the endless feature
    </td>
    <td style="text-align:left">variable</td>
    </tr>
</tbody>
<tbody>
    <tr>
    <td style="text-align:left">Rotation count</td>
    <td style="text-align:left">
        Number of revolutions to rotate the endless axis
    </td>
    <td style="text-align:left">variable  (-10000~10000)</td>
    </tr>
</tbody>
    <tbody>
    <tr>
    <td style="text-align:left">Axis angle</td>
    <td style="text-align:left">
        Angle to set as the current position for the endless axis
    </td>
    <td style="text-align:left">variable</td>
    </tr>
</tbody>
</table>

{% hint style="info" %} 

Rotation count specifies how many revolutions the selected axis will rotate during step movement(-10,000 to 10,000 revolutions).
The allowable range depends on the axis reduction ratio. Typically, setting 1000 revolutions for R1 is acceptable. If you set more than this, the endless command may raise `E0173 Endless rotation overflow` when operating the program. In that case, reduce the specified count.

{% endhint %}



### Example
```python
S1  move P,spd=100%,accu=1,tool=1 
S2  move P,spd=30%,accu=5,tool=1  
    endless turn,axis=6,count=10        # Specify 10 revolutions for axis 6
S3  move L,spd=30%,accu=1,tool=1        # Move to S3 while rotating axis 6 by 10 revolutions
    endless change,axis=6,value=750     # Set axis 6 to 750°
S4  move L,spd=30%,accu=1,tool=1  
    endless reset                       # Convert all endless axes to angles within -180~180°
S5  move L,spd=30%,accu=1,tool=1  
    endless zero                        # Set all endless axes to 0°
S6  move L,spd=30%,accu=1,tool=1  
    end
```
[__SOURCE](3-endless/3-2-rcode/README.md)
# 3.2 R Code

R code functions supported by the endless feature. For basic usage of R code, see the following link:

[Basic R code usage](https://hrbook-hrc.web.app/#/view/doc-hi6-operation/english-${cont_model}-tp630/8-r-code/1-use-r-code)

[__SOURCE](3-endless/3-2-rcode/1-r350-manual-reset.md)
# 3.2.1 R350 Manual Reset of Endless Axis
The manual reset using the R350 code is used when the robot is stopped and the user wants to reset instead of executing the program command (`endless reset`). It can be used in manual or automatic mode.

| **R Code** | **Parameter** | **Description** |
| :--------: | :-----------: | :------------- |
| R350       | 0             | Reset all axes |
| R350       | endless axis number | Reset the specified axis |
[__SOURCE](3-endless/3-2-rcode/2-r354-manual-zero.md)
# 3.2.2 R354 Execute Endless Zero
The manual zero using the R354 code is used when the robot is stopped and the user wants to set the axis position to 0° instead of executing the program command (`endless zero`). It can be used in manual or automatic mode.

| **R Code** | **Parameter** | **Description** |
| :--------: | :-----------: | :------------- |
| R354       | 0             | Zero all axes |
| R354       | endless axis number | Zero the specified axis |
[__SOURCE](3-endless/3-3-error-code.md)
# 3.3 Error Codes

| **Error** | **Message** | **Description** |
| :------: | :---------: | :------------- |
| E0108 | (axis 0) Encoder error: Encoder reset required | The encoder is out of usable range. Please correct the encoder offset and try again. |
| E0172 | (axis 0) Endless rotation position error | This error occurs during initialization when the difference between the backed-up encoder position and the absolute encoder value read at power-on is greater than 0x20000. If this error occurs, re-calibrate the encoder offset for the axis. |
| E0173 | Endless rotation overflow | A rotation amount exceeding the software's significant digits was specified. For large reduction ratio, even rotation counts below 1000 may be impossible to perform at once. Reduce the rotation count specified in the endless command. |
| E0193 | (axis 0) Encoder type not supported for endless | Only encoders with 1024, 2048, 4096, or 8192 pulses per motor revolution are supported by the endless feature. Other encoder types are not supported. |