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

Rotation count specifies how many revolutions the selected axis will rotate during step movement(–10,000 to 10,000 revolutions).
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