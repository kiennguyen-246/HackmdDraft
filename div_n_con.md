# Chia để trị

[TOC]

# Chia để trị

## Mở đầu

**Chia để trị (Divide and conquer, D&C)** chỉ việc chia nhỏ bài toán thành các phần nhỏ có cùng dạng với nó, tới khi có thể giải được một cách dễ dàng (thường là có ngay kết quả), sau đó dùng các kết quả này kết hợp lại để giải được bài toán lớn. Các bước để giải một bài toán chia để trị bao gồm:

-   Chia bài toán thành các bài toán con (thường là chia đôi hoặc xấp xỉ như vậy).
-   Giải các bài toán con. Nếu vẫn gặp khó khăn với các bài toán con, ta lại tiếp tục chia nó thành các bài toán nhỏ hơn cho tới khi dễ dàng tìm được kết quả.
-   Tổng hợp kết quả các bài toán con lại để có kết quả của bài toán lớn nếu cần.

Bạn đọc có thể thấy những bước trên có phần tương đồng với quá trình làm các bài toán đệ quy quay lui cơ bản. Các bài toán đó cũng có thể coi là chia để trị, và đệ quy quay lui cũng là một cách để cài đặt các thuật chia để trị.

Tư tưởng chia để trị cũng xuất hiện rất đa dạng và phổ biến trong các bài toán, thuật toán và cấu trúc dữ liệu: tìm kiếm nhị phân, cấp trúc cây tìm kiếm nhị phân (BST), heap, cây phân đoạn (segment tree), cây BIT/Fenwick, mảng thưa, các thuật toán sắp xếp, luỹ thừa nhanh, ...

## Xác định độ phức tạp

Ta nhắc lại một vài ký hiệu trước khi vào phần này:

-   $\text{log}_a b$: Logarit cơ số $a$ của $b$, là số thực $k$ thoả mãn $a^k = b$. Khi $a = 2$, ta có thể viết là $\text{log } b$ (khác với sách giáo khoa của Việt Nam, trong đó $\text{log }b = \text{log}_{10} b$.
-   $T(n)$: Thời gian chạy thuật toán với kích thước dữ liệu đầu vào là $n$, tính bằng số phép tính.
-   $O()$: Độ phức tạp worst case của thuật toán. Ký hiệu này cũng dùng phổ biến để chỉ độ phức tạp trung bình.
-   $\Theta()$: Độ phức tạp trung bình của thuật toán.

Chi tiết về các ký hiệu độ phức tạp và thời gian bạn đọc có thể tham khảo bài [Độ phức tạp thời gian](https://vnoi.info/wiki/algo/basic/computational-complexity.md).

### Định lý thợ (Master's theorem)

Độ phức tạp của các thuật toán chia để trị cài đặt dưới dạng đệ quy được xác định bằng **định lý thợ**.

Giả sử có một bài toán $P$ với dữ liệu đầu vào có độ lớn là $n$ (mảng có độ dài $n$ chẳng hạn) được cài đặt bằng đệ quy, tại mỗi vòng đệ quy bài toán được chia thành $a$ bài toán con như sau:

```
void P(<input kích thước n>)
{
    if (n < k)  //k là một hằng số
    {
        <giải bài toán trực tiếp (base case)>
    }
    else
    {
        for (int i = 1; i <= a; i ++)
        {
            P(<input kích thước n/b>);
        }
        <kết hợp kết quả các bước trên>
    }
}
```

Nếu ta coi mỗi bài toán con là một nút trên một cây và mỗi lần gọi đệ quy từ một bài toán con ta nối thêm một nút con vào nút biểu diễn bài toán nói trên thì bài toán lớn của chúng ta sẽ có dạng như sau:

![problem tree]()

Tại mỗi nút của cây trên, nếu việc kết hợp kết quả các bài toán con mất $f(n)$ thời gian, thì thời gian chạy tại một nút với kích thước dữ liệu là $n$ có thể được tính theo công thức truy hồi:

$$
T(n) = \begin{cases}
    a \times T(\frac{n}{b}) + f(n) &\text{khi } n >= k\\
    O(1) &\text{khi } n < k
    \end{cases}
$$

Ví dụ, với thuật toán MergeSort, tại mỗi bước ta chia một đoạn có độ dài $n$ thành hai đoạn con có độ dài $n/2$ hoặc xấp xỉ số đó. Thuật toán sẽ có thời gian chạy là $T(n) = 2T(\frac{n}{2}) + O(n)$ khi $n > 1$ và $O(1)$ khi $n = 1$.

Bây giờ, ta lại xét giá trị $f(n)$. Giả sử $f(n)$ viết được dưới dạng $\Theta(n^p\text{log}^qn)$ (Định lý thợ chỉ xét $f(n)$ là độ phức tạp đa thức). Chúng ta có thể tiếp tục thu gọn biểu thức như sau:

-   Nếu $a > b^p$, tức là việc kết hợp kết quả có độ phức tạp không đáng kể so với giải các bài toán con, ta có:
    $$T(n) = \Theta(n^{\text{log}_b a})$$
-   Nếu $a = b^p$, tức là việc kết hợp kết quả có độ phức tạp tương đương giải các bài toán con:
    -   Nếu $q > -1$, ta có:
        $$T(n) = \Theta(n^{\text{log}_b a}\text{log}^{q + 1}n)$$
    -   Nếu $q = -1$, ta có:
        $$T(n) = \Theta(n^{\text{log}_b a}\text{log}\text{log}n)$$
    -   Nếu $q < -1$, ta có:
        $$T(n) = \Theta(n^{\text{log}_b a})$$
-   Nếu $a < b^p$, tức là tức là việc kết hợp kết quả có độ phức tạp rất lớn so với giải các bài toán con:
    -   Nếu $q >= 0$, ta có:
        $$T(n) = \Theta(n^p\text{log}_q n)$$
    -   Nếu $q < 0$, ta có:
        $$T(n) = \Theta(n^k)$$

Một số ví dụ:

-   Thuật toán Tìm kiếm nhị phân (Binary search) mỗi lần chia bài toán thành 2 phần bằng nhau nhưng chỉ xét 1, không cần kết hợp kết quả sẽ có $T(n) = T(n / 2) + O(1)$. Thời gian chạy trung bình của thuật toán là $T(n) = \Theta(\text{log}n)$, ứng với $a = 1, b = 2, p = 0, q = 0$.
-   Thuật toán MergeSort chia đôi dãy hiện tại thành 2 phần bằng nhau, lấy cả 2 và phải xét lại cả 2 phần để lấy kết quả sẽ có độ phức tạp là $T(n) = 2T(n / 2) + O(n)$. Thời gian chạy trung bình của thuật toán là $T(n) = \Theta(n\text{log}n)$, ứng với $a = 2, b = 2, p = 1, q = 0$.
-   Một thuật toán chia để trị có $T(n) = 3T(n / 2) + \text{log}^2n$. Thời gian chạy trung bình của thuật toán là $T(n) = \Theta(n^{\text{log}_2 3})$, tương ứng với $a = 3, b = 2, p = 0, q = 2$.

Định lý thợ là một công cụ hữu hiệu để xác định độ phức tạp của một thuật toán chia để trị. Chỉ cần xác định được số bài toán con, kích thước dữ liệu các bài toán con và độ phức tạp của việc kết hợp dữ liệu, ta dễ dàng tìm ra độ phức tạp chung của bài toán.

## Một số ví dụ áp dụng Chia để trị

Các bài toán áp dụng Chia để trị chỉ có chung một phương pháp như đã nói ở trên. Khi sử dụng, thứ mà ta quan tâm chủ yếu là độ phức tạp; thông thường, chia để trị là công cụ tối ưu độ phức tạp khá hiệu quả.
