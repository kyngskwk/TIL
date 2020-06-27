# 최소 신장 트리(MST) - Kruskal 알고리즘

- KRUSKAL 알고리즘 : 간선을 하나씩 선택해서 MST를 찾는 알고리즘
  1. 최초, 모든 간선을 가중치에 따라 오름차순 으로 정렬
  2. 가중치가 가장 낮은 간선부터 선택하면서 트리를 증가시킴
     - 사이클이 존재하면 다음으로 가중치가 낮은 간선 선택
  3. n-1 개의 간선이 선택될 때 까지 2번을 반복함.

<img width="842" alt="kruskal1" src="https://user-images.githubusercontent.com/60081217/85918338-e84d2400-b89c-11ea-861f-f9b294905b89.png">

<img width="762" alt="kruskal2" src="https://user-images.githubusercontent.com/60081217/85918339-ea16e780-b89c-11ea-8280-535a06764ec6.png">

<img width="784" alt="kruskal3" src="https://user-images.githubusercontent.com/60081217/85918340-ebe0ab00-b89c-11ea-9dde-b10f94e2e85f.png">





### KRUSKAL 알고리즘

------

### 수도코드

```python
MST - KRUSKAL(G, w)
    A <- 0 // 0 : 공집합
    for vertex v in G.V // G.V : 그래프의 정점 집합
        make_set(v) // G.E : 그래프의 간선 집

    G.E에 포함된 간선들을 가중치 w에 의해 정렬 -> 정렬에 시간복잡도가 많이 나올듯

    for 가중치가 낮은 간선(u, v) in G.E 선택(n-1)
        if find_Set(u) != find_set(v)
            A <- A 와 {(u, v)} 의 합집합
            Union(u, v);
        
    return A
```

### 코드로 구현

```python
def make_set(x):
    p[x] = x

def find_set(x):
    if p[x] == x : return x
    else:
        p[x] = find_set(p[x]) # 부모를 찾아서 올라가자! 부모와 내가 같을 때 까지!
        return p[x]

def union(x, y):
    px = find_set(x)
    py = find_set(y)
    if rank[px] > rank[py]:
        p[py] = px
    else:
        p[px] = py
        if rank[px] == rank[py]:
            rank[py] += 1

# 정보를 읽어온다.
V, E = map(int, input())
edges = [list(map(int, input().split())) for i in range(E)]
# print(edges)
# [[0, 5, 60], [0, 1, 32], [0, 2, 31] ... ] : 간선 정보가 이런식으로 들어오면
# 간선을 간선가중체를 기준으로 정렬해준다.
# 람다는 함수를 주저리주저리 안쓰고 짧게 쓴다!
edges.sort(key=lambda x:x[2]) # 내가 만약에 x를 쓰면 x의 두번째 원소(가중치기준)를 가져와줘!
# print(edges) : [[3, 5, 18], [1, 2, 21]...

# make_set : 모든 정점에 대해 집합 생성
p = [0] * V
rank = [0] * V
for i in range(V):
    make_set(i)

# 모든 간선에 대해서 반복 -> V-1개의 간선이 선택될 때 까지
cnt = 0
result = 0
mst = []
for i in range(E):
    # 사이클이면 스킵!
    s, e, c = edges[i][0], edges[i][2]
    if find_set(s) == find_set(e):
        continue
    # 간선 선택
    result += c
    # -> mst에 간선 정보 더하기 / 두정점을 합친다. -> union 연산이다.
    mst.append(edges[i])
    union(s, e)

    cnt += 1
    if cnt == V-1:
        break
# 간선 선택 -> 싸이클이 아닐 떄만 ! -> 사이클이면 skip하자! ->
# 사이클 어케 알지? 선택하려는 간선의 두 정점이 서로 같은 집합이면 -> find_set으로 알 수 있어!
```