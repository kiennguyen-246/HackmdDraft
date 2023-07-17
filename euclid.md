# Thuật toán Euclid

## Mở đầu về Ước chung lớn nhất
Đây là khái niệm tương đối quen thuộc với chúng ta.

Cho hai số nguyên không âm $a$ và $b$. Số tự nhiên $d$ lớn nhất thoả mãn $a \vdots d$ và $b \vdots d$ gọi là **ước chung lớn nhất** (GCD/ƯCLN) của $a$ và $b$. Kí hiệu là $gcd(a, b)$ ($ƯCLN(a, b)$ trong tiếng Việt) hoặc đơn giản hơn $(a, b)$.

$gcd(a, b) = max\{d > 0 : (d | a) \text{ and } (d | b)\}$

Về mặt toán học, với $k \neq 0$ thì $gcd(0, k) = k$, và $gcd(0, 0)$ không xác định. Tuy nhiên, để lập trình tiện lợi ta vẫn quy ước $gcd(0, 0) = 0$.

Có một vài cách để tìm ƯCLN của hai số $a$ và $b$. Cách đơn giản nhất là ... duyệt từng số tự nhiên $d$ một đến $min\{a, b\}$ để kiểm tra điều kiện $a \vdots d$ và $b \vdots d$. Ngoài ra, trong toán học, ta cũng sử dụng phương pháp phân tích thành thừa số nguyên tố để tìm ƯCLN. Phương pháp này không hiệu quả lắm khi lập trình. Thay vào đó, chúng ta sẽ sử dụng thuật toán Euclid.

## Thuật toán Euclid
Thuật toán này được trình bày trong sách "Cơ bản" (Elements) của ông vào khoảng năm 300 TCN, nhưng cũng có thể đã từng xuất hiện trước đó.

Thuật toán này được trình bày đơn giản như sau: 
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

Thư viện `algorithm` của C++ có hỗ trợ hàm `__gcd(a, b)` để tìm ước chung lớn nhất của hai số $a$ và $b$, cũng sử dụng thuật Euclid. Kể từ phiên bản `C++17`, thư viện `numeric` hỗ trợ thêm hàm $gcd(a, b)$ với mục đích tương tự. Các hàm có sẵn này có thể được sử dụng để code ngắn gọn.