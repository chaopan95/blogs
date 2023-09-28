## 基础

## 有向图 & 无向图

## 最小生成树

## 最短路径
各种算法对比

| 算法 | 复杂度 | 源 | 稀疏/稠密图 | 优势 | 劣势 |
| :----: | :----: | :----: | :----: | :----: | :----: |
| Floyd |$O(V^{3})$ | 多源 | 稠密 | 实现简单 | 不适用于大数据集 |
| Heap-Dijkstra | $O(V\log V)$ | 单源 | 稠密 | 稳定 | 不适用于负权重图 |
| Bellman-Ford | $VE$ | 单源 | 稀疏 | 适用于负权重图 | 负权重成环图 |
| SPFA | kE (k mean is 2) | -- | -- | -- | -- |

### Bellman-Ford

### Dijkstra
```cpp
int Dijkstra(int **mat, int n, int b, int e) {
    int *dist = new int [n]{};
    for (int i = 0; i < n; i++) { dist[i] = -1; }
    bool *V = new bool [n]{};
    V[b] = true;
    dist[b] = 0;
    int curNode = b;
    int num = 0;
    while (num < n) {
        for (int j = 0; j < n; j++) {
            if (mat[curNode][j] && !V[j]) {
                if (dist[j] == -1) {
                    dist[j] = dist[curNode] + mat[curNode][j];
                } else if (dist[curNode] + mat[curNode][j] < dist[j]) {
                    dist[j] = dist[curNode] + mat[curNode][j];
                }
            }
        }
        
        int min = (1ll<<31) - 1;
        for (int j = 0; j < n; j++) {
            if (dist[j] != -1 && !V[j] && dist[j] < min) {
                min = dist[j];
                curNode = j;
            }
        }
        V[curNode] = true;
        num++;
    }
    int res = dist[e];
    delete []dist;
    delete []V;
    return res;
}

void ShortPath() {
    int n = 4;
    int **mat = new int *[n];
    for (int i = 0; i < n; i++) {
        mat[i] = new int [n]{};
    }
    mat[1-1][2-1] = mat[2-1][1-1] = 5;
    mat[1-1][3-1] = mat[3-1][1-1] = 6;
    mat[2-1][4-1] = mat[4-1][2-1] = 8;
    mat[3-1][4-1] = mat[4-1][3-1] = 6;
    
    int b = 1, e = 3;
    for (int i = 0; i < n; i++) {
        for (int j = i; j < n; j++) {
            if (Dijkstra(mat, n, i, j) != Floyd(mat, n, i, j)) {
                cout << i << ' ' << j << endl;
            }
        }
    }
    cout << Dijkstra(mat, n, b, e) << endl;
    cout << Floyd(mat, n, b, e) << endl;
 
    for (int i = 0; i < n; i++) { delete []mat[i]; }
    delete []mat;
}
```

### Floyd
```cpp
int Floyd(int **mat, int n, int b, int e) {
    int INF = (1ll << 31) - 1;
    int **dist = new int *[n];
    for (int i = 0; i < n; i++) {
        dist[i] = new int [n]{};
        for (int j = 0; j < n; j++) {
            if (i == j) { continue; }
            if (mat[i][j] == 0) { dist[i][j] = INF; }
            else { dist[i][j] = mat[i][j]; }
        }
    }
    
    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (dist[i][k] == INF || dist[k][j] == INF) {
                    continue;
                }
                if (dist[i][j] == INF) {
                    dist[i][j] = dist[i][k] + dist[k][j];
                } else if (dist[i][j] > dist[i][k] + dist[k][j]) {
                    dist[i][j] = dist[i][k] + dist[k][j];
                }
            }
        }
    }
    
    int res = dist[b][e];
    for (int i = 0; i < n; i++) {
        delete []dist[i];
    }
    delete []dist;
    return res;
}
```

### A*