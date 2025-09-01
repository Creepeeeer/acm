## **z数组定义：**

**z[i]表示从i位置开始与原字符串的最长前缀，显然的z[0]=整个字符串长度**

和manacher几乎同理

### 流程：

**当来到出发点i**

**1.i没有被r包住，那么以i为出发点直接扩展**

**2.i被r包住，前点i-c的扩展范围在大拓展范围之内，那么z[i]=z[i-c]**

**3.i被r包住，前点i-c的扩展范围在大拓展范围之外，那么z[i]=r-i**

**4.i被r包住，前点i-c的扩展范围刚好在边界上，那么从r位置接着往右扩展**

```c++
const int N=1e5;
int z[N+1];
void get_zarray(string s){
    int n=s.size();
    z[0]=n;
    for(int i=1,c=1,r=1,len;i<n;i++){
        len=r>c?min(z[i-c],r-i):0;
        while(i+len<n&&s[i+len]==s[len])len++;
        if(i+len>r){
            r=i+len;
            c=i;
        }
        z[i]=len;
    }
}
```



## e数组定义：

有两个字符串a,b

**e[i]表示a从i出发，b从0出发的最长公共前缀**

### 流程：

先求出b字符串的z数组

其他的把对称点

```c++
const int N=1e5;
int z[N+1],e[N+1];
void get_earray(string a,string b){
    get_zarray(b);
    int n=a.size(),m=b.size();
    for(int i=0,r=0,c=0,len;i<n;i++){
        len=r>i?min(r-i,z[i-c]):0;
        while(i+len<n&&len<m&&a[i+len]==b[len])len++;
        if(i+len>r){
            r=i+len;
            c=i;
        }
        e[i]=len;
    }
}
```

