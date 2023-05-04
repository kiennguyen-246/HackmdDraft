# Đệ quy và Thuật toán quay lui

## Mở đầu
Trong cuộc sống, chúng ta đôi khi bắt gặp những hình ảnh về một vật mà chứa bên trong nó là một vật khác giống hệt nó, như búp bê Matryoska, cửa sổ OBS khi bạn cố dùng nó để quay màn hình của chính nó, sách giáo khoa Toán lớp 3 cũ, [cái này](https://hackmd.io/@kiennguyen246/Sy8rkeYQn), ... Tương tự như vậy, trong khoa học máy tính và lập trình, chúng ta xây dựng khái niệm về đệ quy.

## Đệ quy và giải thuật đệ quy
### Khái niệm
>Ta gọi một đối tượng là **đệ quy** (recursion) nếu nó được định nghĩa qua chính nó hoặc một đối tượng cùng dạng với chính nó bằng quy nạp.

*Ví dụ*:
- Với $n!$ thì ta có $n! = (n - 1)! \times n$
- Gọi $gcd(a, b)$ là ước chung lớn nhất của $a$ và $b$ ($a \geq b$) thì ta có $gcd(a, b) = gcd(b, a \bmod b)$ với $\bmod$ là phép lấy phần dư

Về cơ bản thì ta có thể gọi một hàm là đệ quy nếu hàm đó tự gọi chính nó, với các biến đầu vào có thể khác.

Nếu một bài toán $P$ có lời giải được thực hiện bằng một bài toán con $P'$ có dạng giống $P$ thì đó là một giải thuật đệ quy. Ở đây, $P'$ cần là một bài toán đơn giản hơn $P$, và đương nhiên không cần đến $P$ để giải nó. 

Một bài toán đệ quy có lời giải gồm 2 phần:
- **Phần neo/trường hợp cơ sở (anchor/base case)**: Đây là phần có thể giải trực tiếp mà không cần phải dựa vào một bài toán con nào. Và cũng chính là điểm dừng của lời giải đệ quy.
- **Phần đệ quy**: Nếu phần này không thể giải được bằng neo, ta phải xác định những bài toán con gọi đệ quy và giải chúng. Sau khi giải các bài toán con, ta sử dụng các kết quả để giải bài toán lớn.

Chúng ta hãy cùng xem một số ví dụ

### Tính giai thừa
**Bài toán:** Cho số tự nhiên $n$ ($n \leq 15$). Tính $n!$

**Phân tích:**
Ở phần trên, chúng ta đã tìm được công thức đệ quy của bài toán này: $n! = (n - 1)! \times n$. Khi lập trình đệ quy, cơ bản thuật toán sẽ hoạt động như sau với $n = 2$: để tính $2!$, chúng ta phải tính nó qua $1!$; để tính $1!$ chúng ta phải tính $0!$. Lúc này, ta có hai lựa chọn:
- Tiếp tục với $0! = (-1)! \times 0$
- Thay $0! = 1$ vào và tính tiếp

Lựa chọn thứ nhất có vẻ không khả thi, phần vì $(-1)!$ không xác định, mặt khác nếu thay tiếp thì chúng ta cũng chẳng biết dừng ở đâu cả. Với $0! = 1$, ta có thể thay vào để tính $1!$ rồi. Có $1!$ ta lại thay nó vào tiếp để tính $2!$, và chúng ta có thứ chúng ta cần.

![Mô hình tính giai thừa đệ quy](https://i.imgur.com/OMVG6I2.png)

Phân tích thì dài dòng vậy thôi, còn cài đặt thì rất đơn giản:
``` cpp=
void factorial(int n)
{
    if (n == 0) return 1;    //phần neo
    return factorial(n - 1) * n;    //phần đệ quy
}
```

### Tính số Fibonacci
Dãy Fibonacci là dãy số được định nghĩa theo công thức truy hồi sau:
$$
f_n =
\begin{cases}
0 & \text{với } n = 0\\
1 & \text{với } n = 1\\
f_{n - 2} + f_{n - 1} & \text{với } n > 1
\end{cases}
$$
Văn vẻ hơn thì trong dãy này, mỗi số hạng bằng tổng của hai số hạng liền trước nó. Các giá trị $f_0$, $f_1$ có thể khác một chút tuỳ tài liệu.

**Bài toán:** Tìm số Fibonacci thứ $n$ ($n \leq 20$).

Dựa vào công thức truy hồi đã cho và lập luận kiểu "để tính $f$ này thì ta cần có $f$ kia" như trên, chúng ta có thể cài đặt như sau:

``` cpp=
void fibo(int n)
{
    if (n == 0) return 0;    //phần neo
    if (n == 1) return 1;    //phần neo
    return fibo(n - 2) + fibo(n - 1);    //phần đệ quy
}
```

Cần chú ý rằng ở chương trình này cần có tới 2 trường hợp ở phần neo, vì đó cũng là hai trường hợp không thể áp dụng công thức truy hồi.

**Mở rộng:** Hãy thử sử dụng phương pháp này để cài đặt một chương trình đệ quy tính ƯCLN dựa vào công thức ở ví dụ phía trên.

Đương nhiên, không phải bài toán đệ quy nào cũng để chúng ta nhìn thấy một công thức đệ quy đơn giản như vậy. Thậm chí, đôi khi chúng ta còn chẳng có một công thức cụ thể, mà chỉ đơn thuần là công việc được thực hiện sau đó có điểm tương đồng với phần trước thôi. Lúc này, ta cần giải đáp những câu hỏi:
- Bài toán có thể được giải qua những bài toán con nào tương tự không? Nếu được, đó là gì?
- Tới trạng thái nào, chúng ta sẽ dừng lại? 

## Thuật toán quay lui
### Khái niệm
>Thuật toán **quay lui** (backtracking) dùng để giải bài toán liệt kê các cấu hình. Mỗi cấu hình được xây dựng bằng cách xây dựng từng phần tử, mỗi phần tử được chọn bằng cách thử tất cả các khả năng.

Tóm gọn lại, chúng ta đang đi duyệt qua tất cả các tập hợp (hay dãy, ...), mà mỗi phần tử được xét tất cả các trường hợp có thể của nó. Còn nói trên phương diện quy nạp, nếu cần dựng các tập $\{x_1, x_2, ..., x_n\}$, ta xét mọi giá trị của $x_1$, rồi sau đó duyệt tiếp các tập $\{x_2, x_3, ..., x_n\}$, ..., cho đến khi nào tất cả các giá trị đều đã xác định.

``` cpp=
void backtrack(int pos)
{
    if (<pos là vị trí cuối cùng>)
    {
        <output/lưu lại tập hợp đã dựng nếu thoả mãn>
        return;
    }
    for (<tất cả giá trị i có thể ở vị trí pos>)
    {
        <thêm trạng thái mới vào tập đang xét>
        backtrack(pos + 1);
        <xoá bỏ trạng thái vừa tạo ra khỏi tập>
    }
}
```

Việc tạo trạng thái mới, thêm nó vào tập đang xét rồi cuối cùng xoá bỏ nó ra khỏi tập giải thích cho tên gọi "quay lui" của thuật toán. Đó là việc khôi phục lại trạng thái cũ của tập hợp sau khi kết thúc bước đệ quy.

Đoạn code ở đoạn if vị trí cuối cùng ở trên có thể coi là phần neo của thuật đệ quy.

Bạn đọc có thể thử vẽ sơ đồ tính toán giống như bài giai thừa ở trên và quen sát xem các hàm đệ quy được gọi ra theo thứ tự như thế nào.

### Sinh các dãy nhị phân
**Bài toán:** Liệt kê tất cả các dãy nhị phân độ dài $n$, là dãy có tất cả $n$ ký tự và gồm toàn các ký tự $0$ và $1$.
Ví dụ, với $n = 3$ ta có các dãy $000, 001, 010, 011, 100, 101, 110, 111$.

**Phân tích:**
Với cách xây dựng quay lui như trên, thuật toán sẽ hoạt động kiểu như thế này:

![](https://i.imgur.com/QqypRGl.png)

Tại hàm `gen(1)`, ta xét từng giá trị của ký tự hiện tại, sau đó gọi `gen(2)` với từng ký tự đó. Tương tự như vậy, ta gọi `gen(3)` từ các ký tự ở `gen(2)` và rồi `gen(4)`. Tới `gen(4)`, ta đã duyệt hết các vị trí và không thể thử thêm nữa, nên có thể in ra xâu.

``` cpp=
int n;
string curString;

void genString(int pos)
{
    if (pos > n)
    {
        cout << curString << "\n";
        return;
    }
    for (char i = '0'; i <= '1'; i ++)
    {
        curString.push_back(i);    //thêm ký tự mới vào xâu
        genString(pos + 1);
        curString.pop_back();      //bỏ ký tự này đi
    }
}

int main()
{
    cin >> n;
    curString = "";
    genString(1);

    return 0;
}
```
Chú ý rằng, cách sinh này cũng chưa phải là tốt nhất nếu xét về độ dài của code. Sử dụng các phép toán trên bit của C++ sẽ giúp liệt kê tất cả các dãy trên với một đoạn code đơn giản hơn nhiều mà thời gian chạy vẫn không chậm hơn (tất nhiên là không cần sử dụng đệ quy).

### Sinh tổ hợp (tập hợp con)
**Bài toán:** Cho tập $S = \{1, 2, 3, ..., n\}$. In ra tất cả các tập con có chính xác $k$ phần tử của $S$. Hai tập con là hoán vị của nhau chỉ tính là một.

**Phân tích:**
Có một số ý tưởng cho bài này, như biểu diễn tập hợp bằng một dãy nhị phân rồi tìm các dãy có đúng $k$ phần tử, hay lần lượt xây dựng các số trong dãy sao cho số sau lớn hơn số trước đến khi đủ $k$. Sau đây mình xin phép trình bày giải thuật cho cách thứ hai.

Để tránh trùng lặp, ta luôn luôn dựng các tập con $P$ là các dãy thoả mãn $P_i > P_{i - 1}$. Giả sử ta đã xây dựng được dãy đến vị trí thứ $i$, và $P_i$ là giá trị cuối cùng được thêm vào. Tại vị trí thứ $i+1$, do có $P_{i + 1} > P_i$, nên ta chỉ thử các số từ $P_{i + 1}$ đến $n$.

Phần đệ quy sẽ kết thúc khi tập con đã có đủ $k$ phần tử.

Sử dụng ý tưởng trên ta cài đặt như sau:

``` cpp=
int n, k;
vector <int> curSubset;

void printSubset()
{
    for (int i : curSubset) cout << i << " ";
    cout << "\n";
}

void genSubset(int pos)
{
    int lastNum = (curSubset.empty() ? 0 : curSubset.back());  //số cuối cùng được chọn
    for (int i = lastNum + 1; i <= n; i ++)
    {
        curSubset.push_back(i);
        if (curSubset.size() == k) printSubset();
        else genSubset(pos + 1);
        curSubset.pop_back();
    }
}

int main()
{
    cin >> n >> k;
    curSubset.clear();
    genSubset(1);

    return 0;
}
```

**Mở rộng:** Nếu bỏ đi điều kiện "Hai tập con là hoán vị của nhau chỉ tính là một." thì chúng ta sẽ làm như thế nào? (gợi ý: lúc này thay vì có mọi số lớn hơn chặt số liền trước, ta chỉ cần các số trong tập hợp khác nhau là đủ)

### Bài toán phân tích số
**Bài toán:** Ở một quốc gia có $n$ loại tiền gồm các mệnh giá $a_1, a_2, ..., a_n$ ($n \leq 10$). Có những cách nào để lấy các tờ tiền sao cho tổng mệnh giá của chúng là $S$? Biết rằng mỗi mệnh giá tiền có thể được lấy nhiều lần và hai cách lấy là hoán vị của nhau chỉ tính là một.
Ví dụ: với 3 loại tiền mệnh giá $10, 20, 50$, có $10$ cách lấy tiền để có tổng là $100$, bao gồm $10$ tờ $10$, hoặc $2$ tờ $50$, hoặc $3$ tờ $10$, $1$ tờ $20$ và $1$ tờ $50$, ...

Một cách rất tự nhiên, chúng ta sẽ tiếp tục làm tương như bài trước: lưu các tờ tiền đã có vào một mảng, sau đó lấy tiền sao cho tờ sau có mệnh giá không nhỏ hơn tờ trước. Hàm đệ quy như thế sẽ có dạng `genMoneySet(int pos)`.

Vậy thì khi nào chúng ta dừng lại? Đó là khi tổng số tiền chúng ta đã lấy được đạt mức yêu cầu, hoặc lớn hơn. Khi đó, kết quả hợp lệ sẽ là trường hợp số tiền đạt mức yêu cầu.

Trong quá trình lập trình, song song với việc duy trì một tập hợp tiền đang xây dựng `curMoneySet`, chúng ta sẽ cần thêm một giá trị tổng `curMoneySum` để đơn giản tính toán.

``` cpp=
int n, a[15];
long long S, curMoneySum;
vector <int> curMoneySet;

void printMoneySet()
{
    for (auto i : curMoneySet) cout << a[i] << " ";
    cout << "\n";
}

void genMoneySet(int pos)
{
    int lastIndex = (curMoneySet.empty() ? 1 : curMoneySet.back());
    for (int i = lastIndex; i <= n; i ++)
    {
        //Lấy thêm 1 tờ tiền mới
        curMoneySet.push_back(i);
        curMoneySum += a[i];
        
        //Gọi đệ quy
        if (curMoneySum >= S)
        {
            if (curMoneySum == S) printMoneySet();
        }
        else genMoneySet(pos + 1);
        
        //Bỏ tờ tiền này ra
        curMoneySet.pop_back();
        curMoneySum -= a[i];
    }
}

int main()
{
    cin >> n >> S;
    for (int i = 1; i <= n; i ++) cin >> a[i];
    curMoneySet.clear();
    curMoneySum = 0;
    genMoneySet(1);

    return 0;
}
```

Nếu bạn đọc để ý kỹ thì chúng ta không sử dụng tham số `pos` trong hàm `genMoneySet` vào mục đích gì cả. Có thể bỏ tham số này đi, và chúng ta có một hàm đệ quy không tham số.

### Bài toán xếp hậu
[Xếp hậu](https://en.wikipedia.org/wiki/Eight_queens_puzzle) là bài toán rất kinh điển, có lẽ nếu bạn học đệ quy quay lui ở đâu thì cũng sẽ gặp.

**Bài toán:** Tìm tất cả các cách xếp $n$ ($n \leq 12$) quân Hậu lên một bàn cờ $n \times n$ sao cho không có hai quân Hậu nào có thể ăn được nhau. Nếu có hai cách là hoán vị của nhau thì chỉ tính là một. Hai quân Hậu được gọi là có thể ăn được nhau nếu chúng nằm cùng hàng, cột hoặc đường chéo của bàn cờ.

![](https://i.imgur.com/FQTQrW0.png)
(Hình ảnh tìm trên Google Images)

**Phân tích:**
Giả sử quân Hậu thứ $i$ nằm ở hàng $x_i$ và cột $y_i$ (nhìn hơi ngược một chút nhưng nó sẽ rất hợp cho những bài toán liên quan đến bảng).

Khi nào thì hai quân Hậu $A$ và $B$ ăn nhau?
- Khi $A$ và $B$ nằm cùng hàng, tức là $x_A = x_B$.
- Khi $A$ và $B$ nằm cùng cột, tức là $y_A = y_B$.
- Khi $A$ và $B$ nằm trên cùng đường chéo. Lúc này có hai trường hợp: 
    - $x_A + y_A = x_B + y_B$
    - $x_A - y_A = x_B - y_B$

Đối với đường chéo, bạn đọc có thể tự kiểm chứng thấy rằng, hiệu hoặc tổng của chỉ số hàng và chỉ số cột luôn luôn là một số không đổi đối với hai phần tử cùng đường chéo, lần lượt ứng với đường chéo chính (hướng tây bắc - đông nam) và đường chéo phụ (hướng đông bắc - tây nam). 

Vậy thì, việc của chúng ta bây giờ chỉ là sinh ra những bộ toạ độ đôi một thoả mãn các điều kiện trên thôi. Chúng ta sẽ sinh các bộ toạ độ này lần lượt theo từng hàng, và đảm bảo rằng quân Hậu sau sẽ không cùng cột và cùng đường chéo với quân Hậu trước. Để khỏi phải `for` lại từ đầu tập đã sinh để kiểm tra trùng lặp, chúng ta sẽ duy trì một số mảng đánh dấu: `isInCol[]`, `isInDiag1[]`, `isInDiag2[]`. Giá trị của một phần tử trong các mảng trên sẽ là `true` nếu đã có quân Hậu nằm ở vị trí mà nó đảm nhận việc đánh dấu.

Việc đệ quy sẽ kết thúc nếu ta sinh thành công $n$ quân Hậu. Lúc này, ta chỉ việc in kết quả.

``` cpp=
int n;
bool isInCol[13], isInDiag1[26], isInDiag2[26];
//gọi 2 tập riêng chi hàng và cột
//có thể dùng pair để tóm gọn vào làm 1
vector <int> curQueensSetX, curQueensSetY;

void printQueensSet()
{
    for (int i = 0; i < n; i ++)
    {
        cout << "(" << curQueensSetX[i] << ", " << curQueensSetY[i] << ")";
        if (i < n - 1) cout << ", ";
    }
    cout << "\n";
}

void genQueensSet(int curRow)
{
    for (int curCol = 1; curCol <= n; curCol ++)
    {
        //Xác định đường chéo phụ và chính hiện tại
        int curDiag1 = curRow + curCol;
        int curDiag2 = curRow - curCol + 13;    //+13 để tránh số âm
        
        //Kiểm tra toạ độ mới xem có thoả mãn không
        if (isInCol[curCol] == true) continue;
        if (isInDiag1[curDiag1] == true) continue;
        if (isInDiag2[curDiag2] == true) continue;
        
        //Thêm nó vào tập hợp hiện tại nếu thoả mãn
        curQueensSetX.push_back(curRow);
        curQueensSetY.push_back(curCol);
        isInCol[curCol] = true;
        isInDiag1[curDiag1] = true;
        isInDiag2[curDiag2] = true;
        
        //Thêm tiếp quân tiếp theo hoặc in kết quả
        if (curQueensSetX.size() == n) printQueensSet();
        else genQueensSet(curRow + 1);

        //Xoá quân vừa thêm vào
        curQueensSetX.pop_back();
        curQueensSetY.pop_back();
        isInCol[curCol] = false;
        isInDiag1[curDiag1] = false;
        isInDiag2[curDiag2] = false;
    }
}

int main()
{
    cin >> n;

    memset(isInCol, 0, sizeof(isInCol));
    memset(isInDiag1, 0, sizeof(isInDiag1));
    memset(isInDiag2, 0, sizeof(isInDiag2));

    genQueensSet(1);

    return 0;
}
```

Nếu bạn thực hiện thuật toán một cách chính xác, với $n = 8$ bạn sẽ thu được $92$ cách xếp thoả mãn. Nhìn nhiều vậy thôi, hãy thử tự xếp xem mất bao lâu mới ra :)).

## Kỹ thuật Nhánh cận
Trong một số trường hợp, thay vì yêu cầu liệt kê tất cả các cách chọn thoả mãn, ta sẽ phải tìm xem cách nào là **cách chọn tốt nhất**. Khi đó, việc dùng phương pháp **nhánh cận** (branch and bound) sẽ giúp chúng ta giải những bài toán dạng như vậy.

Thực chất, đây vẫn là thuật toán quay lui, nhưng thay vì in ra hoặc lưu lại tất cả kết quả trong mỗi lần tính toán, ta cập nhật lại trạng thái tốt nhất. Để thuật toán tối ưu hơn, nếu tại một bước, bất kỳ bước nào tiếp theo cũng không thể làm cho kết quả tốt hơn kết quả hiện có, ta có thể bỏ qua nó luôn.

Quay trở lại bài toán phân tích số ở trên. Lần này, ta sẽ thêm vào một điều kiện:
"Số tờ tiền được xếp ra là nhỏ nhất. Nếu có nhiều cách xếp thoả mãn, chọn cách bất kỳ."

Vẫn với tư tưởng đệ quy như trên, chúng ta hoàn toàn có thể liệt kê tất cả cách xếp rồi lấy cách tốt nhất. Tuy nhiên, rõ ràng tại một số cách, số tiền còn lại khi duyệt tới những tờ giữa đã hơi "cấn" rồi. Ví dụ đã có một cách xếp $2 \times 20 + 1 \times 10 + 1 \times 50 = 100$ rồi, việc xét tới $5 \times 10$ thôi đã chẳng còn ý nghĩa gì.  Những cách đó có thể bỏ đi để thuật toán chạy nhanh hơn.

``` cpp=
int n, a[15];
long long S, curMoneySum;
vector <int> curMoneySet, bestSet;

void genMoneySet(int pos)
{
    int lastIndex = (curMoneySet.empty() ? 1 : curMoneySet.back());
    for (int i = lastIndex; i <= n; i ++)
    {
        curMoneySet.push_back(i);
        curMoneySum += a[i];

        if (curMoneySum >= S)
        {
            if (curMoneySum == S)
            {
                bestSet.clear();
                for (int i : curMoneySet) bestSet.push_back(i);
            }
        }
        else if (bestSet.empty() || curMoneySet.size() < bestSet.size()) 
            genMoneySet(pos + 1);

        curMoneySet.pop_back();
        curMoneySum -= a[i];
    }
}

int main()
{
    cin >> n >> S;
    for (int i = 1; i <= n; i ++) cin >> a[i];
    curMoneySet.clear();
    curMoneySum = 0;
    bestSet.clear();
    genMoneySet(1);

    for (int i : bestSet) cout << a[i] << " ";
    cout << "\n";

    return 0;
}
```

## Chú ý thêm
### Vì sao lại dùng đệ quy?
Ưu điểm mà chúng ta thấy ngay được của việc sử dụng đệ quy là viết code ngắn gọn hơn. Lấy ví dụ, khi tính số Fibonacci mà không sử dụng đệ quy, ta sẽ phải tạo hai biến nhớ cho số gần thứ nhì và gần nhất, cộng chúng lại, lưu vào biến mới rồi cập nhật hai biết nhớ; hoặc có thể sử dụng mảng rồi cập nhật lại sao mỗi lần tính. Chúng đều làm cho đoạn code trở nên dài hơn một chút so với việc dùng đệ quy ở trên. Nhưng ở những bài toán lớn hơn, ví dụ như những bài toán sinh dãy ở trên, việc không sử dụng đệ quy sẽ làm bài lời giải của chúng ta cồng kềnh hơn rất nhiều.
Một ưu điểm khác của đệ quy giúp giải dễ dàng các bài toán có dạng một phần nhỏ hơn của công việc cộng thêm một vài lệnh khác, ví dụ như các bài toán duyệt cây và đồ thị.
Tất nhiên, đệ quy không phải công cụ toàn năng. Đệ quy làm cho thuật toán trở nên khó hiểu hơn khi đọc trực tiếp, đặc biệt là với những thuật toán dài. Đệ quy cũng  sử dụng thời gian và bộ nhớ hơn so với phương pháp duyệt trực tiếp, do bộ nhớ cần phải lưu trữ lại stack các hàm đệ quy.

### Một số ứng dụng của đệ quy
Ngoài các bài toán sinh hoặc duyệt vét cạn, đệ quy còn được sử dụng phổ biến trong các bài toán duyệt cây, duyệt đồ thị và quy hoạch động. Rất nhiều bài toán "chia để trị" khác cũng sử dụng đệ quy, điển hình là thuật toán QuickSort.

### Độ phức tạp của đệ quy
Một hàm đệ quy có dạng như sau:
``` cpp=
void recursive(int x)
{
    if (x > n) return;
    for (int i = 1; i <= m; i ++) 
        recursive(x + 1);
}
```
Hàm trên được gọi đệ quy $n$ lần, mỗi lần phải thực hiện $m$ lần vòng lặp nên độ phức tạp sẽ là $O(m^n)$.

Có thể thấy, các thuật toán đệ quy thường có độ phức tạp rất lớn, nhiều khi lên tới hàm mũ, tuy vậy lại có lúc rất nhỏ như hàm tính ƯCLN. Do vậy, việc xác định số lần chạy của hàm đệ quy rất quan trọng. Những bài toán yêu cầu duyệt vét cạn như ở trên thường đòi hỏi phải duyệt trên mọi trạng thái chưa biết, và vì thế dữ liệu đầu vào rất nhỏ.

Ngoài ra, trong một số bài toán yêu cầu tính toán ta cũng có thể lưu lại kết quả của một vài vòng đệ quy để không phải duyệt lại những vòng đã duyệt rồi. Phương pháp này gọi là **đệ quy có nhớ**.

## Luyện tập
- [Một số bài tập trên VNOI](https://oj.vnoi.info/contest/backtrack)
- [MNS](https://community.topcoder.com/stat?c=problem_statement&pm=1744&rd=4545)
- [Bridge Crossing](https://community.topcoder.com/stat?c=problem_statement&pm=1599&rd=4535)
- [Weird Rooks](https://community.topcoder.com/stat?c=problem_statement&pm=3998&rd=6533)
- Giải Sudoku: Hãy thử viết một chương trình sử dụng đệ quy quay lui để giải một bảng Sudoku cho trước [ở đây](https://sudoku.com)

## Tài liệu tham khảo
- Lê Minh Hoàng (2003), *Giải thuật và lập trình*