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

### Description

#### Endless Turn Function(endless turn)
- When moving to the next step, the axis set as "Endless" can be rotated by a specified number of turns.

{% hint style="info" %}  

1. **Number of Rotations:** This refers to the number of turns the specified endless axis will perform while moving through the step. The configurable range of rotations varies depending on the axis's reduction ratio. Typically, setting 1,000 rotations for the R1 axis is not an issue. However, if the rotation amount exceeds the allowable limit for a single execution during program startup, the error **"E0173 Endless rotation amount overflow"** may occur.
2. The function is only valid for the **first step immediately following** the `endless` function record. For subsequent steps, it must be specified again.
3. The target position of the step is calculated as: **[Recorded Position + (Number of Rotations x 360°)]**.
4. If multiple rotation counts are specified for the same axis, only the **final command** issued will be valid.
5. The axis position is **automatically reset** once the target position of the endless step is reached. If the operation is stopped during an endless rotation, the current axis position is not automatically reset; upon restarting the step, the axis will complete the remaining rotation amount.

{% endhint %}  
  
#### Endless Axis Angle Conversion (endless zero, endless change)
- This function allows you to ignore the physical position of the endless axis and set the current position to **0 degree** or a **specifically designated axis angle**.

#### Endless Axis Reset (endless reset)
- This function converts the axis position to an angle within the range of **-180 to 180 degree (one rotation)** while preserving the physical position of the endless axis.

{% endhint %} 

1. If the rotation amount has not been reset (e.g., due to a stop during an endless rotation), the axis may perform an unnecessary reverse rotation when moving to the next step. Using the **Endless Axis Reset** function can prevent this behavior.

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