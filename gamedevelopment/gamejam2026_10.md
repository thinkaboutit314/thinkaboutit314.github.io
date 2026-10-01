### 主题：涌现
### 题目分析
- 涌现在游戏开发视角下的定义
  - 【拥有涌现系统的游戏一定带来涌现体验吗？【游戏罗马】】https://www.bilibili.com/video/BV1VkrSBAEH9?vd_source=6000f1b3bd794882baa372796705eae5
  - 少量核心玩法加上环境交互产生数种玩法
- 结构
  - 类似多叉树
  - 根节点、父节点和子节点
  - <img width="350" height="190" alt="ee46e7bc1cd7a09c8fe47d9e68b64d15" src="https://github.com/user-attachments/assets/ddf11152-bfb1-44f4-943d-6faf1bab4671" />


### 游戏举例
- 塞尔达传说：旷野之息
  - 【自学UE5一个月做的纯C++类塞尔达小游戏Demo-哔哩哔哩】 https://b23.tv/3ok7pLz
  - 【从《塞尔达传说：荒野之息》出发，解构涌现式游戏设计【游戏提灯#13】】https://www.bilibili.com/video/BV1GM4y1u793?vd_source=6000f1b3bd794882baa372796705eae5
- rougelike游戏（例如以撒的结合）

### 设计思路
- 游戏类型：rougelike，类塞游戏
- 核心玩法：根节点
    - 是什么
        - 核心机制（rougelike这一类游戏、以撒的结合里道具可以合并的机制）
        - 一种“能力”（旷野之息里的四个道具）
            - <img width="250" height="150" alt="07054ea607ab4cef2a4d304bce0385ee" src="https://github.com/user-attachments/assets/eb059dc9-e30a-4491-b060-758ecce08578" />
    - 与环境的交互
 
### 可能的实现方式
- 设计几个“根节点”的核心玩法
- 一、关卡闯关 二、塞尔达神庙（从0开始的生活）
