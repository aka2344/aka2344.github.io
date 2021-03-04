---
layout: post
title:  "[종만북]25.상호 베타적 집합-에디터 전쟁"
date:   2021-02-17T14:25:52-05:00
navigation: True
tags: algorithm
subclass: 'post tag-windows'
logo: 'assets/images/ghost.png'
author: aka2344
disqus: true
---

#### 문제

출처 : https://www.algospot.com/judge/problem/read/EDITORWARS

에디터 전쟁은 가장 유명한 자유 소프트웨어 텍스트 편집기인 vi와 Emacs 중 어느 쪽이 더 우월한가를 놓고 인터넷에서 자주 벌어지는 논쟁을 말합니다. 이 논쟁에 참여하는 사람들은 서로 자신이 사용하는 편집기의 장점을 찬양하고 (“vi는 동작도 빠르고, 빠른 편집을 가능하게 한다고”, “Emacs는 LISP을 통해 확장 가능하다고”) 다른 편집기를 헐뜯곤 (“vi-vi-vi는 666이잖아! vi는 악마의 편집기야”, “Emacs는 좋은 운영 체제지. 좋은 편집기가 없는 것만 빼면 완벽해”) 합니다.

모든 회원들이 vi 혹은 Emacs를 사용하는 프로그래밍 동호회에서 연말 파티를 개최하려 합니다. 서로 다른 편집기를 사용하는 사람들이 파티에 함께 참가하면 싸움이 나기 때문에 vi를 사용하는 사람들만 오는 파티, Emacs를 사용하는 사람들만 오는 파티를 따로 하기로 했습니다. 이를 위해 지금까지 모든 회원들이 쓴 댓글을 모아 이들을 두 종류로 분류했습니다.

1. 상호 인정: 이 유형의 댓글은 댓글을 쓴 사람과 원글을 쓴 사람이 같은 편집기를 쓴다는 사실을 의미합니다. 예로 “아이고 이런 편집기를 쓰시다니 뭘 아는 분이네” 혹은 “역시 편집기는 xxx가 짱이죠” 등이 있겠지요.
2. 상호 비방: 이 유형의 댓글은 댓글을 쓴 사람과 원글을 쓴 사람이 다른 편집기를 쓴다는 사실을 의미합니다. 예로 “그런 편집기 쓰면 손가락에 마비가 올 것 같네요” 혹은 “훠어이 악마의 편집기는 물러가라” 등이 있겠지요.

이것만으로는 물론 각 사용자가 어떤 편집기를 쓰는지는 알 수 없지만, 우선 서둘러 장소를 예약해야 하기 때문에 이 정보만으로 파티에 올 수 있는 최대 인원을 알아야 합니다. 댓글 정보가 주어질 때 이 댓글 정보 중 모순되는 것은 없는지 확인하고, 모순되는 것이 없을 때 한 파티의 가능한 최대 인원을 계산하는 프로그램을 작성하세요.



#### 입력

입력의 첫 줄에는 테스트 케이스의 수 C (1≤C≤50)가 주어집니다. 각 테스트 케이스의 첫 줄에는 회원의 수 N (1≤N≤10000)과 분석한 댓글의 개수 M (1≤M≤100000)이 주어집니다. 각 회원은 0에서 N - 1 범위의 숫자로 표현됩니다.

그 후 M줄에 하나씩 각 댓글의 정보가 주어집니다. 각 댓글은 상호 인정, 혹은 상호 비방 댓글입니다. 상호 인정 댓글은 “ACK a b”(0≤a, b<N) 형태로 주어지며 상호 비방 댓글은 “DIS a b” 형태로 주어집니다.



#### 출력

각 테스트 케이스마다 한 줄을 출력합니다.

- ᆞ댓글에 주어진 정보 중 모순이 없는 경우 한 파티에 올 수 있는 사람의 최대 수를 “MAX PARTY SIZE IS x”의 형태로 출력합니다.

- ᆞ댓글들에 주어진 정보 중 모순이 있는 경우, 모순이 처음으로 발생하는 댓글이 몇 번인지를 “CONTRADICTION AT i” 형태로 출력합니다. 댓글의 번호는 입력에 주어진 순서대로 1부터 시작한다고 합시다.

  

### 풀이(책 참고)

본 문제는 상호 배타적 집합을 표현하는 유니온-파인드 자료구조를 사용하여 해결하는 문제이다. 상호 배타적 집합은 전체 집합 중 성격이 같은 여러 개의 부분집합으로 쪼개진 자료구조를 의미한다. 각 부분집합은 공통원소가 없는 상호 배타적인 성격을 가진다. 이러한 상호 배타적 집합을 구현하기 위해 다음과 같은 3가지 연산이 필요하다.

- 초기화 : n개의 원소가 각각의 집합에 포함되어 있도록 초기화한다.
- 합치기(union) 연산 : 두 원소 a, b가 주어질 때 이들이 속한 두 집합을 하나로 합친다.
- 찾기(find) 연산 : 어떤 원소 a가 주어질 때 이 원소가 속한 집합을 반환한다.

이를 구현한 기본적인 형태의 클래스는 다음과 같다.

```java
class OptimizedDisjointSet {
	int[] parent;
	int[] rank;

	OptimizedDisjointSet(int n) {
		parent = new int[n];
		rank = new int[n + 1];
		for (int i = 0; i < n; i++) {
			parent[i] = i;
		}
	}

	int find(int u) {
		if (u == parent[u])
			return u;
		return parent[u] = find(parent[u]);
	}

	void merge(int u, int v) {
		u = find(u);
		v = find(v);
		if (u == v)
			return u;
		if (rank[u] > rank[v]) {
			int temp = u;
			u = v;
			v = temp;
		}
		parent[u] = v;
		if (rank[u] == rank[v])
			++rank[v];
	}
}
```

한 집합에 속하는 원소들을 하나의 트리로 묶어서 표현하는데, 공통 조상인 루트 노드를 기반으로 트리가 형성된다. 이 때, 두 원소가 같은 트리에 속해 있는지를 확인하는 방법은 각 원소가 포함된 트리의 루트를 찾아 비교하는 것이다. 따라서 이를 위해 각 노드의 부모 노드를 저장할 배열 parent가 존재한다. 노드의 초기화는 각 노드가 기본적으로 자기 자신으로 가르키도록 설정한다. 노드의 루트를 찾는 find 연산을 수행하면 해당 노드의 부모 노드를 따라 자기 자신을 가르키는 루트 노드에 도달할 때까지 재귀 호출되는데, 보다 효율적으로 찾기 위해 노드의 루트를 찾아 parent 배열에 저장하도록 한다. 이 과정에서, 재귀적으로 해당 루트 노드로 따라 올라갈때마다 각 노드의 parent 요소값은 루트로 설정되어 다음에 find연산을 수행하여도 곧바로 루트를 출력하도록 구현할 수 있다.

두 집합을 합치는 merge연산은 두 개의 트리 중 하나의 루트 노드의 부모를 다른 루트노드로 설정하는 방식으로 합치게 되는데, 합쳐진 결과가 고르게 퍼진 트리를 유지하기 위해서 각 트리의 높이를 계산해 높이가 낮은 트리를 높은 트리 밑에 추가하도록 함으로써 구현할 수 있다.



문제를 해결하기 위해 위의 구조를 확장하여 구현해야 하는데, 각 트리의 크기를 구해야 하므로 트리의 크기가 저장되는 size배열을 추가하고, 상호 비방의 경우 배타적 집합으로 설정해야 하므로 이를 저장할 enemy 배열을 추가한다. 그리고 입력값으로 주어진 상호 인정, 상호 비방에 따른 연산을 수행할 메서드를 각각 구현한다.

```java
class OptimizedDisjointSet {
	int[] parent;
	int[] rank;
	int[] size;
	int[] enemy;

	OptimizedDisjointSet(int n) {
		parent = new int[n];
		rank = new int[n + 1];
		size = new int[n];
		enemy = new int[n];
		Arrays.fill(size, 1);
		Arrays.fill(enemy, -1);
		for (int i = 0; i < n; i++) {
			parent[i] = i;
		}
	}

	int find(int u) {
		if (u == parent[u])
			return u;
		return parent[u] = find(parent[u]);
	}

	int merge(int u, int v) {
		if (u == -1 || v == -1)
			return Math.max(u, v);
		u = find(u);
		v = find(v);
		if (u == v)
			return u;
		if (rank[u] > rank[v]) {
			int temp = u;
			u = v;
			v = temp;
		}
		parent[u] = v;
		size[v] += size[u];
		if (rank[u] == rank[v])
			++rank[v];
		return v;

	}

	boolean dis(int u, int v) {
		u = find(u);
		v = find(v);
		if (u == v)
			return false;
		int a = merge(u, enemy[v]);
		int b = merge(v, enemy[u]);
		enemy[a] = b;
		enemy[b] = a;
		return true;
	}

	boolean ack(int u, int v) {
		u = find(u);
		v = find(v);
        // 두 집합이 적대 관계일 때 모순
		if (enemy[u] == v)
			return false;
		int a = merge(u, v);
		int b = merge(enemy[u], enemy[v]);
		enemy[a] = b;
		if (b != -1)
			enemy[b] = a;
		return true;
	}

}
```

초기화 단계에서 기본 사이즈는 1로, enemy는 없는 경우에 해당하는 -1로 각 배열을 초기화한다. merge연산의 리턴 타입은 int형으로 합쳐졌을 때 상위 루트에 해당하는 번호를 리턴하도록 한다. dis연산과 ack 연산을 구현하는데, 책에서 정의한 핵심 원칙은 다음과 같다.

- 동지의 적은 나의 적이다 : 나와 상대가 같은 편집기를 쓸 때, 상대와 다른 편집기를 쓰는 사람은 나와 다른 편집기를 쓴다.
- 적의 적은 나의 동지다 : 나와 상대가 다른 편집기를 쓸 때, 상대와 다른 편집기를 쓰는 사람은 나와 같은 편집기를 쓴다.

위 두가지 성질을 이용하면, ack연산과 dis연산에서 이를 활용하여 enemy배열을 통해 적대 관계와 merge연산을 통해 합치기 연산이 가능하며, 모순이 발생한 경우를 체크할 수있다. ack 연산의 경우 두 루트노드를 합치고, 두 루트의 적대 노드를 합쳐 생성된 두 트리에 대해 서로간의 적대 관계를 설정하여 "동지의 적은 나의 적" 원칙을 활용하였다. dis연산의 경우 각 노드와 상대 노드의 적대 노드를 합쳐 생성된 두 트리의 적대 관계를 설정하여 "적의 적은 나의 동지" 원칙을 활용하였다. 

```java
static int maxParty(OptimizedDisjointSet set) {
		int ret = 0;
		for (int node = 0; node < N; node++) {
			if (set.parent[node] == node) {
				int enemy = set.enemy[node];
				if (enemy > node)
					continue;
				int mySize = set.size[node];
				int enemySize = (enemy == -1 ? 0 : set.size[enemy]);
				ret += Math.max(mySize, enemySize);
			}
		}
		return ret;
	}
```

입력값에 따라 ack연산과 dis 연산이 수행되며, 완료되었을 때 최종적으로 위에서 구현한 상호 배타적 집합을 활용해 최대 집합의 크기를 계산하는 메소드 maxParty를 구현한다. 모든 사람에 대해 반복문을 돌며 부모 노드가 자기 자신을 가르키는 루트 노드를 찾아 크기를 계산하는데, 같은 모임 쌍을 두번 세지 않기 위해 적대 관계의 노드 번호가 노드 번호보다 작은 경우만을 세도록 한다. 해당 트리의 크기와, 적대 노드의 트리 크기를 계산하여 최댓값을 계산하고, 이를 누적하여 가장 큰 집합의 크기를 계산하였다.