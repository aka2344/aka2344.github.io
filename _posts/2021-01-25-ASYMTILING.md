---
layout: post
title:  "[종만북]8.동적 계획법-비대칭 타일링"
date:   2021-01-25T14:25:52-05:00
navigation: True
tags: algorithm
subclass: 'post tag-windows'
logo: 'assets/images/ghost.png'
author: aka2344
disqus: true
---
### 문제

출처 : https://www.algospot.com/judge/problem/read/ASYMTILING

![](http://algospot.com/media/judge-attachments/99b44b86e82ea246a21867a6970aedfb/13.png)![](http://algospot.com/media/judge-attachments/eabd9fdeb757541289354b1dde1357f0/12.png)![](http://algospot.com/media/judge-attachments/56f26d8f5217e108489083aa594fca16/11.png)

![](http://algospot.com/media/judge-attachments/b60ba1f71aaa61dde733d5088c75b82b/10.png)![](http://algospot.com/media/judge-attachments/03beebe7a6a34a588d0742a71e6d63e4/09.png)![](http://algospot.com/media/judge-attachments/71701ba4f30e767b1894c86b216a5daa/07.png)

그림과 같이 2 * n 크기의 직사각형을 2 * 1 크기의 타일로 채우려고 합니다. 타일들은 서로 겹쳐서는 안 되고, 90도로 회전해서 쓸 수 있습니다. 단 이 타일링 방법은 좌우 대칭이어서는 안 됩니다. 위 그림은 2 * 5 크기의 직사각형을 채우는 비대칭 타일링 방법 6가지를 보여줍니다. 다음의 2가지는 좌우대칭이기 때문에 세지 않습니다.

![](http://algospot.com/media/judge-attachments/25c64a7a37ecfc8c5b2691d24c237510/14.png)![](http://algospot.com/media/judge-attachments/c9dec0372bcc0b198a30305af57364fa/08.png)

#### 입력

입력의 첫 줄에는 테스트 케이스의 수 C (1 <= C <= 50) 가 주어집니다. 그 후 각 줄에 사각형의 너비 n (1 <= n <= 100) 이 주어집니다.

#### 출력

각 테스트 케이스마다 한 줄에 비대칭 타일링 방법의 수를 1,000,000,007 로 나눈 나머지를 출력합니다.



### 풀이

먼저 2Xn 직사각형을 2X1의 직사각형으로 채우는 경우의 수는 다음과 같이 피보나치 수열로 표현될수 있다.

```java
// cache[n] : 2Xn 크기의 타일을 2X1 타일로 채우는 경우의 수
// MOD = 1000000007
static int tiling(int n) {
		if(n<=0)
			return 0;
		if(cache[n]!=0)
			return cache[n];
		else {
			cache[n] = (tiling(n-1)+tiling(n-2))%MOD;
		}
		return cache[n];
	}
```

2Xn크기의 직사각형을 비대칭 타일로 계산하는 경우의 수는 (전체 경우의 수)-(대칭 타일 경우의 수)로 계산할 수 있다. 따라서, 위의 타일 계산함수를 활용한다면 주어진 크기에서 대칭인 타일만 계산하면 된다.

1. 짝수인 경우

   짝수인 경우, 2xn크기의 타일을 2x(n/2) 타일로 절반으로 쪼갰을 때, 절반크기의 타일을 채우는 경우의 수만큼 양쪽으로 대칭 타일이 생성될 수 있다. 또한, 절반으로 나뉜 타일의 합쳐지는 부분에서 1x2 타일이 2개로 형성되어 겹치지는 경우는 2x(n/2-1) 크기의 타일들로 쪼개진다고 생각할 수 있기 때문에 이 크기의 타일을 채우는 수를 계산하여 대칭타일의 수로 합산해야 한다. 

2. 홀수인 경우

   홀수인 경우, 한쪽 타일이 가로성분으로 1만큼 더 크게 쪼개지는데, 큰 부분에서 세로모양의 2x1 타일로 채우면 나머지 양쪽 부분이 절반으로 나누어지므로 2x(n/2) 크기의 타일을 채우는 경우의 수만큼 대칭 타일이 생성될 수 있다. 짝수와 다르게 나누어진 양쪽 타일에서 합쳐지는 부분이 1x2타일 2개로 채워질 경우 나머지 양쪽 타일의 크기가 달라 전체 타일이  비대칭이 되므로, 이 경우는 고려할 필요가 없다.

   

```java
static int tiling2(int n) {
		int ret = 0;
		if(n==1)
			return 1;
		else if(n==2)
			return 0;
		else if(n%2==0) {
			ret = (tiling(n) - (tiling(n/2)+tiling(n/2-1))%MOD+MOD)%MOD;
		}
		else {
			ret = (tiling(n)+MOD - tiling(n/2)+MOD)%MOD;
		}
		return ret;
			
	}
```

따라서 위와 같이 앞서 정의한 전체 타일을 채우는 함수를 활용하여 비대칭인 타일을 채우는 경우의 수를 계산할 수 있다. 

이 때 주의하여야 할 점은 정수의 크기를 고려하여 모듈로 연산으로 계산되기 때문에, 전체 경우의 수와 대칭인 타일을 빼는 과정에서 cache[n]의 값이 1000000007보다 큰 경우 모듈로 연산에 의해 나머지값이 들어가므로 cache[n/2] 또는 cache[n/2-1]보다 작은 값으로 계산되어 음수로 계산될 수 있다. 따라서 이러한 경우를 방지하기 위해 뺄셈한 값에 모듈로 값만큼 더하여 모듈로 연산을 수행하여야 한다. 

