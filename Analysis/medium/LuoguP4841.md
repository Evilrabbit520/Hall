---
title: LuoguP4841「国家集训队2013」题解
date: 2019-10-01 17:13:34
tags: [Solutions,多项式相关,卷积相关,数学/数论,组合数学,图论]
mathjax: true
---

> NTT+生成函数+多项式求逆

<!--more-->

### Link

[LuoguP4841](https://www.luogu.org/problem/P4841)
[BZOJ3456](http://darkbzoj.tk/problem/3456)

### Description
刚刚解决完电力网络的问题, 阿狸又被领导的任务给难住了.  

刚才说过, 阿狸的国家有n个城市, 现在国家需要在某些城市对之间建立一些贸易路线, 使得整个国家的任意两个城市都直接或间接的连通. 为了省钱, 每两个城市之间最多只能有一条直接的贸易路径. 对于两个建立路线的方案, 如果存在一个城市对, 在两个方案中是否建立路线不一样, 那么这两个方案就是不同的, 否则就是相同的. 现在你需要求出一共有多少不同的方案.  

好了, 这就是困扰阿狸的问题. 换句话说, 你需要求出n个点的简单(无重边无自环)无向连通图数目.  

由于这个数字可能非常大, 你只需要输出方案数mod 1004535809(479 * 2 ^ 21 + 1)即可.

### Samples
#### Input #1
```markdown
3
```
#### Output #1
```markdown
4
```

### Solution

> 约定:这里$\Gamma$表示阶乘函数,即$\Gamma(x) = x!$

---

首先我们设$f_i$为$i$个点的简单无向连通图数目,$g_i$为$i$个点的无向图数目.

首先我们可以得到$g_n = 2^{\binom {n}{2}}$, 因为图上至多有$\binom {n}{2}$条边,每一条边分别有选和不选两种情况.

然后我们考虑把$f$和$g$联系起来,考虑枚举1号点所在的连通块的大小,可得:

$$g_n = \sum_{i=1}^n \binom {n-1}{i-1} f_ig_{n-i}$$

把组合数按照定义式展开:

$$g_n = \Gamma(n-1) \sum_{i=1}^n \frac{f_i}{\Gamma(i-1)} \times \frac{g_{n-i}}{\Gamma(n-i)}$$

两边同时除以$\Gamma(n-1)$可得:

$$\frac {g_n}{\Gamma(n-1)}  = \sum_{i=1}^n \frac{f_i}{\Gamma(i-1)} \times \frac{g_{n-i}}{\Gamma(n-i)}$$

显然等式右面是一个卷积形式,考虑用生成函数$\rm OGF$表示一下:

$$F(x) = \sum_{i=1}^{\infty} \frac{g_n}{\Gamma(i-1)} x^i$$

$$G(x) = \sum_{i=0}^{\infty} \frac{g_n}{\Gamma(i)} x^i$$

$$H(x) = \sum_{i=1}^{\infty} \frac{f_i}{\Gamma(i-1)} x^i$$

根据上面的式子$F = G \times H$,即$H = \frac{F}{G}$, 那么多项式求逆即可.

### Code
```c++
/* Headers */
#include<cstdio>
#include<cstring>
#include<cmath>
#include<cctype>
#include<algorithm>
#include<vector>
#include<queue>
#include<stack>
#include<climits>
#include<iostream>
#include<map>
#define FOR(i,a,b,c) for(int i=(a);i<=(b);i+=(c))
#define ROF(i,a,b,c) for(int i=(a);i>=(b);i-=(c))
#define FORL(i,a,b,c) for(long long i=(a);i<=(b);i+=(c))
#define ROFL(i,a,b,c) for(long long i=(a);i>=(b);i-=(c))
#define FORR(i,a,b,c) for(register int i=(a);i<=(b);i+=(c))
#define ROFR(i,a,b,c) for(register int i=(a);i>=(b);i-=(c))
#define lowbit(x) x&(-x)
#define LeftChild(x) x<<1
#define RightChild(x) (x<<1)+1
#define RevEdge(x) x^1
#define FILE_IN(x) freopen(x,"r",stdin);
#define FILE_OUT(x) freopen(x,"w",stdout);
#define CLOSE_IN() fclose(stdin);
#define CLOSE_OUT() fclose(stdout);
#define IOS(x) std::ios::sync_with_stdio(x)
#define Dividing() printf("-----------------------------------\n");
namespace FastIO{
    const int BUFSIZE = 1 << 20;
    char ibuf[BUFSIZE],*is = ibuf,*its = ibuf;
    char obuf[BUFSIZE],*os = obuf,*ot = obuf + BUFSIZE;
    inline char getch(){
        if(is == its)
            its = (is = ibuf)+fread(ibuf,1,BUFSIZE,stdin);
        return (is == its)?EOF:*is++;
    }
    inline int getint(){
        int res = 0,neg = 0,ch = getch();
        while(!(isdigit(ch) || ch == '-') && ch != EOF)
            ch = getch();
        if(ch == '-'){
            neg = 1;ch = getch();
        }
        while(isdigit(ch)){
            res = (res << 3) + (res << 1)+ (ch - '0');
            ch = getch();
        }
        return neg?-res:res;
    }
    inline void flush(){
        fwrite(obuf,1,os - obuf,stdout);
        os = obuf;
    }
    inline void putch(char ch){
        *os++ = ch;
        if(os == ot)	flush();
    }
    inline void putint(int res){
        static char q[10];
        if(res == 0) putch('0');
        else if(res < 0){putch('-');res = -res;}
        int top = 0;
        while(res){
            q[top++] = res % 10 + '0';
            res /= 10;
        }
        while(top--)	putch(q[top]);
    }
    inline void space(bool x){
    	if(!x) putch('\n');
    	else putch(' ');
    }
}
inline void read(int &x){
    int rt = FastIO::getint();
    x = rt;
}
inline void print(int x,bool enter){
    FastIO::putint(x);
    FastIO::flush();
    FastIO::space(enter);
}
/* definitions */
#define int long long
const int MAXN = 2e6 + 10;
const int mod = 1004535809;
int f[MAXN], g[MAXN], h[MAXN], c[MAXN];
int rev[MAXN], n, m, fac[MAXN], inv[MAXN];  
/* functions */
inline int quick_power(int a, int b) {
	int res = 1, base = a;
	while(b) {
		if(b & 1) res = 1ll * res * base % mod;
		b >>= 1; base = 1ll * base * base % mod;
	}
	return res;
}
inline void NTT(int *F, int n, int type) {
	for(int i = 0; i < n; ++i) if(i < rev[i]) std::swap(F[i], F[rev[i]]);
	for(int i = 1; i < n; i <<= 1) {
		int tk = quick_power(3, (mod - 1) / (i << 1));
		for(int j = 0; j < n; j += (i << 1)) {
			int t = 1, x, y;
			for(int k = 0; k < i; ++k, t = 1ll * t * tk % mod) {
				x = F[j + k], y = 1ll * t * F[j + k + i] % mod;
				F[j + k] = (x + y) % mod; F[j + k + i] = (x - y + mod) % mod;
			}
		}
	}
	if(type == 1) return ;
	int inv = quick_power(n, mod - 2);
	std::reverse(F + 1, F + n);
	for(int i = 0; i < n; ++i) F[i] = 1ll * F[i] * inv % mod;
}
inline void PolyInv(int *f, int *g, int len) {
	if(len == 1) {g[0] = quick_power(f[0], mod - 2); return ;}
	PolyInv(f, g, (len + 1) >> 1);
	register int k = 1, m = 0;
	while(k < (len << 1)) {k <<= 1; ++m;}
	for(int i = 1; i < k; ++i) rev[i] = (rev[i >> 1] >> 1) | ((i & 1) << (m - 1));
	for(int i = 0; i < len; ++i) c[i] = f[i];
	for(int i = len; i < k; ++i) c[i] = 0;
	NTT(c, k, 1); NTT(g, k, 1);
	for(int i = 0; i < k; ++i) g[i] = 1ll * (2 - 1ll * c[i] * g[i] % mod + mod) % mod * g[i] % mod;
	NTT(g, k, -1);
	for(int i = len; i < k; ++i) g[i] = 0;
}
inline PolyMul(int *f, int *g, int len) {
	NTT(f, len, 1); NTT(g, len, 1);
	for(int i = 0; i < len; ++i) g[i] = 1ll * f[i] * g[i] % mod;
	NTT(g, len, -1);
}
inline void init() {
	fac[0] = 1;
	for(int i = 1; i <= n; ++i) fac[i] = fac[i - 1] * i % mod;
	inv[n] = quick_power(fac[n], mod - 2);
	for(int i = n - 1; i >= 0; --i) inv[i] = inv[i + 1] * (i + 1) % mod;
	for(int i = 0; i <= n; ++i)  g[i] = quick_power(2, (i * (i - 1) / 2)) * inv[i] % mod;
}
signed main(void){
	scanf("%d",&n);
	init(); int len = 1; while(len <= n) {len <<= 1;}
	PolyInv(g, h, len);
	for(int i = 1; i <= n; ++i) f[i] = quick_power(2, (i * (i - 1) / 2)) * inv[i - 1] % mod;
	len = 1; while(len <= (n << 1)) {len <<= 1;}
	PolyMul(h,f,len);
	printf("%lld\n", (f[n] * fac[n - 1]) % mod);
	return 0;
}
```

> 还有,以后的题解格式将会依照这篇文章的格式/cy

# THE END

