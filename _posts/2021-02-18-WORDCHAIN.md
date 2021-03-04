---
layout: post
title:  "[종만북]28.그래프의 깊이 우선 탐색-단어 제한 끝말잇기"
date:   2021-02-18T14:25:52-05:00
navigation: True
tags: algorithm
subclass: 'post tag-windows'
logo: 'assets/images/ghost.png'
author: aka2344
disqus: true
---

#### 문제

출처 : https://www.algospot.com/judge/problem/read/WORDCHAIN

끝말잇기는 참가자들이 원을 그리고 앉은 뒤, 시계 방향으로 돌아가면서 단어를 말하는 게임입니다. 이 때 각 사람이 말하는 단어의 첫 글자는 이전 사람이 말한 단어의 마지막 글자와 같아야 합니다. 단어 제한 끝말잇기는 일반적인 끝말잇기와 달리 사용할 수 있는 단어의 종류가 게임 시작 전에 미리 정해져 있으며, 한 단어를 두 번 사용할 수 없습니다. 단어 제한 끝말잇기에서 사용할 수 있는 단어들의 목록이 주어질 때, 단어들을 전부 사용하고 게임이 끝날 수 있는지, 그럴 수 있다면 어떤 순서로 단어를 사용해야 하는지를 계산하는 프로그램을 작성하세요.

#### 입력

입력의 첫 줄에는 테스트 케이스의 수 C (1 <= C <= 50) 가 주어집니다. 각 테스트 케이스의 첫 줄에는 게임에서 사용할 수 있는 단어의 수 N (1 <= N <= 100) 이 주어집니다. 그 후 N 줄에 하나씩 게임에서 사용할 수 있는 단어가 주어집니다. 각 단어는 알파벳 소문자로 구성되어 있으며, 길이는 2 이상 10 이하입니다. 중복으로 출현하는 단어는 없습니다.

#### 출력

각 테스트 케이스마다 한 줄을 출력합니다. 만약 모든 단어를 사용하고 게임이 끝나는 방법이 없다면 "IMPOSSIBLE" 을 출력하고 (따옴표 제외), 방법이 있다면 사용할 단어들을 빈 칸 하나씩을 사이에 두고 순서대로 출력합니다. 방법이 여러 개 있다면 그 중 아무 것이나 출력해도 좋습니다.

### 풀이

풀이에 앞서 오일러 서킷 구조에 대한 이해가 필요하다. 오일러 서킷은 그래프의 모든 간선을 정확히 한번씩 지나 시작점으로 돌아오는 경로를 의미한다. 무향 그래프에서 오일러 서킷의 존재여부는 각 정점에 인접한 간선 수인 차수(degree)에 따라 결정될 수있는데, 모든 간선을 한번씩 지나기 위해서는 각 정점에서 들어간 횟수와 나간 횟수가 같아야 하므로 반드시 모든 정점의 차수가 짝수여야 한다. 그래프의 모든 간선을 지나도록 오일러 서킷을 찾는 방법은 깊이 우선 탐색인 DFS를 활용하여 다음과 같이 구현할  수 있다.

```java
int[][] adj;
void getEulerCircuit(int here, ArrayList<Integer> circuit){
    for(int there=0;there<adj[here].length;there++)
        while(adj[here][there]>0){
            adj[here][there]--;
            adj[there][here]--;
            getEulerCircuit(there, circuit);
        }
    circuit.add(here);
}
```

각 정점간의 간선을 표현한 2차원 배열 adj에 대해 재귀호출로써 DFS를 구현하는데, 정점과 정점 사이에 간선이 존재할 때, 양쪽 간선을 모두 지우고 함수를 재귀호출한다. 이 때, 재귀 호출이 끝나고 함수가 반환될 때 정점번호를 리스트에 추가함으로써 역순으로 간선들이 추가된다. 따라서, 해당 리스트를 뒤집었을 때 오일러 서킷구조를 얻을 수 있다.

오일러 트레일은 모든 간선을 정확히 한번씩 지나지만 시작점과 끝점이 다른 경로를 의미한다. 오일러 트레일을 찾는 방법은 오일러 서킷 구조를 통해 알아낼 수 있는데, 점 a에서 b로 끝나는 오일러 트레일을 얻고자 할 때 b에서 a로의 간선을 임의로 추가한 뒤 오일러 서킷을 찾고, 해당 서킷에서 (b,a) 간선을 다시 끊는다면 a에서 b로 끝나는 오일러 트레일을 간단히 얻을 수 있다. 앞서 오일러 서킷은 모든 점이 짝수점이어야 존재할 수 있으므로, 트레일 구조에서는 시작점과 끝점을 제외하고 모든 정점이 짝수개의 차수를 가져야 하고, 시작점과 끝점의 경우 임의의 간선을 하나 추가해서 생각하였으므로 홀수개의 차수가 있어야 성립할 수 있다.

주어진 문제는 각 단어의 시작 단어와 끝단어가 그래프의 형태로 이어진다고 생각할 때, 단어들을 정확히 한 번씩 사용하여 모두 사용하는 방법을 찾아야 하므로 오일러 서킷 또는 트레일의 구조를 찾아내야 한다. 만약 각 단어를 정점으로써 생각한다면 모든 정점을 한번씩 방문하는 방식(헤밀토리니안 경로)으로 생각해야 되기 때문에, 오일러 구조로 접근하기 위해 각 단어의 시작/끝단어를 정점, 각 단어를 간선으로 표현한다면 오일러 서킷/트레일 구조와 같이 모든 간선(단어)을 정확히 한번씩 지나는 구조를 생각할 수 있다.

```java
// graph[i][j] : i로 시작해서 j로 끝나는 단어목록
// adj[i][j] : i와 j사이의 간선수
// indegree, outdegree : 들어가고 나가는 차수
static void makeGraph(String[] words) {
	graph = new Stack[26][26];
	adj = new int[26][26];
	indegree = new int[26];
	outdegree = new int[26];
	for (int i = 0; i < words.length; i++) {
		int a = words[i].charAt(0) - 'a';
		int b = words[i].charAt(words[i].length() - 1) - 'a';
		if (graph[a][b] == null)
			graph[a][b] = new Stack<String>();
		graph[a][b].push(words[i]);
		adj[a][b]++;
		outdegree[a]++;
		indegree[b]++;
	}
}
```

주어진 단어 목록에 따른 그래프를 생성하는 함수는 위와 같다. 알파벳으로 주어진 각 단어의 시작, 끝 단어의 번호를 2차원 배열로써 생각한다면 특정 단어 i로 시작하고 j로 끝나는 복수개의 목록을 구현하여야 하므로, 2차원 배열 인덱스를 갖는 스택구조를 그래프로 구현할 수 있다. 이 때의 인접 행렬은 각 단어의 시작 단어번호, 끝 단어번호로써 표현될 수 있고, 유향 그래프이므로 각 단어인 정점에서 들어오는 차수와 나가는 차수를 별개로 계산하여야 한다. 시작단어에서 끝단어로 단어가 간선으로 표현되므로, 시작 단어의 나가는 차수와 끝 단어의 들어오는 차수가 각각 1씩 더해지게 된다.

유향 그래프에서의 오일러 서킷 존재 조건을 보면 각 정점에서 들어오는 간선과 나가는 간선의 수가 같아야 하므로, 들어오는 차수와 나가는 차수가 동일하다는 조건이 된다. 마찬가지로, 오일러 트레일의 경우 시작점, 끝점을 제외한 모든 정점의 나가는 차수, 들어오는 차수가 같고 시작점의 나가는 간선, 끝점의 들어오는 간선이 반대 간선보다 1개씩 많아야 한다.

```java
static void getEulerCircuit(int here, ArrayList<Integer> circuit) {
	for (int i = 0; i < 26; i++)
		while (adj[here][i] > 0) {
			adj[here][i]--;
			getEulerCircuit(i, circuit);
		}
	circuit.add(here);
}

static ArrayList<Integer> getEulerTrailOrCircuit() {
	ArrayList<Integer> circuit = new ArrayList<Integer>();
	// 오일러 트레일을 찾아본다 -> 시작점이 존재하는경우
	for (int i = 0; i < 26; i++)
		if (outdegree[i] == indegree[i] + 1) {
			getEulerCircuit(i, circuit);
			return circuit;
		}
	// 트레일이 존재하지 않을 때 간선에 인접한
	// 아무 정점에서 시작하는 서킷을 찾는다.
	for (int i = 0; i < 26; i++)
		if (outdegree[i] != 0) {
			getEulerCircuit(i, circuit);
			return circuit;
		}
	// 모두 실패한 경우
	return null;
}
```

오일러 서킷을 구하는 함수 getEulerCircuit()은 앞서 구현한 무향그래프에서의 함수와 거의 동일한데, 유향의 경우 한 방향으로의 간선만을 생각해야 하므로 양쪽이 아닌 한쪽 방향의 간선을 지우게 된다. 문제에서 오일러 서킷 또는 트레일을 찾는 함수 getEulerTrailOrCircuit()은 먼저 특정 시작점이 존재하는 오일러 트레일을 찾고, 서킷이 존재하지 않을 때 아무 정점에서나 시작하는 서킷을 찾도록 구현한다. 트레일의 시작점에서 나가는 차수는 들어오는 차수보다 하나 더 많아야 하므로 이 조건을 만족하는 시작점을 찾아 트레일을 만든다. 시작점이 존재하지 않아 트레일을 만들 수 없을 때, 나가는 간선이 존재하는 아무 정점에서 시작하는 서킷을 찾게 된다. 이렇게 완성된 트레일 또는 서킷 탐색 함수를 통해 먼저 별도로 서킷/트레일의 존재여부를 검사하여 해당 함수를 호출하여 최종 목록을 얻을  수 있다.

```java
static boolean checkEuler() {
	int plus1 = 0, minus1 = 0;
	for (int i = 0; i < 26; i++) {
		int delta = outdegree[i] - indegree[i];
		if (delta < -1 || 1 < delta)
			return false;
		if (delta == 1)
			plus1++;
		if (delta == -1)
			minus1++;
	}
	return (plus1 == 1 && minus1 == 1) || (plus1 == 0 && minus1 == 0);
}

static String solve(String[] words) {
	makeGraph(words);
	if (!checkEuler())
		return "IMPOSSIBLE";
	ArrayList<Integer> circuit = getEulerTrailOrCircuit();
	if (circuit.size() != words.length + 1)
		return "IMPOSSIBLE";
	Collections.reverse(circuit);
	StringBuilder sb = new StringBuilder();
	for (int i = 1; i < circuit.size(); i++) {
		int a = circuit.get(i - 1), b = circuit.get(i);
		sb.append(graph[a][b].pop() + " ");
	}
	return sb.toString();
}
```

오일러 트레일과 서킷의 존재여부를 검사하는 방법은 앞서 살펴본 차수에 따른 조건을 활용하면 된다. 서킷의 경우 모든 정점의 들어오는/나가는 차수가 같아야 하고 트레일의 경우 시작점과 끝점을 제외한 차수가 모두 같고 시작점, 끝점은 각각 하나씩 많아야 하므로, 함수에서 모든 정점들에 대해 차수를 계산하는 반복문을 통해 시작점, 끝점의 개수를 파악할 수 있다.

최종적으로 목록을 얻는 함수의 경우, 먼저 그래프를 만들고 트레일/서킷의 존재 여부를 검사한 뒤 트레일/서킷을 만들게 된다. 이 때 주의해야 할 점은 모든 간선을 방문하지 않아 그래프가 두개 이상으로 분리되는 경우가 발생할 수 있기 때문에 모든 간선의 방문여부 또한 검사하여야 한다.

