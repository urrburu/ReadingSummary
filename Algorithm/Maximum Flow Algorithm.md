그래프의 최대 매칭 (Maximum Matching)은 두 간선이 같은 정점을 공유하지 않는 간선의 최대 집합을 말한다.

이분 그래프 (Bipartited Graph)는 그래프의 모든 정점을 두 집합 A와 B로 나눌 수 있는 그래프이다. 모든 간선의 끝 점은 A에 하나, B에 하나 있어야 한다. 이분 그래프에서 최대 매칭을 구하는 문제는 Maximum Flow 알고리즘을 이용해서 풀 수 있다.

거의 이분 그래프는 모든 정점이 집합 A = {A1, A2, …, AnA}와 B = {B1, B2, …, BnB}로 나누어져 있고, 모든 간선의 끝 점은 A에 하나, B에 하나있는 그래프이다. 여기까지는 이분 그래프와 동일한 모양을 갖는다. 거의 이분 그래프는 이분 그래프와 다르게 nA-1 + nB-1개의 간선을 추가로 가진다. 추가가 되는 간선은 Ai에서 Ai+1로 가는 간선 (1 ≤ i ≤ nA-1)과 Bi에서 Bi+1로 가는 간선 (1 ≤ i ≤ nB-1)이다. 따라서, 끝 점이 A에 하나, B에 하나 있는 간선의 개수를 M이라고 했을 때, 정점의 수가 nA + nB인 거의 이분 그래프의 간선의 개수는 M + nA-1 + nB-1이 된다.

이 상황에서 가장 큰 매칭을 최대매칭문제, 매칭 문제라고 부른다.

# 네트워크 유량을 이용하면 이분매칭을 아주 간단하게 풀 수 있다.


```C++
const int MAX_N = 100, MAX_M = 100;
int n, m;
bool adj[MAX_N][MAX_M];
vector<int> aMatch, bMatch;
vector<bool> visited;

bool dfs(int a){
    if(visited[a]) return false;
    visited[a] = true;
    for(int b = 0; b < m; b++)
        if(adj[a][b])
            if(bMatch[b] == -1 || dfs(bMatch[b])){
                aMatch[a] = b;
                bMatch[b] = a;
                return true;
            }
    return false;
}

int bipartiteMatch(){
    aMatch = vector<int>(n, -1);
    bMatch = vector<int>(m, -1);
    int size = 0;
    for(int start = 0; start < n; start++){
        visited = vector<bool>(n, false);
        if(dfs(start))++size;
    }
    return size;
}
```

이 코드가 이용하는 첫번째 속성은 이분 매칭을 푸는 유량 네트워크에서 최대 유량은 O(|V|)로 정해져 있다는 것이다.

포드-풀커슨 알고리즘을 DFS로 구현할 때 큰 문제는 수행시간이 총 유량에 비례한다는 점이다. 하지만 이렇게 최대 유량이 제한되어 있는 경우, DFS를 사용해도 문제가 없다. 

코드에서 DFS는 증가 경로를 찾아냈는지 여부를 반환하는데 참이 반환되었을 경우, 해당 간선을 따라 유량을 보내기만 하면 된다. 이것으로 탐색 스패닝 트리상에서 각 정점을 조상하고 소스로부터 싱크까지의 경로를 찾는 작업을 생략할 수 있다. 모든 간선의 용량이 1 이기 때문에 모든 증가 경로의 용량이 1이라는점도 도움이 된다. 
또 다른 속성은 증가 경로를 찾기 위해 탐색을 하는 도중 B에 포함된 정점에 도착했는데, 이 정점이 이미 매칭된 상황이라면 이 간선에 잔여 용량은 남아 있지 않을 것이다. 따라서 현재 정점으로 흘러오는 유량을 상쇄할 수 밖에 없는데, 현재 정점으로 유량을 보내 주는 정점은 현재 정점과 매칭된 정점밖에 없다. dfs에서 a에 인접한 정점 b에 대해 b가 이미 매칭된 상태라면 b에 인접한 정점들을 순회하는 대신 b에 매칭된 정점 bmatch[b]로 곧장 재귀호출을 하는 부분은 이 속성을 이용한 것이다.