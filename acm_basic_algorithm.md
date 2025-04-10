# ST 表

```cpp
struct ST {
    vector<array<int, 21>> dp;
    ST(vector<int> & a) {
        int n = a.size() - 1;
        dp.resize(n + 1);

        for(int i = 1; i <= n; i++) {
            dp[i][0] = a[i];
        }
        int p = __lg(n);
        for(int j = 1; j <= p; j++) {
            for(int i = 1; i + (1 << j) - 1 <= n; i++) {
                dp[i][j] = gcd(dp[i][j-1], dp[i+(1<<(j-1))][j-1]);
            }
        }
    }
    int query(int l, int r) {
        int k = __lg(r-l+1);
        return gcd(dp[l][k], dp[r-(1<<k)+1][k]);
    }
};
```



```cpp
struct ST {
    vector<array<int, 21>> dp;
    ST(vector<int> & a) {
        int n = a.size() - 1;
        dp.resize(n + 1);

        for(int i = 1; i <= n; i++) {
            dp[i][0] = a[i];
        }
        int p = __lg(n);
        for(int j = 1; j <= p; j++) {
            for(int i = 1; i + (1 << j) - 1 <= n; i++) {
                dp[i][j] = max(dp[i][j-1], dp[i+(1<<(j-1))][j-1]);
            }
        }
    }
    int query(int l, int r) {
        int k = __lg(r-l+1);
        return max(dp[l][k], dp[r-(1<<k)+1][k]);
    }
};

void enjoyerist() {
    int n, q; cin >> n >> q;
    vector<int> a(n + 1);
    for(int i = 1; i <= n; i++) {
        cin >> a[i];
    }
    ST st(a);
    for(int i = 1; i <= q; i++) {
        int l, r; cin >> l >> r;
        cout << st.query(l, r) << '\n';
    }
}
```

# int128_t

```cpp
#include<bits/stdc++.h>

using namespace std;
using i128 = __int128;

inline void read(i128 &n){
    i128 x = 0,f = 1;
    char ch = getchar();
    while(ch < '0' || ch > '9') { if(ch=='-') f=-1; ch = getchar();}
    while(ch >= '0' && ch <= '9') { x= (x * 10) + (ch ^ 48); ch = getchar();}
    n = x * f;
}

inline void print(i128 n){
    if(n < 0 ){ putchar('-'); n *= -1;}
    if(n > 9) print(n / 10);
    putchar(n % 10 + '0');
}


istream & operator>>(istream & is, i128 & n) {
    string s; is >> s;
    bool neg = false;
    n = 0;
    for(char & c : s) {
        if(c == '-') neg = true;
        else n = (n * 10) + (c ^ 48);
    }
    if(neg) n *= -1;
    return is;
}

ostream & operator<<(ostream & os, i128 n) {
    if(!n) return os << 0;
    string s = "";
    if(n < 0) n *= -1, os << '-';
    while(n) s.push_back('0' + n % 10), n /= 10;
    reverse(s.begin(), s.end());
    return os << s;
}

int32_t main(){
    std::ios::sync_with_stdio(false), std::cin.tie(NULL);
    
    i128 a, b; cin >> a >> b;
    cout << a << b << '\n';
    return 0;
}
```

# 高精度

```cpp
#include<bits/stdc++.h>

using namespace std;

constexpr int maxn = 1010;

struct BigInt{
    int d[maxn], len;
    void clean() { while(len > 1 && !d[len-1]) len--; }

    BigInt()           { memset(d, 0, sizeof(d)); len = 1; }
    BigInt(int num)    { *this = num; }
    BigInt(string & num) { *this = num; }

    BigInt operator = (string & num){
        memset(d, 0, sizeof(d)); len = num.size();
        for(int i = 0; i < len; i++) d[i] = num[len-1-i]^48;
        clean();
        return *this;
    }
    BigInt operator = (int num) {
        string s = to_string(num);
        *this = s;
        return *this;
    }
    BigInt operator + (const BigInt & x){
        BigInt c = *this; int i;
        for (i = 0; i < x.len; i++){
            c.d[i] += x.d[i];
            if (c.d[i] > 9) c.d[i] -= 10, c.d[i+1]++;
        }
        while (c.d[i] > 9) c.d[i++] -= 10, c.d[i]++;
        c.len = max(len, x.len);
        if (c.d[i] && c.len <= i) c.len = i+1;
        return c;
    }
    BigInt operator - (const BigInt& b){
        BigInt c = *this; int i;
        for (i = 0; i < b.len; i++){
            c.d[i] -= b.d[i];
            if (c.d[i] < 0) c.d[i]+=10, c.d[i+1]--;
        }
        while (c.d[i] < 0) c.d[i++]+=10, c.d[i]--;
        c.clean();
        return c;
    }
    BigInt operator * (const BigInt& b) const{
        int i, j; BigInt c; c.len = len + b.len;
        for(j = 0; j < b.len; j++) 
            for(i = 0; i < len; i++)
                c.d[i+j] += d[i] * b.d[j];
        for(i = 0; i < c.len-1; i++)
            c.d[i+1] += c.d[i]/10, c.d[i] %= 10;
        c.clean();
        return c;
    }
    BigInt operator / (const BigInt & b){
        int i, j;
        BigInt c = *this, a = 0;
        for(i = len - 1; i >= 0; i--) {
            a = a*10 + d[i];
            for(j = 0; j < 10; j++) 
                if(a < b*(j+1)) break;
            c.d[i] = j;
            a = a - b*j;
        }
        c.clean();
        return c;
    }
    BigInt operator / (const int b) {
        int a = 0;
        BigInt c = 0;
        for(int i = len-1; i >= 0; i--) {
            a = a * 10 + d[i];
            if(a >= b) {
                c.d[i] = a / b;
                a %= b;
            }
        }
        for(int i = 0; i < maxn; i++) {
            if(c.d[i] > 10) {
                c.d[i+1] += c.d[i] / 10;
                c.d[i] %= 10;
            }
            if(c.d[i]) c.len = i + 1;
        }
        return c;
    }
    BigInt operator % (const BigInt& b){
        int i, j;
        BigInt a = 0;
        for (i = len - 1; i >= 0; i--) {
            a = a*10 + d[i];
            for (j = 0; j < 10; j++)
                if (a < b*(j+1))
                    break;
            a = a - b*j;
        }
        return a;
    }
    int operator % (const int b) {
        int ans = 0;
        for(int i = len - 1; i >= 0; i--) {
            ans = ans * 10 + d[i];
            ans %= b;
        }
        return ans;
    }
    void operator += (const BigInt & b) {
        *this = *this + b;
    }
    void operator -= (const BigInt & b) {
        *this = *this - b;
    }
    void operator *= (const BigInt & b) {
        *this = *this * b;
    }
    void operator /= (const BigInt & b) {
        *this = *this / b;
    }
    void operator %= (const BigInt & b) {
        *this = *this % b;
    }
    
    bool operator < (const BigInt & b) const{
        if(len != b.len) return len < b.len;
        for(int i = len-1; i >= 0; i--)
            if(d[i] != b.d[i]) return d[i] < b.d[i];
        return false;
    }  
    bool operator >(const BigInt & b) const{return b < *this;}
    bool operator<=(const BigInt & b) const{return !(b < *this);}
    bool operator>=(const BigInt & b) const{return !(*this < b);}
    bool operator!=(const BigInt & b) const{return b < *this || *this < b;}
    bool operator==(const BigInt & b) const{return !(b < *this) && !(b > *this);}
  
    string str() const{
        string s; s.resize(len);
        for(int i = 0; i < len; i++) s[len-1-i] = d[i]+'0';
        return s;
    }
};

BigInt sqrt(const BigInt & n) {
    BigInt ans;
    BigInt l = BigInt(0), r = n;
    while(l <= r) {
        BigInt mid = (l + r) / BigInt(2);
        if(mid * mid >= n) {
            ans = mid;
            r = mid - BigInt(1);
        }
        else {
            l = mid + BigInt(1);
        }
    }
    return ans;
}

istream& operator >> (istream & is, BigInt & x) {
    string s; is >> s; x = s;
    return is;
}

ostream& operator << (ostream & os, const BigInt & x) {
    os << x.str();
    return os;
}

int main() {
    ios::sync_with_stdio(false), cin.tie(NULL);
    // 
    BigInt a; int b; cin >> a >> b;
    cout << a / b << '\n';
}
```

# 计算几何

```cpp
#include<iostream>
#include<cmath>
#include<vector>

using namespace std;

const double pi = acos(-1.0);
constexpr double eps = 1e-8;

struct Point {
    double x, y;
    Point() : x(0), y(0) {}
    Point(double _x, double _y) : x(_x), y(_y) {}
};

constexpr int sgn(double x) {
    if(fabs(x) < eps) return 0;
    else return x < 0 ? -1 : 1;
}
constexpr int dcmp(double x, double y) {
    if(fabs(x - y) < eps) return 0;
    else return x < y ? -1 : 1;
}

Point operator + (const Point & A, const Point & B) {
    return Point(A.x + B.x, A.y + B.y);
}
Point operator - (const Point & A, const Point & B) {
    return Point(A.x - B.x, A.y - B.y);
}
Point operator * (const Point & A, const int k) {
    return Point(A.x * k, A.y * k);
}
Point operator / (const Point & A, const int k) {
    return Point(A.x / k, A.y / k);
} 
bool operator == (const Point & A, const Point & B) {
    return (sgn(A.x - B.x) == 0 && sgn(A.y - B.y) == 0);
}

constexpr double distance(const Point & A, const Point & B) {
    return hypot(A.x - B.x, A.y - B.y);
}
constexpr double distance2(const Point & A, const Point & B) {
    return ((A.x - B.x) * (A.x - B.x) + (A.y - B.y) * ((A.y - B.y)));
}
constexpr double dot(const Point & A, const Point & B) {
    return (A.x * B.x + A.y * B.y);
}
constexpr double cross(const Point & A, const Point & B) {
    return (A.x * B.y) - (A.y * B.x);
}
// a-b 与 a-c 做差积
constexpr double cross(const Point & A, const Point & B, const Point & C) {
    return cross(A - B, A - C);
}
// 判断线段(a-b) 与 (c-d) 是否相交: true 为交，反之 为不交
constexpr bool is_cross(Point & a, Point & b, Point & c, Point & d) {
    return (cross(a, b, c) * cross(a, b, d) <= 0);
}
// 求两线(a-u) 与 (b-v)段 交点
constexpr Point getNode(Point & a, Point & u, Point & b, Point & v) {
    double t = cross((a - b), v) / cross(v, u);
    return Point(a + u * t);
}

int main() {
    ios::sync_with_stdio(false);//, cin.tie(NULL);
    // poj 1106
    // Point mid;
    // double r; 
    // while(cin >> mid.x >> mid.y >> r && r > 0) {
    //     int m; cin >> m;
    //     vector<Point> t;
    //     while(m--) {
    //         Point a; cin >> a.x >> a.y;
    //         if(distance2(a, mid) <= r * r) {
    //             t.push_back(a);
    //         }
    //     }
    //     int ans = 0;
    //     for(int i = 0; i < t.size(); i++) {
    //         int cnt = 0;
    //         for(int j = 0; j < t.size(); j++) {
    //             if(cross(t[i] - mid, t[j] - mid) >= 0) {
    //                 cnt++;
    //             }
    //         }
    //         ans = max(ans, cnt);
    //     }
    //     cout << ans << '\n';
    // }
    return 0;
}
```

# 树状数组

```cpp
#include<bits/stdc++.h>

using namespace std;

template <class T> struct fenwick {
    int n; vector<T> tree;

    fenwick(int _n = 0) : n(_n + 1), tree(_n + 1, {}) {}
    void add(int i, T d) {
        assert(i > 0);
        for( ; i < n; i += (i & -i)) tree[i] += d;
    }
    T ask(int i) {
        assert(i >= 0);
        T sum {};
        for( ; i; i -= (i & -i)) sum += tree[i];
        return sum;
    }
    T ask(int l, int r) {
        assert(l <= r);
        return ask(r) - ask(l - 1);
    }
    int kth(int k) {
        int sum = 0, x = 0;
        for(int i = 20; i >= 0; i--){
            x += (1 << i);
        if(x >= n || sum + tree[x] >= k)
            x -= (1 << i);
        else
            sum += tree[x];
        }
        return x + 1;
    }
};

int main(){
    int n, q; cin >> n >> q;
    vector<int>a(n+1);
    fenwick<int>fw(n+100);
    for(int i = 1; i <= n; i++){
        cin >> a[i];
        fw.add(a[i], 1);
    }
    while(q--){
        int k; cin >> k;
        cout << fw.kth(k) << '\n';
    }
    
    return 0;
}
```

# 线段树处理区间最值问题

```cpp
#include<bits/stdc++.h>
#define int long long
using namespace std;

constexpr int N = 1e5 + 10;
constexpr int inf = 2e9;

template<typename T>
class segmentTree_min_max {
    #define lson p<<1
    #define rson p<<1|1 
private :
    struct Node {
        int l, r;
        T min;
        T max;
    };
    vector<Node> tree;
    int n = 0;
    inline void push_up(int p) {
        tree[p].min = min(tree[lson].min, tree[rson].max);
        tree[p].max = max(tree[lson].max, tree[rson].max);
    }
public :
    vector<T> A;
    segmentTree_min_max(int _n) : n(_n) {
        tree.resize((_n + 1) << 2);
        A.resize(_n + 1, 0);
    }
    void build(int l, int r, int p = 1) {
        tree[p].l = l;
        tree[p].r = r;
        if(l == r) {
            tree[p].min = tree[p].max = A[l];
            return;
        }
        int mid = l + r >> 1;
        build(l, mid, lson);
        build(mid + 1, r, rson);
        push_up(p);
    }
    void update(int pos, T val, int p = 1) {
        if(tree[p].l == pos && tree[p].r == pos) {
            tree[p].min = val;
            tree[p].max = val;
            return;
        }
        int mid = tree[p].l + tree[p].r >> 1;
        if(pos <= mid) update(pos, val, lson);
        else update(pos, val, rson);
        push_up(p);
    }
    T query_max(int l, int r, int p = 1) {
        if(l <= tree[p].l && tree[p].r <= r) return tree[p].max;
        int mid = tree[p].l + tree[p].r >> 1;
        T ans = -inf;
        if(mid >= l) ans = max(ans, query_max(l, r, lson));
        if(mid < r)  ans = max(ans, query_max(l, r, rson));
        return ans;
    }
    T query_min(int l, int r, int p = 1) {
        if(l <= tree[p].l && tree[p].r <= r) return tree[p].min;
        int mid = tree[p].l + tree[p].r >> 1;
        T ans = inf;
        if(mid >= l) ans = min(ans, query_min(l, r, lson));
        if(mid < r)  ans = min(ans, query_min(l, r, rson));
        return ans;
    }
};

int32_t main(){
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int m, p; cin >> m >> p;
    segmentTree_min_max<int> seg(m);
    seg.build(1, m);

    int a = 0;
    int n = 0;
    for(int i = 1; i <= m; i++) {
        char c; int t; cin >> c >> t;
        if(c == 'Q') {
            a = seg.query_max(n-t+1, n);
            cout << a << '\n';
        }
        else {
            n++;
            seg.update(n, (a + t) % p);
        }
    }
    
    return 0;
}
```

# 线段树区间加法

```cpp
struct segment {
    inline int lson(int p) {return (p << 1); }
    inline int rson(int p) {return (p << 1 | 1); }

    struct Info {
        int l, r;
        int sum;
    
        friend Info operator+(const Info & lhs, const Info & rhs) {
            Info info;
            info.l = lhs.l;
            info.r = rhs.r;
            info.sum = lhs.sum + rhs.sum;
            return info;
        }
    };

    struct Tag {
        int add;
    };

    vector<Info> info;
    vector<Tag> tag;

    segment(int n, vector<int> & a) : info((n + 1) << 2), tag((n + 1) << 2) {
        build(1, n, 1, a);
    }

    inline void push_up(int p) {
        info[p] = info[lson(p)] + info[rson(p)];
    }

    inline void push_down(int p) {
        if(tag[p].add) {
            info[lson(p)].sum += (info[lson(p)].r - info[lson(p)].l + 1) * tag[p].add;
            info[rson(p)].sum += (info[rson(p)].r - info[rson(p)].l + 1) * tag[p].add;
            tag[lson(p)].add += tag[p].add;
            tag[rson(p)].add += tag[p].add;
            tag[p].add = 0;
        }
    }

    void build(int x, int y, int p, vector<int> & a) {
        info[p].l = x;
        info[p].r = y;
        if(x == y) {
            info[p].sum = a[x];
            return;
        }

        int mid = x + y >> 1;
        build(x, mid, lson(p), a);
        build(mid + 1, y, rson(p), a);

        push_up(p);
    }

    void update(int l, int r, int d, int p = 1) {
        if(l <= info[p].l && info[p].r <= r) {
            info[p].sum += (info[p].r - info[p].l + 1) * d;
            tag[p].add += d;
            return;
        }

        push_down(p);

        int mid = info[p].l + info[p].r >> 1;
        if(mid >= l) update(l, r, d, lson(p));
        if(mid <  r) update(l, r, d, rson(p));

        push_up(p);
    }

    int rangSum(int l, int r, int p = 1) {
        if(l <= info[p].l && info[p].r <= r) {
            return info[p].sum;
        }
        push_down(p);
        int mid = info[p].l + info[p].r >> 1;
        int sum = 0;
        if(mid >= l) sum += rangSum(l, r, lson(p));
        if(mid <  r) sum += rangSum(l, r, rson(p));

        return sum;
    }
};

void enjoyerist() {
    int n, q; cin >> n >> q;
    vector<int> a(n + 1);
    for(int i = 1; i <= n; i++) {
        cin >> a[i];
    }

    segment seg(n, a);

    for(int i = 1; i <= q; i++) {
        int op; cin >> op;
        if(op == 1) {
            int x, y, k; cin >> x >> y >> k;
            seg.update(x, y, k);
        }
        if(op == 2) {
            int x, y; cin >> x >> y;
            cout << seg.rangSum(x, y) << '\n';
        }
    }
}
```



# 线段树区间乘法

```cpp
struct segment {
    inline int lson(int p) {return (p << 1); }
    inline int rson(int p) {return (p << 1 | 1); }
    int mod;
    struct Info {   
        int l, r;
        int sum;
    
        friend Info operator+(const Info & lhs, const Info & rhs) {
            Info info;
            info.l = lhs.l;
            info.r = rhs.r;
            info.sum = lhs.sum + rhs.sum;
            return info;
        }
    };

    struct Tag {
        int add, mul;
        Tag() {
            add = 0;
            mul = 1;
        }
    };

    vector<Info> info;
    vector<Tag> tag;

    segment(int n, int m, vector<int> & a) : info((n + 1) << 2), tag((n + 1) << 2) {
        mod = m;
        build(1, n, 1, a);
    }

    inline void push_up(int p) {
        info[p] = info[lson(p)] + info[rson(p)];
    }

    inline void push_down(int p) {

        info[lson(p)].sum = (info[lson(p)].sum * tag[p].mul
            + (info[lson(p)].r - info[lson(p)].l + 1) * tag[p].add) % mod;
        info[rson(p)].sum = (info[rson(p)].sum * tag[p].mul
            + (info[rson(p)].r - info[rson(p)].l + 1) * tag[p].add) % mod;

        tag[lson(p)].mul = tag[lson(p)].mul * tag[p].mul % mod;
        tag[rson(p)].mul = tag[rson(p)].mul * tag[p].mul % mod;

        tag[lson(p)].add = (tag[lson(p)].add * tag[p].mul + tag[p].add) % mod;
        tag[rson(p)].add = (tag[rson(p)].add * tag[p].mul + tag[p].add) % mod;

        tag[p].add = 0;
        tag[p].mul = 1;
    }

    void build(int x, int y, int p, vector<int> & a) {
        info[p].l = x;
        info[p].r = y;
        if(x == y) {
            info[p].sum = a[x];
            return;
        }

        int mid = x + y >> 1;
        build(x, mid, lson(p), a);
        build(mid + 1, y, rson(p), a);

        push_up(p);
    }

    void update_add(int l, int r, int d, int p = 1) {
        if(l <= info[p].l && info[p].r <= r) {
            info[p].sum += (info[p].r - info[p].l + 1) * d % mod;
            info[p].sum %= mod;
            tag[p].add += d;
            tag[p].add %= mod;
            return;
        }

        push_down(p);
        
        int mid = info[p].l + info[p].r >> 1;
        if(mid >= l) update_add(l, r, d, lson(p));
        if(mid <  r) update_add(l, r, d, rson(p));

        push_up(p);
    }

    void update_mul(int l, int r, int d, int p = 1) {
        if(l <= info[p].l && info[p].r <= r) {
            info[p].sum = (info[p].sum * d) % mod;
            tag[p].mul = (tag[p].mul * d) % mod;
            tag[p].add = (tag[p].add * d) % mod;
            return;
        }

        push_down(p);
        
        int mid = info[p].l + info[p].r >> 1;
        if(mid >= l) update_mul(l, r, d, lson(p));
        if(mid <  r) update_mul(l, r, d, rson(p));

        push_up(p);
    }

    int rangSum(int l, int r, int p = 1) {
        if(l <= info[p].l && info[p].r <= r) {
            return info[p].sum;
        }
        push_down(p);
        int mid = info[p].l + info[p].r >> 1;
        int sum = 0;
        if(mid >= l) sum += rangSum(l, r, lson(p));
        sum %= mod;
        if(mid <  r) sum += rangSum(l, r, rson(p));
        sum %= mod;
        return sum;
    }
};


void enjoyerist() {
    int n, m, q; cin >> n >> q >> m;
    vector<int> a(n + 1);
    for(int i = 1; i <= n; i++) {
        cin >> a[i];
    }

    segment seg(n, m, a);

    for(int i = 1; i <= q; i++) {
        int op; cin >> op;
        if(op == 1) {
            int x, y, k; cin >> x >> y >> k;
            seg.update_mul(x, y, k);
        }
        if(op == 2) {
            int x, y, k; cin >> x >> y >> k;
            seg.update_add(x, y, k);
        }
        if(op == 3) {
            int x, y; cin >> x >> y;
            cout << seg.rangSum(x, y) << '\n';
        }
    }
}
```

# 可持久化线段树

##  查询历史版本数组

```cpp
#pragma GCC optimize(3, "Ofast", "inline")

#include<bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define pdd pair<double, double>
#define lowbit(x) (x & -x)
#define all(x) x.begin(), x.end()
 
using namespace std;
using i32 = int32_t;
using i64 = long long;
using i128 = __int128_t;

constexpr i32 inf = 2e9; 
constexpr i64 INF = 1e18; 
constexpr i64 mod = 1e9 + 7; 
constexpr i32 dx[] = {-1, 1, 0, 0};
constexpr i32 dy[] = {0, 0, -1, 1};

inline int iceil(int a, int b) { return (a + b - 1) / b;}
int qpow(int a, int b) { int ans = 1; while(b) { if(b&1) ans *= a; b >>= 1; a *= a;} return ans;}

constexpr int N = 1e6 + 10;
struct {
    int lson, rson;
    int val;
}tree[N * 23];
int root[N], A[N];
int idx = 0;

#define lson(x) tree[x].lson
#define rson(x) tree[x].rson
// 建树
int build(int l, int r) {
    int rt = ++idx;
    if(l == r) {
        tree[rt].val = A[l];
        return rt;
    }
    int mid = l + r >> 1;
    lson(rt) = build(l, mid);
    rson(rt) = build(mid + 1, r);
    return rt;
}
// 将第pos改为val，最开始的p表示某个版本的线段树根节点
int update(int pos, int val, int l, int r, int p) {
    int rt = ++idx;
    tree[rt] = tree[p];
    if(l == r) {
        tree[rt].val = val;
    }
    else {
        int mid = l + r >> 1;
        if(pos <= mid) lson(rt) = update(pos, val, l, mid, lson(rt));
        else rson(rt) = update(pos, val, mid + 1, r, rson(rt));
    }
    return rt;
}
// 查询第pos个位置的值，p为某版本线段树根节点
int query(int pos, int l, int r, int p) {
    if(l == r) return tree[p].val;
    int mid = l + r >> 1;
    if(pos <= mid) return query(pos, l, mid, lson(p));
    else return query(pos, mid + 1, r, rson(p));
}

void enjoyerist() {
    int n, q; cin >> n >> q;
    idx = 0;
    for(int i = 1; i <= n; i++) cin >> A[i];
    
    root[0] = build(1, n);

    for(int i = 1; i <= q; i++) {
        int v, op, loc; cin >> v >> op >> loc;
        if(op == 1) {
            int val; cin >> val;
            root[i] = update(loc, val, 1, n, root[v]);
        }
        else {
            root[i] = root[v];
            cout << query(loc, 1, n, root[v]) << '\n';
        }
    }
}

i32 main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);

    i32 T = 1; // std::cin >> T;
    while(T--) enjoyerist();
    return 0;
}
```

## 查询区间第k小

```cpp
#include <bits/stdc++.h>

using namespace std;

constexpr int N = 2e5 + 10;
struct {
    int lson, rson;
    int cnt;
}tree[N * 23];
int root[N];
#define lson(x) tree[x].lson
#define rson(x) tree[x].rson
int idx = 0;

int build(int l, int r) {
    int rt = ++idx;
    tree[rt].cnt = 0;
    if(l == r) return rt;
    int mid = l + r >> 1;
    lson(rt) = build(l, mid);
    rson(rt) = build(mid + 1, r);
    return rt;
}
// 在版本p线段树上生成一颗新的线段树
int update(int val, int l, int r, int p) {
    int rt = ++idx;
    tree[rt] = tree[p];
    tree[rt].cnt++;
    if(l == r) return rt;
    int mid = l + r >> 1;
    if(val <= mid) lson(rt) = update(val, l, mid, lson(p));
    else rson(rt) = update(val, mid + 1, r, rson(p));
    return rt;
}
// 查询第k小
int query(int x, int y, int l, int r, int k) {
    if(l == r) return l;
    int mid = l + r >> 1;
    int d = tree[lson(y)].cnt - tree[lson(x)].cnt;
    if(k <= d) return query(lson(x), lson(y), l, mid, k);
    else return query(rson(x), rson(y), mid + 1, r, k - d);
}

// 查询小于等于t的个数
int query(int x, int y, int l, int r, int t) {
    if(t < l) return 0;
    if(r <= t) return tree[y].cnt - tree[x].cnt;

    int cnt = 0;
    int mid = l + r >> 1;
    cnt += query(lson(x), lson(y), l, mid, t);
    cnt += query(rson(x), rson(y), mid + 1, r, t);
    return cnt;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, q; cin >> n >> q;
    idx = 0;
    vector<int> a(n + 1);
    vector<int> b(n + 1);
    for(int i = 1; i <= n; i++) {
        cin >> a[i];
        b[i] = a[i];
    }
    sort(b.begin() + 1, b.end());
    int len = unique(b.begin() + 1, b.end()) - b.begin();
    auto getid = [&](int x) -> int {
        return lower_bound(b.begin() + 1, b.begin() + len, x) - b.begin();
    };

    root[0] = build(1, len);

    
    for(int i = 1; i <= n; i++) {
        root[i] = update(getid(a[i]), 1, len, root[i-1]);
    }
    for(int i = 1; i <= q; i++) {
        int l, r, k; cin >> l >> r >> k;
        int pos = query(root[l-1], root[r], 1, len, k);
        cout << b[pos] << '\n';
    }

    return 0;
}
```



# 带懒标记线段树(jiangly)

```cpp
#include <bits/stdc++.h>
#define int long long
using i64 = long long;
using u64 = unsigned long long;
using u32 = unsigned;
using u128 = unsigned __int128;

/// 带懒标记线段树
// 左闭右开
template<class Info, class Tag> struct LazySegmentTree {
    int n;
    std::vector<Info> info;
    std::vector<Tag> tag;
    LazySegmentTree() : n(0) {}
    LazySegmentTree(int _n, Info _v = Info()) {
        init(_n, _v);
    }
    template<class T>
    LazySegmentTree(std::vector<T> _init) {
        init(_init);
    }
    void init(int _n, Info _v = Info()) {
        init(std::vector(_n + 1, _v));
    }

    template<class T>
    void init(std::vector<T> _init) {
        n = _init.size() + 1;
        info.assign(4 << std::__lg(n), Info());
        tag.assign(4 << std::__lg(n), Tag());
        auto build = [&](auto & build, int p, int l, int r) -> void {
            if (r - l == 1) {
                info[p] = _init[l];
                return;
            }
            int mid = (l + r) >> 1;
            build(build, p << 1, l, mid);
            build(build, p << 1 | 1, mid, r);
            pull(p);
        };
        build(build, 1, 1, n + 1);
    }
    void pull(int p) {
        info[p] = info[p << 1] + info[p << 1 | 1];
    }
    void apply(int p, const Tag &v) {
        info[p].apply(v);
        tag[p].apply(v);
    }
    void push(int p) {
        apply(p << 1, tag[p]);
        apply(p << 1 | 1, tag[p]);
        tag[p] = Tag();
    }
    void modify(int p, int l, int r, int x, const Info &v) {
        if (r - l == 1) {
            info[p] = v;
            return;
        }
        int mid = (l + r) >> 1;
        push(p);
        if (x < mid) {
            modify(p << 1, l, mid, x, v);
        } else {
            modify(p << 1 | 1, mid, r, x, v);
        }
        pull(p);
    }
    void modify(int p, const Info &v) {
        modify(1, 1, n + 1, p, v);
    }
    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (l >= y || r <= x) {
            return Info();
        }
        if (l >= x && r <= y) {
            return info[p];
        }
        int mid = (l + r) >> 1;
        push(p);

        // 下面这样写是为了方便进行区间合并 
        if(mid <= x) return rangeQuery(p << 1 | 1, mid, r, x, y);
        if(mid >= y) return rangeQuery(p << 1, l, mid, x, y);
        return rangeQuery(p << 1, l, mid, x, y) + rangeQuery(p << 1 | 1, mid, r, x, y);
    }
    Info rangeQuery(int l, int r) {
        return rangeQuery(1, 1, n + 1, l, r);
    }
    void rangeApply(int p, int l, int r, int x, int y, const Tag &v) {
        if (l >= y || r <= x) {
            return;
        }
        if (l >= x && r <= y) {
            apply(p, v);
            return;
        }
        int mid = (l + r) >> 1;
        push(p);
        rangeApply(p << 1, l, mid, x, y, v);
        rangeApply(p << 1 | 1, mid, r, x, y, v);
        pull(p);
    }
    void rangeApply(int l, int r, const Tag &v) {
        return rangeApply(1, 1, n + 1, l, r, v);
    }
};


struct Tag {
    
    void apply(const Tag &t) {
     
    }
};
 
struct Info {
   

    void apply(const Tag &t) {
       
    }
};
 
Info operator+(const Info & a, const Info & b) {
    
}
 
void solve() {
    int n, m;
    std::cin >> n >> m;
    std::vector<Info> a(n + 1);
    for(int i = 1; i <= n; i++) {
        std::cin >> a[i].sum;
    }
    LazySegmentTree<Info, Tag> seg(a);

    for(int i = 1; i <= m; i++) {
        int op; std::cin >> op;
        if(op == 1) {
            int x, y; i64 k; std::cin >> x >> y >> k;
            seg.rangeApply(x, y + 1, Tag(k));
        }
        if(op == 2) {
            int x, y; std::cin >> x >> y;
            std::cout << seg.rangeQuery(x, y + 1).sum << '\n';
        }
    }
}
 
int32_t main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    
    int t = 1;
    // std::cin >> t;
    
    while (t--) {
        solve();
    }
    return 0;
}
```

## 区间乘法与加法

```cpp
#include <bits/stdc++.h>
#define int long long
using i64 = long long;
using u64 = unsigned long long;
using u32 = unsigned;
using u128 = unsigned __int128;

int mod;

// 带懒标记线段树
// 左闭右开
template<class Info, class Tag> struct LazySegmentTree {
    int n;
    std::vector<Info> info;
    std::vector<Tag> tag;
    LazySegmentTree() : n(0) {}
    LazySegmentTree(int _n, Info _v = Info()) {
        init(_n, _v);
    }
    template<class T>
    LazySegmentTree(std::vector<T> _init) {
        init(_init);
    }
    void init(int _n, Info _v = Info()) {
        init(std::vector(_n + 1, _v));
    }

    template<class T>
    void init(std::vector<T> _init) {
        n = _init.size() + 1;
        info.assign(4 << std::__lg(n), Info());
        tag.assign(4 << std::__lg(n), Tag());
        auto build = [&](auto & build, int p, int l, int r) -> void {
            if (r - l == 1) {
                info[p] = _init[l];
                return;
            }
            int mid = (l + r) >> 1;
            build(build, p << 1, l, mid);
            build(build, p << 1 | 1, mid, r);
            pull(p);
        };
        build(build, 1, 1, n + 1);
    }
    void pull(int p) {
        info[p] = info[p << 1] + info[p << 1 | 1];
    }
    void apply(int p, const Tag &v) {
        info[p].apply(v);
        tag[p].apply(v);
    }
    void push(int p) {
        apply(p << 1, tag[p]);
        apply(p << 1 | 1, tag[p]);
        tag[p] = Tag();
    }
    void modify(int p, int l, int r, int x, const Info &v) {
        if (r - l == 1) {
            info[p] = v;
            return;
        }
        int mid = (l + r) >> 1;
        push(p);
        if (x < mid) {
            modify(p << 1, l, mid, x, v);
        } else {
            modify(p << 1 | 1, mid, r, x, v);
        }
        pull(p);
    }
    void modify(int p, const Info &v) {
        modify(1, 1, n + 1, p, v);
    }
    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (l >= y || r <= x) {
            return Info();
        }
        if (l >= x && r <= y) {
            return info[p];
        }
        int mid = (l + r) >> 1;
        push(p);
        return rangeQuery(p << 1, l, mid, x, y) + rangeQuery(p << 1 | 1, mid, r, x, y);
    }
    Info rangeQuery(int l, int r) {
        return rangeQuery(1, 1, n + 1, l, r);
    }
    void rangeApply(int p, int l, int r, int x, int y, const Tag &v) {
        if (l >= y || r <= x) {
            return;
        }
        if (l >= x && r <= y) {
            apply(p, v);
            return;
        }
        int mid = (l + r) >> 1;
        push(p);
        rangeApply(p << 1, l, mid, x, y, v);
        rangeApply(p << 1 | 1, mid, r, x, y, v);
        pull(p);
    }
    void rangeApply(int l, int r, const Tag &v) {
        return rangeApply(1, 1, n + 1, l, r, v);
    }
};

struct Tag {
    int add, mul;
    Tag(int _add = 0, int _mul = 1) : add(_add), mul(_mul) {}

    void apply(const Tag &t) {
        mul = (mul * t.mul) % mod;
        add = (add * t.mul + t.add) % mod;
    }
};
 
struct Info {
    int sum, cnt;
    Info(int _sum = 0, int _cnt = 1) : sum(_sum), cnt(_cnt) {}
    void apply(const Tag &t) {
        sum = (sum * t.mul + t.add * cnt) % mod;
    }
};
 
Info operator+(const Info & a, const Info & b) {
    Info tmp;
    tmp.sum = (a.sum + b.sum) % mod;
    tmp.cnt = a.cnt + b.cnt;
    return tmp;
}
 
void solve() {
    int n, m;
    std::cin >> n >> m >> mod;
    std::vector<Info> a(n + 1);
    for(int i = 1; i <= n; i++) {
        int x; std::cin >> x;
        a[i] = Info(x);
    }
    LazySegmentTree<Info, Tag> seg(a);

    for(int i = 1; i <= m; i++) {
        int op; std::cin >> op;
        if(op == 1) {
            int x, y, k; std::cin >> x >> y >> k;
            seg.rangeApply(x, y + 1, Tag(0, k));
        }
        else if(op == 2) {
            int x, y, k; std::cin >> x >> y >> k;
            seg.rangeApply(x, y + 1, Tag(k, 1));
        }
        else if(op == 3) {
            int x, y; std::cin >> x >> y;
            std::cout << seg.rangeQuery(x, y + 1).sum << '\n';
        }
    }
}
 
int32_t main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    
    int t = 1;
    // std::cin >> t;
    
    while (t--) {
        solve();
    }
    return 0;
}
```



# 不带懒更新，单点修改线段树

```cpp
// 不带懒标记线段树
// 单点修改/区间查询
// 左闭右开

template<class Info> struct SegmentTree {
    int n;
    std::vector<Info> info;
    SegmentTree() : n(0) {}
    SegmentTree(int _n, Info _v = Info()) {
        init(_n, _v);
    }
    template<class T>
    SegmentTree(std::vector<T> _init) {
        init(_init);
    }
    void init(int _n, Info _v = Info()) {
        init(std::vector(_n + 1, _v));
    }

    template<class T>
    void init(std::vector<T> _init) {
        n = _init.size() + 1;
        info.assign(4 << std::__lg(n), Info());
        auto build = [&](auto & build, int p, int l, int r) -> void {
            if (r - l == 1) {
                info[p] = _init[l];
                return;
            }
            int mid = (l + r) >> 1;
            build(build, p << 1, l, mid);
            build(build, p << 1 | 1, mid, r);
            pull(p);
        };
        build(build, 1, 1, n + 1);
    }
    void pull(int p) {
        info[p] = info[p << 1] + info[p << 1 | 1];
    }
    void modify(int p, int l, int r, int x, const Info &v) {
        if (r - l == 1) {
            info[p] = v;
            return;
        }
        int mid = (l + r) >> 1;
        if (x < mid) {
            modify(p << 1, l, mid, x, v);
        } else {
            modify(p << 1 | 1, mid, r, x, v);
        }
        pull(p);
    }
    void modify(int p, const Info &v) {
        modify(1, 1, n + 1, p, v);
    }

    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (l >= y || r <= x) {
            return Info();
        }
        if (l >= x && r <= y) {
            return info[p];
        }
        int mid = (l + r) >> 1;
        return rangeQuery(p << 1, l, mid, x, y) + rangeQuery(p << 1 | 1, mid, r, x, y);
    }
    Info rangeQuery(int l, int r) {
        return rangeQuery(1, 1, n + 1, l, r);
    }
};

struct Info {
    int g = 0;
};
 
Info operator+(const Info & a, const Info & b) {
    return Info{std::max(a.g, b.g)};
}
```





# 质因数分解

```cpp
#include<bits/stdc++.h>

using namespace std;

using i64 = long long;
using i128 = __int128_t;

i64 mul(i64 a, i64 b, i64 m) {
    return static_cast<i128>(a) * b % m;
}
i64 power(i64 a, i64 b, i64 m) {
    i64 res = 1 % m;
    for (; b; b >>= 1, a = mul(a, a, m))
        if (b & 1)
            res = mul(res, a, m);
    return res;
}
bool isprime(i64 n) {
    if (n < 2) return false;
    static constexpr int A[] = {2, 3, 5, 7, 11, 13, 17, 19, 23};
    int s = __builtin_ctzll(n - 1);
    i64 d = (n - 1) >> s;
    for (auto a : A) {
        if (a == n) return true;
        i64 x = power(a, d, n);
        if (x == 1 || x == n - 1) continue;
        bool ok = false;
        for (int i = 0; i < s - 1; ++i) {
            x = mul(x, x, n);
            if (x == n - 1) {
                ok = true;
                break;
            }
        }
        if (!ok) return false;
    }
    return true;
}

vector<i64> factorize(i64 n) {
    vector<i64> p;
    function<void(i64)> f = [&](i64 n) {
        if (n <= 10000) {
            for (int i = 2; i * i <= n; ++i)
                for (; n % i == 0; n /= i)
                    p.push_back(i);
            if (n > 1)
                p.push_back(n);
            return;
        }
        if (isprime(n)) {
            p.push_back(n);
            return;
        }
        auto g = [&](i64 x) {
            return (mul(x, x, n) + 1) % n;
        };
        i64 x0 = 2;
        while (true) {
            i64 x = x0;
            i64 y = x0;
            i64 d = 1;
            i64 power = 1, lam = 0;
            i64 v = 1;
            while (d == 1) {
                y = g(y);
                ++lam;
                v = mul(v, std::abs(x - y), n);
                if (lam % 127 == 0) {
                    d = std::gcd(v, n);
                    v = 1;
                }
                if (power == lam) {
                    x = y;
                    power *= 2;
                    lam = 0;
                    d = std::gcd(v, n);
                    v = 1;
                }
            }
            if (d != n) {
                f(d);
                f(n / d);
                return;
            }
            ++x0;
        }
    };
    f(n);
    std::sort(p.begin(), p.end());
    return p;
}

int main(){
    if(isprime(17)) cout << "YES\n";
    else cout << "NO\n";
    return 0;
}
```

# 自动取模

## 1

```cpp
#include <bits/stdc++.h>

using namespace std;

using i64 = long long;

template<class T> constexpr T power(T a, i64 b) {
    T ans {1};
    while(b) {
        if(b & 1) ans = ans * a;
        a = a * a;
        b >>= 1;
    }
    return ans;
}

constexpr i64 mul(i64 a, i64 b, i64 P) {
    i64 res = a * b - i64(1.0L * a * b / P - 0.5L) * P;
     return res % P;
}

template<i64 P> struct MInt {
    i64 x;
    constexpr MInt() : x {0} {}
    constexpr MInt(i64 x) : x {norm(x % getMod())} {}
    static i64 Mod;
    constexpr static i64 getMod() {
        if (P > 0) {
            return P;
        } else {
            return Mod;
        }
    }
    constexpr static void setMod(i64 _Mod) {
        Mod = _Mod;
    }
    constexpr i64 norm(i64 x) const {
        if (x < 0) {
            x += getMod();
        }
        if (x >= getMod()) {
            x -= getMod();
        }
        return x;
    }
    constexpr i64 val() const {
        return x;
    }
    constexpr MInt inv() const {
        return power(*this, getMod() - 2);
    }
    constexpr MInt operator-() const {
        MInt res;
        res.x = (x == 0 ? 0 : getMod() - x);
        return res;
    }
    constexpr MInt &operator+=(MInt rhs) & {
        x = norm(x + rhs.x);
        return *this;
    }
    constexpr MInt &operator-=(MInt rhs) & {
        x = norm(x - rhs.x);
        return *this;
    }
    constexpr MInt &operator*=(MInt rhs) & {
        if (getMod() < (1LL << 31)) {
            x = x * rhs.x % i32(getMod());
        } else {
            x = mul(x, rhs.x, getMod());
        }
        return *this;
    }
    constexpr MInt &operator/=(MInt rhs) & {
        return *this *= rhs.inv();
    }
    friend constexpr MInt operator+(MInt lhs, MInt rhs) {
        MInt res = lhs;
        res += rhs;
        return res;
    }
    friend constexpr MInt operator-(MInt lhs, MInt rhs) {
        MInt res = lhs;
        res -= rhs;
        return res;
    }
    friend constexpr MInt operator*(MInt lhs, MInt rhs) {
        MInt res = lhs;
        res *= rhs;
        return res;
    }
    friend constexpr MInt operator/(MInt lhs, MInt rhs) {
        MInt res = lhs;
        res /= rhs;
        return res;
    }
    friend constexpr istream &operator>>(istream &is, MInt &a) {
        i64 v; is >> v;
        a = MInt(v);
        return is;
    }
    friend constexpr ostream &operator<<(ostream &os, const MInt &a) {
        return os << a.val();
    }
    friend constexpr bool operator==(MInt lhs, MInt rhs) {
        return lhs.val() == rhs.val();
    }
    friend constexpr bool operator!=(MInt lhs, MInt rhs) {
        return lhs.val() != rhs.val();
    }
    friend constexpr strong_ordering operator<=>(MInt lhs, MInt rhs) {
        return lhs.val() <=> rhs.val();
    }
};

template<> i64 MInt<0>::Mod = 998244353;
constexpr int P = 1e9 + 7;
using Z = MInt<P>;

struct Comb {
    int n;
    vector<Z> _fac;
    vector<Z> _invfac;
    vector<Z> _inv;
     
    Comb() : n{0}, _fac{1}, _invfac{1}, _inv{0} {}
    Comb(int n) : Comb() {
        init(n);
    }
     
    void init(int m) {
        if (m <= n) return;
        _fac.resize(m + 1);
        _invfac.resize(m + 1);
        _inv.resize(m + 1);
         
        for (int i = n + 1; i <= m; i++) {
            _fac[i] = _fac[i - 1] * i;
        }
        _invfac[m] = _fac[m].inv();
        for (int i = m; i > n; i--) {
            _invfac[i - 1] = _invfac[i] * i;
            _inv[i] = _invfac[i] * _fac[i - 1];
        }
        n = m;
    }
     
    Z fac(int m) {
        if (m > n) init(2 * m);
        return _fac[m];
    }
    Z invfac(int m) {
        if (m > n) init(2 * m);
        return _invfac[m];
    }
    Z inv(int m) {
        if (m > n) init(2 * m);
        return _inv[m];
    }
    Z binom(int n, int m) {
        if (n < m || m < 0) return 0;
        return fac(n) * invfac(m) * invfac(n - m);
    }
} comb;

int main() {
    ios::sync_with_stdio(false), cin.tie(NULL);
    
    int n, m, q; cin >> n >> m >> q;
    // 要使用动态取模是需要 using Z = Mint<0>;
    // Z::setMod(q);
    
    // vector<vector<Z>>C(n+1, vector<Z>(n+1, 0));
    // for(int i = 0; i <= n; i++){
    //     C[i][0] = 1;
    //     for(int j = 1; j <= i; j++){
    //         C[i][j] = (C[i-1][j-1] + C[i-1][j]);
    //     }
    // }
    
    // Z ans = 0;
    // for(int i = 1; i <= n; i++) {
    //     ans += C[n][i] * power(Z(2), (n - i) * (m - 1)) * power((power(Z(2), i) - 1), m - 1);
    // }
    // cout << ans << '\n';
    return 0;
}
```

## 2

```cpp
template<class T> constexpr T power(T a, int b) {
    T ans {1};
    while(b) {
        if(b & 1) ans = ans * a;
        a = a * a;
        b >>= 1;
    }
    return ans;
}
 
template<u64 m> struct mint {
    u64 x;
    constexpr mint() : x(0ULL) {}
    constexpr mint(i64 _x) : x(((_x %= static_cast<i64>(m)) < 0) ? (_x + static_cast<i64>(m)) : _x) {}
    
    constexpr mint& operator+=(const mint &a) {
        x += a.x;
        if(x >= m) {
            x -= m;
        }
        return *this;
    }
    constexpr mint& operator-=(const mint &a) {
        x -= a.x;
        if(x >= m) {
            x += m;
        }
        return *this;
    }
    constexpr mint& operator*=(const mint &a) {
        if(m < (1ULL << 31)) {
            x = x * a.x % m;
        }
        else {
            x = (x * a.x - u64(1.0L * x * a.x / m - 0.5L) * m) % m;
        }
        return *this;
    }
    constexpr mint inv() {
        return power(x, m - 2);
    }
    constexpr mint& operator/=(const mint &a) {
        return *this *= a.inv();
    }
 
    friend constexpr mint operator+(mint a, const mint &b) {
        a += b;
        return a;
    }
    friend constexpr mint operator-(mint a, const mint &b) {
        a -= b;
        return a;
    }
    friend constexpr mint operator*(mint a, const mint &b) {
        a *= b;
        return a;
    }
    friend constexpr mint operator/(mint a, const mint &b) {
        a /= b;
        return a;
    }
   
    friend constexpr bool operator==(const mint &a, const mint &b) {
        return a.x == b.x;
    }
    friend constexpr bool operator!=(const mint &a, const mint &b) {
        return a.x != b.x;
    }
    friend constexpr strong_ordering operator<=>(const mint &a, const mint &b) {
        return a.x <=> b.x;
    }
 
 
    friend ostream& operator<<(ostream &os, const mint &a) {
        os << a.x;
        return os;
    }
    friend istream& operator>>(istream &is, mint &a) {
        is >> a.x;
        return is;
    }
};

constexpr int P = 998244353;
using Z = mint<P>;
```



# 组合数

## 固定模数

```cpp
// mod必须为质数，并且 n < mod
template<const int mod> struct Comb {
    int n;
    vector<int> fact, invfact;
    Comb(int _n) : n(_n), fact(_n + 1), invfact(_n + 1) {
        fact[0] = invfact[0] = 1;
        for(int i = 1; i <= n; i++) {
            fact[i] = fact[i - 1] * i % mod;
        }
        invfact[n] = qpow(fact[n], mod - 2, mod);
        for(int i = n; i >= 2; i--) {
            invfact[i - 1] = invfact[i] * i % mod;
        }
    }
    int binom(int n, int m) {
        if(n < m || m < 0) return 0;
        return fact[n] * invfact[m] % mod * invfact[n - m] % mod;
    }
    int perm(int n, int m) {
        if(n < m || m < 0) return 0;
        return fact[n] * invfact[n - m] % mod;
    }
    // 卢卡斯定理
    int lucas(int n, int m) {
        if(m == 0) return 1;
        return lucas(n / mod, m / mod) * binom(n % mod, m % mod) % mod;
    }
};
```

## 模数不固定(**速度很慢**)

```cpp
#include <bits/stdc++.h>

using i64 = long long;
using u64 = unsigned long long;
using u32 = unsigned;
using u128 = unsigned __int128;
#define int long long
using namespace std;

int qpow(int a, int b, int mod) {
    int ans {1};
    while(b) {
        if(b & 1) ans = ans * a % mod;
        b >>= 1;
        a = a * a % mod;
    }
    return ans;
}
// mod必须为质数，并且 n < mod
struct Comb {
    int n, mod;
    vector<int> fact, invfact;
    Comb(int _n, int _mod) : n(_n), mod(_mod), fact(_n + 1), invfact(_n + 1) {
        fact[0] = invfact[0] = 1;
        for(int i = 1; i <= n; i++) {
            fact[i] = fact[i - 1] * i % mod;
        }
        invfact[n] = qpow(fact[n], mod - 2, mod);
        for(int i = n; i >= 2; i--) {
            invfact[i - 1] = invfact[i] * i % mod;
        }
    }
    int binom(int n, int m) {
        if(n < m || m < 0) return 0;
        return fact[n] * invfact[m] % mod * invfact[n - m] % mod;
    }
    int perm(int n, int m) {
        if(n < m || m < 0) return 0;
        return fact[n] * invfact[n - m] % mod;
    }
    // 卢卡斯定理
    int lucas(int n, int m) {
        if(m == 0) return 1;
        return lucas(n / mod, m / mod) * binom(n % mod, m % mod) % mod;
    }
};

int32_t main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    
    Comb<(int)(1e9 + 7)> comb(1e6);
    std::cout << comb.binom(90 , 1) << '\n';
    std::cout << comb.perm(4, 2);
    
    return 0;
}
```



# manacher 求最长回文数

```cpp
int manacher(string & s) {
    int n = s.length();
    vector<int> dp(n, 1);
    int ans = 1;
    for(int i = 2, l, r = 1; i < n; i++) {
        if(i <= r) dp[i] = min(dp[r - i + l], r - i + 1);
        while(i + dp[i] < n && s[i - dp[i]] == s[i + dp[i]]) {
            dp[i]++;
        }
        if(i + dp[i] - 1 > r) l = i - dp[i] + 1, r = i + dp[i] - 1;
        ans = max(ans, dp[i] - 1);
    }
    return ans;
}

void enjoyerist() {
    string s, t = ""; cin >> s;
    t.push_back('$');
    t.push_back('#');
    for(int i = 0; i < s.length(); i++) {
        t.push_back(s[i]);
        t.push_back('#');
    }
    cout << manacher(t) << '\n';

}
```





# 字典树

```cpp
#include<bits/stdc++.h>

using namespace std;

// 字典树
constexpr int N = 2e7;
int ch[N][26];
int cnt[N];
int idx;
// 插入字符
void insert(string & s) {
    int p = 0;
    for(int i = 0; i < s.length(); i++) {
        int j = s[i] - 'a';
        if(!ch[p][j]) {
            ch[p][j] = ++idx;
        }
        p = ch[p][j];
    }
    cnt[p]++;
}
// 查询某字符出现次数
int query(string & s) {
    int p = 0;
    for(int i = 0; i < s.length(); i++) {
        int j = s[i] - 'a';
        if(!ch[p][j]) return 0;
        p = ch[p][j];
    }
    return cnt[p];
}

int main() {
    ios::sync_with_stdio(false), cin.tie(NULL);


    return 0;
}
```

# 并查集

```cpp
struct DSU {
    vector<int> fa, sz;
    DSU(int n) {
        fa.assign(n + 1, 0);
        sz.assign(n + 1, 1);
        iota(fa.begin(), fa.end(), 0);
    }
    int find(int x) {   
        return fa[x] == x ? x : (fa[x] = find(fa[x]));
    }
    void merge(int x, int y) {
        x = find(x);
        y = find(y);
        if(x == y) return;
        if(sz[x] < sz[y]) swap(x, y);
        fa[y] = x;
        sz[x] += sz[y];
    }
    int size(int x) {
        return sz[find(x)];
    }
};
```



```cpp
int n, m; cin >> n >> m;
vector<int> fa(n+1);
iota(fa.begin(), fa.end(), 0);
function<int(int)> find = [&](int x) {
    return x == fa[x] ? x : (fa[x] = find(fa[x]));
};
function<void(int, int)> merge = [&](int x, int y) {
    x = find(x);
    y = find(y);
    if(x != y) {
        fa[x] = y;
    }
};
// 查询
// find(i) 是否等于 i
```



# 欧拉筛 欧拉函数 莫比乌斯函数

```cpp
constexpr int N = 1e7+10;

bool isprime[N];
int prime[N];
int mu[N];
int phi[N];

void euler(){
    memset(isprime, true, sizeof(isprime));
    isprime[0] = isprime[1] = false;
    int cnt = 0;
    mu[1] = 1;
    phi[1] = 1;
    for(int i = 2; i < N; i++){
        if(isprime[i]) {
            mu[i] = -1;
            phi[i] = i - 1;
            prime[++cnt] = i;
        }
        for(int j = 1; j <= cnt && i * prime[j] < N; j++){
            isprime[i * prime[j]] = false;
            if(i % prime[j]==0) {
                phi[i * prime[j]] = phi[i] * prime[j];
                break;
            }
            mu[i * prime[j]] = -mu[i];
            phi[i * prime[j]] = phi[i] * (prime[j] - 1);
        }
    }
}
```

# 杜教筛

![bd9943a85c989ce6527605d9dc9cb9ce](C:/Users/18735/Documents/Tencent Files/1873564884/nt_qq/nt_data/Pic/2024-12/Ori/bd9943a85c989ce6527605d9dc9cb9ce.jpeg)

```cpp
// 杜教筛杜教筛应用
#include<bits/stdc++.h>
#define int long long
using namespace std;
using i32 = int32_t;
using i64 = long long;
using i128 = __int128_t;

inline int iceil(int a, int b) { return (a + b - 1) / b;}
int qpow(int a, int b) { int ans = 1; while(b) { if(b&1) ans *= a; b >>= 1; a *= a;} return ans;}

constexpr int N = 5e6+10;

bool isprime[N];
int prime[N];
int mu[N];
int phi[N];

void euler(){
    memset(isprime, true, sizeof(isprime));
    isprime[0] = isprime[1] = false;
    int cnt = 0;
    mu[1] = 1;
    phi[1] = 1;
    for(int i = 2; i < N; i++){
        if(isprime[i]) {
            mu[i] = -1;
            phi[i] = i - 1;
            prime[++cnt] = i;
        }
        for(int j = 1; j <= cnt && i * prime[j] < N; j++){
            isprime[i * prime[j]] = false;
            if(i % prime[j]==0) {
                phi[i * prime[j]] = phi[i] * prime[j];
                break;
            }
            mu[i * prime[j]] = -mu[i];
            phi[i * prime[j]] = phi[i] * (prime[j] - 1);
        }
    }
    for(int i = 1; i < N; i++) {
        mu[i] += mu[i-1];
        phi[i] += phi[i-1];
    }
}

unordered_map<int, i64> S_phi;
unordered_map<int, int> S_mu;

int get_sum(int n) {
    return (1LL + n) * n / 2LL;
}

int get_S_phi(int n) {
    if(n < N) return phi[n];
    if(S_phi[n]) return S_phi[n];
    int ans = get_sum(n);
    for(int i = 2, j; i <= n; i = j + 1) {
        j = n / (n / i);
        ans -= (j - i + 1LL) * get_S_phi(n / i);
    }
    return S_phi[n] = ans;
}

int get_S_mu(int n) {
    if(n < N) return mu[n];
    if(S_mu[n]) return S_mu[n];

    int ans = 1;
    for(int i = 2, j; i <= n; i = j + 1) {
        j = n / (n / i);
        ans -= (j - i + 1LL) * get_S_mu(n / i);
    }
    return S_mu[n] = ans;
}

void enjoyerist() {
    int n; cin >> n;
    cout << get_S_phi(n) << ' ' << get_S_mu(n) << '\n';
}

i32 main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);

    euler();
 
    i32 T = 1;  std::cin >> T;
    while(T--) enjoyerist();
    return 0;
}
```



# 快读

```cpp
inline int read(){
    int s = 0, f = 1;
    char ch = getchar();
    while(ch <'0' || ch >'9') {if(ch=='-') f = -1; ch = getchar();}
    while(ch>='0' && ch <= '9') {s = (s<<3)+(s<<1)+(ch^48); ch = getchar();}
    return s*f;
}
```

#  快速幂

```cpp
i64 qpow(i64 a, int b, i64 mod) {
    i64 ans {1};
    while(b) {
        if(b & 1) ans = ans * a % mod;
        b >>= 1;
        a = a * a % mod;
    }
    return ans;
}
```

# 矩阵快速幂

```cpp
#include <bits/stdc++.h>
#define int int64_t
using i32 = int32_t;
using namespace std;

constexpr int mod = 1e9 + 7;

struct matrix{
    int n, m;
    vector<vector<int>> mat;
    matrix(int _n, int _m) {
        n = _n;
        m = _m;
        mat.assign(n, vector<int>(m, 0));
    };
    friend matrix operator *(matrix & a, matrix & b) {
        int n = a.n, m = b.m;
        int p = a.m;
        matrix res(n, m);
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < m; j++) {
                for(int k = 0; k < p; k++) {
                    res.mat[i][j] += a.mat[i][k] * b.mat[k][j];
                    res.mat[i][j] %= mod;
                }
            }
        }
        return res;
    }
};

matrix qpow(matrix a, int b) {
    int n = a.n;
    matrix res(n, n);
    for(int i = 0; i < n; i++) {
        res.mat[i][i] = 1;
    }
    while(b) {
        if(b & 1) res = res * a;
        a = a * a;
        b >>= 1;
    }
    return res;
}

i32 main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n; cin >> n;
    matrix a(1, 2);
    a.mat = {{1, 1}};
    matrix b(2, 2);
    b.mat = {
        {0, 1},
        {1, 1}
    };
    matrix ans = qpow(b, n - 1);
    ans = a * ans;
    cout << ans.mat[0][0] << '\n';
    return 0;
}
```



# 龟速乘

 ```cpp
// int 要为 64 位
int mul(int a, int b, int mod) {
    int res = 0;
    while(b) {
        if(b & 1) res = (res + a) % mod;
        b >>= 1;
        a <<= 1;
        a %= mod;
    }
    return res;
}
 ```



#  线性逆元

```cpp
constexpr int N = 3e6+10;
int n, mod;
int inv[N];

void inv_init(){
    inv[1] = 1;
    for(int i = 2; i <= n; i++){
        inv[i] = (mod - mod / i) * inv[mod % i] % mod;
    }
}
```

# 辗转相除法/欧几里得

```cpp
int gcd(int a, int b) {
    return b == 0 ? a : gcd(b, a % b);
}
```

# 扩展欧几里得

```cpp
int exgcd(int a, int b, int & x, int & y) {
    if(b == 0) {
        x = 1, y = 0;
        return a;
    }
    int x1, y1, d;
    d = exgcd(b, a % b, x1, y1);
    x = y1, y = x1 - a / b * y1;
    return d;
}
```

```cpp
// ax + by = gcd(a, b)
int exgcd(int a, int b, int & x, int & y) {
    x = 1, y = 0;
    int x1 = 0, y1 = 1;
    int q;
    while (b) {
        q = a / b;
        tie(x, x1) = make_tuple(x1, x - q * x1);
        tie(y, y1) = make_tuple(y1, y - q * y1);
        tie(a, b)  = make_tuple(b, a - q * b);
    }
    return a;
}
```



# 中国剩余定理

```cpp
void solve() {
    int n; cin >> n;
    vector<int> a(n), b(n);
    for(int i = 0; i < n; i++) {
        cin >> a[i];
    }
    int M = 1;
    for(int i = 0; i < n; i++) {
        cin >> b[i];
        M *= b[i];
    }
    int ans = 0;
    for(int i = 0; i < n; i++) {
        int x, y;
        int m = M / b[i];
        exgcd(m, b[i], x, y);
        int inv_m = (x % M + M) % M;
        ans += mul(mul(a[i], m, M), inv_m, M);
        ans %= M;
    }
    cout << ans << '\n';
}
```

# 扩展中国剩余定理

```cpp
// 注意下面可能会溢出 r为余数; m为模数
int excrt(vector<pii> & rm) {
    auto & [r1, m1] = rm.front();
    int x, y;
    for(int i = 1; i < rm.size(); i++) {
        const auto & [r2, m2] = rm[i];
        int d = exgcd(m1, m2, x, y);
        assert((r2 - r1) % d == 0);
        x = (r2 - r1) / d * x;
        x = (x % (m2 / d) + (m2 / d)) % (m2 / d);
        r1 = m1 * x + r1;
        m1 = m1 / d * m2;
    }
    return (r1 % m1 + m1) % m1;
}
```



# 向上取整

```cpp
// 对负数要出问题
template<typename T> T iceil(T a, T b) {return (a + b - 1) / b;}
```

# 整数分块

$$
1^2 + 2^2 + 3^2 + 4^2+\dots + n^2 = 
$$






$$
1^3 + 2^3 + 3^3 + \cdots + n^3 = \displaystyle {\frac {(1 + n)\cdot n} {2}}
$$


![image-20241007230218729](C:/Users/18735/AppData/Roaming/Typora/typora-user-images/image-20241007230218729.png)
$$
\sum ^n _i { \lfloor \frac {n} {i} \rfloor }
$$


```cpp
#include <bits/stdc++.h>

using namespace std;
using i64 = int64_t;
using u64 = uint64_t;
using i128 = __int128_t;
constexpr int N = 1e7+3;

#define int long long
#define double long double

void solve() {
    int n, k; cin >> n >> k;
    int ans = n * k;
    for(int i = 1, j = 1; i <= n; i = j + 1) {
        if(i >= k) break;
        j = min(k / (k / i), n);
        ans -= ((i + j) * (j - i + 1) / 2) * (k / i);
    }
    cout << ans << '\n';
}

int32_t main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int T = 1; // cin >> T;
    while(T--) solve();
}
```



# 线性基

```cpp
// https://acm.hdu.edu.cn/showproblem.php?pid=3949
#include <bits/stdc++.h>

#define int int64_t

constexpr int N = 1e4 + 10;
int p[N];
int n;
bool zero = false;

void guass() {
    int k = 1;
    for(int i = 62; i >= 0; i--) {
        int j;
        for(j = k; j <= n; ++j) 
            if(p[j] >> i & 1) break;
        if(j > n) continue;
        std::swap(p[k], p[j]);
        for(j = 1; j <= n; ++j)
            if(k != j && p[j] >> i & 1) 
                p[j] ^= p[k];
        k++;
    }
    k--;
    if(k != n) zero = true;
    else       zero = false;
    n = k;
}

// 高斯消元法
int get_max() {
    int ans = 0;
    for(int i = 1; i <= n; i++) {
        ans ^= p[i];
    }
    return ans;
}

// 求第k小 - 需要先进行高斯消元
int query_xor(int k) {
    int ans = 0;
    if(zero) k--;
    if(!k) return 0;
    for(int i = n; i; --i) {
        if(k & 1) ans ^= p[i];
        k >>= 1; 
    }
    if(k) return -1;
    else  return ans;
}

int32_t main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);

    int T; std::cin >> T;
    for(int i = 1; i <= T; i++) {
        std::cin >> n;
        for(int j = 1; j <= n; j++) {
            std::cin >> p[j];
        }
        guass();
        int q; std::cin >> q;
        std::cout << std::format("Case #{}:\n", i);
        for(int j = 1; j <= q; j++) {
            int k; std::cin >> k;
            std::cout << query_xor(k) << '\n';
        }
    }
    return 0;
}
```

```cpp
using namespace std;
using i64 = int64_t;

constexpr int N = 64;
constexpr int INF = 1e18;

struct Basis{
    bool zero;
    int p[N] {};
    Basis() {zero = false;}
    void insert(int x) {
        for(int i = 62; ~i; i--) {
            if(x >> i & 1) {
                if(!p[i]) { p[i] = x; return;}
                x ^= p[i];
            }
        }
        zero = true;
    }
    int get_max() {
        int ans = 0;
        for(int i = 62; ~i; i--) {
            ans = max(ans, ans ^ p[i]);
        }
        return ans;
    }
    int get_min() {
        if(zero) return 0;
        int ans = INF;
        for(int i = 0; i <= 62; i++) {
            if(p[i]) return p[i];
        }
    }
    int kth(int k) {
        if(zero) k--;
        if(!k) return 0;
        for(int i = 1; i <= 62; i++) {
            for(int j = 0; j < i; j++) {
                if(p[i] & (1LL << j))
                    p[i] ^= p[j];
            }
        }
        int ans = 0;
        for(int i = 0; i <= 62; i++) {
            if(p[i]) {
                if(k & 1) ans ^= p[i];
                k >>= 1;
            }
        }
        if(k) return -1;
        else return ans;
    }
};
```

# 拓扑排序

```cpp
#include <bits/stdc++.h>

using namespace std;

using i32 = int32_t;
using i64 = int64_t;

int n, m;
constexpr int N = 1e5 + 10;
vector<int> edge[N], tp;
// 入度
int in[N];

bool bfs_toposort() {
    // 求字典序最小则将其换成优先队列
    queue<int> q;
    for(int i = 1; i <= n; i++) {
        if(in[i] == 0) 
            q.push(i);
    }
    while(!q.empty()) {
        int u = q.front(); q.pop();
        tp.emplace_back(u);
        for(auto & v : edge[u]) {
            if(--in[v] == 0) 
                q.push(v);
        }
    }
    return (tp.size() == n);
}

int vis[N];
bool dfs(int x) {
    // 染色处理
    //  0 没经过 
    // -1 正路过
    //  1 已经过
    vis[x] = -1;
    for(auto u : edge[x]) {
        if(vis[u] < 0) return false;
        else if(!vis[u]) {
            if(!dfs(u)) return false;
        }
    }
    vis[x] = 1;
    tp.emplace_back(x);
    return true;
}

bool dfs_toposort() {
    memset(vis, 0, sizeof(vis));
    for(int i = 1; i <= n; i++) {
        if(!vis[i]) 
            if(!dfs(i)) return false;     
    }
    reverse(tp.begin(), tp.end());
    return 1;
}

i32 main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n >> m;
    for(int i = 1; i <= m; i++) {
        int u, v; cin >> u >> v;
        edge[u].emplace_back(u, v);
        in[v]++;
    }
    // if(!toposort()) {
    //     cout << -1 << '\n';
    // }
    // else {
    //     for(auto x : tp) {
    //         cout << x << ' ';
    //     }
    // }
    return 0;
}

```

# 链式前向星

```cpp
// 链式前向星
const int N = 2e5 + 10;
const int M = 1e6 + 10;
int idx = 0; 
struct {
    int v, w, next;
} e[M];
int head[N];
void init() {memset(head, -1, sizeof(head));}
void add(int u, int v, int w = 0) {
    e[idx] = {v, w, head[u]};
    head[u] = idx++;
}
// for(int i = head[u]; ~i; i = e[i].next) {
// int v = e[i].v;
```



# 最小生成树

```cpp
// prim最小生成树
#include <bits/stdc++.h>

using namespace std;
using i32 = int32_t;
using i64 = int64_t;
using pii = pair<int, int>;

constexpr int N = 2E5 + 10;
constexpr int inf = 2E9;
vector<pii> edge[N];
int dist[N], vis[N];

// 最小生成树权值
int ans = 0;
// n 为点数, m为边数
int n, m;
bool prim(int s) {
    priority_queue<pii, vector<pii>, greater<pii>>q;
    fill(dist, dist + n + 1, inf);
    dist[s] = 0;
    q.push({dist[s], s});
    
    int cnt = 0;
    while(!q.empty()) {
        int u = q.top().second;
        q.pop();
        // u已出圈
        if(vis[u]) continue;
        // 标记出圈
        vis[u] = 1;
        ans += dist[u];
        if(dist[u] != inf) cnt++;
        for(auto [v, w] : edge[u]) {
            if(dist[v] > w) {
                dist[v] = w;
                q.push({w, v});
            }
        }
    }
    // 是否生成最小树
    return cnt == n;
}

i32 main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n >> m;
    for(int i = 1; i <= m; i++) {
        int u, v, w; cin >> u >> v >> w;
        edge[u].emplace_back(v, w);
        edge[v].emplace_back(u, w);
    }
    if(prim(1)) {
        cout << ans << '\n';
    }
    else {
        cout << "orz\n";
    }
    
    return 0;
}
```

```cpp
// kruskal最小生成树
#include <bits/stdc++.h>

using namespace std;
using i32 = int32_t;
using i64 = int64_t;
using pii = pair<int, int>;

constexpr int N = 2E5 + 10;
constexpr int inf = 2E9;
struct Edge{
    int u, v, w;
    friend bool operator<(Edge & a, Edge & b) {
        return a.w < b.w;
    }
}edge[N];

// 最小生成树权值
int ans = 0;
// n 为点数, m为边数
int n, m;

int fa[N];
int find(int x) {
    if(fa[x] == x) return x;
    else return fa[x] = find(fa[x]);
}

bool Kruskal() {
    int cnt = 0;
    sort(edge + 1, edge + m + 1);
    iota(fa, fa + n + 1, 0);
    for(int i = 1; i <= m; i++) {
        int x = find(edge[i].u);
        int y = find(edge[i].v);
        if(x != y) {
            fa[x] = y;
            ans += edge[i].w;
            cnt++;
        }
    }
    return cnt == n-1;
}

i32 main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n >> m;
    for(int i = 1; i <= m; i++) {
        int u, v, w; cin >> u >> v >> w;
        edge[i] = {u, v, w};
    }
    if(Kruskal()) {
        cout << ans << '\n';
    }
    else {
        cout << "orz\n";
    }
    
    return 0;
}
```

# 树的直径

## 1：两次DFS

```cpp
void enjoyerist() {
    int n; cin >> n;
    vector<vector<int>> edge(n + 1);
    for(int i = 1; i < n; i++) {
        int u, v; cin >> u >> v;
        edge[u].emplace_back(v);
        edge[v].emplace_back(u);
    }

    vector<int> dis(n + 1);
    auto dfs = [&](auto & dfs, int u, int father, int d) -> void {
        dis[u] = d;
        for(auto & v : edge[u]) {
            if(v == father) continue;
            dfs(dfs, v, u, d + 1);
        }
    };
    dfs(dfs, 1, 0, 0);
    int start = max_element(dis.begin() + 1, dis.end()) - dis.begin();
    dfs(dfs, start, 0, 0);
    int end = max_element(dis.begin() + 1, dis.end()) - dis.begin();
    int diameter = dis[end];
    cout << diameter << '\n';
}
```

## 2：树形DP

```cpp
void enjoyerist() {
    int n; cin >> n;
    vector<vector<int>> edge(n + 1);
    for(int i = 1; i < n; i++) {
        int u, v; cin >> u >> v;
        edge[u].emplace_back(v);
        edge[v].emplace_back(u);
    }

    vector<int> dp(n + 1);
    int diameter = 0;
    auto dfs = [&](auto & dfs, int u, int father) -> void {
        for(auto & v : edge[u]) {
            if(v == father) continue; 
            dfs(dfs, v, u);
            diameter = max(diameter, dp[v] + dp[u] + 1);
            dp[u] = max(dp[u], dp[v] + 1);
        }
    };
    dfs(dfs, 1, 0);
    cout << diameter << '\n';
}
```



# 树链剖分

```cpp
#include <bits/stdc++.h>

using namespace std;


// Heavy Light Decomposition(HLD) 重链剖分
struct HLD {
    int n, cnt, root;
    vector<vector<int>> edge;
    vector<int> fa, dep, son, siz, top, dfn, seq;
    
    HLD(int _n) : n(_n) {
        cnt = 0;
        fa.resize(n + 1);
        dep.resize(n + 1);
        son.resize(n + 1);
        siz.resize(n + 1);
        top.resize(n + 1);
        dfn.resize(n + 1);
        seq.resize(n + 1);
        edge.resize(n + 1);
    }

    void add_edge(int u, int v) {
        edge[u].emplace_back(v);
        edge[v].emplace_back(u);
    }

    void work(int _root = 1) {
        root = _root;
        dfs1(root, 0);
        dfs2(root, root);
    }

    void dfs1(int u, int father) {
        fa[u] = father;
        siz[u] = 1;
        dep[u] = dep[father] + 1;
        for(auto & v : edge[u]) {
            if(v == father) continue;
            dfs1(v, u);
            siz[u] += siz[v];
            if(siz[son[u]] < siz[v]) {
                son[u] = v;
            }
        }
    }

    void dfs2(int u, int t) {
        top[u] = t;
        dfn[u] = ++cnt;
        seq[cnt] = u;

        if(!son[u]) return;

        dfs2(son[u], t);

        for(auto & v : edge[u]) {
            if(v == fa[u] || v == son[u]) continue;
            dfs2(v, v);
        }
    }

    int lca(int u, int v) {
        while(top[u] != top[v]) {
            if(dep[top[u]] > dep[top[v]]) {
                u = fa[top[u]];
            }
            else {
                v = fa[top[v]];
            }
        }
        return dep[u] < dep[v] ? u : v;
    }

    int distance(int u, int v)  {
        return dep[u] + dep[v] - 2 * dep[lca(u, v)];
    }

    // 返回 u - v 路径跳转的重链(仅含头尾)的dfn序
    auto getPath(int u, int v) {
        vector<pair<int, int>> path;
        while (top[u] != top[v]) {
            if (dep[top[u]] > dep[top[v]]) {
                path.emplace_back(dfn[top[u]], dfn[u]);
                u = fa[top[u]];
            } else {
                path.emplace_back(dfn[top[v]], dfn[v]);
                v = fa[top[v]];
            }
        }
        if (dep[u] < dep[v]) {
            path.emplace_back(dfn[u], dfn[v]);
        } else {
            path.emplace_back(dfn[v], dfn[u]);
        }
        return move(path);
    }
};

int main() {
    
}
```



## 树链剖分模板题

```cpp
#pragma GCC optimize(3, "Ofast", "inline")

#include<bits/stdc++.h>
#define int long long

using namespace std;
using i32 = int32_t;

int mod;

// 带懒标记线段树
// 左闭右开
// 带懒标记线段树
// 左闭右开
template<class Info, class Tag> struct LazySegmentTree {
    int n;
    std::vector<Info> info;
    std::vector<Tag> tag;
    LazySegmentTree() : n(0) {}
    LazySegmentTree(int _n, Info _v = Info()) {
        init(_n, _v);
    }
    template<class T>
    LazySegmentTree(std::vector<T> _init) {
        init(_init);
    }
    void init(int _n, Info _v = Info()) {
        init(std::vector(_n + 1, _v));
    }

    template<class T>
    void init(std::vector<T> _init) {
        n = _init.size() + 1;
        info.assign(4 << std::__lg(n), Info());
        tag.assign(4 << std::__lg(n), Tag());
        auto build = [&](auto & build, int p, int l, int r) -> void {
            if (r - l == 1) {
                info[p] = _init[l];
                return;
            }
            int mid = (l + r) >> 1;
            build(build, p << 1, l, mid);
            build(build, p << 1 | 1, mid, r);
            pull(p);
        };
        build(build, 1, 1, n + 1);
    }
    void pull(int p) {
        info[p] = info[p << 1] + info[p << 1 | 1];
    }
    void apply(int p, const Tag &v) {
        info[p].apply(v);
        tag[p].apply(v);
    }
    void push(int p) {
        apply(p << 1, tag[p]);
        apply(p << 1 | 1, tag[p]);
        tag[p] = Tag();
    }
    void modify(int p, int l, int r, int x, const Info &v) {
        if (r - l == 1) {
            info[p] = v;
            return;
        }
        int mid = (l + r) >> 1;
        push(p);
        if (x < mid) {
            modify(p << 1, l, mid, x, v);
        } else {
            modify(p << 1 | 1, mid, r, x, v);
        }
        pull(p);
    }
    void modify(int p, const Info &v) {
        modify(1, 1, n + 1, p, v);
    }
    Info rangeQuery(int p, int l, int r, int x, int y) {
        if (l >= y || r <= x) {
            return Info();
        }
        if (l >= x && r <= y) {
            return info[p];
        }
        int mid = (l + r) >> 1;
        push(p);

        // 下面这样写是为了方便进行区间合并 
        if(mid <= x) return rangeQuery(p << 1 | 1, mid, r, x, y);
        if(mid >= y) return rangeQuery(p << 1, l, mid, x, y);
        return rangeQuery(p << 1, l, mid, x, y) + rangeQuery(p << 1 | 1, mid, r, x, y);
    }
    Info rangeQuery(int l, int r) {
        return rangeQuery(1, 1, n + 1, l, r);
    }
    void rangeApply(int p, int l, int r, int x, int y, const Tag &v) {
        if (l >= y || r <= x) {
            return;
        }
        if (l >= x && r <= y) {
            apply(p, v);
            return;
        }
        int mid = (l + r) >> 1;
        push(p);
        rangeApply(p << 1, l, mid, x, y, v);
        rangeApply(p << 1 | 1, mid, r, x, y, v);
        pull(p);
    }
    void rangeApply(int l, int r, const Tag &v) {
        return rangeApply(1, 1, n + 1, l, r, v);
    }
};


struct Tag {
    int add = 0;
    Tag(int _add = 0) : add(_add) {}
    void apply(const Tag &t) {
        add = (add + t.add) % mod;
    }
};
 
struct Info {
    int sum = 0;
    int cnt = 1;
    Info(int _sum = 0, int _cnt = 1) : sum(_sum), cnt(_cnt) {}
    void apply(const Tag &t) {
        sum = (sum + t.add * cnt) % mod;
    }
};
 
Info operator+(const Info & a, const Info & b) {
    return Info{(a.sum + b.sum) % mod, (a.cnt + b.cnt) % mod};
}


// Heavy Light Decomposition(HLD) 重链剖分
struct HLD {
    int n, cnt, root;
    vector<vector<int>> edge;
    vector<int> fa, dep, son, siz, top, dfn, seq;
    
    HLD(int _n) : n(_n) {
        cnt = 0;
        fa.resize(n + 1);
        dep.resize(n + 1);
        son.resize(n + 1);
        siz.resize(n + 1);
        top.resize(n + 1);
        dfn.resize(n + 1);
        seq.resize(n + 1);
        edge.resize(n + 1);
    }

    void add_edge(int u, int v) {
        edge[u].emplace_back(v);
        edge[v].emplace_back(u);
    }

    void work(int _root = 1) {
        root = _root;
        dfs1(root, 0);
        dfs2(root, root);
    }

    void dfs1(int u, int father) {
        fa[u] = father;
        siz[u] = 1;
        dep[u] = dep[father] + 1;
        for(auto & v : edge[u]) {
            if(v == father) continue;
            dfs1(v, u);
            siz[u] += siz[v];
            if(siz[son[u]] < siz[v]) {
                son[u] = v;
            }
        }
    }

    void dfs2(int u, int t) {
        top[u] = t;
        dfn[u] = ++cnt;
        seq[cnt] = u;

        if(!son[u]) return;

        dfs2(son[u], t);

        for(auto & v : edge[u]) {
            if(v == fa[u] || v == son[u]) continue;
            dfs2(v, v);
        }
    }

    int lca(int u, int v) {
        while(top[u] != top[v]) {
            if(dep[top[u]] > dep[top[v]]) {
                u = fa[top[u]];
            }
            else {
                v = fa[top[v]];
            }
        }
        return dep[u] < dep[v] ? u : v;
    }

    int distance(int u, int v)  {
        return dep[u] + dep[v] - 2 * dep[lca(u, v)];
    }

    // 返回 u - v 路径跳转的重链(仅含头尾)的dfn序
    auto getPath(int u, int v) {
        vector<pair<int, int>> path;
        while (top[u] != top[v]) {
            if (dep[top[u]] > dep[top[v]]) {
                path.emplace_back(dfn[top[u]], dfn[u]);
                u = fa[top[u]];
            } else {
                path.emplace_back(dfn[top[v]], dfn[v]);
                v = fa[top[v]];
            }
        }
        if (dep[u] < dep[v]) {
            path.emplace_back(dfn[u], dfn[v]);
        } else {
            path.emplace_back(dfn[v], dfn[u]);
        }
        return move(path);
    }
};


void enjoyerist() {
    int n, m, r;
    cin >> n >> m >> r >> mod;

    vector<int> a(n + 1);
    for(int i = 1; i <= n; i++) {
        cin >> a[i];
    }
    HLD hld(n);
    for(int i = 1; i < n; i++) {
        int u, v; cin >> u >> v;
        hld.add_edge(u, v);
    }

    hld.work(r);

    LazySegmentTree<Info, Tag> seg(n);
    for(int i = 1; i <= n; i++) {
        seg.modify(hld.dfn[i], Info(a[i], 1));
    }

    for(int i = 1; i <= m; i++) {
        int op; cin >> op;
        if(op == 1) {
            int u, v, z; cin >> u >> v >> z;
            auto path = hld.getPath(u, v);
            for(auto & [x, y] : path) {
                seg.rangeApply(x, y + 1, Tag(z));
            }
        }
        else if(op == 2) {
            int u, v; cin >> u >> v;
            int ans = 0;
            auto path = hld.getPath(u, v);
            for(auto & [x, y] : path) {
                ans += seg.rangeQuery(x, y + 1).sum;
            }
            cout << ans % mod << '\n';
        }
        else if(op == 3) {
            int x, z; cin >> x >> z;
            seg.rangeApply(hld.dfn[x], hld.dfn[x] + hld.siz[x], Tag(z));
        }
        else if(op == 4) {
            int x; cin >> x;
            cout << seg.rangeQuery(hld.dfn[x], hld.dfn[x] + hld.siz[x]).sum << '\n';
        }
    }
}

i32 main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
 
    i32 T = 1; // std::cin >> T;
    while(T--) enjoyerist();
    return 0;
}
```





# LCA

## 倍增

```cpp
// lca - 倍增法求取
#include <bits/stdc++.h>

using namespace std;
using i32 = int32_t;
using i64 = int64_t;

constexpr int N = 2e6 + 10;
vector<int> edge[N];
int fa[N][21], dep[N];

void dfs(int u, int father) {
    dep[u] = dep[father] + 1;
    fa[u][0] = father;
    for(int i = 1; i <= 20; i++) {
        fa[u][i] = fa[fa[u][i-1]][i-1];
    }
    for(auto v : edge[u]) {
        if(v != father) 
            dfs(v, u);
    }
}
int lca(int u, int v) {
    if(dep[u] < dep[v]) swap(u, v);
    for(int i = 20; i >= 0; i--) {
        if(dep[fa[u][i]] >= dep[v]) {
            u = fa[u][i];
        }
    }
    if(u == v) return u;
    for(int i = 20; i >= 0; i--) {
        if(fa[u][i] != fa[v][i]) {
            u = fa[u][i], v = fa[v][i];
        }
    }
    return fa[u][0];
}

i32 main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m, s; cin >> n >> m >> s;
    for(int i = 1; i < n; i++) {
        int x, y; cin >> x >> y;
        edge[x].emplace_back(y);
        edge[y].emplace_back(x);
    }
    dfs(s, 0);
    for(int i = 1; i <= m; i++) {
        int a, b; cin >> a >> b;
        cout << lca(a, b) << '\n';
    }
    return 0;
}
```

## 树链剖分

```cpp
// lca - 树链剖分求取
#include <bits/stdc++.h>

using namespace std;
using i32 = int32_t;
using i64 = int64_t;

constexpr int N = 2e6 + 10;
vector<int> edge[N];
int fa[N], dep[N];
int top[N], son[N], siz[N];

void dfs1(int u, int father) {
    fa[u] = father;
    dep[u] = dep[father] + 1;
    siz[u] = 1;
    for(auto v : edge[u]) {
        if(v == father) continue;
        dfs1(v, u);
        siz[u] += siz[v];
        if(siz[son[u]] < siz[v]) son[u] = v;
    }
}
// 剖分重链
void dfs2(int u, int t) {
    top[u] = t;
    if(!son[u]) return;
    // 重儿子
    dfs2(son[u], t);
    for(auto v : edge[u]) {
        if(v == fa[u] || v == son[u]) continue;
        // 轻儿子
        dfs2(v, v);
    }
}
int lca(int u, int v) {
    while(top[u] != top[v]) {
        if(dep[top[u]] < dep[top[v]]) swap(u, v);
        u = fa[top[u]];
    }
    return (dep[u] < dep[v] ? u : v);
}

i32 main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m, s; cin >> n >> m >> s;
    for(int i = 1; i < n; i++) {
        int x, y; cin >> x >> y;
        edge[x].emplace_back(y);
        edge[y].emplace_back(x);
    }
    dfs1(s, 0);
    dfs2(s, s);
    for(int i = 1; i <= m; i++) {
        int a, b; cin >> a >> b;
        cout << lca(a, b) << '\n';
    }
    return 0;
}
```

## tarjan

```CPP
// lca - tarjan
#include <bits/stdc++.h>

using namespace std;
using i32 = int32_t;
using i64 = int64_t;
using pii = pair<int, int>;

constexpr int N = 2e6 + 10;
vector<int> edge[N];
vector<pii> query[N];
int fa[N], ans[N];
bool vis[N];

// 并查集
int find(int u) {
    if(u == fa[u]) return u;
    else return fa[u] = find(fa[u]);
}

void tarjan(int u) {
    vis[u] = true;
    for(auto v : edge[u]) {
        if(!vis[v]) {
            tarjan(v);
            fa[v] = u;
        }
        
    }
    for(auto [v, i] : query[u]) {
        if(vis[v]) {
            ans[i] = find(v);
        }
    }
}
i32 main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n, m, s; cin >> n >> m >> s;
    for(int i = 1; i < n; i++) {
        int x, y; cin >> x >> y;
        edge[x].emplace_back(y);
        edge[y].emplace_back(x);
    }
    iota(fa, fa + n + 10, 0);
    for(int i = 1; i <= m; i++) {
        int a, b; cin >> a >> b;
        query[a].emplace_back(b, i);
        query[b].emplace_back(a, i);
    }
    tarjan(s);
    for(int i = 1; i <= m; i++) {
        cout << ans[i] << '\n';
    }
    return 0;
}
```

# 最短路

## floyd

```cpp
void enjoyerist() {
    int n, m; cin >> n >> m;
    vector<vector<int>> dis(n + 1, vector<int>(n + 1, inf));
    for(int i = 1; i <= n; i++) 
        dis[i][i] = 0;

    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            int u, v, w; cin >> u >> v >> w;
            dis[u][v] = min(dis[u][v], w);
            dis[v][u] = min(dis[v][u], w);
        }
    }

    for(int k = 1; k <= n; k++) {
        for(int i = 1; i <= n; i++) {
            for(int j = 1; j <= n; j++) {
                dis[i][j] = min(dis[i][j], dis[i][k] + dis[k][j]);
            }
        }
    }

    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= n; j++) {
            cout << dis[i][j] << ' ';
        }
        cout << '\n';
    }

}
```

## Dijkstra 

```cpp
#include<bits/stdc++.h>

using namespace std;
#define int long long
using i64 = long long;
using pii = pair<int, int>;
constexpr i64 inf = (1LL << 31) - 1LL;

int32_t main() {
    ios::sync_with_stdio(false), cin.tie(NULL);

    int n, m, s; cin >> n >> m >> s;
    
    vector<vector<pii>> edge(n+1);
    for(int i = 1; i <= m; i++) {
        int u, v, w; cin >> u >> v >> w;
        edge[u].push_back({v, w});
    }
    auto dijkstra = [&](int s) {
        vector<i64> dis(n+1, inf);
        dis[s] = 0;
        priority_queue<pii, vector<pii>, greater<pii>> pq;
        pq.push({0LL, s});
        while(!pq.empty()) {
            auto [d, u] = pq.top(); pq.pop();
            if(d > dis[u]) continue;
            for(auto & [v, w] : edge[u]) {
                if(dis[v] > dis[u] + w) {
                    dis[v] = dis[u] + w;
                    pq.push({dis[v], v});
                }
            }
        }
        return move(dis);
    };
    
    auto ans = dijkstra(s);
    for(int i = 1; i <= n; i++) {
        cout << ans[i] << " \n"[i == n];
    }
    return 0;
}
```

# 割边

```cpp
struct EDCC {
    int n, m, now;
    vector<vector<int>> edge;
    vector<pii> bridge;
    vector<int> dfn, low;

    EDCC(int n) : n(n), edge(n + 1), low(n + 1), dfn(n + 1) {
        m = now = 0;
    }
    void add(int u, int v) {
        m++;
        edge[u].emplace_back(v);
        edge[v].emplace_back(u);
    }
    void tarjan(int u, int father) {
        dfn[u] = low[u] = ++now;
        for(const auto & v : edge[u]) {
            if(!dfn[v]) {
                tarjan(v, u);
                low[u] = min(low[u], low[v]);
                if(low[v] > dfn[u]) {
                    bridge.emplace_back(min(u, v), max(u, v));
                }
            }
            else if(v != father) {
                low[u] = min(low[u], dfn[v]);
            }
        }
    }
    auto work() {
        // for (int i = 1; i <= n; i++) { // 避免图不连通
        //     if (!dfn[i]) {
        //         tarjan(i, 0);
        //     }
        // }
        tarjan(1, 0);
        sort(bridge.begin(), bridge.end());
        for(auto [u, v] : bridge) {
            cout << u << ' ' << v << '\n';
        }
    }
};

i32 main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    
    int n, m; cin >> n >> m;
    EDCC fuck(n);
    for(int i = 1; i <= m; i++) {
        int u, v; cin >> u >> v;
        fuck.add(u, v);
    }
    fuck.work();

    return 0;
}
```

# 割点

```cpp
struct EBCC {
    int n;
    vector<vector<int>> edge;
    vector<int> dfn, low, vis;
    int idx, cnt;
    
    EBCC() {}
    EBCC(int n) {
        this->n = n;
        cnt = 0;
        dfn.resize(n + 1);
        low.resize(n + 1);
        vis.resize(n + 1);
        edge.resize(n + 1);
    }

    void add_edge(int u, int v) {
        edge[u].emplace_back(v);
        edge[v].emplace_back(u);
    }

    void dfs(int u, int father) {
        int child = 0;
        dfn[u]  = low[u] = ++idx;
        for(auto & v : edge[u]) {
            if(!dfn[v]) {
                child++;
                dfs(v, u);
                low[u] = min(low[u], low[v]);
                if(u != father && low[v] >= dfn[u] && !vis[u]) {
                    vis[u] = 1;
                    cnt++;
                }
            }
            else if(v != father) {
                low[u] = min(low[u], dfn[v]);
            }
        }
        if(u == father && child >= 2 && !vis[u]) {
            vis[u] = 1;
            cnt++;
        }
    }

    // 给出的不一定是联通图
    void work() {
        for(int i = 1; i <= n; i++) {
            if(!dfn[i]) {
                idx = 0;
                dfs(i, i);
            }
        }
    }
};
```





# 拓展

## 三维偏序

```cpp
#pragma GCC optimize(3, "Ofast", "inline")

#include<bits/stdc++.h>
#define int long long
#define double long double
#define pii pair<int, int>
#define pdd pair<double, double>
#define lowbit(x) (x & -x)
#define all(x) x.begin(), x.end()
 
using namespace std;
using i32 = int32_t;
using i64 = long long;
using i128 = __int128_t;

constexpr i32 N = 1e3 + 10; 
constexpr i32 inf = 2e9; 
constexpr i64 INF = 1e18; 
constexpr i64 mod = 1e9 + 7; 
constexpr i32 dx[] = {-1, 1, 0, 0};
constexpr i32 dy[] = {0, 0, -1, 1};

inline int iceil(int a, int b) { return (a + b - 1) / b;}
int qpow(int a, int b) { int ans = 1; while(b) { if(b&1) ans *= a; b >>= 1; a *= a;} return ans;}


template <class T> class fenwick {
private: int n; vector<T> tree;
public:
    fenwick(int _n = 0):n(_n + 1), tree(_n + 1, 0) {}
    void add(int i, T d) {
        for( ; i < n; i += (i&-i)) tree[i] += d;
    }
    T ask(int i) {
        T sum = 0;
        for( ; i; i -= (i&-i)) sum += tree[i];
        return sum;
    }
    T ask(int l, int r) {
        return ask(r) - ask(l-1);
    }
    int kth(int k) {
        int sum = 0, x = 0;
        for(int i = 20; i >= 0; i--){
        	x += (1 << i);
        if(x >= n || sum + tree[x] >= k)
        	x -= (1 << i);
        else
        	sum += tree[x];
        }
   	 	return x + 1;
    }
};

struct tlp {
    int u, v, w;
    int cnt, res;
    friend bool operator==(tlp & a, tlp & b) {
        return (a.u == b.u && a.v == b.v && a.w == b.w);
    }
};


void enjoyerist() {
    int n, k; cin >> n >> k;
    
    vector<tlp> a(n + 1);
    for(int i = 1; i <= n; i++) {
        cin >> a[i].u >> a[i].v >> a[i].w;
        a[i].cnt = 1;
    }
    sort(a.begin() + 1, a.end(), [&](tlp & s1, tlp & s2) {
        if(s1.u != s2.u) return s1.u < s2.u;
        else if(s1.v != s2.v) return s1.v < s2.v;
        else return s1.w < s2.w;
    });

    int tot = 0;
    for(int i = 1; i <= n; i++) {
        if(a[tot] == a[i]) {
            a[tot].cnt++;
        }
        else {
            a[++tot] = a[i];
        }
    }

    fenwick<int> fw(k + 1);

    auto cdq = [&](auto && cdq, int l, int r) {
        if(l == r) return;
        int mid = l + r >> 1;
        cdq(cdq, l, mid);
        cdq(cdq, mid + 1, r);

        sort(a.begin() + l, a.begin() + mid + 1, [&](tlp & s1, tlp & s2) {
            if(s1.v != s2.v) return s1.v < s2.v;
            else return s1.w < s2.w;
        });
        sort(a.begin() + mid + 1, a.begin() + r + 1, [&](tlp & s1, tlp & s2) {
            if(s1.v != s2.v) return s1.v < s2.v;
            else return s1.w < s2.w;
        });

        int i = l, j = mid + 1;
        for( ; j <= r; j++) {
            for( ; i <= mid && a[i].v <= a[j].v; i++) {
                fw.add(a[i].w, a[i].cnt);
            }
            a[j].res += fw.ask(a[j].w);
        }
        for(j = l; j < i; j++) {
            fw.add(a[j].w, -a[j].cnt);
        }
    };

    cdq(cdq, 1, tot);

    vector<int> ans(n + 1);
    for(int i = 1; i <= tot; i++) {
        ans[a[i].cnt + a[i].res - 1] += a[i].cnt;
    }
    for(int i = 0; i < n; i++) {
        cout << ans[i] << '\n';
    }
}

i32 main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
 
    i32 T = 1;  // std::cin >> T;
    while(T--) enjoyerist();
    return 0;
}
```

