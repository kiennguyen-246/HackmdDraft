# Bài toán Luồng cực đại trên mạng

Luồng cực đại và Lát cắt hẹp nhất là những bài toán quan trọng trong lớp các bài toán về đồ thị. Trong lập trình thi đấu, do việc cài đặt tương đối dài và có độ phức tạp lớn, chúng thường ít phổ biến hơn những bài toán đồ thị khác. Bài viết sau đây sẽ giới thiệu một vài nội dung cơ bản về bài toán luồng cực đại và các thuật toán liên quan.

## Một số khái niệm sử dụng trong bài viết
Để hiểu hơn về phần này, bạn đọc nên có sẵn những kiến thức cơ bản về đồ thị, cũng như biểu diễn và duyệt (BFS, DFS, ...) chúng. 

Bài viết sẽ không nêu lại các khái niệm cơ bản về đồ thị.

- **Ký hiệu đồ thị $G(V, E)$**: Đồ thị tập các đỉnh là $V$ và tập các cạnh là $E$
- **Cạnh đi vào đỉnh $u$**: Các cạnh có dạng $(v, u)$, với $v$ là đỉnh bất kỳ của đồ thị.
- **Cạnh đi ra khỏi đỉnh $u$**: Các cạnh có dạng $(u, v)$, với $v$ là đỉnh bất kỳ của đồ thị.
- **Đường đi đơn từ $s$ tới $t$**: Dãy các đỉnh $s, u_1, u_2, ..., u_k, t$ sao cho giữa hai đỉnh liên tiếp trong dãy tồn tại một cạnh nối chúng theo đúng chiều như trên.

## Bài toán Luồng cực đại

### Các định nghĩa
Một đồ thị $G(V, E)$ được gọi là **mạng** (network) nếu nó là đồ thị **có hướng**, trong đó:
- Tồn tại một đỉnh $s$ không có cạnh đi vào, gọi là **đỉnh phát/nguồn** (source)
- Tồn tại một đỉnh $t$ không có cạnh đi ra, gọi là **đỉnh thu/đích** (sink)
- Mỗi cạnh $(u, v)$ được gán một trọng số $c(u, v)$, gọi là **khả năng thông qua** (capacity) của cạnh.

![](https://hackmd.io/_uploads/rkBl97iL3.png)

*Một mạng hợp lệ. Đỉnh phát và đỉnh thu được đánh dấu bằng hai màu khác.*

Một **luồng** (flow) trên mạng $G(V, E)$ là một phép gán cho mỗi cạnh $(u, v)$ một số thực $f(u, v)$ thoả mãn:
- Luồng trên mỗi cạnh có giá trị không vượt quá khả năng thông qua của cạnh đó:
$0 \le f(u, v) \le c(u, v), \forall u, v \in V$
- Với mọi đỉnh $v$ không trùng với đỉnh phát $s$ và đỉnh thu $t$, tổng luồng trên các cạnh đi vào $v$ bằng tổng luồng trên các cạnh đi ra $v$. Tính chất này tương đối giống với định luật I Kirchoff của dòng điện.
$\sum\limits_{v \in V, \exists (v, u) \in E} f(v, u) = 
\sum\limits_{w \in V, \exists (u, w) \in E} f(u, w)$
- Giá trị $f(u, v)$ được gọi là **luồng trên cạnh $(u, v)$**
- **Giá trị của luồng** là tổng luồng trên các cạnh đi ra khỏi đỉnh phát, cũng chính là tổng luồng trên các cạnh đi ra khỏi đỉnh thu.

![](https://hackmd.io/_uploads/Syb-57oL2.png)

*Một luồng hợp lệ. Giá trị `f/c` trên cạnh biểu diễn luồng/khả năng thông qua.*

Một **lát cắt** (cut) $(A, B)$ trên mạng là một cách chia các đỉnh trên đồ thị mạng thành hai tập hợp sao cho $s \in A, t \in B$.
Tổng các giá trị khả năng thông qua trên các cạnh nối giữa một đỉnh thuộc $A$ và một đỉnh thuộc $B$ được gọi là **khả năng thông qua** (cut value) của lát cắt $(A, B)$
 
 $c(A, B) = \sum\limits_{u \in A, v \in B} c(u, v)$
 
 ![](https://hackmd.io/_uploads/BJm1po283.png)

*Một lát cắt hợp lệ với hai tập $A = \{1, 2, 5\}$ và $B = \{3, 4, 6\}$. Mỗi tập con của lát cắt được đánh dấu bằng một màu khác nhau. Lát cắt này có khả năng thông qua là $6 + 5 + 1 + 6 = 17$.*

**Định lý**: Trên cùng một mạng, tất cả mọi luồng đều có giá trị không lớn hơn khả năng thông qua của một lát cắt bất kỳ.

**Chứng minh**:

Xét luồng có giá trị $f$ và lát cắt $(A, B)$ trên một mạng bất kỳ. Ta có: 

$f = 
\sum\limits_{u \in A, v \in B} f(u, v) - 
\sum\limits_{u \in B, v \in A} f(u, v) \\
\le
\sum\limits_{
\begin{subarray}{l}
   u \in A, v \in B
\end{subarray}} f(u, v) \\
\le
\sum\limits_{
\begin{subarray}{l}
   u \in A, v \in B
\end{subarray}} c(u, v) \\
= c(A, B)$ (đpcm)

Có rất nhiều hình ảnh thực tế để miêu tả một mạng và luồng như trên, như một mạng điện, một mạng kết nối dữ liệu giữa các máy, ... Nếu ta hiểu mạng như một hệ thống ống nước, nó sẽ như sau:
- Nước chảy qua một hệ thống các ống, từ nguồn nước (đỉnh phát) đến bồn chứa (đỉnh thu).
- Mỗi ống có một giới hạn nhất định. Lượng nước chảy qua ống này không thể vượt quá giới hạn này.
- Hiển nhiên, tại mỗi điểm nút (trừ điểm đầu và điểm cuối), có bao nhiêu nước đến thì sẽ có bấy nhiêu nước chảy đi. Nước không tự sinh ra và mất đi, chúng chỉ chảy từ điểm này sang điểm khác.
- Và tất nhiên tổng lượng nước xuất hiện trong mạng sẽ là lượng nước ta cấp cho nguồn. Bể chứa cũng sẽ thu được từng đó nước.
- Còn một lát cắt là một cách bỏ đi các ống sao cho nước không thể chảy từ nguồn đến bể nữa bằng bất kỳ cách nào.

### Bài toán
**Đề bài**: Cho mạng $G(V, E)$ với $m$ đỉnh và $n$ cạnh có đỉnh phát là $s$, đỉnh thu là $t$ ($n \le 1000, 1 \le s, t \le n$). Hãy tìm một luồng trong mạng sao cho giá trị của nó là lớn nhất. 
Luồng này gọi là **luồng cực đại** trên mạng $G$.

*Đề bài VNOI*: [NKFLOW](https://oj.vnoi.info/problem/nkflow)

## Phương pháp Ford-Fulkerson. Thuật toán Edmonds-Karp.
**Đôi lời về lịch sử thuật toán:**

Năm 1956, L. R. Ford Jr. và D. R. Fulkerson đề xuất một phương pháp để tìm ra luồng cực đại trên mạng. Tuy nhiên, phương pháp này không chỉ rõ việc tìm *đường tăng luồng* như thế nào. Đến năm 1972, Jack Edmonds and Richard Karp đã hoàn thiện phương pháp trên bằng cách sử dụng thuật BFS để tìm *đường tăng luồng*. 

Nhiều tài liệu mà chúng ta đang dùng có sử dụng cụm từ "thuật toán Ford-Fulkerson" để gọi thuật tìm luồng cực đại hoàn chỉnh, và biến "thuật toán Edmonds-Karp" thành một thuật xa lạ kì quặc nào đó. Điều này có lẽ cũng ... không hẳn là sai. Bài viết này sẽ sử dụng tên Edmonds-Karp cho thuật toán, và chỉ gọi là "phương pháp Ford-Fulkerson" thôi. Bạn đọc muốn hiểu theo cách nào cũng được.

### Đường tăng luồng
Với mọi cạnh $(u, v)$, ta định nghĩa thêm giá trị $f(v, u) = -f(u, v)$. Về mặt ý nghĩa, việc định nghĩa này cho ta biết luồng hiện tại trên cạnh này có thể giảm đi một lượng bao nhiêu.
Lưu ý rằng ta **không** định nghĩa $c(v, u) = c(u, v)$, giá trị này vẫn được mặc định bằng $0$.

Định nghĩa **luồng thặng dư** (residual flow) trên một cạnh tại một thời điểm là hiệu của khả năng thông qua và giá trị luồng hiện tại trên cạnh đó:
$r(u, v) = c(u, v) - f(u, v)$
Giá trị này cũng áp dụng cho cả các cạnh đảo (cạnh có luồng âm), khi đó $r(v, u) = 0 - f(v, u) = f(u, v)$.

Với các giá trị $r(u, v)$ này, ta có thể xây dựng một **đồ thị tăng luồng/đồ thị thặng dư** (residual network), gồm các cạnh $(u, v)$, mỗi cạnh có trọng số là $r(u, v)$. Mỗi cạnh cho ta biết có thể tăng/giảm luồng trên đồ thị gốc bao nhiêu.

Một **đường tăng luồng** (augmenting path) là một đường đi đơn trên đồ thị tăng luồng. Đối chiếu lại với đồ thị gốc, đó sẽ là một đường đi đơn (có thể đi ngược chiều) qua những cạnh có $r(u, v) > 0$. Trên đường này, chúng ta có thể thực hiện tăng giá trị của luồng trên mỗi cạnh.

![](https://hackmd.io/_uploads/H1DsnroU2.png)

Đường màu xanh-đỏ là một đường tăng luồng trên đồ thị tăng luồng trên. Các cạnh đứt chính là các cạnh "ngược" so với mạng ban đầu; chúng có giá trị $f$ âm.

![](https://hackmd.io/_uploads/r1OzTrj83.png)

Đem đối chiếu đồ thị tăng luồng trên về đồ thị gốc, ta được đường tăng luồng như thế này. Trong hình dưới, giá trị của luồng ($f$) trên các cạnh thuộc đường tăng luồng đã được tăng $1$ đơn vị.

Việc xây dựng cả một đồ thị tăng luồng sau từng bước rất tốn thời gian và bộ nhớ. Vì vậy, chúng ta sẽ chỉ sử dụng đồ thị gốc, và thực hiện tìm đường tăng luồng trực tiếp trên đồ thị này.

Còn nếu bạn muốn hiểu theo kiểu "ống nước" thì đường tăng luồng có thể coi như một đường nước chảy từ nguồn đến bể chứa. Đối với các "ống đi ngược" như "ống" $(5, 2)$ trên hình, ta hiểu đây là một cách phân phối lại nước: thêm $1$ đơn vị nước vào nút $5$ sẽ dẫn đến việc phải bớt $1$ đơn vị từ ống $(2, 5)$ để đảm bảo đoạn sau vẫn đủ nước; ở đầu $2$ phần nước thay vì chảy vào ống này đi ra đầu $5$ thì nó sẽ đưa phần nước này sang ống $(2, 4)$.

### Thuật toán
Đầu tiên ta gán giá trị mọi luồng trên tất cả mọi cạnh thành $0$.

Ta đi tìm một đường tăng luồng có thể có trên đồ thị. Nhắc lại rằng, đường tăng luồng chỉ chứa các cạnh (kể cả cạnh ngược) có $r > 0$, hay $c - f > 0$.

Trên đường này, với mỗi cạnh $(u, v)$, ta tăng giá trị của luồng trên cạnh này ($f(u, v)$) lên $\Delta$ đơn vị, với $\Delta$ là giá trị $r(u, v)$ nhỏ nhất trên đường tăng luồng vừa tìm được. Đồng thời, ta cũng phải giảm $f(v, u)$ đi $\Delta$ để luôn có $f(u, v) = -f(v, u)$.

Một cách dễ hiểu hơn thì tại bước này, ta tăng giá trị của luồng trên đường vừa tìm được đến mức tối đa có thể.

Ta lặp đi lặp lại việc tăng luồng cho đến khi nào không thể tìm được đường tăng luồng nữa thì thôi. Khi đó, giá trị của luồng trong cả mạng chính là luồng cực đại mà ta cần tìm.

![](https://hackmd.io/_uploads/rykkYIhLh.gif)

Hình GIF trên mô tả phương pháp Ford-Fulkerson trên mạng ta vừa lấy ví dụ trong bài viết này. Chú ý rằng có một bước, chúng ta đã phải sử dụng cạnh ngược.

### Tính đúng đắn

**Định lý**: Phương pháp Ford-Fulkerson cho kết quả là luồng cực đại.

**Chứng minh**:

Giả sử thuật toán cho một luồng có giá trị là $f^{*}$

Tại bước cuối cùng của thuật toán, chúng ta không thể tìm được một đường tăng luồng nào từ $s$ tới $t$ nữa. Gọi $S$ là tập tất cả các đỉnh trên đồ thị có thể đi tới từ $s$ theo một đường tăng luồng, và $T$ là tập các đỉnh còn lại. Khi đó $(S, T)$ là một lát cắt trên mạng.

Ta chứng minh $f^{*} = c(S, T)$. Nhắc lại rằng $c(S, T)$ là khả năng thông qua của lát cắt $(S, T)$ 

Gọi $(u, v)$ là một cạnh bất kỳ nối từ $S$ sang $T$, với $u \in S, v \in T$. Cạnh $(u, v)$ phải thoả mãn $f(u, v) = c(u, v)$, nếu không sẽ tồn tại một đường tăng luồng đi từ $s$ sang tập $T$, trái với giả thiết.

Lại gọi $(u', v')$ là một cạnh bất kỳ nối từ $T$ sang $S$, với $u' \in T, v' \in S$. Nếu $f(u', v') > 0$, sẽ tồn tại một đường tăng luồng đi qua cạnh ngược $(v', u')$ do $f(v', u') < 0 = c(u', v')$, trái với giả thiết không tồn tại đường đi từ $S$ sang $T$.

Lấy tổng tất cả các đẳng thức $f(u, v) = c(u, v)$ và $f(v', u') = 0$ với mọi cặp đỉnh thoả mãn một trong hai trường hợp trên, ta được:
$f^* = c(A, B)$

Nhưng theo định lý về luồng và lát cắt đã trình bày ở trên ta có $f^* \le c(A, B)$ nên đây là luồng cực đại. (đpcm)

**Hệ quả**: 
- Khả năng thông qua của lát cắt hẹp nhất trên một mạng bằng giá trị của luồng cực đại trên mạng đó. **Lát cắt hẹp nhất** (mincut) là lát cắt có khả năng thông qua nhỏ nhất trong số mọi lát cắt thuộc mạng.
- Nếu mọi giá trị $c$ trên luồng đều là số nguyên thì giá trị luồng cực đại cũng là số nguyên.

### Tìm đường tăng luồng
Để tìm đường tăng luồng, ta chỉ phải tìm một đường để đi từ $s$ tới $t$, qua các cạnh có $r(u, v) = c(u, v) - f(u, v) > 0$. Đây chỉ là một bài toán duyệt đồ thị đơn giản, ta có thể thử áp dụng DFS, BFS, ... để duyệt.

Hai thuật BFS và DFS có độ phức tạp giống nhau, nhưng trên thực tế BFS chạy nhanh hơn DFS một chút khi đi tìm đường tăng luồng. Thuật Edmonds-Karp sử dụng BFS để tìm đường tăng luồng.

### Cài đặt
``` cpp=
#include <bits/stdc++.h>

using namespace std;

const int maxn = 1001;

int n, m, s, t;
vector <int> adj[maxn];    //đồ thị lưu kiểu danh sách kề
int c[maxn][maxn], f[maxn][maxn], trace[maxn], maxFlow;

//BFS để tìm đường tăng luồng
void bfs()
{
    fill(trace, trace + n + 1, 0);
    trace[s] = -1;

    queue <int> bfsQueue;
    bfsQueue.push(s);

    while (!bfsQueue.empty())
    {
        int u = bfsQueue.front();
        bfsQueue.pop();
        for (auto v : adj[u])
        {
            //Không dẫm lại đường cũ theo đúng luật BFS
            if (trace[v]) continue;
			
	    //Không đi qua cạnh có r(u, v) = c(u, v) - f(u, v) = 0
            if (f[u][v] - c[u][v] == 0) continue;
			
            //Các công việc còn lại của BFS
            trace[v] = u;
            bfsQueue.push(v);
        }
    }
}

//Hàm tăng luồng
void incFlow()
{
    //Đi ngược theo đường tăng luồng để tìm giá trị delta = c - f tốt nhất
    int delta = 1e9 + 7;
    int v = t;
    while (v != s)
    {
        int u = trace[v];
        delta = min(delta, c[u][v] - f[u][v]);
        v = u;
    }

    maxFlow += delta;
	
    //Đi ngược theo đường tăng luồng một lần nữa để cập nhật giá trị f
    v = t;
    while (v != s)
    {
        int u = trace[v];
        f[u][v] += delta;
        f[v][u] -= delta;
        v = u;
    }

}

int32_t main()
{
    cin >> n >> m >> s >> t;
    for (int u, v, i = 1; i <= m; i ++)
    {
        cin >> u >> v;
        cin >> c[u][v];
        adj[u].push_back(v);
        adj[v].push_back(u);	//lưu thêm cạnh ngược để có thể chạy qua nó khi tăng luồng
    }

    maxFlow = 0;
    trace[t] = -1;
	
    //Tăng luồng đến khi không tăng được nữa
    while (trace[t])
    {
        bfs();
        if (trace[t]) incFlow();
    }

    cout << maxFlow;

    return 0;
}
```

### Độ phức tạp
Trong bài toán chúng ta xét, tất cả các khả năng thông qua của các cạnh đều là số nguyên. Do đó, mỗi bước tăng luồng đều làm tăng giá trị của luồng lên ít nhất $1$ đơn vị. Khi sử dụng thuật BFS hoặc DFS để tìm đường tăng luồng, độ phức tạp sẽ vào cỡ $O(E)$. Do đó, độ phức tạp của phương pháp Ford-Fulkerson sẽ là $O(mf)$, với $f$ là giá trị của luồng cực đại trên mạng.

Với thuật toán Edmonds-Karp, khi sử dụng BFS, sau $O(mn)$ lần tìm đường tăng luồng, chúng ta sẽ tìm được kết quả. Độ phức tạp của thuật toán này là $O(m^2n)$.
Bạn có thể tham khảo chứng minh độ phức tạp này tại [đây](https://brilliant.org/wiki/edmonds-karp-algorithm/).

## Thuật toán Dinitz


## Bài toán ví dụ
**Đề bài**: Có $n$ người, $n$ ($1 < n \le 200$) việc. Người thứ $i$ thực hiện công viêc $j$ mất $C[i, j]$ đơn vị thời gian. Giả sử tất cả bắt đầu vào thời điểm $0$, hãy tìm cách bố trí mỗi công việc cho mỗi người sao cho thời điểm hoàn thành công việc là sớm nhất có thể.
*Đề bài VNOI*: [ASSIGN1](https://oj.vnoi.info/problem/assign1)

**Phân tích**:
Đề bài của bài toán không hề có một dấu vết gì của "luồng cực đại" hay "lát cắt hẹp nhất" cả, thậm chí còn không hề nói gì đến đồ thị. Tuy nhiên, nếu ta biểu diễn bài toán một đồ thị $2n$ đỉnh, với $n$ đỉnh bên trái là $n$ người, $n$ đỉnh bên phải là $n$ công việc, và cạnh nối giữa một đỉnh $i$ bên trái và một đỉnh $j$ bên phải có trọng số là thời gian người $i$ làm xong việc $j$, thì ta sẽ chỉ cần phải chọn $n$ cạnh không chung đỉnh có tổng trọng số nhỏ nhất thôi. Lúc này, bài toán đã xuất hiện một số tính chất giống với bài toán lát cắt hẹp nhất. Tuy nhiên, để đồ thị có dạng mạng, ta phải thêm một đỉnh nguồn và một đỉnh thu. Từ đó, ta sẽ thêm đỉnh nữa, đỉnh nguồn nối với các đỉnh trái và đỉnh thu nối với các đỉnh phải. Trọng số của các cạnh chắc chắn cần là $\infty$, để tránh việc lấy lát cắt hẹp nhất rơi vào những cạnh trung gian này.

Phần cài đặt thuật toán trên sẽ dành cho bạn đọc.

**Chú ý thêm**: Đồ thị ta vừa tạo là một **đồ thị hai phía** (đồ thị có thể chia các đỉnh làm hai tập sao cho các đỉnh cùng một tập đôi một không có cạnh nối trực tiếp). Với những bài toán như vậy, sử dụng phương pháp cặp ghép cực đại sẽ cho hiệu quả cao hơn, đồng thời cũng dễ cài đặt hơn dùng luồng cực đại.