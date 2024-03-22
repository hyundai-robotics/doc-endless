# 3.1 endless 명령어

### 설명
- 다음 스텝으로 이동하면서 엔드리스로 설정된 축을 지정한 회전수만큼 회전시킬 수 있습니다.
- 엔드리스 축의 물리적인 위치를 보전하면서 -180~180deg 이내의 각도로 환산할 수 있습니다.
- 엔드리스 축의 물리적인 위치를 무시하고 현재의 위치를 0deg 또는 지정한 각도로 설정할 수 있습니다.

### 문법

```python
endless turn,axis=<축 번호>,count=<회전 수>
endless change,axis=<축 번호>,value=<축 각도>
endless reset
endless zero
```

### 파라미터
<table>
<thead>
    <tr>
    <th style="text-align:left">항목</th>
    <th style="text-align:left">의미</th>
    <th style="text-align:left">기타</th>
    </tr>
</thead>
<tbody>
    <tr>
    <td style="text-align:left">동작</td>
    <td style="text-align:left">
        - turn: 다음 스텝으로 이동할 때 엔드리스 축을 지정한 회전 수만큼 회전<br>
        - change: 엔드리스 축의 현재 위치를 지정한 축 각도로 설정<br>
        - reset: 엔드리스 축의 현재 위치를 -180~180deg이내의 각도로 환산<br>
        - zero: 엔드리스 축의 현재 위치를 0deg로 설정
    </td>
    <td style="text-align:left">문자열</td>
    </tr>
</tbody>
<tbody>
    <tr>
    <td style="text-align:left">축 번호</td>
    <td style="text-align:left">
        엔드리스 기능을 사용할 축 번호
    </td>
    <td style="text-align:left">변수</td>
    </tr>
</tbody>
<tbody>
    <tr>
    <td style="text-align:left">회전 수</td>
    <td style="text-align:left">
        엔드리스 축을 회전할 회전 수
    </td>
    <td style="text-align:left">변수(-10000~10000)</td>
    </tr>
</tbody>
    <tbody>
    <tr>
    <td style="text-align:left">축 각도</td>
    <td style="text-align:left">
        엔드리스 축의 현재 위치로 설정할 축 각도
    </td>
    <td style="text-align:left">변수</td>
    </tr>
</tbody>
</table>

{% hint style="info" %} 

회전 수는 해당 축이름의 1회에 회전할 회전수를 설정합니다. (-10000~10000회전)
회전수는 축의 감속비에 따라 설정가능한 범위가 달라집니다. 통상적으로 R1축의 경우 1000회전 설정은 문제가 없습니다. 그러나 그 이상으로 설정하는 경우 프로그램을 기동할 때 endless 명령에서 'E0173 엔드리스 회전량의 오버플로우' 에러가 발생할 수 있습니다. 이 경우 1회에 회전할 수 있는 회전량의 범위를 벗어난 것이므로 줄여서 설정해야 합니다.

{% endhint %}



### 사용 예
```python
S1  move P,spd=100%,accu=1,tool=1 
S2  move P,spd=30%,accu=5,tool=1  
    endless turn,axis=6,count=10        # 6축을 10회전 지정
S3  move L,spd=30%,accu=1,tool=1        # S3로 이동할 때 6축을 10회전하면서 이동
    endless change,axis=6,value=750     # 6축을 750deg로 설정
S4  move L,spd=30%,accu=1,tool=1  
    endless reset                       # 모든 엔드리스 축을  -180~180deg 이내의 각도로 환산
S5  move L,spd=30%,accu=1,tool=1  
    endless zero                        # 모든 엔드리스 축을 0deg로 설정
S6  move L,spd=30%,accu=1,tool=1  
    end
```