# Search 大话搜索

- [搜索简介](#搜索简介)
- [广度优先遍历-BFS](#广度优先遍历-BFS)
- [BFS双向搜索](#BFS双向搜索)
- [深度优先遍历-DFS](#深度优先遍历-DFS)
- [DFS双向搜索](#DFS双向搜索)
- [BackTrack回溯](#BackTrack-回溯)

## 搜索简介
搜索一般指在**有限**的状态空间中进行枚举，通过**穷尽所有的可能**来找到符合条件的解或者解的个数。  
实际上搜索题目本质就是将题目中的**状态映射为图中的点**，将**状态间的联系映射为图中的边**。  
根据题目信息构建状态空间，然后对状态空间进行遍历，遍历过程需要记录和维护状态，并通过剪枝和数据结构等提高搜索效率。

状态空间可以是线性和非线性的，状态空间的数据结构不一定跟input一样。

### 状态空间
状态空间其实是一个图结构。节点 => 状态；边 => 状态之间的联系（从题目中给出的）
构建状态空间 => 构建图。

### 搜索树
BFS和DFS都是对状态图遍历的方式，只不过一个是广度优先，一个是深度优先。    
本质上，对图进行遍历会生成一颗**搜索树**。为了避免重复访问，我们需要**记录已经访问过的节点**（防止环的产生）。（这是搜索算法共有的)。   
如果是在树中遍历，因为树的定义是无环图，所以不用记录‘已经访问过的节点’来避免重复访问



-----
## 广度优先遍历 BFS
我们不断从队头取出状态，然后将此状态对应的决策产生的所有新的状态（对于BFS来说是状态空间中的当前点的邻边）推入队尾，重复以上过程直至队列为空即可。 
队列有： 单调性和二值性 
**单调性**指队列的层数（也就是到起始点的距离 是单调递增的)  
**二值性**是指queue里最多有两层的值
所以BFS适合求最短距离  
求最短距离：记录dist，也可以防止环的产生。第一次到达一个点后再次到达此点的距离一定比第一次到达大。

**注意比较**： 比较BFS和DFS的不同，因为BFS每次遍历的点都是距离起始点同样距离的点，然后每次这个距离++，所以BFS适合求最短路径这样的问题，而DFS则更适合有递推关系的。

DFS 通常都是有递推关系的，而递归关系就是图的边。根据递归关系大家可以选择使用前序遍历或者后序遍历。

BFS 由于其单调性，因此适合求解最短距离问题。

双向搜索的本质是将复杂度的常数项从一个影响较大的位置（比如指数位）移到了影响较小的位置（比如系数位）。

回溯的本质就是暴力枚举所有可能。要注意的是，由于回溯通常结果集都记录在回溯树的路径上，因此如果不进行撤销操作， 则可能在回溯后状态不正确导致结果有差异， 因此需要在递归到底部往上冒泡的时候进行撤销状态。回溯我建议大家画决策图图进行学习。

**BFS 的核心在于求最短问题时候可以提前终止。**  
层次遍历是一种不需要提前终止的 BFS 的副产物。
BFS 也不是树独有的。  
BFS 使用的是 queue， queue 是 FIFO

```JavaScript
const visited = {}
function bfs() {
 let q = new Queue()
 q.push(初始状态)
 while(q.length) {
  let i = q.pop()
  if (visited[i]) continue
  for (i的可抵达状态j) {
   if (j 合法) {
    q.push(j)
   }
  }
 }
 // 找到所有合法解
}
```
#### 复杂度

时间复杂度：O(n + m)
n 是点数, m 是边数
空间复杂度：O(n)

### BFS双向搜索  
#### 为什么要用双向搜索
为了解决搜索空间爆炸问题；朴素的BFS中空间的瓶颈主要取决于搜索空间的最大宽度。  
换句话说，搜索树越来越大，队列中的节点随之增多；这种增长通常是指数级别的。`(a ^ x)`  现在可以将指数的常系数移动到多项式系数。
如果起点到终点只有一条路径，双向搜索并不会加快。

#### 什么时候能用双向搜索
状态空间的边是双向的

**双向搜索基本思路**
1. 创建**两个队列**分别用于两个方向的搜索
2. 创建**两个哈希表**用于解决**相同节点重复搜索**和**记录转换次数**
3. 为了尽可能让两个搜索方向‘平均’：每次从队列中取值进行扩展时，先判断哪个队列容量较少
4. 如果在搜索过程中**搜到对方搜过的节点**(也就是另一个记录路径的哈希表里有当前点），则找到了最短路径。

例题： [LC 127 Word Ladder II](https://github.com/lilyzhaoyilu/LeetCode-Notes/blob/master/Basic200II/BFS%20%26%20DFS/LC127.%20Word%20Ladder.md)
```
// 只有两个队列都不空，才有必要继续往下搜索
// 如果其中一个队列空了，说明从某个方向搜到底都搜不到该方向的目标节点
// memo {word => steps from origin}
while(!queue1.isEmpty() && !queue2.isEmpty()) {
    if (queue1.size() < queue2.size()) {
        update(queue1, memo1, memo2);
    } else {
        update(queue2, memo2, memo1);
    }

    
}

var update = function(curQueue, curMemo, theOtherMemo){
   const curWord = curQueue.shift()

   search all possible next Word from curWord
   1. if curMemo has it, continue
   2. if otherMemo has it, return curMemo.get(curWord) + otherMemo.get(nextWord) + 1
   3. else, push the possible next step into queue & record it into the curMemo
                  curMemo(nextWord, curMemo.get(curWord) + 1)
  
}
```

**保存笛卡尔积**  
127. 单词接龙
140. 单词拆分 II
816. 模糊坐标    


-----

## 深度优先遍历 DFS

### 基本流程 & 模板
1. 跟节点入stack
2. stack.pop() 取出节点： 1)如果是目标，结束搜索并回传结果； 2)如果不是目标，将它**尚未检验过的子节点**加入stack.
3. 重复 2 
4. 如果不存在尚未检测过的子节点，将上一级节点加入stack中；重复2
5. 重复4
6. 若stack为空，表示整张图都检测过了。

```JavaScript
const visited = {}
var dfs = function(i){
   if(visited[i] || isTheResult){
      return
   }

   visited[i] = true
   for(根据i能到达的下个状态j || nei of graph[i]){
      if(!visited[nei]){
         dfs(nei)
      }
   }
}

```

### 遍历顺序
那么如果搜索过程中，当前点的结果需要依赖其他节点（大多数情况都会有依赖），那么遍历顺序就变得重要。

1. **前序**  
   如果当前节点需要依赖其父节点信息  
   比如：求树的深度，递归公式是 f(x) = 深度  f(儿子) = f(爸) + 1； f的信息依赖父节点，所以前序遍历更方便。 

2. **后序**  
   如果当前节点需要依赖其子节点计算信息，自底向上。  
   比如：求子节点的个数，f(x) = sum [f(儿子) f(儿子2)...f(最后一个儿子)] ； f的信息依赖于儿子，所以后序遍历更方便。

### 深度优先遍历技巧

#### 迭代加深（带参，剪枝）


#### DFS双向搜索
   有时候问题规模很大，直接搜索会超时。此时可以考虑从起点搜索到问题规模的一半。然后将此过程中产生的状态存起来。接下来目标转化为在存储的中间状态中寻找满足条件的状态。进而达到降低时间复杂度的效果。该算法，本质上是将位于指数位的常数项挪动到了系数位。
   比如

   [LC 1755. Closest Subsequence Sum ](https://leetcode-cn.com/problems/closest-subsequence-sum/)的时间复杂度是O(2^N)，如果压缩的话就是 O(m * 2^ m) 其中m是 n/2 
   16.最接近的三数之和  
   1049.最后一块石头的重量 II  
   1774.最接近目标价格的甜点成本

#### BackTrack 回溯
回溯的空间复杂度：栈的最大深度，也就是组合中最长的那一项的长度 （比如O(N))  
时间复杂度：  
|   |排列树      |子集树    | 可重复选择树 |
| ------------- | ------------- |------------- | ------------- |
|   |N!          |a ^n  | n ^ n  |
| 时间复杂度  | O(p(n) * n!)  |O(p(n) * 2n)  | O(p(n) * n^n) |


其中，p(n)表示生成一个节点所需的时间复杂度

-----




[双向搜索宫水三叶](https://mp.weixin.qq.com/s/CsAx6FydjW4U0KFafVwb1Q)  
[91大话搜索](https://leetcode-solution.cn/solutionDetail?type=2&id=3003&max_id=3008)
