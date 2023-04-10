## DFS

深度优先遍历

### [剑指 Offer 32 - II. 从上到下打印二叉树 II](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

深度优先遍历，使用层次level确定将数值添加到哪一层

遍历子节点，层次level加一

需要注意的是，如果当前层次还没有初始化，先进行初始化

```java
	private void dfs(TreeNode root,int level)
    {
        if(root==null)
        {
            return;
        }
        if(list.size()<level+1)
        {
            list.add(new ArrayList<Integer>());
        }
        list.get(level).add(root.val);
        dfs(root.left,level+1);
        dfs(root.right,level+1);
    }
```





### [剑指 Offer 32 - III. 从上到下打印二叉树 III](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-iii-lcof/)

和32-II类似，添加了层次判断，决定是逆序还是顺序

```java
	private void dfs(TreeNode root,int level)
    {
        if(root==null)
        {
            return;
        }
        if(list.size()<level+1)
        {
            list.add(new ArrayList<Integer>());
        }
        if((level&1)==0)
        {
            list.get(level).add(root.val);//顺序
        }
        else
        {
            list.get(level).add(0,root.val);//逆序
        }
        dfs(root.left,level+1);
        dfs(root.right,level+1);
    }
```

### [剑指 Offer 37. 序列化二叉树](https://leetcode-cn.com/problems/xu-lie-hua-er-cha-shu-lcof/)

可以不考虑LeetCode使用的序列化方法，而是使用先序遍历，并且将节点的空左右节点补全，如果节点为空则字符串加入"null"，并以”,“分割每个节点的值，序列化过程如下：

```java
	public String serialize(TreeNode root) {
        StringBuilder sb=new StringBuilder();
        if(root==null)
        {
            return "null,";
        }
        else{
            sb.append(root.val+",");
        }
        sb.append(serialize(root.left));
        sb.append(serialize(root.right));
        return sb.toString();
    }
```



反序列化首先将字符串以”,“分割为数组，并构造列表list，

```java
	public TreeNode deserialize(String data) {
        //TreeNode root=null;
        String[] arr=data.split(",");
        List<String> list=new ArrayList<>();
        for(String s:arr)
        {
            list.add(s);
        }
        return dfs(list);
    }

```

使用dfs构造二叉树，构造过程中，如果是”null“，表明该节点为空，直接返回，空节点没有子节点

否则不为空，将值取出并生成新节点，每次都是取列表的第一个元素，因此每次取出后都需要删除列表的第一个元素，来表示构造的进度

```java
	private TreeNode dfs(List<String> list)
    {
        String cur=list.get(0);
        list.remove(0);
        if(cur.equals("null"))
        {
            return null;
        }
        TreeNode node=new TreeNode(Integer.parseInt(cur));
        node.left=dfs(list);
        node.right=dfs(list);
        return node;
    }
```



### [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

根据题意，被围绕的区域不能与边界上的O相连

因此从四个边界出发，进行深度优先搜索，将连通的O都置为"-"，这样操作后矩阵中保留的O就是被围绕的区域

搜索完后对矩阵进行更新，此时矩阵中的"-"都还原为O，矩阵中的O都变成X

```java
	int n,m;
    public void solve(char[][] board) {
        n=board.length;
        m=board[0].length;
        if(m==0)
        {
            return;
        }
        for(int i=0;i<n;i++)
        {
            dfs(board,i,0);
            dfs(board,i,m-1);
        }
        for(int i=0;i<m;i++)
        {
            dfs(board,0,i);
            dfs(board,n-1,i);
            
        }
        for(int  i=0;i<n;i++)
        {
            for(int j=0;j<m;j++)
            {
                if(board[i][j]=='O')
                {
                    board[i][j]='X';
                }
                if(board[i][j]=='-')
                {
                    board[i][j]='O';
                }
            }
        }
    }
    public void dfs(char[][] board,int i,int j)
    {
        if(i<0||j<0||i==n||j==m||board[i][j]!='O')
        {
            return;
        }
        board[i][j]='-';
        dfs(board,i,j+1);
        dfs(board,i,j-1);
        dfs(board,i+1,j);
        dfs(board,i-1,j);
        
    }
```



### 200.岛屿数量

[200. 岛屿数量 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/number-of-islands/)

将二维网格看成一个无向图，竖直或水平相邻的 1 之间有边相连。

为了求出岛屿的数量，我们可以扫描整个二维网格。如果一个位置为 1，则以其为起始节点开始进行深度优先搜索。在深度优先搜索的过程中，每个搜索到的 1都会被重新标记为 0，这样就可以唯一确定每一个岛屿。

最终岛屿的数量就是我们进行深度优先搜索的次数。

### [417. 太平洋大西洋水流问题](https://leetcode-cn.com/problems/pacific-atlantic-water-flow/)

如果一个点一个点的判断能否流入大西洋，太平洋，那么深度遍历将会非常复杂

逆向思维，看边界能与哪些点连接，左上为一组，右下为一组，然后两组取公共点就是答案

点的上下左右移动可以取巧：

```java
int[] move=new int[]{-1,0,1,0,-1};
```

对于点(i,j)，上下左右移动后的点就是（i+move[k],j+move[k+1]）k=0~3



```java
		m=heights.length;
        n=heights[0].length;
        boolean[][] reachP=new boolean[m][n];//记录能否到太平洋
        boolean[][] reachA=new boolean[m][n];//记录能否到大西洋
```

### [464. 我能赢吗](https://leetcode.cn/problems/can-i-win/)



```java
class Solution {
    //如果第一次就能达到或超过目标，直接返回true
    //如果所有的数加起来刚好等于目标，则需要看总共有多少个数，个数为奇数，就是true，否则就是false
    Boolean[] memo;
    //used的第i位表示第i个数字被使用的情况
    public boolean canIWin(int maxChoosableInteger, int desiredTotal) {
        if(maxChoosableInteger>=desiredTotal){
            return true;
        }
        int max=(maxChoosableInteger+1)/2*maxChoosableInteger;
        if(max<desiredTotal){
            return false;
        }
        else if(max==desiredTotal){
            if(maxChoosableInteger%2==0){
                return false;
            }else{
                return true;
            }
        }
        memo=new Boolean[1<<maxChoosableInteger+1];
        return dfs(maxChoosableInteger,desiredTotal,0,0);
    }

    private boolean dfs(int maxChoosableInteger,int desiredTotal,int used,int cur){
        if(memo[used]!=null){
            return memo[used];
        }
        boolean re=false;
        for(int i=1;i<=maxChoosableInteger;i++){
            //如果第i位已经被使用了，继续
            if(((used>>(i-1))&1)==1){
                continue;
            }
            //没有被使用，就要考虑它被使用的情况
            //先手加上i之后直接胜出的情况
            if(cur+i>=desiredTotal){
                re=true;
                break;
            }
            //先手加上i之后，后手再走都无法胜出，那么先手能赢
            if(!dfs(maxChoosableInteger,desiredTotal,used|(1<<(i-1)),cur+i)){
                re=true;
                break;
            }
        }
        memo[used]=re;
        return re;
    }
}
```

### 538.把二叉搜索树转换为累加树

[538. 把二叉搜索树转换为累加树 - 力扣（LeetCode） (leetcode-cn.com)](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/)

二叉搜索树是一棵空树，或者是具有下列性质的二叉树：

若它的左子树不空，则左子树上所有节点的值均小于它的根节点的值；

若它的右子树不空，则右子树上所有节点的值均大于它的根节点的值；

它的左、右子树也分别为二叉搜索树。

由这样的性质我们可以发现，二叉搜索树的中序遍历是一个单调递增的有序序列。如果我们反序地中序遍历该二叉搜索树，即可得到一个单调递减的有序序列。

所以可以反序中序遍历二叉搜索树，向右再根最后左

累加这个顺序遍历的节点值，就可以转换为累加树

```java
	int sum=0;
    public TreeNode convertBST(TreeNode root) {
        if(root!=null)
        {
            convertBST(root.right);
            sum+=root.val;
            root.val=sum;
            convertBST(root.left);
        }
        return root;
    }
```

### [1254. 统计封闭岛屿的数目](https://leetcode-cn.com/problems/number-of-closed-islands/)

封闭的岛屿中每一块陆地上下左右要么是陆地，要么是水

因此封闭岛屿中不能有边界的陆地

```java
	int m,n;
    int[][] visited;
    public int closedIsland(int[][] grid) {
        int re=0;
        visited=grid;//0表示陆地，1表示水，-1表示已经访问过
        m=grid.length;
        n=grid[0].length;
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(visited[i][j]==0){
                    if(dfs(i,j)){
                        re++;
                    }
                }
            }
        }
        return re;
    }

    private boolean dfs(int i,int j){
        if(i<0||i>=m||j<0||j>=n){
            return false;//能通到边界，不是封闭岛屿
        }
        if(visited[i][j]==1||visited[i][j]==-1){
            return true;
        }
        visited[i][j]=-1;
        
        boolean f1=dfs(i-1,j);
        boolean f2=dfs(i+1,j);
        boolean f3=dfs(i,j-1);
        boolean f4=dfs(i,j+1);
        return f1&&f2&&f3&&f4;
        //不能图方便直接写成return dfs(i-1,j)&&dfs(i+1,j)&&dfs(i,j-1)&&dfs(i,j+1);
        //这样无法完成完整的遍历整个岛屿，因为如果搜索到某个边界陆地，就直接返回false，其他的陆地不会被搜索到
        //从而得不到正确的答案
    }
```


