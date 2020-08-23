# JZ1 二维数组中的查找
## 题目：
在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
## 解法一 
最直观的解法，大部分人都能很快写出来吧。直接暴搜，稍微利用一下递增的信息。

对于每个一维数组来说，最后一个是整个数组的最大值。同时又满足垂直递增，因此可以在搜索前判断一下范围。
- 如果target比一维数组的最后值还要大，那就没必要搜它了。
- 同理，target比一维数组的第一个数字还要小，就不用管了。

```javascript
//通过简单判断进行搜索范围划定，降低复杂度
function Find(target, array)
{
    // write code here
    var num = array.length;
    var nlength = array[0].length;
    for(var i = 0; i < array.length; i++)
    {
        if(array[i][nlength] < target || array[i][0] > target)//范围划定
        {
            continue;
        }else{
            for(var j = 0; j < nlength; j++)
            {
                if(target == array[i][j])
                {
                    return true;
                }
            }
        }
    }
    return false;
}
```
效率如下：
![解法一效率](K:%5CTypora%5Cimages%5C17370cdc33e3e06d)
不过这就没二分搜索什么事了，这题八成要考的就是这玩意儿。

## 解法二
利用二分搜索
### 关于二分搜索
二分搜索是一种基本的搜索算法，在拥有递增、递减等规律性数据结构中有重要应用。由于其简单易懂的思路、清晰的递归过程，是有序前提下不二的算法选择。

二分查找法的O(log n)让它成为十分高效的算法。不过它的缺陷却也是那么明显的。就在它的限定之上：

- 必须有序，我们很难保证我们的数组都是有序的。当然可以在构建数组的时候进行排序，可是又落到了第二个瓶颈上：它必须是数组。

- 数组读取效率是O(1)，可是它的插入和删除某个元素的效率却是O(n)。因而导致构建有序数组变成低效的事情。

不过是题外话了。回到本题，借用牛客网的题解图，对于一维数组的二分折半搜索图示如下：

![一维二分查找](K:%5CTypora%5Cimages%5C17370d7f4674bcd3)

假设目标tar在arr[1]处，那么我们的二分过程就是(来源为牛客网)：
1. 设初始值：定义一个二分的开始下标为l，结束下标为r，如图所示：
2. 二分一半，中间位置为 mid = l + （（r - l) >> 1)， val>>1, 表示val右移一位相当于val／2，相当于 l+(r-l)/2,这样的写法是防止溢出。如果写成 mid = （l+r)/2; l+r可能会溢出。
3.  如果 tar == arr[mid]，说明找到tar
4. 比较：如果tar > arr[mid], 说明tar在区间[mid+1, r]中，l = mid + 1
5. 如果tar < arr[mid]，说明tar在区间[l, mid-1]中, r = mid - 1

所以进一步改进一维数组的搜索方式：

```js
function Find(target, array)
{
    // write code here
    var num = array.length;
    var nlength = array[0].length;
    for(var i = 0; i < num; i++)
    {
        if(array[i][nlength] < target || array[i][0] > target)//范围划定
        {
            continue;
        }else{
            var low = 0;
            var high = nlength - 1;
            while(low <= high)
            {
                var mid = parseInt((low + high) / 2);
                if(target < array[i][mid])
                {
                    high = mid-1;
                    
                }else if(target > array[i][mid])
                {
                    low = mid+1;
                }else{
                    return true;
                }
            }
        }
    }
    return false;
}
```

![](K:%5CTypora%5Cimages%5C17371c50cd4e6de7)

## 解法三
拓展到二维数组还有一种类似于动态规划（？）的算法。牛客网中有把基标定在右上角的解析，那我就定在左下角来分析吧。

![二维折半](K:%5CTypora%5Cimages%5C17370dccf4cd37b9)
根据题意，我们能知道val右侧均> val，其上侧均< val，假设俺们的target位于arr[1][2]处。

首先进行tar与当前val的判断，若tar > val，说明tar必定不在小于val的区域内，所以val可以右移进入下一层递归了。arr[3][0] -> arr[3][1]

![大于情况](K:%5CTypora%5Cimages%5C173711ba5e14f39a)
若tar < val说明，tar不在大于val的区域内，val可以上移了。arr[3][]

![小于情况](K:%5CTypora%5Cimages%5C173711bff9f1c85d)
 把val的位置横向看做x坐标，纵向看做y坐标，就可以变相得出：
 - 若tar > val, x++（val右移）
 - 若tar < val, y++（val上移）

**复杂度分析**

时间复杂度：O(m+n) ，其中m为行数，n为列数，最坏情况下，需要遍历m+n次。

空间复杂度：O(1)


```js
function Find(target, array)
{
    var num = array.length;
    var nlength = array[0].length;
    for(var x = 0, y = 0; x < nlength && y <= num - 1; )
    {
        if(target < array[num - 1 - y][x])//注意这里要-1以及在二维数组中需要进行坐标切换
        {
            y++;
        }else if(target > array[num - 1 - y][x])
        {
            x++;
        }else
        {
            return true;
        }
    }
    return false;
}
```

![二分方法](K:%5CTypora%5Cimages%5C17371ae35bed1571)
啧看起来提升效果不多呀。顺便说如果想在牛客网上刷运行时间的话，最好自己写处理函数，会快很多。

在函数前加上这个：

```js
while(line=readline()){
    var index = line.indexOf(',');
    var left = parseInt(line.substring(0,index));
    var right = JSON.parse(line.substring(index+1));
    print(Find(left,right))
}
```

![](K:%5CTypora%5Cimages%5C17371b09f6258296)
登登登~自个儿parse就是比内部过输入数据模式切换快啊。

# JZ2 替换空格
## 题目
请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

## 解法
啊这，这不就是我js的大天下？一行搞定

```js
function replaceSpace(str)
{
    // write code here
    return str.replace(/ /g, "%20")
    //str.replace(/\s/g,'%20')这样写也可以。
}
```
顺便说一声，如果直接用str.replace(" ", "%20")是8行的，因为replace**只替换首个匹配**。

# JZ3 从头到尾打印链表
## 题目
输入一个链表，按链表从尾到头的顺序返回一个ArrayList。

## 解法
先来复习下链表，长这样：

![链表结构](K:%5CTypora%5Cimages%5C17371d6f78166bb2)
那么只要构造一个数组，将数据每次都从头部插入（由于数据是倒着插入的，最后会得到一个正着的数组）

```js
/*function ListNode(x){
    this.val = x;
    this.next = null;
}*/
function printListFromTailToHead(head)
{
    // write code here
    var arr = new Array;
    while(head != null)
    {
        arr.unshift(head.val);
        head = head.next;
    }
    return arr;
}
```

![链表结果](K:%5CTypora%5Cimages%5C17371dbb8620f47c)

这里有很多关于链表的操作，后面可以拎出来练练：[JS中的算法与数据结构——链表(Linked-list)](https://juejin.im/entry/6844903498362912775)

# JZ4 重建二叉树
## 题目
输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

# 解法：
复习下二叉树，如果是一棵完全二叉树长这样：

![完全二叉树](K:%5CTypora%5Cimages%5C17371e1fe4fd8edd)

前序遍历：根结点 ---> 左子树 ---> 右子树（先遍历根节点，然后左右））

中序遍历：左子树---> 根结点 ---> 右子树（在中间遍历根节点）

后序遍历：左子树 ---> 右子树 ---> 根结点（最后遍历根节点）

层次遍历：只需按层次遍历即可

思路：
1. 根据前序遍历序列找到根节点的值（根据前序遍历规律知道第一个就是根节点了）
2. 在中序遍历序列中找到根节点的位置
3. 找到左子树根节点（中序遍历中根结点的前一个位置，如果存在的话，就是左子树的根结点）
4. 找到右子树根节点（中序遍历中根结点的后一个位置，如果存在的话，就是右子树的根结点）
5. 递归地重建子树（使用左子树的前序、中序序列构建左子树；使用右子树的前序、中序序列构建右子树）


```js
function reConstructBinaryTree(pre, vin)
{
    // write code here
    if(vin.length == 0)
        return null;
    var ret = {};
    if(vin.length > 1)
    {
        var root = pre[0];
        var i = vin.indexOf(root);
        var left_vin = vin.slice(0, i);//根据中序遍历原理，根结点前面的一定是它下面的左子树结点们
        var left_pre = pre.slice(1, left_vin.length+1);//根据前序遍历原理，它的左子树的前序遍历，只需要从根节点之后摘出相应左子树结点个数就可以。
        var right_vin = vin.slice(i+1, vin.length);//根据中序遍历原理，根结点后面的一定是它下面的左子树结点们
        var right_pre = pre.slice(left_vin.length+1, pre.length);//后面的都是右节点的前序遍历了（因为前->左->右，而前和左已经取完了，剩下都是右了）
        ret = {
            val: root,
            left: reConstructBinaryTree(left_pre, left_vin),
            right: reConstructBinaryTree(right_pre, right_vin)
        }
    }else if(vin.length == 1){
        ret = {
            val: pre[0],
            left: null,
            right: null
        }
    }
    return ret;
}
```
![结果](K:%5CTypora%5Cimages%5C173720bf464957cd)

# #JZ5 用两个栈实现队列
## 题目
用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

## 解法
队列就是像排队一样，一个接一个，先来排队的先出去。

而栈则是像杯子一样，先进来的韭菜被压在下面，后进来的韭菜先被割走（斜眼笑）

要变成队列就很简单了呀~想象一下，这里有两个杯子，要怎样才能实现整齐地先来先被割的韭菜队列呢？

进来一系列的韭菜，在栈中他们就是
```
|=============================================
|（底）1号韭菜--2号韭菜--……---最后来的韭菜（顶）
|=============================================
```
这样的顺序，那么只要把它反过来像杯子倒水一样扣在另一个栈里，不就变成这样子了：

```
            ===============================================|
得到解放<---(顶)1号韭菜--2号韭菜--……---最后来的韭菜(底)      |
            ===============================================|
```
就可以按序倒出去了，这就是pop的实现方法了。这时候数据是存在2号栈中的，要是又有新数据来了怎么办，那就倒回1号栈中然后把新韭菜塞进来就OK了。
```
|=============================================
|（底）1号韭菜--2号韭菜--……---最后来的韭菜（顶）<--新来的韭菜
|=============================================
```
那么代码就很好写了：

```js
var stack1 = [];//默认数据先装这里
var stack2 = [];
function push(node)
{
    // write code here
    if(stack2.length > 0)
    {
        while(stack2.length > 0)
        {
            stack1.push(stack2.pop());
        }
    }
     return stack1.push(node);
    
}
function pop()
{
    // write code here
    if(stack1.length > 0){
            while(stack1.length > 0)
            {
                stack2.push(stack1.pop());
            }
    }
    return stack2.pop();

}
```

![运行结果](K:%5CTypora%5Cimages%5C173721e39657951b)

好像上算法课讲过一道更难的，忘了题目了，下次见到了补充进来吧。

# JZ6 旋转数组的最小数字
## 题目
把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。
例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。
NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。
## 解法一
恕我直言，好像没什么意思啊，第一直觉，O(n)扫一遍
```
while(line=readline()){
    //将json字符串转换为JavaScript值或对象
    let arr = JSON.parse(line);
    print(minNumberInRotateArray(arr));
}
function minNumberInRotateArray(rotateArray)
{
    // write code here
    var len = rotateArray.length;
    if(len == 0)
        return 0;
    var min = rotateArray[0];
    for(var i = 0; i < len; i++)
    {
        if(min > rotateArray[i]){
            min = rotateArray[i];
        }
    }
    return min;
}
```
![效率](K:%5CTypora%5Cimages%5C17376ccec4afc05d)
js搜索有更简单的办法：

```
function minNumberInRotateArray(rotateArray)
{
    return Math.min(...rotateArray)
}
```
## 解法二
仔细观察下题目，会发现有着某种设定，输入的总是经过一次旋转的数组，也就是说会形成从小到大然后一个跌越再从小到大这样的规律。而我们要找的便是中间那个跌越点，就是整个数组的最小值。

也就是说实际上最小的元素就是两个子数组的分界线。

既然两个子数组是有序的，可以用**二分查找**来找。

1. 我们用两个指针left,right分别指向数组的第一个元素和最后一个元素。按照题目的旋转的规则，第一个元素应该是大于最后一个元素的
2. 找到数组的中间元素。
```
(1)array[mid] > array[high]:
出现这种情况的array类似[3,4,5,6,0,1,2]，此时最小数字一定在mid的右边。
low = mid + 1
(2)array[mid] == array[high]:
出现这种情况的array类似 [1,0,1,1,1] 或者[1,1,1,0,1]，此时最小数字不好判断在mid左边
还是右边,这时只好一个一个试 ，把范围缩小一步
high = high - 1
(3)array[mid] < array[high]:
出现这种情况的array类似[2,2,3,4,5,6,6],此时最小数字一定就是array[mid]或者在mid的左
边。因为右边必然都是递增的。
high = mid
```

代码如下：

```
while(line=readline()){
    //将json字符串转换为JavaScript值或对象
    let arr = JSON.parse(line);
    print(minNumberInRotateArray(arr));
}
function minNumberInRotateArray(rotateArray)
{
    // write code here
    var len = rotateArray.length;
    if(len == 0)    return 0;
    var left = 0;
    var right = len - 1;
    while(left < right)
    {
        var mid = parseInt((left + right) / 2);
        if(rotateArray[mid] > rotateArray[right])
        {
            left = mid + 1;
        }else if(rotateArray[mid] < rotateArray[right])
        {
            right = mid;
        }else{
            right--;
        }
    }
    return rotateArray[left];
}
```
![效率](K:%5CTypora%5Cimages%5C17376dde81ca17b9)
# JZ7 斐波那契数列
## 题目
大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0，第1项是1）。
n<=39

## 解法一
一个比较丑陋的解法，直接依照公式。
F(1)=1，F(2)=1, F(n)=F(n - 1)+F(n - 2)

```
function Fibonacci(n)
{
    // write code here
    if(n == 0) return 0;
    if(n == 1) return 1;
    if(n == 2) return 1;
    var ret = 0;
    ret = Fibonacci(n-1) + Fibonacci(n-2);
    return ret;
}

```
![可以看到效果差的出奇](K:%5CTypora%5Cimages%5C17376e6c1323cae3)
可以看到效果差的出奇。
## 解法二
一种动态规划方法.详细看注释

```
function Fibonacci(n)
{
    // write code here
    if(n == 0) return 0;
    if(n == 1) return 1;
    if(n == 2) return 1;
    let a = 0, b = 1;
    while(n--)
    {
        b += a;//形成新的后值
        a = b - a;//相当于原来b的值
    }
    return a;
}
```

![xiaolv](K:%5CTypora%5Cimages%5C17376ee57cbadb45)
这成绩进排行榜了吧可以。

还有一种从榜上看到的写法：

```
function Fibonacci(n)
{
    // write code here
    const arr=[0,1]
    for(let i=2;i<=n;i++){
        arr[i]=arr[i-1]+arr[i-2]
    }
    return arr[n]
}
```
# JZ8 跳台阶
## 题目
一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。
## 解法一
很典型的递归啦，一般都练到过吧伙伴们？说下思路，一次跳两级或者一级，那么我们就作为青蛙站在某个台阶上思考蛙生，我只有两种可能来到这里：1.从我下面一级跳上来的2.从我下面两级那里跳上来的。

得到这样的表达式：

```
if(n == 1)  return 1;
if(n == 2)  return 3;

F(n) = F(n-1) + F(n-2);
```
代码如下：
```
function jumpFloor(number)
{
    // write code here
    if(number == 1)  return 1;
    if(number == 2)  return 2;
    let ret = 0;
    ret = jumpFloor(number - 1) + jumpFloor(number - 2);
    return ret;
}
```

![xiaolv ](K:%5CTypora%5Cimages%5C17376f367b7dbbae)
一般不进行优化的递归也就这种效率了。
## 解法二
同样可以用动态规划进行优化。

```
function jumpFloor(number)
{
    // write code here
    if(number == 1)  return 1;
    if(number == 2)  return 2;
    let ret = 0;
    let a = 1, b = 2, c = 0;//分别表示跳上一级、二级有几种方法 三级的还没算，每次移步都算下一步的值
    while(number > 2)
    {
        c = a + b;
        a = b;
        b = c;
        number--;
    }
    return c;
}
```
![xiaolv](K:%5CTypora%5Cimages%5C17376fcd3b7967d3)

榜上有一种写法：

```
function jumpFloor(number) {
      let dp = []
      dp[0] = 1
      dp[1] = 2
      for (let i = 2; i < number; i++) {
        dp[i]=dp[i-1]+dp[i-2]
      }
      return dp[number-1]
    }
```
# JZ9 变态跳台阶
## 题目
一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。
## 解法
哦豁，真的变态。但是其实很简单啦。

每个台阶可以看作一块木板，让青蛙跳上去，n个台阶就有n块木板，最后一块木板是青蛙到达的位子， 必须存在，其他 (n-1) 块木板可以任意选择是否存在（存在即是选择了踏上这块跳板），则每个木板有存在和不存在两种选择，(n-1) 块木板 就有 [2^(n-1)] 种跳法，可以直接得到结果。

所要求的序列为：0,1,2,4,8,16,……

```
function jumpFloorII(number)
{
    // write code here
    if(number == 1)    return 1;
    return jumpFloorII(number-1)*2;
}
```
![](K:%5CTypora%5Cimages%5C1737731ae8d21b0c)
这才是真的算法题吧（头秃的那种）
# JZ10 矩形覆盖
## 题目
我们可以用2`*`1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2`*`1的小矩形无重叠地覆盖一个2`*`n的大矩形，总共有多少种方法？

比如n=3时，2*3的矩形块有3种覆盖方法：

![](K:%5CTypora%5Cimages%5C173773333d9fb587)

# 解法
这里主要是思路的问题，我一开始用没想明白，分奇偶解决，但是不知为啥一直溢出。后面重新分析了一下。

n = 1时 return 1；

n = 2时，只有||和=两种情况；

n = 3时，有上面三种情况；

n = 4时，有从n=3出发的变形情况：和=||和||||和|=|这样三种情况，所以只在n=3的基础上往后面添加了一个竖着的。

从n=2出发的变形情况||=，==，=||和||||（这两种重复了），所以只在n=2的基础上往后面添加了两个横着的情况。

这样的话n=4的情况就是和n=2的情况+n=3的情况数量一致。

其他情况都是类似的。可以自然得到这样一个规律，f(n) = f(n-1) + f(n-2)，就是我们之前做过的斐波那契数列了。

```
function rectCover(number)
{
    // write code here
    if(number == 0) return 0;
    if(number == 1) return 1;
    if(number == 2) return 2;
    let a = 1, b = 2;
    while(number-- >= 2)
    {
        b += a;
        a = b - a;
    }
    return a;
}
```

![](K:%5CTypora%5Cimages%5C17377605740a999c)
注意下我们的起始值与之前的斐波那契不大一样。

# JZ11 二进制中1的个数
## 题目
输入一个整数，输出该数32位二进制表示中1的个数。其中负数用补码表示。
## 解法
顺便复习下如何用js十进制转二进制：

```
parseInt(n).toString(2)
```
这题一开始也挺蒙的，后面看了高人指点，能够领略一二，有种玩数竞的感觉。

1100 减去1后 -》1011.

可以发现减1的结果是把最右边的一个1开始的所有位都取反了。

这个时候如果我们再把原来的整数和减去1之后的结果做与运算：1100&1011=1000.

也就是说，把一个整数减去1，再和原整数做与运算，**会把该整数最右边一个1变成0**.

**那么一个整数的二进制有多少个1，就可以进行多少次这样的操作**。

```
function NumberOf1(n)
{
    // write code here
    var count = 0;
    while(n)
    {
        n = n & (n-1);
        count++;
    }
    return count;
    
}
```

![xiaolv](https://user-gold-cdn.xitu.io/2020/7/24/1737cc0d717c452e?w=172&h=59&f=png&s=1953)

# JZ12 数值的整数次方
## 题目描述
给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

保证base和exponent不同时为0
## 答案
### 解法一
pow函数的简单实现
```js
  /**
   * @return {number}
   */
  function Power(base, exponent) {
      // write code here
      if (exponent === 0) {
          return 1;
      } else if (exponent === 1) {
          return base;
      }
      let ret = base;
      if (exponent > 0) {
          for (let i = 1; i < exponent; i++) {
              ret *= base;
          }
          return ret;
      } else {
          for (let i = 1; i < Math.abs(exponent); i++) {
              ret *= base;
          }
          return 1 / ret;
      }
  }
```
### 解法二
应用ES6最新运算符 **
```
function Power(base, exponent)
{
    // write code here
    return base**exponent;
}
```
# JZ13 调整数组顺序使奇数在偶数前面
## 题目描述
输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。
## 解法一
开两个数组分别记录
```
function reOrderArray(array)
{
    let oddArray = [];
        let evenArray = [];
        for(let i = 0; i < array.length; i++)
        {
            if(array[i]%2 === 0){
                evenArray.push(array[i]);
            }else{
                oddArray.push(array[i]);
            }
        }
        return oddArray.concat(evenArray);
}
```
## 解法二
相当于一个特殊条件判断的插入排序.是奇数的话把他插入到前面，其余数字后移
```
function reOrderArray2(array) {
    let k = 0;
    for(let i = 0; i < array.length; i++)
    {
        let tmp = array[i];
        if(tmp & 1){//是奇数
            for(let j = i - 1; j >= k; j--)//到已经排序好的为止
            {
                array[j+1] = array[j];
            }
            array[k++] = tmp;
        }
    }
    return array;
}
console.log(reOrderArray2([1,2,3,4,5,6]))
```
# JZ14 链表中倒数第k个节点
## 题目描述
输入一个链表，输出该链表中倒数第k个结点。
## 解法
快慢指针，相距k个，那么快指针到末尾时，慢指针的位置便是倒数k个
```
function FindKthToTail(head, k) {
    if (!head || k < 1) return null;
    let slowptr = head;
    let fastptr = head;
    let count = 0;
    while (fastptr != null && count < k) {
        count++;
        fastptr = fastptr.next;
    }
    if (count < k) return null;//k的取值超过了链表本身的长度
    while (fastptr != null) {
        fastptr = fastptr.next;
        slowptr = slowptr.next;
    }
    return slowptr;
}
```
# JZ15 反转链表
## 题目描述
输入一个链表，反转链表后，输出新链表的表头。
## 解答
基本思路，保存当前指针.next.next，再将当前指针.next指向当前指针，然后前移
```
function ReverseList(pHead) {
    // write code here
    if (!pHead) return null;
    if (!pHead.next) return pHead;
    let p1 = pHead;
    let p2 = pHead.next;
    p1.next = null;
    let temp;
    while (p2 != null) {
        temp = p2.next;
        p2.next = p1;
        p1 = p2;
        p2 = temp;
    }
    return p1;
}
```

# JZ16 合并两个排序的链表
## 题目描述
输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。
## 解法一
无递归。构造一个链表头，指向每次比较中较小的那个。如果最后某串链表还有多余，则指向剩下的那串链表。
```js
function Merge(pHead1, pHead2)
{
    var head = new ListNode(0);
    var pHead = head;
    while(pHead1 != null && pHead2!= null)
    {
        if(pHead1.val < pHead2.val)
        {
            head.next = pHead1;
            pHead1 = pHead1.next;
        }else{
            head.next = pHead2;
            pHead2 = pHead2.next;
        }
        head = head.next;
    }
    if(pHead1 === null)
    {
        head.next = pHead2;
    }
    if(pHead2 === null)
    {
        head.next = pHead1;
    }
    return pHead.next;
}
```
## 解法二
递归法，很好理解。每次要得到下一个位置就进行递归计算
```js
function Merge(pHead1, pHead2) {
    let p = null;
    if(!pHead1) return pHead2;//pHead1已经遍历完毕
    if(!pHead2) return pHead1;
    if(pHead1.val < pHead2.val)
    {
        p = pHead1;
        p.next = Merge(pHead1.next, pHead2);//本次选取phead1，phead1前进
    }else{
        p = pHead2;
        p.next = Merge(pHead1, pHead2.next);
    }
    return p;
}
```
# JZ17 树的子结构
## 题目描述
输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）
## 解法
子结构定义：树A和树B的根结点相等，并且树A的左子树和树B的左子树相等，树A的右子树和树B的右子树相等

主要是进行每个节点的匹配，首先找基准点，看以当前为根节点向下匹配是否能成功，不能的话就以左子树为基准点，再不能，以右子树为基准点
```js
/**
 * @return {boolean}
 */
function HasSubtree(pRoot1, pRoot2)
{
    // write code here
    var result = false;
    if(pRoot1 != null && pRoot2 != null)//如果root为零，直接返回false
    {
        //看以当前根节点作为基准是否和root2一致，如果不一致找左子树，再找右子树
        return (doeTreeHaveSubTree(pRoot1, pRoot2) || HasSubtree(pRoot1.left, pRoot2) || HasSubtree(pRoot1.right, pRoot2));
    }
    return result;
}
function doeTreeHaveSubTree(node1, node2) {
    if(node2 === null)
    {
        return true;//要找的树已经遍历完了，ok
    }
    if(node1 === null)
    {
        return false;
    }
    if(node1.val !== node2.val)
    {
        return false;//没匹配上
    }
    return doeTreeHaveSubTree(node1.left, node2.left) && doeTreeHaveSubTree(node1.right, node2.right);//分别匹配左右子树
}
```
# JZ18 二叉树的镜像
## 题目描述
操作给定的二叉树，将其变换为源二叉树的镜像。
## 解法
记住每次递归都是你想要的值，那么你只要把左子树定义为右边递归的值，右子树赋值为左边递归的值即可。
```js
function TreeNode(x) {
    this.val = x;
    this.left = null;
    this.right = null;
}

function Mirror(root) {
    if (!root) return null;
    var temp = root.left;
    root.left = Mirror(root.right);
    root.right = Mirror(temp);
    return root;
}
```
# JZ19 顺时针打印矩阵
## 题目描述
输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.
## 解法
可以定义左右上下边界，每次递归以后把边界往里收，注意边界情况。
```js
function printMatrix(matrix)
{
    // write code here
    var low = 0;
    var high = matrix.length-1;
    var left = 0;
    var right = matrix[0].length-1;
    var ret = [];
    while(low <= high && left <= right)
    {
        for(let i = left; i <= right; i++)
        {
            ret.push(matrix[low][i]);//从左到右
        }
        if(low === high){break;}//只有一行，默认执行完从左到右后就不再执行了
        for(let j = low+1; j <= high; j++)
        {
            ret.push(matrix[j][right]);//从上到下
        }
        if(left === right){break;}//只有一列，默认执行完从上到下后就不再执行了
        for(let m = right-1; m >= left; m--)
        {
            ret.push(matrix[high][m]);//从右到左
        }
        for(let n = high-1; n >= low+1; n--)
        {
            ret.push(matrix[n][left]);//从下到上
        }
        low++;
        high--;
        left++;
        right--;
    }
    return ret;
}
```
# JZ20 包含min函数的栈
## 题目描述
定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））
## 解法
这个好像没什么好说，求最小值可以用math库的函数
```js
    let stack = [];
    function push(node)
    {
        // write code here
        return stack.push(node);
    }
    function pop()
    {
        // write code here
        return stack.pop();
    }
    function top()
    {
        // write code here
        return stack.pop();
    }
    function min()
    {
        // write code here
        return Math.min(...stack);
        //也可以return Math.min.apply(this, stack)
    }
```

# JZ21 栈的压入和弹出序列
## 题目描述
输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）
## 解题思路
构造一个验证栈，当push和pop栈不相等时，说明此时进行的只是压栈，把push栈中的元素压入验证栈中，push指针后移；如果相等，说明此时元素被弹出，将验证栈中元素弹出，push指针pop指针后移，同时进行连续验证（pop后面的指针元素是否与先前压入的元素一致）。
```js
function IsPopOrder(pushV, popV) {
    // write code here
    //1,2,3,4,5  4,5,3,2,1  4,3,5,1,2
    let stackV = [];
    let indexPush = 0;
    let indexPop = 0;
    while (indexPop < popV.length && indexPush < pushV.length) {
        if (pushV[indexPush] !== popV[indexPop]) {
            //不相等，继续压
            stackV.push(pushV[indexPush]);
            indexPush++;
        } else {
            //相等，指针后移，弹出栈中与pop匹配元素
            indexPop++;
            indexPush++;
            while (stackV.length !== 0 && stackV[stackV.length - 1] === popV[indexPop]) {
                indexPop++;
                stackV.pop();
            }
        }
    }
    return stackV.length === 0;
}
```
# JZ22 从上往下打印二叉树
## 题目描述
从上往下打印出二叉树的每个节点，同层节点从左至右打印。
## 解答
基本思路，构造返回队列，每次推入当前节点的左子树，再推入右子树。然后按照队列顺序先打印左子树的值，再打印右子树的值。
```js
function PrintFromTopToBottom(root) {
    // write code here
    if (!root) return [];
    let queue = [];
    let ret = [];
    queue.push(root);
    while (queue.length !== 0) {
        let node = queue.shift();//每次取头，构造队列
        ret.push(node.val);
        if (node.left) {
            queue.push(node.left);
        }
        if (node.right) {
            queue.push(node.right);
        }
    }
    return ret;
}
```
# JZ23 二叉搜索树的后序遍历序列
## 题目描述
输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则返回true,否则返回false。假设输入的数组的任意两个数字都互不相同。
## 解答
先复习下二叉搜索树：

在二叉搜索树中：
1. 若任意结点的左子树不空，则左子树上所有结点的值均不大于它的根结点的值。
2. 若任意结点的右子树不空，则右子树上所有结点的值均不小于它的根结点的值。
3. 任意结点的左、右子树也分别为二叉搜索树。

![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6162522ca2a24e2b87c839cda0916875~tplv-k3u1fbpfcp-zoom-1.image)

后序遍历（左右根），二叉搜索树（左<根<右）

这题的思路其实很简单，根据后序遍历可以知道每串树序列的最后一个一定是根节点，那么只要判断出左右子树的位置就可以了,所以从左到右扫一遍只要找到第一个比根大的点，就是右子树的串起始位置。区分出左右子树后再进行递归判断。
```js
function VerifySquenceOfBST(sequence) {
    // write code here
    if (!sequence || sequence.length === 0) return false;
    return isBST(sequence, 0, sequence.length - 1);
}

function isBST(sequence, start, end) {
    if (start >= end) return true;//无序列可找了
    let split = start;//寻找左右子树的断点位置
    let val = sequence[end];//根节点是后序遍历序列中的最后一个
    for (; split < end && sequence[split] < val; split++) ;//左子树都是小于根节点的,此时的split既是断点位置(右子树起点)
    for (let i = split; i < end; i++) {
        if (sequence[i] < val) {
            return false;//如果右子树中有小于的数，就是错的
        }
    }
    return isBST(sequence, start, split - 1) && isBST(sequence, split, end - 1);//end-1是必须的，需要把此次已经匹配掉的root给拿掉，因为它不属于右子树的一部分
}
```
# JZ24 二叉树中和为某一值的路径
## 题目描述
输入一颗二叉树的根节点和一个整数，按字典序打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。
## 解答
定义全局数组进行存储，深度优先遍历进行数值计算，如果等于所给值，记录此条路径，否则将结点进行弹出检查右边路径。
```js
var path;
var res;

function FindPath(root, expectNumber) {
    // write code here
    path = [];
    res = [];
    if (root == null) return [];
    calculatePath(root, expectNumber);
    return path;
}

function calculatePath(node, expectNumber) {
    let val = node.val;
    res.push(val);//记录值相当于记录路径
    if (val == expectNumber && node.left == null && node.right == null) {
        path.push(res.slice());//找到路，把现在存储的路径返回
    } else {
        if (node.left != null) calculatePath(node.left, expectNumber - val);//左子树有值，可以继续往下走
        if (node.right != null) calculatePath(node.right, expectNumber - val);
    }
    res.pop();//上一条路已经选择完了，可以退出到父节点上
}
```
# JZ25 复杂链表的复制
## 题目描述
输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针random指向一个随机节点），请对此链表进行深拷贝，并返回拷贝后的头结点。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）
## 解法
这个我觉得还是有点复杂，可以想到的直觉就是先扫一遍复制基础的，然后再针对每个结点再扫一遍拿到random同时进行去重操作，但是这样复杂度就有点高了。

看到大佬们给出了一种漂亮的解法，把第一遍扫的复制的一般关系直接挂在相对应的前面结点的后头，这样在找random结点的时候就不用害怕重复性了。
![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/960563f7ec8e4100b143d76d9845202c~tplv-k3u1fbpfcp-zoom-1.image)
```js
function Clone(pHead) {
    // write code here
    if (pHead === null) return null;
    let currentNode = pHead;
    //第一遍扫，复制一般关系，插入到对应结点之后
    while (currentNode !== null) {
        let cloneNode = new RandomListNode(currentNode.label);//加入的结点原本不存在需要new
        let nextNode = currentNode.next;
        currentNode.next = cloneNode;
        cloneNode.next = nextNode;
        currentNode = nextNode;
    }

    //第二遍扫，把random关系添加上（只要添加到相应位置的next结点上就可以了）
    currentNode = pHead;
    while (currentNode !== null) {
        currentNode.next.random = currentNode.random === null ? null : currentNode.random.next;
        currentNode = currentNode.next.next;
    }

    //第三遍扫，每各一个分开
    currentNode = pHead;
    let retNode = pHead.next;
    while (currentNode !== null) {
        let cloneNode = currentNode.next;
        currentNode.next = cloneNode.next;
        cloneNode.next = cloneNode.next === null ? null : cloneNode.next.next;
        currentNode = currentNode.next;
    }
    return retNode;
}
```