---
layout: post
title:  "[종만북]6.무식하게 풀기-게임판 덮기"
navigation: True
date:   2019-11-04T14:25:52-05:00
tags: algorithm
subclass: 'post tag-windows'
logo: 'assets/images/ghost.png'
author: aka2344
disqus: true
---



### 문제

![BoardCover](http://algospot.com/media/judge-attachments/2b7bfee35cbec2f4e799bb011ac18f69/03.png)

출처 : https://algospot.com/judge/problem/read/BOARDCOVER
H*W 크기의 게임판이 있습니다. 게임판은 검은 칸과 흰 칸으로 구성된 격자 모양을 하고 있는데 이 중 모든 흰 칸을 3칸짜리 L자 모양의 블록으로 덮고 싶습니다.  
이 때 블록들은 자유롭게 회전해서 놓을 수 있지만, 서로 겹치거나, 검은 칸을 덮거나, 게임판 밖으로 나가서는 안 됩니다. 위 그림은 한 게임판과 이를 덮는 방법을 보여줍니다.  
게임판이 주어질 때 이를 덮는 방법의 수를 계산하는 프로그램을 작성하세요.  


입력  
입력의 첫 줄에는 테스트 케이스의 수 C (C <= 30) 가 주어집니다. 각 테스트 케이스의 첫 줄에는 2개의 정수 H, W (1 <= H,W <= 20) 가 주어집니다. 다음 H 줄에 각 W 글자로 게임판의  모양이 주어집니다. # 은 검은 칸, . 는 흰 칸을 나타냅니다. 입력에 주어지는 게임판에 있는 흰 칸의 수는 50 을 넘지 않습니다.  

출력  
한 줄에 하나씩 흰 칸을 모두 덮는 방법의 수를 출력합니다.

### 풀이(나의 풀이)

먼저 게임판의 경우, HxW크기의 2차원 배열로 설정하는데, 이 때 배열 탐색에 있어서 범위를 벗어나는 경우 발생하는 예외를 방지하고자, 배열의 테두리 개념으로 (H+2)x(W+2) 크기의 배열로 설정하여 해당 배열을 모두 초기값 'o'로 초기화하였다. 그 후, 테두리를 제외한 안쪽 부분에서 char형 입력값을 받아서 안쪽 부분을 덮인 부분과 덮이지 않은 부분으로 구성되도록 설정하였다.  

기본 접근 방식은 앞서 풀었던 [Picnic](https://aka2344.github.io/algorithm/2019/11/05/picnic.html) 문제와 유사하게 접근하였는데, 이 문제의 경우 아직 덮이지 않은 블록을 찾아서 블록을 덮고, 다시 재귀함수를 호출하여 나머지 블록을 덮는 방식으로 진행하여, 덮인 블록을 다시 안덮여진 상태로 원상복구하는  형태로 진행한다. 이 때, Picnic 문제와 마찬가지로 중복된 경우, 내용은 같고 순서만 다른 경우를 카운팅하는 것을 방지하고자 블록을 덮여나가는 방식을 위에서부터 가장 가로/세로 위치가 앞에 있는 블록부터 찾아서 해당 블록부터 덮여나가는 방식으로 설계한다.

```java
static int findBlock(char[][] Block) {
	int firstI = -1;
	int firstJ = -1;
	loop: for (int i = 0; i < Block.length; i++) {
		for (int j = 0; j < Block[0].length; j++) {
			if (Block[i][j] == '.') {
				firstI = i;
				firstJ = j;
				break loop;
			}
		}
	}
	if (firstI == -1)
		return 1;
	int ret = 0;
	boolean chk = false;
	if (Block[firstI][firstJ + 1] == '.' && Block[firstI + 1][firstJ] == '.') {
		Block[firstI][firstJ] = Block[firstI][firstJ + 1] = Block[firstI + 1][firstJ] = '#';
		ret += findBlock(Block);
		Block[firstI][firstJ] = Block[firstI][firstJ + 1] = Block[firstI + 1][firstJ] = '.';
		chk = true;
	}
	if (Block[firstI][firstJ + 1] == '.' && Block[firstI + 1][firstJ + 1] == '.') {
		Block[firstI][firstJ] = Block[firstI][firstJ + 1] = Block[firstI + 1][firstJ + 1] = '#';
		ret += findBlock(Block);
		Block[firstI][firstJ] = Block[firstI][firstJ + 1] = Block[firstI + 1][firstJ + 1] = '.';
		chk = true;
	}
	if (Block[firstI + 1][firstJ + 1] == '.' && Block[firstI + 1][firstJ] == '.') {
		Block[firstI][firstJ] = Block[firstI + 1][firstJ + 1] = Block[firstI + 1][firstJ] = '#';
		ret += findBlock(Block);
		Block[firstI][firstJ] = Block[firstI + 1][firstJ + 1] = Block[firstI + 1][firstJ] = '.';
		chk = true;
	}
	if (Block[firstI + 1][firstJ - 1] == '.' && Block[firstI + 1][firstJ] == '.') {
		Block[firstI][firstJ] = Block[firstI + 1][firstJ - 1] = Block[firstI + 1][firstJ] = '#';
		ret += findBlock(Block);
		Block[firstI][firstJ] = Block[firstI + 1][firstJ - 1] = Block[firstI + 1][firstJ] = '.';
		chk = true;
	}
	if (!chk)
		return 0;
	return ret;
	}
 
```

앞부분은 2중 for문을 통해 덮이지 않은 부분을 찾아 break로 중단하는 형태로 Picnic문제와 동일한 형태이고, 이때 기준 블록 Block[i],[j]로부터 취할수 있는 모양 4가지를 각각 조건문으로 취하여 재귀함수를 진행한다.  

이 떄 앞서 푼 문제와 큰 차이점은, 블록을 덮는 과정에서 기준 블록으로부터 위의 4가지 형태 중 어느것도 칠할 수 없는 경우, 즉 L자 형태로 칠할 수 없는 구조인 경우를 boolean형 변수 chk로 판별결과를 저장하여 저장할 수 없는 false인 경우 0을 리턴하도록 한 것이다. 이 경우, 더 이상 블록을 칠하는 재귀함수가 진행되지 않고, 리턴된 0값이 ret값에 더해지게 된다.
