---
layout: post
title:  "[종만북]21.트리의 구현과 순회-문자열 합치기"
date:   2021-02-14T14:25:52-05:00
navigation: True
tags: algorithm
subclass: 'post tag-windows'
logo: 'assets/images/ghost.png'
author: aka2344
disqus: true
---

#### 문제

출처 : https://www.algospot.com/judge/problem/read/TRAVERSAL

트리를 순회하는 알고리즘은 트리의 모든 노드들을 특정 순서에 맞춰 방문하지만, 트리는 배열처럼 1차원적인 구조가 아니기 때문에 단 한 가지의 당연한 순서가 존재하지 않습니다. 때문에 필요에 맞춰 순서를 정의해야 합니다. 이진 트리(binary tree)는 모든 노드에 왼쪽과 오른쪽, 최대 두 개의 자손이 있는 트리를 말하는데, 이진 트리의 순회 순서 중 유명한 세 가지로 전위 순회(preorder traverse), 중위 순회(inorder traverse) 그리고 후위 순회(postorder traverse)가 있습니다. 이들은 모두 왼쪽 서브트리를 오른쪽보다 먼저 방문한다는 점에선 같지만, 트리의 루트를 언제 방문하는지가 서로 다릅니다.

전위 순회는 맨 처음에 트리의 루트를 방문하고, 왼쪽과 오른쪽 서브트리를 순서대로 방문합니다. 중위 순회는 왼쪽과 오른쪽 서브트리 사이에 트리의 루트를 방문하고, 후위 순회는 왼쪽과 오른쪽 서브트리를 모두 방문한 뒤에야 루트를 방문합니다.

다음 그림은 이진 트리의 한 예를 보여 줍니다. 이 트리를 전위 순회하면 모든 노드를 27, 16, 9, 12, 54, 36, 72의 순서대로 방문하게 됩니다. 반면 중위 순회했을 때는 9, 12, 16, 27, 36, 54, 72의 순서로, 후위 순회했을 때는 12, 9, 16, 36, 72, 54, 27의 순서로 방문하게 되지요.

![](http://algospot.com/media/judge-attachments/bbdb1ada72f7eb16c9291ed59d4761ff/traversal.png)

어떤 이진 트리를 전위 순회했을 때 노드들의 방문 순서와, 중위 순회했을 때 노드들의 방문 순서가 주어졌다고 합시다. 이 트리를 후위 순회했을 때 각 노드들을 어떤 순서대로 방문하게 될지 계산하는 프로그램을 작성하세요.



#### 입력

입력의 첫 줄에는 테스트 케이스의 수 C (1≤C≤100)가 주어집니다. 각 테스트 케이스는 세 줄로 구성되며, 첫 줄에는 트리에 포함된 노드의 수 N (1≤N≤100)이 주어집니다. 그 후 두 줄에 각각 트리를 전위 순회했을 때와 중위순회 했을 때의 노드 방문 순서가 N개의 정수로 주어집니다. 각 노드는 1000 이하의 자연수로 번호 매겨져 있으며, 한 트리에서 두 노드의 번호가 같은 일은 없습니다.



#### 출력

각 테스트 케이스마다, 한 줄에 해당 트리의 후위 순회했을 때 노드들의 방문 순서를 출력합니다.



### 풀이

입력으로 조어진 전위 순회 리스트와, 중위 순회 리스트를 통해 후위 순회 리스트를 찾아내야 한다. 먼저 전위 순회 리스트는 항상 루트를 우선적으로 방문하므로, 맨 앞의 요소는 해당 트리의 루트임을 알 수 있다. 이를 통해 중위 순회 리스트에서 해당 루트 성분의 위치를 찾는다면, 루트를 기준으로 왼쪽 서브트리와 오른쪽 서브트리의 크기와 구간을 알 수 있게 된다. 따라서 왼쪽 서브트리와 오른쪽 서브트리를 통해 재귀적으로 후위 순회를 출력할 수 있는 함수를 구현할 수 있다.

```java
static void printPostOrder(List preorder, List inorder) {
		int N = preorder.size();
		if (preorder.isEmpty())
			return;
		int root = (int)preorder.get(0);
		int L = inorder.indexOf(root);
		printPostOrder(preorder.subList(1, L+1), inorder.subList(0, L));
		printPostOrder(preorder.subList(L+1, N), inorder.subList(L+1, N));
		System.out.print(root + " ");
	}
```
