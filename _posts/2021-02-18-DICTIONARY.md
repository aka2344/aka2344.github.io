---
layout: post
title:  "[종만북]28.그래프의 깊이 우선 탐색-고대어 사전"
date:   2021-02-18T14:25:52-05:00
navigation: True
tags: algorithm
subclass: 'post tag-windows'
logo: 'assets/images/ghost.png'
author: aka2344
disqus: true
---

#### 문제

출처 : https://www.algospot.com/judge/problem/read/DICTIONARY

아마추어 고고학자인 일리노이 존스는 시카고 근교에서 고대 문명의 흔적을 찾아냈습니다. 그 흔적 중에는 이 언어의 사전도 포함되어 있었는데, 이 사전에 포함된 단어들은 모두 영어의 소문자 알파벳으로 구성되어 있었지만 사전에 포함된 단어의 순서들이 영어와 서로 달랐습니다. 발굴팀은 단어들이 사전 순이 아닌 다른 순서대로 정렬되어 있는지, 아니면 알파벳들의 순서가 영어와 서로 다른 것인지를 알고 싶어합니다.

일리노이 존스는 이 언어에서는 알파벳들의 순서가 영어와 서로 다를 뿐, 사전의 단어들은 사전 순서대로 배치되어 있다는 가설을 세웠습니다. 이 가설이 사실이라고 가정하고, 단어의 목록으로부터 알파벳의 순서를 찾아내려고 합니다.

예를 들어 다섯 개의 단어 gg, kia, lotte, lg, hanhwa 가 사전에 순서대로 적혀 있다고 합시다. gg가 kia보다 앞에 오려면 이 언어에서는 g가 k보다 앞에 와야 합니다. 같은 원리로 k는 l앞에, l은 h앞에 와야 한다는 것을 알 수 있지요. lotte 가 lg 보다 앞에 오려면 o가 g 보다 앞에 와야 한다는 것도 알 수 있습니다. 이들을 종합하면 다섯 개의 알파벳 o, g, k, l, h 의 상대적 순서를 알게 됩니다.

사전에 포함된 단어들의 목록이 순서대로 주어질 때 이 언어에서 알파벳의 순서를 계산하는 프로그램을 작성하세요.



#### 입력

입력의 첫 줄에는 테스트 케이스의 수 C (1 <= C <= 50) 가 주어집니다. 각 테스트 케이스의 첫 줄에는 사전에 포함된 단어의 수 N (1 <= N <= 1000) 이 주어집니다. 그 후 N 줄에 하나씩 사전에 포함된 단어가 순서대로 주어집니다. 각 단어는 알파벳 소문자로 구성되어 있으며, 길이는 1 이상 20 이하입니다. 중복으로 출현하는 단어는 없습니다.



#### 출력

각 테스트 케이스마다 한 줄을 출력합니다. 만약 알파벳들의 순서에 모순이 있다면 "INVALID HYPOTHESIS"를 출력하고, 모순이 없다면 26개의 소문자로 알파벳들의 순서를 출력합니다. 만약 가능한 순서가 여러 개 있다면 아무 것이나 출력해도 좋습니다.

### 풀이

그래프 구조를 구현하고 깊이 우선 탐색을 통해 위상정렬된 결과를 구하는 문제이다. 알파벳의 순서에 따른 의존관계가 나타나므로 이를 그래프 구조로 표현하고 위상정렬을 수행할 수 있다. 순서대로 두 단어가 주어졌을 때, 단어의 순서를 결정하는 것은 단어의 앞에서부터 비교하여 처음으로 다른 두 알파벳 순서에 의해 결정되므로, 두 알파벳의 각 번호에 따른 연결 형태를 2차원 배열로써 구현할 수 있다.

```java
static void makeGraph(ArrayList<String> words) {
		adj = new int[26][26];
		for (int j = 1; j < words.size(); j++) {
			int i = j - 1;
			int len = Math.min(words.get(i).length(), words.get(j).length());
			for (int k = 0; k < len; k++) {
				if (words.get(i).charAt(k) != words.get(j).charAt(k)) {
					int a = words.get(i).charAt(k) - 'a';
					int b = words.get(j).charAt(k) - 'a';
					adj[a][b] = 1;
					break;
				}
			}
		}
	}
```

알파벳은 총 26개이므로, 각각의 알파벳 번호에 따른 연결 형태가 2차원 배열의 그래프 adj로 구현되었다. 이 때, 알파벳의 순서는 단방향으로 나타나게 된다. 이에 따른 위상정렬은 각 정점을 DFS방식으로 방문하였을 때 방문 순서로써 알아낼 수 있다.

```java
static void dfs(int here) {
		visited[here] = 1;
		for (int i = 0; i < 26; i++)
			if (visited[i] != 1 && adj[here][i] == 1)
				dfs(i);
		order.add(here);
	}
```

각 정점은 연결되어있는 다음 정점이 존재할 때마다 dfs가 재귀호출되어 가장 깊은 정점까지 호출되며, 마지막으로 방문한 가장 깊은 정점번호가 order 리스트에 기록된다. 따라서 이 order리스트를 뒤집었을 때 처음부터 방문한 순서인 알파벳 순서가 나타나게 된다. 이 때, 문제에서 순서가 모순인 경우는 단방향으로 표현된 각 정점간의 관계에서 반대쪽으로 가는 간선이 존재할 경우 모순이 발생하는데, 이는 각 정점들에서 반대쪽으로 가는 간선의 존재 여부를 검사함으로써 확인할 수 있다.

```java
static ArrayList<Integer> sortAns() {
		visited = new int[26];
		order = new ArrayList<Integer>();
		for (int i = 0; i < 26; i++)
			if (visited[i] != 1)
				dfs(i);
		Collections.reverse(order);
		for(int i=0;i<26;i++)
			for(int j=i+1;j<26;j++)
				if(adj[order.get(j)][order.get(i)]==1)
					return null;
		return order;

	}
```

방문 순서가 저장된 리스트 order에서 i<j인 order(j)->order(i)는 정렬된 방문순서에서 반대 방향으로의 방문을 나타내므로 이를 인접행렬 adj를 통해 반대 방향으로의 방문 가능여부를 파악할 수 있다.