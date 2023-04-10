广度优先遍历

广度优先搜索（breadth-fifirst search，BFS）不同与深度优先搜索，它是一层层进行遍历的，因

此需要用先入先出的队列而非先入后出的栈进行遍历。由于是按层次进行遍历，广度优先搜索时

按照“广”的方向进行遍历的，也常常用来处理最短路径等问题。

这里要注意，深度优先搜索和广度优先搜索都可以处理可达性问题，即从一个节点开始是否

能达到另一个节点。因为深度优先搜索可以利用递归快速实现，很多人会习惯使用深度优先搜索

刷此类题目。实际软件工程中，笔者很少见到递归的写法，因为一方面难以理解，另一方面可能

产生栈溢出的情况；而用栈实现的深度优先搜索和用队列实现的广度优先搜索在写法上并没有太

大差异，因此使用哪一种搜索方式需要根据实际的功能需求来判断。

### [剑指 Offer 32 - I. 从上到下打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

利用队列实现BFS

首先将根节点入队列q，如果根节点root为空，则直接返回空数组

对于队列中的每一个节点，出队后，将其值加入结果list，再将其不为空的左右子节点加入队列

```java
		q.offer(root);
        while(!q.isEmpty())
        {
            TreeNode node=q.poll();
            list.add(node.val);
            if(node.left!=null)
            {
                q.offer(node.left);
            }
            if(node.right!=null)
            {
                q.offer(node.right);
            }
        }
```

最后将list遍历转化为数组
### [剑指 Offer 32 - II. 从上到下打印二叉树 II](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

和32-I类似，增加层次

每一层就是队列当前的所有节点，所以首先将队列长度size保留，然后将队列中的前size个节点按照32-I中的方式处理

```java
		q.offer(root);
        while(!q.isEmpty())
        {
            int size=q.size();
            list.add(new ArrayList<Integer>());
            while(size>0)
            {
                TreeNode node=q.poll();
                list.get(level).add(node.val);
                if(node.left!=null)
                {
                    q.offer(node.left);
                }
                if(node.right!=null)
                {
                    q.offer(node.right);
                }
                size--;
            }
            level++;
        }
```

### [剑指 Offer 32 - III. 从上到下打印二叉树 III](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

在32-II的基础上添加对层次的判断，决定是逆序还是顺序添加数值

```java
		q.offer(root);
        while(!q.isEmpty())
        {
            int size=q.size();
            List<Integer> list=new ArrayList<>();
            while(size>0)
            {
                TreeNode node=q.poll();
                if((level&1)==0)
                {
                    list.add(node.val);//顺序
                }
                else
                {
                    list.add(0,node.val);//逆序
                }
                if(node.left!=null)
                {
                    q.offer(node.left);
                }
                if(node.right!=null)
                {
                    q.offer(node.right);
                }
                size--;
            }
            re.add(list);
            level++;
        }
```

### [127. 单词接龙](https://leetcode-cn.com/problems/word-ladder/)

建图+BFS

只有在两个字符串仅有一个字符不同时，它们才连通

从beginWord开始广度优先搜索直到endWord
### [279. 完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

可以将每个整数看成图中的一个节点，如果两个整数之差为一个平方数，那么这两个整数所在的节点就有一条边。

要求解最小的平方数数量，就是求解从节点 n 到节点 0 的最短路径。

```java
	List<Integer> squares=new ArrayList<>();
    public int numSquares(int n) {
        generateSquares(n);
        boolean[] mark=new boolean[n+1];
        Queue<Integer> q=new LinkedList<>();
        int step=0;
        q.offer(n);
        while(!q.isEmpty()){
            int size=q.size();
            step++;
            while(size-->0){
                int cur=q.poll();
                for(int s:squares){
                    int next=cur-s;
                    if(next<0){
                        break;
                    }
                    if(next==0){
                        return step;
                    }
                    if(!mark[next]){
                        q.offer(next);
                        mark[next]=true;
                    }
                }
            }
        }
        return n;
    }
    private void generateSquares(int n){
        int i=1;
        while(i*i<=n){
            squares.add(i*i);
            i++;
        }
    }
```

不需要乘法也能生成平方数

```java
	//生成平方数的列表，利用(n+1)^2-n^2=2*n+1
    private void generateSquares(int n){
        int i=1;
        int diff=3;
        while(i<=n){
            squares.add(i);
            i+=diff;
            diff+=2;
        }
    }
```



### [310. 最小高度树](https://leetcode-cn.com/problems/minimum-height-trees/)

首先，我们看了样例，发现这个树并不是二叉树，是多叉树。
然后，我们可能想到的解法是：根据题目的意思，就挨个节点遍历bfs，统计下每个节点的高度，然后用map存储起来，后面查询这个高度的集合里最小的就可以了。
但是经过尝试这样会超时。
于是我们看图（题目介绍里面的图）分析一下，发现，越是靠里面的节点越有可能是最小高度树。
所以，我们可以这样想，我们可以倒着来。
我们从边缘开始，先找到所有出度为1的节点，也就是叶子节点，然后把所有出度为1的节点进队列，然后不断地bfs，最后找到的就是两边同时向中间靠近的节点，那么这个中间节点就相当于把整个距离二分了，那么它当然就是到两边距离最小的点，也就是到其他叶子节点最近的节点了。

得到的解决方案就是寻找最中间的节点，一层一层地删除叶子节点，最后一层就是答案、

```java
	//最中间的节点就是答案，一层一层地删除叶子节点
    public List<Integer> findMinHeightTrees(int n, int[][] edges) {
        List<Integer>[] arr=new ArrayList[n];//保存与当前节点的邻接节点
        Queue<Integer> q=new LinkedList<>();
        List<Integer> re=new ArrayList<>();
        if(n==1){//只有一个节点，直接返回0，面向测试用例编程
            re.add(0);
            return re;
        }
        int[] degree=new int[n];//保存节点的度
        for(int i=0;i<n;i++){
            arr[i]=new ArrayList<Integer>();
        }
        for(int[] edge:edges){//根据边，得到节点互通的关系
            arr[edge[0]].add(edge[1]);
            arr[edge[1]].add(edge[0]);
            degree[edge[0]]++;
            degree[edge[1]]++;
        }
        for(int i=0;i<n;i++){
            if(degree[i]==1){
                q.offer(i);//将度为1的节点即叶子节点入队列
            }
        }
        while(!q.isEmpty()){
            re=new ArrayList<>();//每一轮re都会更新
            int size=q.size();
            while(size>0){
                size--;
                int cur=q.poll();
                re.add(cur);//re每一轮bfs都会更新，最后一轮bfs到的叶子节点为答案
                for(int i=0;i<arr[cur].size();i++){
                    int temp=arr[cur].get(i);
                    degree[temp]--;//将当前节点的邻接节点度减一，即相当于把当前节点cur删除
                    if(degree[temp]==1){
                        q.offer(temp);//将度为1 的节点即叶子节点入队列
                    }
                }
            }
        }
        return re;//此时的re为最中间的一层叶子节点，就是答案
    }
```



### [934. 最短的桥](https://leetcode-cn.com/problems/shortest-bridge/)

先确定第一个岛的边界，可以通过dfs实现，把第一个岛都变成-1

从边界开始bfs，一圈一圈往外扩张，直到与另一个岛相邻，扩张的圈数就是答案

用队列实现bfs，具体的，在dfs确定第一个岛的范围时，将第一个岛边界的外围水坐标入队列

```java
	private void dfs(int[][] grid,Queue<int[]>q,int i,int j){
        if(i<0||i>=n||j<0||j>=n||grid[i][j]==-1){
            return;
        }
        if(grid[i][j]==0)
        {
            q.offer(new int[]{i,j});//第一个岛边界外的点进入队列，便于后续bfs
            return;
        }
        grid[i][j]=-1;
        for(int k=0;k<4;k++){
            dfs(grid,q,i+move[k],j+move[k+1]);
        }
    }
```



之后将队列中的水坐标出队列，并对其上下左右的坐标进行判断：

如果是0，则是水，将其入队列，并置为-1，表明扩张

如果是-1，则是第一座岛的范围，不做处理

如果是1，则是第二座岛，扩张结束，返回圈数level

```java
		//bfs一圈一圈地扩张
        int level=0;//扩张的圈数
        while(!q.isEmpty()){
            level++;//由于队列中是第一座岛的外围水坐标，相当于已经扩张了一圈，因此圈数level初始就需要加1，
            int size=q.size();
            while(size>0){
                size--;
                int[] cur=q.poll();
                for(int k=0;k<4;k++){
                    int x=cur[0]+move[k];
                    int y=cur[1]+move[k+1];
                    if(x>=0&&x<n&&y>=0&&y<n){//防止越界
                        if(grid[x][y]==0){//是水，扩张
                            grid[x][y]=-1;
                            q.offer(new int[]{x,y});
                        }else if(grid[x][y]==1){//到了下一个岛
                            return level;
                        }
                    }
                }
            }
        }
```


