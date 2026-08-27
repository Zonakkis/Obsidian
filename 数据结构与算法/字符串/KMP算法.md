---
时间复杂度: O(n+m)
空间复杂度: O(n)
---
# 定义
---
>[!note] KMP算法
>KMP算法，即Knuth–Morris–Pratt算法，利用[[前缀函数]]高效搜索字符串中的模式。
# 过程
---
定义`i`, `j`两个指针，`i`用于匹配字符串，`j`用于匹配模式，初始时`i = 0, `j = 0`。

`pattern[i] == pattern[j]`时，匹配成功，`j++`。

`pattern[i] != pattern[j]`时，匹配失败，若`j != 0`（即存在已经匹配的前缀`pattern[0...j - 1]`），此时有`pattern[0...j - 1] == str[i - j...i - 1]`。
此时`str[i - j...i - 1]`（也就是`pattern[0...j - 1]`）无法作为`pattern[i]`和`str[j]`的共同前缀，因此需要在`pattern[0...j - 1]`中找到一个最长真前缀（这个前缀同时也是`pattern[i - j...i - 1]`（也就是`pattern[0...j - 1]`）的最长真后缀）使得`pattern[i] == str[j]`，即需要找到`pattern[0...j - 1]`的最长真前缀真后缀，所以令`j = lsp[j - 1]`，若依然`pattern[i] != str[j]`，重复上述操作，直至匹配成功（`j++`）或`j == 0`。

若`j == 模式长度`，匹配完成，若要继续搜索，令``j = lsp[j - 1]``（即模式本身的最大真前缀真后缀作为已匹配的前缀）即可。
# 实现
---
```c++
int N, lps[N];  \\ lps[i]默认为0

void LPS(string pattern)  
{  
    for (int i = 1, j = 0; i < pattern.size(); i++)  
    {  
       while (j > 0 && pattern[i] != pattern[j])  
          j = lps[j - 1];  
       if (pattern[i] == pattern[j])  
          lps[i] = ++j;  
    }  
}  
  
void KMP(string str, string pattern)  
{  
    LPS(pattern);  
    for (int i = 0, j = 0; i < str.size(); i++)  
    {  
       while (j > 0 && str[i] != pattern[j])  
          j = lps[j - 1];  
       if (str[i] == pattern[j])  
          j++;  
       if (j == pattern.size())  
       {  
          cout << i - pattern.size() + 2 << endl;  
          j = lps[j - 1];  
       }  
    }  
}
```