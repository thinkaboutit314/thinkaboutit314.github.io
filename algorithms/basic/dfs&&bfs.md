# DFS 回溯算法
## 1. 核心思想
DFS 的本质是利用程序调用栈，对状态空间树进行彻底的遍历，可以类比成一个**执着**的人。在处理带有特定约束的组合问题时，回溯与剪枝是降低时间复杂度的核心。
<img width="734" height="509" alt="F638A53C0A374F8C1790343038" src="https://github.com/user-attachments/assets/6ee51de3-64d9-4641-ad5e-1293181e67ad" />

## 2.例题：整数加法分解 (非递增约束)
**题目描述**：将正整数 N (N <= 20) 分解为多个正整数之和，要求分解的序列非递增，且包含双重排序约束。

## 3. C++ 实现
```cpp
// 注意边界检查
void dfs(int rem, int start, vector<int>& path) {
    if (rem == 0) {
        printPath(path);
        return;
    }
    // 剪枝：i 的上限取 start 和 rem 中的较小值，强制保证非递增
    for (int i = min(start, rem); i >= 1; i--) {
        path.push_back(i);
        dfs(rem - i, i, path);
        path.pop_back(); // 回溯，恢复现场
    }
}
```mermaid
mindmap
  root((DFS 回溯算法))
    核心要素
      程序调用栈与递归
      状态转移路径 (Path)
      边界终止条件
    实战：整数分解
      题目：将正整数 N 进行加法分解
      要求 1：双重排序约束
      要求 2：序列非递增
    剪枝逻辑 (Pruning)
      当前层选择上限
      ::icon(fa fa-scissors)
      受到上一层起点限制 (i <= start)
      受到剩余总量限制 (i <= rem)
      最终合并：i = min(start, rem)
    心智模型调整
      易错：深陷栈帧追踪的认知割裂
      解法：建立全局黑盒信任
      专注构造当前层的输入输出
