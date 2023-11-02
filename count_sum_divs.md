# Số các ước và Tổng các ước

[TOC]

## Bài toán đếm số ước

### Bài toán 1

**Đề bài**: Cho một số nguyên dương $N$. Đếm số ước của $N$.

Với nhiều coder, đây là một trong những bài toán beginner không thể không làm qua. Đây là bài toán thường được sử dụng như một ví dụ về việc phải tìm cách để tối ưu số lần lặp.

#### Cách 1: Duyệt để tìm ước

Giải thuật ngây thơ nhất rất đơn giản: từ nhận xét mọi ước của $N$ đều không vượt quá $N$, ta duyệt mọi số nguyên dương trong đoạn $[1, N]$ và đếm số lượng ước của $N$ trong đó:

```cpp=
int divCount(int n) {
    int ret = 0;
    for (int i = 1; i <= n; i ++)
        ret += (n % i == 0);
    return ret;
}
```

Cải tiến đầu tiên cho bài toán này mà ta có thể sử dụng là nhận xét rằng nếu $d_0|N$ thì $\frac{N}{d_0}|N$. Do vậy, ứng với mỗi số nguyên $d|N < \sqrt{N}$, tồn tại hai số là $d$ và $\frac{N}{d}$ cũng là ước của $N$.. Trong trường hợp $N$ là số chính phương, ứng với $\sqrt{N}$ chỉ có một ước, do $\sqrt{N} = \frac{N}{\sqrt{N}}$. Ta chỉ cần duyệt các số nguyên dương không vượt quá $\sqrt{N}$ là bao hết mọi ước của $N$.

```cpp=
int divCount(int n) {
    int ret = 0;
    for (int i = 1; i * i <= n; i ++) {
        ret += 2 * (n % i == 0);
        if (i * i == n) ret --;
    }
    return ret;
}
```

Hai giải thuật trên có độ phức tạp thời gian lần lượt là $O(N)$ và $O(\sqrt{N})$ và chạy tốt với $N \leq 10^6$ và $N \leq 10^{12}$. Rất khó để cải thiện độ phức tạp này để phù hợp với $N$ lớn hơn.

#### Cách 2: Sử dụng sàng nguyên tố

Ta biết rằng, nếu một số $N$ được phân tích ra thừa số nguyên tố thành:
$$N = \prod_{i = 1}^{k}{p_i^{m_i}} = p_1^{m_1} \times p_2^{m_2} ... \times p_k^{m_k}$$
với $p_i$ là các số nguyên tố, $m_i$ là các số nguyên dương thì số ước của $N$ là:
$$\delta(N) = \prod_{i = 1}^{k}{(m_i + 1)}$$

:::spoiler **Chứng minh (nhấn để hiện)**
Mỗi ước $d$ của $N$ khi phân tích thành thừa số nguyên tố sẽ có dạng:
$$d = \prod_{i = 1}^{k}{p_i^{\mu_i}}$$
vói $0 \leq \mu_i \leq m_i$.
Mỗi cách chọn số $d$ ứng với một cách chọn bộ số $\mu_i$ nên số cách chọn thoả mãn là $\prod_{i = 1}^{k}{(m_i + 1)}$.
:::

Để giải bài toán này theo cách phân tích ra thừa số nguyên tố, đầu tiên ta sử dụng sàng nguyên tố để lưu lại các số nguyên tố nhỏ hơn $N$. Sau đó, lần lượt chia $N$ cho các số trên rồi thu lại số lần chia ứng với mỗi số; đó chính là số mũ của các thừa số nguyên tố tương ứng. Cuối cùng, áp dụng công thức trên để tính ra kết quả. Cách làm này mất $O(N)$ thời gian và không gian để chuẩn bị sàng, và $O(\pi(N)) \approx O(\frac{N}{\text{ln }N})$ thời gian để phân tích, với $\pi(n)$ là số lượng số nguyên tố nhỏ hơn $N$. Tổng độ phức tạp là $O(N)$.

Sử dụng nhận xét sau, ta giảm được độ phức tạp xuống $O(\sqrt{N} )$. Ta viết $N = X \times Y$. Trong đó, số $X$ là tích của tất cả các thừa số nguyên tố của $N$ có cơ số nhỏ hơn $\sqrt{N}$. Khi đó, $Y$ chắc chắn là một số nguyên tố, do $Y$ không có ước nào $\sqrt{N}$ và $\sqrt{Y} < \sqrt{N}$. Khi cài đặt, sau khi $N$ cho toàn bộ các thừa số nguyên tố đã chuẩn bị sẵn, giá trị $N$ còn lại chính là $Y$.

```cpp=
const int MAXN = 1e6;

vector<int> primes;
vector<bool> isPrime;

void sieve() {
    isPrime.assign(MAXN + 1, 1);
    isPrime[0] = isPrime[1] = 0;
    for (int i = 2; i <= MAXN; i++) {
        if (!isPrime[i]) continue;
        primes.push_back(i);
        for (int j = i + i; j <= MAXN; j += i) {
            isPrime[j] = 0;
        }
    }
}

int countDiv(long long n) {
    vector<int> powV;
    for (auto p : primes) {
        int cnt = 0;
        while (n % p == 0) {
            n /= p;
            ++cnt;
        }
        if (cnt) powV.push_back(cnt);
    }
    if (n != 1) powV.push_back(1);
    int ret = 1;
    for (auto i : powV) ret *= (i + 1);
    return ret;
}

void solution() {
    sieve();
    long long n;
    cin >> n;
    cout << countDiv(n);
}
```

#### Cải tiến

Cho tới bước này, ta có thể giải được bài toán với $N \leq 10^{12}$. Ta sẽ cải tiến để bài toán giải được với $N \leq 10^{18}$, với độ phức tạp là khoảng $O(\sqrt[3]{N})$.

Vẫn viết $N$ dưới dạng $N = X \times Y$ như trên và cũng với $X$ được định nghĩa tương tự như trên, lúc này ta không thể chắc chắn $Y$ là số nguyên tố nữa. Tuy nhiên, lúc này $Y$ lại chỉ có thể thuộc vào một trong ba trường hợp:

-   $Y$ là số nguyên tố. Khi đó nó chỉ có $2$ ước là $1$ và $Y$, và khi đó $\delta(N) = \delta(X) \times 2$.
-   $Y$ là bình phương của một số nguyên tố. Khi đó nó có $3$ ước là $1$, $\sqrt{Y}$ và $Y$, và $\delta(N) = \delta(X) \times 3$.
-   $Y$ là tích của hai số nguyên tố. Khi đó nó có $4$ ước và $\delta(N) = \delta(X) \times 4$.

Để kiểm tra trường hợp thứ nhất, ta có thể dùng [thuật toán Rabin-Miller](https://vnoi.info/wiki/algo/algebra/primality_check.md#3-thu%E1%BA%ADt-to%C3%A1n-rabin-miller). Để kiểm tra trường hợp thứ hai, ta kiểm tra xem $Y$ có phải số chính phương không. Các số còn lại sẽ rơi vào trường hợp thứ ba.

```cpp=
const int MAXN = 1e6;

vector<int> primes;
vector<bool> isPrime;

void sieve() {
    isPrime.assign(MAXN + 1, 1);
    isPrime[0] = isPrime[1] = 0;
    for (int i = 2; i <= MAXN; i++) {
        if (!isPrime[i]) continue;
        primes.push_back(i);
        for (int j = i + i; j <= MAXN; j += i) {
            isPrime[j] = 0;
        }
    }
}

long long binaryPower(long long a, long long k, long long n) {
    a = a % n;
    long long res = 1;
    while (k) {
        if (k & 1)
            res = (res * a) % n;
        a = (a * a) % n;
        k /= 2;
    }
    return res;
}

bool test(long long a, long long n, long long k, long long m) {
    long long mod = binaryPower(a, m, n);
    if (mod == 1 || mod == n - 1)
            return 1;
    for (int l = 1; l < k; ++l) {
        mod = (mod * mod) % n;
        if (mod == n - 1)
            return 1;
    }
    return 0;
}

bool isPrimeRabinMiller(long long n) {
    vector<int> checkSet = {2, 3, 5, 7, 
                            11, 13, 17, 19, 
                            23, 29, 31, 37};
    if (n <= 37) {
        for (int i : checkSet) {
            if (i == n) return 1;
        }
        return 0;
    }

    long long k = 0, m = n - 1;
    while (m % 2 == 0) {
        m /= 2;
        k++;
    }

    for (auto a : checkSet)
        if (!test(a, n, k, m))
            return 0;
    return 1;
}

bool isSquare(long long x) {
    int l = 1, r = (int) 1e9 + 1;
    while (r - l > 1) {
        int mid = (l + r) / 2;
        if (1ll * mid * mid <= x) l = mid;
        else r = mid;
    }
    return (1ll * l * l == x);
}

int countDiv(long long n) {
    vector<int> powV;
    for (auto p : primes) {
        int cnt = 0;
        while (n % p == 0) {
            n /= p;
            ++cnt;
        }
        if (cnt) powV.push_back(cnt);
    }
    if (n != 1) {
        if (isPrimeRabinMiller(n)) powV.push_back(1);
        else if (isSquare(n)) powV.push_back(2);
        else {
            powV.push_back(1);
            powV.push_back(1);
        }
    }
    int ret = 1;
    for (auto i : powV) ret *= (i + 1);
    return ret;
}

void solution() {
    sieve();
    long long n;
    cin >> n;
    cout << countDiv(n);
```

Phần Rabin-Miller có độ phức tạp là $O(12\text{log }N)$. Phần kiểm tra số chính phương có độ phức tạp là $O(\text{log }N)$ (Ta không sử dụng hàm `sqrt()` do kiểu `double` không chính xác với số lớn). Tổng hợp lại, độ phức tạp của giải thuật trên là $O(\sqrt[3]{N})$.

Từ nhận xét trên, bạn đọc có thể thấy là ta còn tối ưu được tới $O(\sqrt[4]{N}), O(\sqrt[5]{N}), ...$ Tuy nhiên, càng giảm độ phức tạp ta lại càng phải xét nhiều trường hợp của $Y$, và theo đó khả năng dính bug ngày càng tăng cao. Ngoài ra, $N \leq 10^{18}$ cũng là vừa đủ với kiểu dữ liệu là số.