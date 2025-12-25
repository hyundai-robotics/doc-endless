# 3.1 endless 명령어
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

### 설명
#### 엔드리스 회전 기능(endless turn)
- 다음 스텝으로 이동하면서 엔드리스로 설정된 축을 지정한 회전수만큼 회전시킬 수 있습니다.  
  
{% hint style="info" %}  

1. 회전수는 지정한 번호의 엔드리스 축이 스텝을 이동하며 회전할 회전수 입니다.  
회전 수는 축의 감속비에 따라 설정가능한 범위가 달라집니다. 통상적으로 R1축의 경우 1000회전 설정은 문제가 없습니다. 그러나 프로그램 기동 시 1회에 회전할 수 있는 회전량의 범위를 벗어나면 'E0173 엔드리스 회전량의 오버플로우' 에러가 발생할 수 있습니다. 
2. endless 함수가 기록된 이후의 최초 스텝에만 기능이 유효하며, 이후의 스텝에는 다시 지정해야 합니다.
3. 스텝의 목표위치는 '기록한 위치 + 회전 수 X 360(deg)'입니다.
4. 동일 축에 대해서 동일한 회전축의 회전 수를 지정하면 최종 명령으로 지정된 회전 수만 유효합니다.  
5. 엔드리스 스텝의 목표위치에 도달하면 축 위치는 자동으로 리셋됩니다. 엔드리스 회전 중에 정지키시면 현재의 축 위치는 자동으로 리셋되지 않고, 스텝 재기동 시 남은 만큼의 회전량을 이동합니다.

{% endhint %}  
  
#### 엔드리스 축각도 변환 기능(endless zero, endless change)
- 엔드리스 축의 물리적인 위치를 무시하고 현재 위치를 0deg 또는 지정한 축 각도로 설정할 수 있습니다.  
  
#### 엔드리스 축 리셋 기능(endless reset)
- 엔드리스 축의 물리적인 위치를 보전하면서 -180~180deg(1회전) 이내의 각도로 변환할 수 있습니다.  

{% hint style="info" %}  

1. 엔드리스 회전 중 정지 등으로 회전량이 리셋되지 않은 상태에서는 다음 스텝까지 불필요하게 역회전할 수 있습니다. 엔드리스 축 리셋 기능을 사용하면 이를 방지할 수 있습니다.

{% endhint %}  
  
### 사용 예
```python
S1  move P,spd=100%,accu=1,tool=1 
S2  move P,spd=30%,accu=5,tool=1  
    endless turn,axis=6,count=10        # 6축을 10회전 지정
    endless turn,axis=7,count=5         # 7축을 5회전 지정
S3  move L,spd=30%,accu=1,tool=1        # S3로 이동할 때 6축을 10회전, 7축을 5회전 하면서 이동
    endless change,axis=6,value=750     # 6축을 750deg로 설정
S4  move L,spd=30%,accu=1,tool=1  
    endless reset                       # 모든 엔드리스 축을  -180~180deg 이내의 각도로 환산
S5  move L,spd=30%,accu=1,tool=1  
    endless zero                        # 모든 엔드리스 축을 0deg로 설정
S6  move L,spd=30%,accu=1,tool=1  
    end
```