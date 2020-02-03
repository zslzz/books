#kmp算法（https://zhuanlan.zhihu.com/p/83334559）（https://www.zhihu.com/question/21923021）
利用匹配失败后的信息，尽量减少模式串与主串的匹配次数以达到快速匹配的目的   
匹配字串采用状态机array 记录每次 
核心在于next数组 **next[j]就是第j个元素前j-1个元素首尾重合部分个数加一**    
**next[j]=Max(k | 1<k<j且'P1..Pk-1' = Pj-k+1....Pj-1 )  
 next[j]=0 j=1  
 next[j]=1 首尾重合数为0** 
```java
class Solution {
      static int[] getNext(String P) {
              int p_len = P.length();
              int i = 0;   // P 的下标
              int j = -1;//作为首尾重合个数的指针
              int next[]=new int[p_len];
              next[0] = -1;
      
              while (i < p_len-1) {
                  if (j == -1 || P.charAt(i) == P.charAt(j)) {
                      i++;
                      j++;
                      next[i] = j;
                  } else
                      j = next[j];//从前方已匹配的开始计算
              }
              return next;
          }
     public static int KMP(String ts, String ps) {
        char[] t = ts.toCharArray();
        char[] p = ps.toCharArray();
        int i = 0; // 主串的位置
        int j = 0; // 模式串的位置
        int[] next = getNext(ps);
        while (i < t.length && j < p.length) {
             if (j == -1 || t[i] == p[j]) { // 当j为-1时，要移动的是i，当然j也要归0
                  i++;
                  j++;
             } else {// i不需要回溯了
                      // i = i - j + 1;
             j = next[j]; // j回到指定位置
             }
        }
        if (j == p.length) {
             return i - j;
         } else {
             return -1;
         }
     }
}
```

