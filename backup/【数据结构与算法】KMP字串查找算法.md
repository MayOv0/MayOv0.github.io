## 目录

- [BF朴素算法查找子串](#bf朴素算法查找子串)
- [KMP算法](#kmp算法)
  - [部分匹配表的计算](#部分匹配表的计算)
    - [前缀，后缀，部分匹配值概念](#前缀后缀部分匹配值概念)
    - [编程上计算部分匹配值的思路](#编程上计算部分匹配值的思路)
    - [部分匹配值计算代码](#部分匹配值计算代码)
  - [KMP算法实现](#kmp算法实现)
  - [时间复杂度和空间复杂度](#时间复杂度和空间复杂度)

---

# BF朴素算法查找子串

<img alt="Image" src="https://wobushi0x00000000.oss-cn-shenzhen.aliyuncs.com/images/DataStructure/41_kmp_string_search/1.png" />

时间复杂度 `O(m*n)` 空间复杂度 `O(1)`

```cpp
int bf(const char* s, const char* t)
{
    int sl = strlen(s), tl = strlen(t);

    int i = 0, j = 0;
    while (i < sl && j < tl)
    {
        if (s[i] == t[j])
        {
            i++; j++;
        }
        else
        {
            i = i - j + 1;
            j = 0;
        }
    }

    if (j == tl)
    {
        return i - j;
    }
    else
    {
        return -1;
    }
}

int main()
{
    const char* p1 = "ABCDCABDEFG";
    const char* p2 = "ABD";

    int ret = bf(p1, p2);
    if (ret != -1)
    {
        cout << "找到了,下标为" << ret << endl;
    }
}

int sub_str_index(const char* s, const char* p)
{

    int ret = -1;
    int sl = strlen(s);
    int pl = strlen(p);
    int len = sl - pl;
    for (int i = 0; (ret < 0) && (i <= len); i++)
    {
        bool equal = true;
        for (int j = 0; equal && (j < pl); j++)
        equal = equal && (s[i + j] == p[j]);
        ret = (equal ? i : -1);
    }
    return ret;
}


```

<img alt="Image" src="https://wobushi0x00000000.oss-cn-shenzhen.aliyuncs.com/images/DataStructure/41_kmp_string_search/2.png" />
这种暴力解法在很多地方做了无用功

减少无用的对比就可以提高效率，如何做到移动合适的位置呢？

通过肉眼我们可以清楚的看出可以右移一定位数减少没必要的比对，但如何在程序中计算？
<img alt="Image" src="https://wobushi0x00000000.oss-cn-shenzhen.aliyuncs.com/images/DataStructure/41_kmp_string_search/3.png" />

# KMP算法

匹配失败时的右移位数与子串本身相关， 与目标串无关

移动位数＝己匹配的字符数－对应的部分匹配值

任意子串都存在—个唯—的部分匹配表

## 部分匹配表的计算
<img alt="Image" src="https://wobushi0x00000000.oss-cn-shenzhen.aliyuncs.com/images/DataStructure/41_kmp_string_search/4.png" />

### 前缀，后缀，部分匹配值概念

前缀：除了最后—个字符以外，—个字符串的全部头部组合

后缀：除了第—个字符以外，—个字符串的全部尾部组合

部分匹配值：前缀和后缀最长共有元素的长度
<img alt="Image" src="https://wobushi0x00000000.oss-cn-shenzhen.aliyuncs.com/images/DataStructure/41_kmp_string_search/5.png" />
<img alt="Image" src="https://wobushi0x00000000.oss-cn-shenzhen.aliyuncs.com/images/DataStructure/41_kmp_string_search/6.png" />

### 编程上计算部分匹配值的思路

**LL值即longest lenth（前缀，后缀交集元素的最大长度）**

**当前要求的LL值由历史LL值推导**

**当可选LL值为0，直接比对首尾元素**
<img alt="Image" src="https://wobushi0x00000000.oss-cn-shenzhen.aliyuncs.com/images/DataStructure/41_kmp_string_search/7.png" />

1 a一个字符自然不存在什么LL值，即为0

2 ab之前的部分的LL值为0，直接比对首尾值，`a!=b`，LL值为0

3 aba之前的部分的LL值为0，直接比对首尾值，`a==a`，LL值为0+1=1

4 abab之前的部分的LL值为1，`a==a`已经比对过了，接着比对`b==b`，LL值为1+1=2

5 ababa之前的部分的LL值为2,不再重复比对已经比对过的部分，接着比对`a==a`,LL值为2+1=3

6 ababax之前部分的LL值为3，不再重复比对已经比对过的部分，接着比对`a!=x`

尝试再扩展前后缀，以容纳该字符串后缀必包含的末尾字符，重合的部分aba的LL值为1，所以从下标为1的元素b尝试，`b!=x`，再次尝试，重合的部分为a，LL值为0，比对首尾`a!=x`，LL值为0

### 部分匹配值计算代码

```cpp
int* make_pmt(const char* p)
{
    int len = strlen(p);
    int* ret = static_cast<int*>(malloc(sizeof(int) * len));

    if (ret != nullptr)
    {
        int ll = 0;

        ret[0] = 0;

        for (int i = 1; i < len; i++)
        {
            while ((ll > 0) && (p[ll] != p[i]))
            {
                ll = ret[ll - 1];
            }

            if (p[ll] == p[i])
            {
                ll++;
            }
            ret[i] = ll;
        }
    }

    return ret;

}

```

## KMP算法实现
<img alt="Image" src="https://wobushi0x00000000.oss-cn-shenzhen.aliyuncs.com/images/DataStructure/41_kmp_string_search/8.png" />

这里还是有些难以理解，这里最后一个等式指j（下标值）等于移动前的j值-右移位数，然后通过这个等式推导出j=PMT[j-1]的结论

下面是实现代码

```cpp
int kmp(const char* s, const char* p)
{
    int ret = -1;
    int sl = strlen(s);
    int pl = strlen(p);
    int* pmt = make_pmt(p);

    if ((pmt != nullptr) && (0 < pl) && (pl <= sl))
    {
        for (int i = 0, j = 0; i < sl; i++)
        {
            while ((j > 0) && (s[i] != p[j]))
            {
                j = pmt[j - 1];
            }

            if (s[i] == p[j])
            {
                j++;
            }

            if (j == pl)
            {
                ret = i + 1 - pl;
                break;
            }
        }
    }

    free(pmt);

    return ret;
}

```

## 时间复杂度和空间复杂度

时间复杂度：`O(m+n)`

空间复杂度：`O(m)`
