---
title: "[kata][python] 프로그래머스 - 네트워크"
layout: post
tag:
- kata
category: kata
author: itholic
sitemap:
  changefreq: daily
  priority: 1.0
---

# 네트워크

출처: <a href="https://programmers.co.kr/learn/courses/30/lessons/43162" target="_blank">프로그래머스 - 네트워크</a>

## 문제

네트워크란 컴퓨터 상호 간에 정보를 교환할 수 있도록 연결된 형태를 의미합니다. 예를 들어, 컴퓨터 A와 컴퓨터 B가 직접적으로 연결되어있고, 컴퓨터 B와 컴퓨터 C가 직접적으로 연결되어 있을 때 컴퓨터 A와 컴퓨터 C도 간접적으로 연결되어 정보를 교환할 수 있습니다. 따라서 컴퓨터 A, B, C는 모두 같은 네트워크 상에 있다고 할 수 있습니다.

컴퓨터의 개수 n, 연결에 대한 정보가 담긴 2차원 배열 computers가 매개변수로 주어질 때, 네트워크의 개수를 return 하도록 solution 함수를 작성하시오.

##### 제한사항

- 컴퓨터의 개수 n은 1 이상 200 이하인 자연수입니다.
- 각 컴퓨터는 0부터 `n-1`인 정수로 표현합니다.
- i번 컴퓨터와 j번 컴퓨터가 연결되어 있으면 computers[i][j]를 1로 표현합니다.
- computer[i][i]는 항상 1입니다.

##### 입출력 예

| n    | computers                         | return |
| ---- | --------------------------------- | ------ |
| 3    | [[1, 1, 0], [1, 1, 0], [0, 0, 1]] | 2      |
| 3    | [[1, 1, 0], [1, 1, 1], [0, 1, 1]] | 1      |

##### 입출력 예 설명

예제 #1
아래와 같이 2개의 네트워크가 있습니다.
![image0.png](https://grepp-programmers.s3.amazonaws.com/files/ybm/5b61d6ca97/cc1e7816-b6d7-4649-98e0-e95ea2007fd7.png)

예제 #2
아래와 같이 1개의 네트워크가 있습니다.
![image1.png](https://grepp-programmers.s3.amazonaws.com/files/ybm/7554746da2/edb61632-59f4-4799-9154-de9ca98c9e55.png)



## 내 풀이

컨셉은 이렇다.

n개의 컴퓨터는 각자 고유의 인덱스(0 ~ n-1)가 있고, 이 인덱스를 key로 딕셔너리를 만든다.

각 key의 value는 본인과 연결관계가 있는 컴퓨터의 인덱스 리스트이다.

예를들어 위 #예제1 을 딕셔너리로 만든다면,

```python
{
    0: [1], 
    1: [0],
    2: []
} 
```

와 같은 형태가 될 것이다.

이를 통해 그래프를 그릴 수 있고, 모든 컴퓨터를 출발점으로해서 그래프를 탐색해볼 수 있다.

<br/>

이 때, 탐색 경로를 분석해보면 서로 연결된 컴퓨터끼리는 어디서 출발하든 같은 노드 셋트를 가지고 있을것이다.

출발점과 끝점은 다르더라도, 노드 세트에 포함된 노드의 종류는 항상 같다는 것이다.

<br/>

예를들어 [1, 2, 3, 4, 5] 와 [5, 2, 3, 1, 4] 는 출발점과 도착점이 다르지만 1, 2, 3, 4, 5 라는 같은 노드 셋트를 가지고 있다.

이를 string으로 연결해 "12345" 라는 하나의 네트워크로 봐도 무방할 것이다.

만약 [2, 1, 4], [4, 1, 2], [1, 4, 2] 라는 세 개의 탐색 경로가 나온다면, 이는 "124" 라는 하나의 네트워크로 볼 수 있다.

이런식으로 총 서로 다른 네트워크가 몇 개 나오는지 세어보면된다.

<br/>

코드로 표현해보자.

```python
  1 def dfs(graph, start_node):
  2     visit = list()
  3     stack = list()
  4
  5     stack.append(start_node)
  6
  7     while stack:
  8         node = stack.pop()
  9         if node not in visit:
 10             visit.append(node)
 11             stack.extend(graph[node])
 12
 13     return visit
 14
 15
 16 def solution(n, computers):
 17     graph = {node: [] for node in range(n)}
 18
 19     for i, computer in enumerate(computers):
 20         for j, conn in enumerate(computer):
 21             if i != j and conn == 1:
 22                 graph[i].append(j)
 23
 24     paths = map(sorted, [dfs(graph, node) for node in graph])
 25
 26     return len(set(["".join(map(str, path)) for path in paths]))
```

(1~13) <a href="https://itholic.github.io/python-bfs-dfs/" target="_blank">지난 포스팅</a>에서 다루었던 DFS 코드를 그대로 가져왔다.

(17) 우선 각 컴퓨터(이하 노드)의 인덱스 번호를 key로 딕셔너리를 만들고, value는 빈 리스트로 초기화했다.

(19~22) 자신을 제외하고, 자신과 연결된 노드(값이 1인것)를 딕셔너리의 리스트에 추가해준다.

(24) 모든 노드를 출발점으로 그래프를 한 번씩 순회하고, 순회 경로를 정렬했다.

앞서 설명했듯이 [2, 1, 4], [4, 1, 2], [1, 4, 2] 를 모두 [1, 2, 4] 로 만들어 같은 네트워크인지 판별하기 위함이다.

(26) 정렬된 모든 경로를 순회하며, 각 순회 경로를 string으로 연결해준다. "124"와 같은 형태가 될 것이다.

굳이 string으로 바꾸는 이유는, 이렇게해야 set을 통해 중복을 제거할 수 있기 때문이다.

무슨 말이냐면,

예를들어 `[[1, 2, 3, 4, 5], [1, 2, 3, 4, 5], [1, 2, 4], [1, 2, 4], [1, 2, 4]]` 와 같은 리스트가 있다면,

set을 통해 `{[1, 2, 3, 4, 5], [1, 2, 4]}` 와 같이 중복을 제거할 수 없다.

하지만 각 요소를 string으로 연결해 `["12345", "12345", "124", "124", "124"]` 와 같은 형태로 바꾸어준다면,

set을 통해 {"12345", "124"} 와 같이 중복을 제거할 수 있다.

중복을 제거한 후에는 길이를 구해 리턴하면 끝이다.



## 분석

최근 그래프를 공부하고있는데, 확실히 예전에 못풀던 몇 개의 문제를 풀 수 있게 되었다.

비록 아직 숙달되지 않아서 시간이 많이 걸리긴 하지만, 아예 아이디어조차 떠오르지 않던 시절에 비하면 많은 발전이다.

하지만 아직 BFS, DFS를 각각 어떤 상황에서 활용하면 좋을지에대한 명확한 개념이 잡히지 않는다.

대충 흐릿흐릿하게 감은 잡히는데, 실무에서 사용해보면 확실히 차이점을 느낄 수 있을 것 같다.

공부하고 문제푸는 것으로 만족하지 말고, 실무에 적극 활용해서 배운것들을 실제로 써먹을 수 있도록 해보자.

그래프나 DFS, BFS등의 개념을 생각하며 문제를 풀려니까 머리가 복잡해진다.

<br/>

그래도 이러한 개념을 아예 모를때에는 솔루션 아이디어조차 떠오르지 않아서 막막했었는데,

개념을 어느정도 익히니 확실히 아이디어는 금방 떠오르는 것 같고, 구현단계에서 고민을 하게되니 한결 낫다.

<br/>

예전에 거짓말이라는 문제를 풀 때 어떤 사람 풀이중에 그래프 개념을 활용한 것이 있었는데,

그때는 코드도 이해되지 않고 문제만 해결하면 된다는 생각이 있었기때문에 무심코 넘어갔었다.

<br/>

물론 반드시 어떤 문제에는 어떤 개념을 활용해서 풀어야 한다는 절대적인 규칙같은것은 없지만,

그래도 여러 개념을 두루두루 익혀놓으면 보다 효율적인 접근법이 수월하게 떠오른다는 강점이 생기는 것 같다.

너무 개념에 매몰되지는 말되, 유사시 떠올릴 수 있을 정도까지는 이것저것 다양하게 익혀보자.