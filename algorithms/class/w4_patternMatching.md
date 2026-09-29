---
算法: 模式匹配 (Pattern Matching Algorithms)
date: 2026-09-29
tags: 
  - 字符串
  - 模式匹配
---

**模式匹配算法（Pattern Matching Algorithms）**是在一个较长的“主串”（长度为 $n$）中，寻找一个或多个特定的“模式串”（长度为 $m$）出现的位置。[C++代码实现.md](https://github.com/user-attachments/files/32795894/C%2B%2B.md)
[Uploading C++代码实现.md…]()
。

按照匹配机制和应用场景，主流的模式匹配算法主要分为两大类：**单模式匹配**与**多模式匹配**。

---

## 常用单模式匹配算法及 C++ 实现

这类算法用于在一个主串中查找**一个**特定的模式串。

### 1. BF 算法 (暴力匹配)
最基础的实现方式，双指针直接比对。

*   **核心思想**：从主串的第一个字符开始，与模式串逐个字符对比。如果遇到不匹配的字符，主串的指针回退（回溯）到上一次开始的下一个字符，模式串指针回到起点，重新开始比对。
*   **时间复杂度**：最好情况为 $O(n)$，最坏情况为 $O(n \times m)$。
*   **适用场景**：日常简单开发、数据量极小的场景。代码实现最简单，不易出错。

```cpp
#include <iostream>
#include <string>

using namespace std;

// 返回模式串在主串中首次出现的索引，未找到返回 -1
int bruteForce(const string& text, const string& pattern) {
    int n = text.length();
    int m = pattern.length();
    
    if (m == 0) return 0;
    
    for (int i = 0; i <= n - m; i++) {
        int j = 0;
        while (j < m && text[i + j] == pattern[j]) {
            j++;
        }
        // 如果 j 等于模式串长度，说明全部匹配成功
        if (j == m) {
            return i;
        }
    }
    return -1;
}
```

### 2. RK 算法 (Rabin-Karp)
利用滚动哈希进行匹配。为了防止溢出，通常会对一个大素数取模。
*   **核心思想**：引入了**哈希（Hash）**机制。它先计算模式串的哈希值，然后利用“滑动窗口”每次计算主串中长度为 $m$ 的子串的哈希值。如果哈希值不相等，说明字符串绝对不匹配；如果哈希值相等，再逐个字符对比以排除哈希冲突。
*   **时间复杂度**：平均情况为 $O(n+m)$，最坏情况（频繁发生哈希冲突）为 $O(n \times m)$。
*   **适用场景**：查重系统、简单的多模式匹配（通过对比多个哈希值）。

```cpp
#include <iostream>
#include <string>

using namespace std;

int rabinKarp(const string& text, const string& pattern) {
    int n = text.length();
    int m = pattern.length();
    if (m == 0) return 0;

    const int d = 256; // 字符集大小
    const int q = 101; // 一个素数，用于取模防止溢出
    
    int h = 1;
    for (int i = 0; i < m - 1; i++) {
        h = (h * d) % q;
    }

    int pHash = 0; // 模式串的哈希值
    int tHash = 0; // 主串当前窗口的哈希值

    // 计算模式串和主串第一个窗口的哈希值
    for (int i = 0; i < m; i++) {
        pHash = (d * pHash + pattern[i]) % q;
        tHash = (d * tHash + text[i]) % q;
    }

    for (int i = 0; i <= n - m; i++) {
        // 哈希值匹配时，还需要进行二次确认，排除哈希冲突
        if (pHash == tHash) {
            int j = 0;
            for (j = 0; j < m; j++) {
                if (text[i + j] != pattern[j]) break;
            }
            if (j == m) return i;
        }

        // 计算下一个窗口的哈希值 (滚动哈希)
        if (i < n - m) {
            tHash = (d * (tHash - text[i] * h) + text[i + m]) % q;
            // 如果哈希值为负数，转换为正数
            if (tHash < 0) tHash = (tHash + q);
        }
    }
    return -1;
}
```

### 3. KMP 算法

KMP 的核心在于求解 `next` 数组，遇到不匹配时主串指针 `i` 不回退，仅回退模式串指针 `j`。

*   **核心思想**：利用已经匹配过的信息，保证**主串指针永远不回溯**。它通过对模式串进行预处理，生成一个 `next` 数组（记录“最长公共前后缀”的长度）。当发生字符失配时，直接根据 `next` 数组将模式串向右滑动尽可能远的距离，避免无效对比。
*   **时间复杂度**：稳定在 $O(n+m)$。
*   **适用场景**：主串以“数据流”形式输入（无法回溯），或者主串和模式串具有大量重复字符的场景。

```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

// 构建 next 数组
vector<int> getNext(const string& pattern) {
    int m = pattern.length();
    vector<int> next(m, 0);
    int j = 0; // 前缀末尾位置，也代表最长公共前后缀长度
    
    for (int i = 1; i < m; i++) {
        // 当发生不匹配时，向前回溯寻找更短的相同前后缀
        while (j > 0 && pattern[i] != pattern[j]) {
            j = next[j - 1];
        }
        if (pattern[i] == pattern[j]) {
            j++;
        }
        next[i] = j;
    }
    return next;
}

int kmpSearch(const string& text, const string& pattern) {
    if (pattern.empty()) return 0;
    
    int n = text.length();
    int m = pattern.length();
    vector<int> next = getNext(pattern);
    
    int j = 0; // 模式串指针
    for (int i = 0; i < n; i++) { // 主串指针 i 永不回退
        while (j > 0 && text[i] != pattern[j]) {
            // 失配时，模式串指针根据 next 数组跳转
            j = next[j - 1];
        }
        if (text[i] == pattern[j]) {
            j++;
        }
        if (j == m) {
            return i - m + 1; // 匹配成功，返回起始位置
        }
    }
    return -1;
}
```

### 4. BM 算法 (坏字符规则简化版)

完整的 BM 算法包含“坏字符规则”和“好后缀规则”。由于“好后缀规则”代码极其冗长复杂，实际开发或面试中常仅使用“坏字符规则”进行实现，也能获得极大的性能提升。以下为基于坏字符规则的 BM 实现：

*   **核心思想**：不同于常规的从左向右比对，BM 算法选择**从模式串的尾部开始向前比对**。它结合了“坏字符规则（Bad Character）”和“好后缀规则（Good Suffix）”，在发生失配时，可以让模式串一次性向右跳跃多个字符。
*   **时间复杂度**：最好情况可以达到 $O(n/m)$，最坏情况为 $O(n+m)$。
*   **适用场景**：**实际工业应用中最快的单模式匹配算法**。绝大多数文本编辑器（如 Ctrl+F 查找）和 `grep` 命令的底层实现都基于 BM 或其变种（如 Sunday 算法）。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

// 记录模式串中每个字符最后出现的位置（坏字符规则字典表）
vector<int> getBadCharTable(const string& pattern) {
    const int SIZE = 256; // 假设为 ASCII 字符集
    vector<int> bc(SIZE, -1);
    for (int i = 0; i < pattern.length(); i++) {
        bc[(int)pattern[i]] = i; // 记录字符最后出现的下标
    }
    return bc;
}

int boyerMoore(const string& text, const string& pattern) {
    int n = text.length();
    int m = pattern.length();
    if (m == 0) return 0;

    vector<int> bc = getBadCharTable(pattern);
    
    int i = 0; // 主串滑动窗口的起始位置
    while (i <= n - m) {
        int j;
        // 从模式串末尾开始向前匹配
        for (j = m - 1; j >= 0; j--) {
            if (text[i + j] != pattern[j]) {
                break; // 发现坏字符
            }
        }
        
        if (j < 0) {
            return i; // 匹配成功
            // 如果要查找多个匹配项，这里可以将 i += m，不直接 return
        } else {
            // 坏字符在模式串中的位置为 j
            // 坏字符在 text 中的字符为 text[i+j]
            // bc[text[i+j]] 是坏字符在模式串中最右侧出现的位置
            // 将模式串向右滑动，使得这两个字符对齐
            int move = j - bc[(int)text[i + j]];
            // 保证 move 至少为 1（避免由于模式串中存在更靠右的坏字符导致模式串倒退）
            i += max(1, move);
        }
    }
    return -1;
}
```
---

## 多模式匹配算法

这类算法用于在一个主串中**同时**查找**多个**不同的模式串。

### 5. Trie 树 (字典树)

*   **核心思想**：将所有要查找的模式串构建成一棵树，主串在树上进行路径匹配。
*   **特点**：适合模式串集合固定，主串不断变化，且模式串有大量公共前缀的场景。

### 6. AC 自动机 (Aho-Corasick)

*   **核心思想**：可以理解为 **Trie 树 + KMP 算法**的结合体。它在 Trie 树的基础上增加了“失败指针（Fail Pointer）”。当在某条路径上匹配失败时，可以直接通过失败指针跳转到另一条可能匹配的路径，无需主串回溯。
*   **时间复杂度**：$O(n + m + z)$ （其中 $z$ 为匹配到的模式串数量）。
*   **适用场景**：敏感词过滤系统、杀毒软件特征码扫描、网络入侵检测系统（IDS）。

---

## 核心算法对比总结

| 算法 | 平均时间复杂度 | 最坏时间复杂度 | 核心策略 | 实际效率表现 |
| :--- | :--- | :--- | :--- | :--- |
| **BF** | $O(n \times m)$ | $O(n \times m)$ | 穷举比对，指针双回溯 | 最慢，仅做教学或极小数据使用 |
| **RK** | $O(n+m)$ | $O(n \times m)$ | 滚动哈希，数值比对 | 中等，性能由哈希函数设计决定 |
| **KMP** | $O(n+m)$ | $O(n+m)$ | 寻找最长公共前后缀，主串不回退 | 稳定且快，但常数项略大 |
| **BM** | $O(n/m)$ | $O(n+m)$ | 从后向前比对，利用坏字符/好后缀大幅跳跃 | **极快**，模式串越长跳得越远 |
