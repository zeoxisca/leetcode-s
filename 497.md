# Random Point in Non-overlapping Rectangles
给定一个非重叠轴对齐矩形的列表 rects，写一个函数 pick 随机均匀地选取矩形覆盖的空间中的整数点。

提示：

> 整数点是具有整数坐标的点。
> 矩形周边上的点包含在矩形覆盖的空间中。
> 第 i 个矩形 rects [i] = [x1，y1，x2，y2]，其中 [x1，y1] 是左下角的整数坐标，[x2，y2] 是右上角的整数坐标。
> 每个矩形的长度和宽度不超过 2000。
> 1 <= rects.length <= 100
> pick 以整数坐标数组 [p_x, p_y] 的形式返回一个点。
> pick 最多被调用10000次。

示例 1：

> 输入: 
> 
> ["Solution","pick","pick","pick"]
> 
> [[[[1,1,5,5]]],[],[],[]]
> 
> 输出: 
> [null,[4,1],[4,1],[3,3]]

示例 2：

> 输入: 
> ["Solution","pick","pick","pick","pick","pick"]
> 
> [[[[-2,-2,-1,-1],[1,0,3,0]]],[],[],[],[],[]]
> 
> 输出: 
> 
> [null,[-1,-2],[2,0],[-2,-1],[3,0],[-2,-2]]
> 
>

#### 输入语法的说明：

输入是两个列表：调用的子例程及其参数。Solution 的构造函数有一个参数，即矩形数组 rects。pick 没有参数。参数总是用列表包装的，即使没有也是如此。

---------------------


一看到题目懵逼了，什么是随机的均匀的选取几个点。有固定算法吗？答案怎么验证？难道会算分布率吗？不知从何下手。
想了半天，没办法啊这，那就调个库碰碰运气吧。random库随机抽一个矩形，然后随机抽一个长宽。


```
class Solution:

    def __init__(self, rects):
        """
        :type rects: List[List[int]]
        """
        self.length = len(rects)
        self.rects = rects
        

    def pick(self):
        """
        :rtype: List[int]
        """
        import random
        pos = random.randint(0, self.length-1)
        x = random.randint(min(self.rects[pos][0], self.rects[pos][2]), max(self.rects[pos][0], self.rects[pos][2]))
        y = random.randint(min(self.rects[pos][1], self.rects[pos][3]), max(self.rects[pos][1], self.rects[pos][3]))
        return [x,y]

```

  当然AC是不可能AC的，但是竟然过了32个样例。再试一次，还是32个。这通过数量竟然是稳定的。但我的输出明显不一样。这就很邪门。
我连错在哪都看不懂，这题没法做啊。
之后看了评论里的一个小提示，有些疑惑，怎么就跟面积扯上关系。思考了一下感觉应该和在整个平面上均匀抽点有关。也就是说，面积越大，抽点越多，面积越小，抽点越小。在输入太多的情况下，点的分布密集的不均衡度就大起来。因此就卡住样例了。因此在抽矩形的时候，要考虑已经抽过的次数。大的尽量多抽，小的少抽。

这就有点烦了。 

不过之前看到过某种方法，忘记了名字，我称其为接雨法。假设有雨滴落在青青草地，一群小屁孩拿着锅碗瓢盆接雨（谁知道借来干啥）。雨滴落下来的位置显然是随机的。但是当我们去计算落在小屁孩那里的概率时，概率直观的可以表现为他们拿着的容器的大小。盆大的概率大，碗小的概率小。那现在不难发现，盆就是题目里的矩形，雨落在谁那儿就代表了我们现在随机选取哪个矩形，面积对应面积，雨滴可以用可靠的randint函数代替。

那么现在我们可以继续生成随机数，但不是直接拿来用，而是看看他“掉”到哪个盆里去了。也就是看他掉下来的范围是哪里。这个范围我们可以将矩形的面积抽象出来代替。计算所有矩形的面积，不断累加。并不断记录累加的矩形是哪一个。之后再算随机数在此间的位置，因为彼此之间的间隔差就是大小。存在字典中，key就是数轴。确定了key，value就是对应的矩形，于是我们就可以将矩形根据面积随机取出来了。

这是一种很巧妙的方法。把面积巧妙的应用进去了。

恰巧的，python提供了一个bisect库来做类似的事情。那么简单的更改一下代码，就可以做出来了。

```
…………
sume = 0
        for i in range(self.length):
            sume += (abs(rects[i][0] - rects[i][2])+1) * (abs(rects[i][1] - rects[i][3])+1)
            self.sums.append(sume)
        self.sume = sume    
        
    def kickTheRandomAss(self):
        pos = random.randint(0, self.sume-1)
        import bisect
        return bisect.bisect(self.sums, pos)
        

    def pick(self):
        """
        :rtype: List[int]
        """
        
        pos = self.kickTheRandomAss()
……

```

AC，时间可够长的，毕竟这种算法涉及到列表查来查去，慢也是正常，看最快的解题，到时跟我这个算法没差，比我快了300ms。想来也是电脑差异的问题。
