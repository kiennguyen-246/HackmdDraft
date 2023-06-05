# Bài toán Luồng cực đại trên mạng

Luồng cực đại và Lát cắt hẹp nhất là những bài toán quan trọng trong lớp các bài toán về đồ thị. Trong lập trình thi đấu, do việc cài đặt tương đối dài và có độ phức tạp lớn, chúng thường ít phổ biến hơn những bài toán đồ thị khác. Bài viết sau đây sẽ giới thiệu một vài nội dung cơ bản về bài toán luồng cực đại và các thuật toán liên quan.

## Một số khái niệm sử dụng trong bài viết
Để hiểu hơn về phần này, bạn đọc nên có sẵn những kiến thức cơ bản về đồ thị, cũng như biểu diễn và duyệt (BFS, DFS, ...) chúng. 

Bài viết sẽ không nêu lại các khái niệm cơ bản về đồ thị.

- **Ký hiệu đồ thị $G(V, E)$**: Đồ thị tập các đỉnh là $V$ và tập các cạnh là $E$
- **Cung**: cạnh có hướng trên đồ thị.
- **Cạnh/cung đi vào đỉnh $u$**: Các cạnh có dạng $(v, u)$, với $v$ là đỉnh bất kỳ của đồ thị.
- **Cạnh/cung đi ra khỏi đỉnh $u$**: Các cạnh có dạng $(u, v)$, với $v$ là đỉnh bất kỳ của đồ thị.
- **Đường đi đơn từ $S$ tới $T$**: Dãy các đỉnh $S, u_1, u_2, ..., u_k, T$ sao cho giữa hai đỉnh liên tiếp trong dãy tồn tại một cạnh nối chúng theo đúng chiều như trên.

## Bài toán Luồng cực đại
### Các định nghĩa
Một đồ thị $G(V, E)$ được gọi là **mạng** (network) nếu nó là đồ thị **có hướng**, trong đó:
- Tồn tại một đỉnh $S$ không có cung đi vào, gọi là **đỉnh phát/nguồn** (source)
- Tồn tại một đỉnh $T$ không có cung đi ra, gọi là **đỉnh thu/đích** (sink)
- Mỗi cung $(u, v)$ được gán một trọng số $c(u, v)$, gọi là **thông lượng** (capacity) của cung.

![](https://hackmd.io/_uploads/rkBl97iL3.png)

*Một mạng hợp lệ. Đỉnh phát và đỉnh thu được đánh dấu bằng hai màu khác.*

Một **luồng** (flow) trên mạng $G(V, E)$ là một phép gán cho mỗi cung $(u, v)$ một số thực $f(u, v)$ thoả mãn:
- Luồng trên mỗi cung có giá trị không vượt quá thông lượng của cung đó:
$0 <= $f(u, v) <= c(u, v) \forall u, v \in V$
- Với mọi đỉnh $v$ không trùng với đỉnh phát $S$ và đỉnh thu $T$, tổng luồng trên các cung đi vào $v$ bằng tổng luồng trên các cung đi ra $v$.
$\sum_{
\begin{subarray}{l}
   v \in V, \exists (v, u) \in E
\end{subarray}} f(v, u) = 
\sum_{
\begin{subarray}{l}
   w \in V, \exists (u, w) \in E
\end{subarray}} f(v, u)$
Tính chất này tương đối giống với định luật I Kirchoff của dòng điện.
- Giá trị $f(u, v)$ được gọi là **luồng trên cung $(u, v)$**
- **Giá trị của luồng** là tổng luồng trên các cung đi ra khỏi đỉnh phát, cũng chính là tổng luồng trên các cung đi ra khỏi đỉnh thu.

![](https://hackmd.io/_uploads/Syb-57oL2.png)

*Một luồng hợp lệ. Giá trị `a/b` trên cạnh được hiểu là `f/c`, với `f` là luồng còn `c` là thông lượng.*

Có rất nhiều hình ảnh thực tế để miêu tả một mạng và luồng như trên, như một mạng điện, một mạng kết nối dữ liệu giữa các máy, ... Nếu ta hiểu mạng như một hệ thống ống nước, nó sẽ như sau:
- Nước chảy qua một hệ thống các ống, từ nguồn nước (đỉnh phát) đến bồn chứa (đỉnh thu).
- Mỗi ống có một giới hạn nhất định. Lượng nước chảy qua ống này không thể vượt quá giới hạn này.
- Hiển nhiên, tại mỗi điểm nút (trừ điểm đầu và điểm cuối), có bao nhiêu nước đến thì sẽ có bấy nhiêu nước chảy đi. Nước không tự sinh ra và mất đi, chúng chỉ chảy từ điểm này sang điểm khác.
- Và tất nhiên tổng lượng nước xuất hiện trong mạng sẽ là lượng nước ta cấp cho nguồn. Bể chứa cũng sẽ thu được từng đó nước.

### Bài toán
Cho mạng $G(V, E)$ với $m$ đỉnh và $n$ cạnh có đỉnh phát là $S$, đỉnh thu là $T$ ($n \le 1000, 1 \le S, T \le n$). Hãy tìm một luồng trong mạng sao cho giá trị của nó là lớn nhất. 
Luồng này gọi là **luồng cực đại** trên mạng $G$.
*Đề bài VNOI*: [NKFLOW](https://oj.vnoi.info/problem/nkflow)

## Phương pháp Ford-Fulkerson. Thuật toán Edmonds-Karp.
**Đôi lời về lịch sử thuật toán:**

Năm 1956, L. R. Ford Jr. và D. R. Fulkerson đề xuất một phương pháp để tìm ra luồng cực đại trên mạng. Tuy nhiên, phương pháp này không chỉ rõ việc tìm *đường tăng luồng* như thế nào. Đến năm 1972, Jack Edmonds and Richard Karp đã hoàn thiện phương pháp trên bằng cách sử dụng thuật BFS để tìm *đường tăng luồng*. 

Nhiều tài liệu mà chúng ta đang dùng có sử dụng cụm từ "thuật toán Ford-Fulkerson" để gọi thuật tìm luồng cực đại hoàn chỉnh, và biến "thuật toán Edmonds-Karp" thành một thuật xa lạ kì quặc nào đó. Điều này có lẽ cũng ... không hẳn là sai. Còn bài viết này sẽ sử dụng tên Edmonds-Karp cho thuật toán, và chỉ gọi là "phương pháp Ford-Fulkerson" thôi. Bạn đọc muốn hiểu theo tên nào cũng được.

### Đường tăng luồng
Với mọi cung $(u, v)$, ta định nghĩa thêm giá trị $f(v, u) = -f(u, v)$. Về mặt ý nghĩa, việc định nghĩa này cho ta biết luồng hiện tại trên cung này có thể giảm đi một lượng đúng bằng như vậy. Còn vì sao lại là $f(v, u)$ chứ không phải cái gì khác, chúng ta sẽ biết ở phần sau.
Lưu ý rằng ta **không** định nghĩa $c(v, u) = c(u, v)$, giá trị này vẫn được mặc định bằng $0$.

Định nghĩa **luồng thặng dư** (residual flow) trên một cung tại một thời điểm là hiệu của thông lượng và giá trị luồng hiện tại trên cung đó:
$r(u, v) = c(u, v) - f(u, v)$
Giá trị này cũng áp dụng cho cả các cung đảo (cung có luồng âm), khi đó $r(v, u) = 0 - f(v, u) = f(u, v)$.

Với các giá trị $r(u, v)$ này, ta có thể xây dựng một **đồ thị tăng luồng/đồ thị thặng dư** (residual network), gồm các cung $(u, v)$, mỗi cung có trọng số là $r(u, v)$. Mỗi cạnh cho ta biết có thể tăng/giảm luồng trên đồ thị gốc bao nhiêu.

Một **đường tăng luồng** (augmenting path) là một đường đi đơn trên đồ thị tăng luồng. Đối chiếu lại với đồ thị gốc, đó sẽ là một đường đi đơn (có thể đi ngược chiều) qua những cung có $r(u, v) > 0$. Trên đường này, chúng ta có thể thực hiện tăng giá trị của luồng trên mỗi cung.
