# 백준 단계별로 풀어보기 - 동적 계획법 2
[백준](https://www.acmicpc.net)의 단계별로 풀어보기 중 [동적 계획법 2](https://www.acmicpc.net/step/17)의 몇몇 문제를 풀어본 곳입니다.  
코드에 등장하는 여러 함수들의 이름은 그때그때 생각나는대로 적었기에, 그 뜻이 덜 와닿을수도 있습니다.  
또한, 홈페이지에서 주어진 input의 형태와 코드에서 가정하는 input의 형태가 다를 수도 있습니다. 형태 변환은 어려운 문제가 아니고, 백준 단계별로 풀어보기에서 가정하는 input의 형태는 항상 동일하므로 이 부분은 엄밀하게 작성되지 않았을 수도 있습니다.  
모든 코드는 배워가는 입장에서 작성한 만큼, 더 좋은 방법이 있을 수 있습니다.

## 1. 파일 합치기
- [문제](https://www.acmicpc.net/problem/11066)  
소설가인 김대전은 소설을 여러 장(chapter)으로 나누어 쓰는데, 각 장은 각각 다른 파일에 저장하곤 한다. 소설의 모든 장을 쓰고 나서는 각 장이 쓰여진 파일을 합쳐서 최종적으로 소설의 완성본이 들어있는 한 개의 파일을 만든다. 이 과정에서 두 개의 파일을 합쳐서 하나의 임시파일을 만들고, 이 임시파일이나 원래의 파일을 계속 두 개씩 합쳐서 소설의 여러 장들이 연속이 되도록 파일을 합쳐나가고, 최종적으로는 하나의 파일로 합친다. 두 개의 파일을 합칠 때 필요한 비용(시간 등)이 두 파일 크기의 합이라고 가정할 때, 최종적인 한 개의 파일을 완성하는데 필요한 비용의 총 합을 계산하시오.

예를 들어, C1, C2, C3, C4가 연속적인 네 개의 장을 수록하고 있는 파일이고, 파일 크기가 각각 40, 30, 30, 50 이라고 하자. 이 파일들을 합치는 과정에서, 먼저 C2와 C3를 합쳐서 임시파일 X1을 만든다. 이때 비용 60이 필요하다. 그 다음으로 C1과 X1을 합쳐 임시파일 X2를 만들면 비용 100이 필요하다. 최종적으로 X2와 C4를 합쳐 최종파일을 만들면 비용 150이 필요하다. 따라서, 최종의 한 파일을 만드는데 필요한 비용의 합은 60+100+150=310 이다. 다른 방법으로 파일을 합치면 비용을 줄일 수 있다. 먼저 C1과 C2를 합쳐 임시파일 Y1을 만들고, C3와 C4를 합쳐 임시파일 Y2를 만들고, 최종적으로 Y1과 Y2를 합쳐 최종파일을 만들 수 있다. 이때 필요한 총 비용은 70+80+150=300 이다.

소설의 각 장들이 수록되어 있는 파일의 크기가 주어졌을 때, 이 파일들을 하나의 파일로 합칠 때 필요한 최소비용을 계산하는 프로그램을 작성하시오.

- 코드 
~~~
import numpy as np

def file():
    data = input().split('\n')
    N = int(data[0])
    for i in range(1,N+1):
        data[2*i - 1] = int(data[2*i - 1])
        data[2*i] = list(map(int, data[2*i].split(' ')))
    for i in range(1,N+1):
        M = len(data[2*i])
        minmat = np.zeros((M,M), int)
        for n in range(1,M):
            td = data[2*i]
            m = 0
            while m+n < M:
                minmat[m][m+n] = min(minmat[m][k] + minmat[k+1][m+n] + sum(td[m:m+n+1]) for k in range(m,m+n))
                m += 1
        print(minmat[0][M-1])
~~~

- 설명  
이론적으로는 예전에 풀었던 DP 문제와 해결 방법이 같습니다. 하지만, 이 코드에서 사용한 풀이 방법은 [이전 포스트](https://github.com/Overlaine-l/Baekjoon-step-by-step-Dynamic-Programming-1)와 다른 점이 있습니다.  
동적 프로그래밍에서는 문제를 부분으로 쪼개 푸는 만큼 재귀 표현이 자주 쓰이는데, 이 문제 역시 재귀를 쓸만함에도 불구하고 재귀를 사용하지 않았습니다. 그 이유는 다음과 같습니다.  
이 코드를 작성할 때 사용한 3.7버전 기준으로, 파이썬에서는 약 1000번 이상의 재귀문이 쌓이면 에러가 납니다. 이 문제의 경우, 파일의 개수가 늘어남에 따라 쌓이는 재귀문이 exponential하게 커지기 때문에 N이 조금만 커져도 쉽게 에러가 나곤 합니다. 또한, 재귀문으로 그대로 짤 경우 같은 계산을 반복하여 진행하는 사태가 생깁니다. 해당 내용에 대해 더 알고 싶다면, 관련 서적(예전 포스트에 추천한 책이 있습니다)을 읽어보시기를 권합니다.  
두 가지 문제를 해결하기 위해 문제를 거꾸로 푸는 방식을 사용합니다. 즉, 먼저 부분 문제를 푼 후, 전체 문제를 이미 풀린 부분 문제를 이용해 해결합니다.  
우선, 인접한 2개의 파일을 합치는 최소 비용을 구합니다. 당연히 구할 필요도 없이 두 파일의 크기를 합친 것과 같습니다. 이 값들을 저장해둡니다. 그 다음으로는, 인접한 3개의 파일을 합치는 최소 비용을 구합니다. 순서대로 파일 1 2 3으로 명명하면, (1 2) 3, 1 (2 3) 두 방법이 있습니다. 이 때, (1 2)와 (2 3)을 계산할 필요 없이 앞에서 구한 값을 사용합니다.  
데이터를 저장하기 위해, 행렬을 사용합니다. (i,j)-th entry 값을 파일 i i+1 ... j를 합치는 최소 비용으로 두면 index의 혼동 없이 데이터를 저장하고 사용할 수 있을 겁니다.  

- 참고  
이러한 방식을 bottom-up이라고 합니다. 반대로, 전체 문제를 먼저 코딩한 후 재귀를 통해 부분으로 내려가는 방식을 top-down이라고 합니다. 이 용어들은 공학 전반에서 사용되는 용어이니(개념은 같습니다) 알아두시면 유용하게 쓰실 수 있을 겁니다.  

## 2. 행렬 곱셈 순서 
- [문제](https://www.acmicpc.net/problem/11049)  
크기가 N×M인 행렬 A와 M×K인 B를 곱할 때 필요한 곱셈 연산의 수는 총 N×M×K번이다. 행렬 N개를 곱하는데 필요한 곱셈 연산의 수는 행렬을 곱하는 순서에 따라 달라지게 된다.

행렬 N개의 크기가 주어졌을 때, 모든 행렬을 곱하는데 필요한 곱셈 연산 횟수의 최솟값을 구하는 프로그램을 작성하시오. 입력으로 주어진 행렬의 순서를 바꾸면 안 된다.

- 코드 
~~~
import numpy as np

def mat_mul_order(N):
    sizes = []
    for i in range(N):
        sizes.append(list(map(int, input().split(' '))))
    for i in range(N-1):
        if sizes[i][1] != sizes[i+1][0]:
            return print('invalid input')
    
    mul_min = np.zeros((N,N), int)
    
    for i in range(1,N):
        j = 0
        while j + i < N:
            mul_min[j][i+j] = min(mul_min[j][k] + mul_min[k+1][i+j] + sizes[j][0]*sizes[k][1]*sizes[i+j][1] for k in range(j,i+j))
            j += 1
            
    return mul_min[0][N-1]
~~~

- 설명  
이 문제는 위의 문제와 사실상 동일합니다. 다만 연산이 조금 더 복잡해진 정도의 차이는 있습니다.  
이 문제를 굳이 가져온 이유는, DP의 예시로 자주 소개되는 유명한 형태의 문제 중 하나이기 때문입니다. 계산이 단순하지 않기에 코딩 자체의 난이도도 조금 있고, bottom-up 방식을 이해하기도 정말 좋다고 생각합니다.  

- 참고
문제와는 관련없는 내용이지만, 문제 마지막에 주어진 '입력으로 주어진 행렬의 순서를 바꾸면 안 된다'의 이유는, 행렬의 순서가 바뀌면 답이 달라지기 때문입니다. 이를 non-commutative라고 합니다.  

## 3. 앱 
- [문제](https://www.acmicpc.net/problem/7579)  
현재 N개의 앱, A1, ..., AN이 활성화 되어 있다고 가정하자. 이들 앱 Ai는 각각 mi 바이트만큼의 메모리를 사용하고 있다. 또한, 앱 Ai를 비활성화한 후에 다시 실행하고자 할 경우, 추가적으로 들어가는 비용(시간 등)을 수치화 한 것을 ci 라고 하자. 이러한 상황에서 사용자가 새로운 앱 B를 실행하고자 하여, 추가로 M 바이트의 메모리가 필요하다고 하자. 즉, 현재 활성화 되어 있는 앱 A1, ..., AN 중에서 몇 개를 비활성화 하여 M 바이트 이상의 메모리를 추가로 확보해야 하는 것이다. 여러분은 그 중에서 비활성화 했을 경우의 비용 ci의 합을 최소화하여 필요한 메모리 M 바이트를 확보하는 방법을 찾아야 한다.

- 코드 
~~~
def disable():
    data = input().split('\n')
    required = list(map(int, data[0].split(' ')))
    M = required[1]
    byte = list(map(int, data[1].split(' ')))
    cost = list(map(int, data[2].split(' ')))
    
    def mincost(M,byte,cost):
        if M <= 0:
            return 0
        b = byte.pop(0)
        c = cost.pop(0)
        if sum(byte) < M:
            return mincost(M-b,byte,cost)+c
        return min(mincost(M,byte,cost), mincost(M-b,byte,cost)+c)
    
    return mincost(M,byte,cost)
~~~

- 설명  
이 문제는 [이전 포스트](https://github.com/Overlaine-l/Baekjoon-step-by-step-Dynamic-Programming-1)의 베낭 문제와 비슷합니다. 유일한 차이점이라면, M 바이트 이상이 확보되는 순간 더 앱을 비활성화 시킬 필요가 없도록 코드를 작성해야 하는 것입니다. 이 부분은 mincost 함수의 첫 번째 if문이 담당합니다.
