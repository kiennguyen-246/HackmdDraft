## Mở đầu về Ước chung lớn nhất
Đây là khái niệm tương đối quen thuộc với chúng ta.

Cho hai số tự nhiên $a$ và $b$. Số nguyên dương $d$ lớn nhất thoả mãn $d | a$ và $d | b$ gọi là **ước chung lớn nhất** (GCD/ƯCLN) của $a$ và $b$. Kí hiệu là $gcd(a, b)$ ($ƯCLN(a, b)$ trong tiếng Việt) hoặc đơn giản hơn $(a, b)$.

$gcd(a, b) = max\{d > 0 : (d | a) \text{ and } (d | b)\}$

Về mặt toán học, với $k \neq 0$ thì $gcd(0, k) = k$, và $gcd(0, 0)$ không xác định. Tuy nhiên, để lập trình tiện lợi ta vẫn quy ước $gcd(0, 0) = 0$.

Có một vài cách để tìm ƯCLN của hai số $a$ và $b$. Cách đơn giản nhất là ... duyệt từng số tự nhiên $d$ một đến $min\{a, b\}$ để kiểm tra điều kiện $d | a$ và $d | b$. Ngoài ra, trong toán học, ta cũng sử dụng phương pháp phân tích thành thừa số nguyên tố để tìm ƯCLN. Phương pháp này không hiệu quả lắm khi lập trình. Thay vào đó, chúng ta sẽ sử dụng thuật toán Euclid.

## Thuật toán Euclid
Thuật toán này được trình bày trong tác phẩm "Cơ sở" (Elements) của Euclid vào khoảng năm 300 TCN, nhưng cũng có thể đã từng xuất hiện trước đó.

Thuật toán được thực hiện bằng cách liên tục áp dụng công thức sau cho tới khi ra kết quả:
$gcd(a, b) = \begin{cases}
   a &\text{, if } b = 0\\
   gcd(b, a \text{ mod } b) &\text{, otherwise }
\end{cases}$  

### Chứng minh
Nếu $d$ là ước của $a$ và $b$, hiển nhiên nó cũng là ước của $a - b$.

Nếu $d'$ là ước của $b$ và $b - a$, hiển nhiên nó cũng là ước của $b + (a - b) = a$.

Do vậy với ba số $a$, $b$, $a - b$, nếu $d$ là ước của một trong ba số trên thì sẽ là ước của hai số còn lại, tức là $ƯC(a, b) = ƯC(b, a - b)$. Điều này dẫn đến $gcd(a, b) = gcd(b, a - b)$.

Phép tính $a - b$ sau khi thực hiện $\lfloor \frac{a}{b} \rfloor$ lần thì sẽ thoả mãn $a \leq b$.  Số $a$ sau khi trừ đi $\lfloor \frac{a}{b} \rfloor$ lần $b$ trở thành $a - b\lfloor \frac{a}{b} \rfloor = a \text{ mod } b$. 

Vậy $gcd(a, b) = gcd(b, a \text{ mod } b)$ (đpcm).

### Cài đặt
``` cpp=
int gcd(int a, int b)
{
    if (b == 0) return a;
    return gcd(b, a % b);
}
```

Hoặc ngắn hơn:
``` cpp=
int gcd(int a, int b)
{
    return (b ? gcd(b, a % b) : a);
}
```

### Độ phức tạp
Người ta chứng minh được rằng thuật toán Euclid có độ phức tạp trung bình là $O(log(min\{a, b\}))$. Bạn có thể tham khảo chứng minh chi tiết tại [GeeksforGeeks](https://www.geeksforgeeks.org/time-complexity-of-euclidean-algorithm/).

Thuật toán chạy chậm nhất khi $a = F_n$, $b = F_{n - 1}$, với $F_i$ là số Fibonacci thứ $i$. Khi đó thuật toán cần thực hiện $n - 2$ lần đệ quy.

### Cải tiến
So với các phép toán khác, phép lấy phần dư (%) chậm hơn một chút dù vẫn có độ phức tạp là $O(1)$. Chúng ta có thể xây dựng một cách cài đặt khác không sử dụng phép toán này.

Ta có một số tính chất sau: 
- $gcd(2k, 2h) = 2gcd(k, h)$
- $gcd(2k, 2h + 1) = gcd(k, 2h + 1)$

Kết hợp với $gcd(a, b) = gcd(b, a - b)$ ta cài đặt như sau (Code chép từ CP Algorithms):
```cpp=
int gcd(int a, int b) 
{
    if (!a || !b) return a | b;
    int shift = __builtin_ctz(a | b);
    a >>= __builtin_ctz(a);
    do 
	{
        b >>= __builtin_ctz(b);
        if (a > b)
            swap(a, b);
        b -= a;
    } while (b);
    return a << shift;
}
```

Cải tiến này không làm thay đổi độ phức tạp và cũng không làm cho chương trình chạy nhanh hơn nhiều lắm.

### Vài chú ý
- Thư viện `algorithm` của C++ có hỗ trợ hàm `__gcd(a, b)` để tìm ước chung lớn nhất của hai số $a$ và $b$, cũng sử dụng thuật Euclid. Kể từ phiên bản `C++17`, thư viện `numeric` hỗ trợ thêm hàm `gcd(a, b)` với mục đích tương tự. Các hàm có sẵn này có thể được sử dụng để code ngắn gọn.
- Để tính bội chung nhỏ nhất (BCNN) của hai số, ta dùng công thức: $lcm(a, b) = \frac{a \times b}{gcd(a, b)}$. Kể từ `C++17`, thư viện `numeric` cũng hỗ trợ cả hàm `lcm(a, b)` cho phép tính BCNN của hai số.

## Thuật toán Euclid mở rộng
Với hai số tự nhiên $a$ và $b$, thuật toán này được sử dụng để viết $d = gcd(a, b)$ dưới dạng **tổ hợp tuyến tính**. Nói cách khác, thuật toán này sẽ tìm một bộ giá trị nguyên $(x, y)$ thoả mãn:

$ax + by = d$

Ví dụ: 

$gcd(55, 80) = 5\\
5 = 55 \times 3 + 80 \times (-2)$

Các số $x, y$ thoả mãn đẳng thức trên luôn tồn tại theo bổ đề sau:

**Bổ đề Bézout**: Với hai số nguyên $a$, $b$ có ƯCLN là $d$, tồn tại hai số nguyên $x$ và $y$ thoả mãn $ax + by = d$. Hơn nữa, tất cả các số nguyên $D$ có dạng $D = aX + bY$ đều là bội của $d$. 

:::spoiler **Chứng minh**
Rõ ràng, luôn tồn tại các giá trị $x, y$ để $ax + by > 0$. Gọi $d'$ là số nguyên dương nhỏ nhất thoả mãn $d' = ax' + by'$ (với $x', y'$ là các số nguyên). Ta chứng minh rằng $d'$ là ƯCLN của $a$ và $b$.

Đặt $a = d'q + r &(0 \leq r < d')\\
\Rightarrow r = a - qd'\\
\Rightarrow r = a - q(ax' + by')\\
\Rightarrow r = a(1 - qx') + b(-qy')$

Do đó $r$ cũng có dạng $ax + by$. Tuy nhiên, số dương nhỏ nhất có dạng như vậy là $d'$, thế nhưng $0 \leq r < d'$ nên $r = 0$, đồng nghĩa với $d' | a$.

Chứng minh tương tự ta cũng được $d' | b$. Từ đó suy ra $d$ là ước chung của $a$ và $b$.

Xét $c$ là một ước chung bất kỳ khác $d'$ của $a$ và $b$. Đặt $a = cu, b = cv$ với $u, v$ là số nguyên. Ta có:

$d' = ax' + bt'\\
\Rightarrow d' = cux' + cvy'\\
\Rightarrow d' = c(ux' + vy')$

Suy ra $c | d'$. Vì $d'$ dương và khác $c$ nên $c < d'$. 

Vậy $d'$ là ƯCLN của $a$ và $b$. Bổ đề được chứng minh.
:::

Ứng dụng trực tiếp của thuật toán này là các phương trình Diophantus, sẽ được thảo luận ở phần sau.

### Mô tả thuật toán
Gọi $d$ là ƯCLN của $a$ và $b$.

Khi thực hiện thuật toán Euclid (không mở rộng) để tìm $d$, sau khi biến đổi hoàn tất ta thu được $(a, b) = (d, 0)$. Lúc này ta có $d = d \times 1 + 0 \times 0$.

Từ bộ $(a, b) = (d, 0)$ và $(x, y) = (1, 0)$ , ta truy lại giá trị $(a, b)$ ở bước trước và thay đổi các hệ số $x, y$ để đẳng thức $d = ax + by$ đúng trong bước này.

Giả sử tại một bước ta có $(a, b) = (a_0, b_0)$. Đặt $a_0 = b_0q + r &(q, r \in \N, r < b_0) $. Ta thấy $q = \lfloor \frac{a_0}{b_0} \rfloor$ và $r = a_0 \text{ mod } b_0$. 

Lại giả sử sau khi áp dụng thuật toán Euclid mở rộng, ta được bộ $(a, b) = (b_0, r)$ và hệ số là $(x_1, y_1)$. Ta cần tìm các hệ số $(x_0, y_0)$ để:

$a_0x_0 + b_0y_0 = d$

Có $d = b_0x_1 + ry_1\\
\Rightarrow d = b_0x_1 + (a_0 - b_0q)y_1\\
\Rightarrow d = a_0y_1 + b_0(x_1 - qy_1)\\
\Rightarrow \begin{cases}
	x_0 = y_1  \\
	y_0 = x_1 - qy_1 = x_1 - \lfloor \frac{a_0}{b_0} \rfloor y_1
\end{cases}$

Liên tục cập nhật các hệ số $(x, y)$ theo công thức trên tới khi thu được $(a, b)$ như ban đầu, ta sẽ thu được kết quả.

### Cài đặt
``` cpp=
// Hàm trả về ƯCLN của a và b đồng thời thay đổi giá trị của x, y
int extEuclid(int a, int b, int& x, int& y)
{
    if (b == 0)
    {
        x = 1;
        y = 0;
        return a;
    }
    int q = a / b;
    int r = a - b * q;
    int x1 = 0, y1 = 0;
    int d = extEuclid(b, r, x1, y1);
    x = y1;
    y = x1 - q * y1;
    return d;
}
```

### Độ phức tạp
Thuật toán Euclid mở rộng thực tế chỉ là thêm một vài bước tính toán vào thuật toán Euclid thường nên độ phức tạp vẫn là $O(log(min\{a, b\}))$.

## Phương trình Diophantus tuyến tính hai ẩn
Phương trình Diophantus (Diophantine function) tuyến tính hai ẩn có dạng như sau:

$ax + by = c \space (a, b, c \in \Z)$ 

Phương trình trên có vô số nghiệm $(x, y)$ thực. Tuy nhiên, ta chỉ quan tâm đến các nghiệm nguyên của phương trình.

### Thuật toán tìm nghiệm
Khi $a = b = 0$, phương trình có nghiệm $(x, y) = (k, h) \space(k, h \in \Z)$ nếu $c = 0$ và vô nghiệm nếu $c = 0$

Khi $a \neq 0, b = 0$ phương trình có nghiệm $(x, y) = (\frac{c}{a}, k) \space(k \in \Z)$ nếu $a | c$ và vô nghiệm nếu $a \nmid c$. Tương tự khi $a = 0, b \neq 0$.

Bây giờ ta chỉ xét các trường hợp $a \neq 0, b \neq 0$.

:::spoiler **Tìm nghiệm số học**
Từ $ax + by = c$ ta rút ra:

$ax \equiv c (\text{mod } b)$

Vế trái và modulo của đồng dư thức trên cùng chia hết cho $d = gcd(a, b)$. Do vậy, $d | c$. Nếu điều ngược lại xảy ra, phương trình vô nghiệm.

Chia hai vế và modulo của đồng dư thức cho $d$ được:

$\frac{a}{d} \times x \equiv \frac{c}{d} (\text{mod } \frac{b}{d})$

Vì $(\frac{a}{d}, \frac{b}{d}) = 1$ nên tồn tại nghịch đảo modulo $\frac{b}{d}$ của $\frac{a}{d}$. Nhân hai vế của đồng dư thức với giá trị này được:

$x \equiv \frac{c}{d} \times (\frac{a}{d}) ^{-1} (\text{mod } \frac{b}{d})$ 

Do đó họ các nghiệm của phương trình là:

$\begin{cases}
	x = \frac{b}{d} \times k + \frac{c}{d} \times \alpha &(k \in \Z, \frac{a}{d} \times \alpha \equiv 1 (\text{mod } \frac{b}{d})) \\
	y = \frac{c - ax}{b}
\end{cases}$
:::

Ta đã biết phương trình chỉ có nghiệm nếu $gcd(|a|, |b|) | c$. Nếu điều kiện này không thoả mãn, ta có thể kết luận phương trình vô nghiệm.

Giả sử $a, b$ là các số dương. Đặt $d = gcd(a, b)$. 

Sử dụng thuật toán Euclid mở rộng, ta có:

$ax' + by' = d \space(x', y' \in \Z)$

Nhân hai vế của phương trình với $\frac{c}{d}$ được:

$a(x' \times \frac{c}{d}) + b(y' \times \frac{c}{d}) = c$

Do đó phương trình có nghiệm: 

$\begin{cases}
	x_0 = x' \times \frac{c}{d}  \\
	y_0 = y' \times \frac{c}{d}
\end{cases}$

Trường hợp $a, b$ không dương, ta thay đổi dấu của $x, y$ để thoả mãn đẳng thức.

Thay nghiệm $(x_0, y_0)$ trở lại phương trình, ta được:

$ax_0 + by_0 = c\\
\Rightarrow a(x_0 + k\times\frac{b}{d}) + b(y_0 - k\times\frac{a}{d}) = c \space (k \in \Z)$

Từ đẳng thức này ta kết luận các nghiệm của phương trình có dạng:

$\begin{cases}
	x = x_0 + k \times \frac{b}{d} &(k \in \Z)\\
	y = y_0 - k \times \frac{a}{d}
\end{cases}$

Chốt lại, để tìm nghiệm của một phương trình Diophantus tuyến tính 2 ẩn, ta tìm các hệ số $x', y'$ từ thuật toán Euclid mở rộng, rồi từ các hệ số này áp dụng vào các công thức trên để tính ra kết quả.

### Cài đặt
Đoạn chương trình sau tìm **một** nghiệm nguyên của phương trình $ax + by = c$, với $a, b \neq 0$:

``` cpp=

```
