```json
{
  "updated_at": "2020-07-11",
  "updated_by": "KelipuTe",
  "tags": "数据结构,Data Structure,树,Tree,二叉树,Binary Tree"
}
```

---

## 二叉树

二叉树（Binary Tree）是树形结构的一个重要类型。

### 定义

二叉树是n个有限元素的集合，该集合或者为空、或者由一个称为根（root）的元素及两个不相交的、被分别称为左子树和右子树的二叉树组成，是有序树。当集合为空时，称该二叉树为空二叉树。在二叉树中，一个元素也称作一个结点。二叉树特点是每个结点最多只能有两棵子树，且有左右之分。

### 特殊类型

1、满二叉树：如果一棵二叉树只有度为0的结点和度为2的结点，并且度为0的结点在同一层上，则这棵二叉树为满二叉树。

满二叉树，叶子只能出现在最下一层，非叶子节点的度一定是2。**在同样深度的二叉树中，满二叉树结点数最多**。

2、完全二叉树：深度为k，有n个结点的二叉树当且仅当其每一个结点都与深度为k，有n个结点的满二叉树中编号从1到n的结点一一对应时，称为完全二叉树。

完全二叉树的特点是叶子结点只可能出现在层序最大的两层上，并且某个结点的左分支下子孙的最大层序与右分支下子孙的最大层序相等或大1。层序最大层的叶子结点一定集中在左边的连续位置。如果结点度为1，则该结点只有左孩子。**在同样结点数的二叉树，完全二叉树的深度最小**。

3、斜树：除了根节点外，只有左结点（左斜树）或只有右结点（右斜树）的二叉树。

### 二叉树性质

性质1：二叉树的第i层上至多有$2^{i-1}(i≥1)$个节点。

性质2：深度为h的二叉树中至多含有$2^h-1$个节点。

性质3：若在任意一棵二叉树中，有n个叶子节点，有n2个度为2的节点，则必有n0=n2+1 。

对于任何一棵二叉树，假设叶子结点数为n0，度为1的结点数为n1，度为2的结点数为n2。则$n0+n1+n2=n$。又因为连接（分叉）数总等于总结点数n减1（除根结点外，每个子节点都要连接父结点），得到$n1+2*n2=n-1$。然后，联立方程得出结论。

性质4：具有n个节点的完全二叉树深为$\lfloor\log_2(x)\rfloor+1$（其中x表示不大于n的最大整数，$\lfloor\log_2(x)\rfloor$表示对计算结果向下取整）。

性质5：若对一棵有n个节点的完全二叉树进行顺序编号（1≤i≤n），那么，对于编号为i（i≥1）的节点：

- 当i=1时，该节点为根，它无双亲节点。
- 当i>1时，该节点的双亲节点的编号为i/2 。
- 若2i≤n，则有编号为2的左孩子，否则没有左孩子。
- 若2+1≤n，则有编号为2i+1的右孩子，否则没有右孩子。

### 二叉树遍历

> 1
> /   \
> 2    3
> \      \
> 5     4

前序（先序）遍历（PreOrderTraversal，NLR）：根左右，12534。

中序遍历：左根右（InOrderTraversal，LNR），25134。

后序遍历：左右根（PostOrderTraversal，LRN），52431。

广度优先（层次）遍历（Breadth First Search，BFS）：按层从左到右，12354。

深度优先遍历（Depth First Search，DFS）

已知前序遍历序列和中序遍历序列，可以唯一确定一课二叉树。

已知中序遍历序列和后序遍历序列，可以唯一确定一课二叉树。

已知前序遍历序列和后序遍历序列，**不能**唯一确定一课二叉树。

##### PHP 代码

树节点的定义

```php
/**
 * 二叉树结点
 * Class ErChaShuJieDian
 * @package App\ShuJuJieGou
 */
class ErChaShuJieDian
{
    /**
     * @var int [结点值]
     */
    public $jieDianZhi;

    /**
     * @var ErChaShuJieDian [左指针]
     */
    public $zuoZhiZhen;

    /**
     * @var ErChaShuJieDian [右指针]
     */
    public $youZhiZhen;

    /**
     * ErChaShuJieDian constructor.
     * @param $jieDianZhi [结点值]
     */
    public function __construct($jieDianZhi)
    {
        $this->jieDianZhi = $jieDianZhi;
        $this->zuoZhiZhen = null;
        $this->youZhiZhen = null;
    }
}
```

为了方便保存，我们用一个一维数组保存一棵二叉树。首先将二叉树用空结点补全成满二叉树，然后，从上到下，从左到右，依次编号后存入数组对应位置。如上文中的二叉树就可以表示成数组`[1,2,3,null,5,4,null]`，这也是二对叉树进行前序遍历的结果。将数组转换成二叉树的代码如下。

```php
/**
 * 二叉树
 * Class ErChaShu
 * @package App\ShuJuJieGou
 */
class ErChaShu
{
    /**
     * @var array [二叉树元素数组]
     */
    protected $yuanSuList;

    /**
     * @var ErChaShuJieDian [根结点]
     */
    protected $genJieDian;

    /**
     * ErChaShu constructor.
     * @param $yuanSuList [二叉树元素数组]
     */
    public function __construct($yuanSuList = [])
    {
        $this->yuanSuList = [];
        $this->genJieDian = null;

        if (is_array($yuanSuList) && count($yuanSuList) > 0) $this->setYuanSuList($yuanSuList);
    }

    /**
     * 设置二叉树元素数组
     * @param $yuanSuList
     */
    public function setYuanSuList($yuanSuList)
    {
        if (!is_array($yuanSuList)) return;
        $this->yuanSuList = $yuanSuList;
        $this->buildTreeWithArray();
        $this->qianXuBianLiXiuJian();
    }

    /**
     * 用数组构造二叉树
     */
    protected function buildTreeWithArray()
    {
        if (count($this->yuanSuList) === 0) return null;
        // 创建根结点
        $this->genJieDian = new ErChaShuJieDian($this->yuanSuList[0]);
        for ($i = 1, $numLen = count($this->yuanSuList); $i < $numLen; $i++) {
            // 依次添加结点
            $jieDian = new ErChaShuJieDian($this->yuanSuList[$i]);
            $this->chaRuJieDianByNLR($jieDian);
        }
    }

    /**
     * 获取二叉树
     * @return ErChaShuJieDian
     */
    public function getErChaShu()
    {
        return $this->genJieDian;
    }
}
```

插入结点的方法，这里用队列实现。

```php
    /**
     * 以先序遍历的顺序插入结点（根左右）
     * @param ErChaShuJieDian $jieDian
     */
    protected function chaRuJieDianByNLR($jieDian)
    {
        // 初始化一个队列
        $duiLie = [];
        // 根结点入队
        array_unshift($duiLie, $this->genJieDian);
        while (!empty($duiLie)) {
            // 持续遍历结点，直到队列为空
            // 队列元素出队
            $tempJieDian = array_pop($duiLie);
            if ($tempJieDian->zuoZiShu === null) {
                // 如果左结点为空就插入结点
                $tempJieDian->zuoZiShu = $jieDian;

                return;
            } else {
                // 左结点先入队
                array_unshift($duiLie, $tempJieDian->zuoZiShu);
            }
            if ($tempJieDian->youZiShu === null) {
                // 如果右结点为空就插入结点
                $tempJieDian->youZiShu = $jieDian;

                return;
            } else {
                // 右结点后入队
                array_unshift($duiLie, $tempJieDian->youZiShu);
            }
        }
    }
```

在构造完二叉树之后，我们要把填充的空叶子结点剪了。

```php
    /**
     * 使用前序遍历移除多余的空结点
     */
    protected function qianXuBianLiXiuJian()
    {
        $this->qianXuBianLiXiuJianDiGui($this->genJieDian);
    }

    /**
     * @param ErChaShuJieDian $genJieDian
     */
    protected function qianXuBianLiXiuJianDiGui($genJieDian)
    {
        if ($genJieDian === null) return;
        if ($genJieDian->jieDianZhi === null) return;
        if ($genJieDian->zuoZhiZhen !== null && $genJieDian->zuoZhiZhen->jieDianZhi === null)
            $genJieDian->zuoZhiZhen = null;
        else $this->qianXuBianLiXiuJianDiGui($genJieDian->zuoZhiZhen);
        if ($genJieDian->youZhiZhen !== null && $genJieDian->youZhiZhen->jieDianZhi === null)
            $genJieDian->youZhiZhen = null;
        else $this->qianXuBianLiXiuJianDiGui($genJieDian->youZhiZhen);
    }
```

前序遍历，中序遍历，后序遍历都采用递归实现。

```php
    /**
     * 前序遍历
     */
    public function qianXuBianLi()
    {
        return $this->qianXuBianLiDiGui($this->genJieDian);
    }

    /**
     * @param ErChaShuJieDian $genJieDian
     * @return string
     */
    protected function qianXuBianLiDiGui($genJieDian)
    {
        if ($genJieDian === null) return '';
        if ($genJieDian->jieDianZhi === null) return '';
        $xuLie = '';
        $xuLie .= $genJieDian->jieDianZhi . ';';
        $xuLie .= $this->qianXuBianLiDiGui($genJieDian->zuoZhiZhen);
        $xuLie .= $this->qianXuBianLiDiGui($genJieDian->youZhiZhen);
        return $xuLie;
    }

    /**
     * 中序遍历
     * @return string
     */
    public function zhongXuBianLi()
    {
        return $this->zhongXuBianLiDiGui($this->genJieDian);
    }

    /**
     * @param ErChaShuJieDian $genJieDian
     * @return string
     */
    protected function zhongXuBianLiDiGui($genJieDian)
    {
        if ($genJieDian === null) return '';
        if ($genJieDian->jieDianZhi === null) return '';
        $xuLie = '';
        $xuLie .= $this->zhongXuBianLiDiGui($genJieDian->zuoZhiZhen);
        $xuLie .= $genJieDian->jieDianZhi . ';';
        $xuLie .= $this->zhongXuBianLiDiGui($genJieDian->youZhiZhen);

        return $xuLie;
    }

    /**
     * 后序遍历
     */
    public function houXuBianLi()
    {
        return $this->houXuBianLiDiGui($this->genJieDian);
    }

    /**
     * @param ErChaShuJieDian $genJieDian
     * @return string
     */
    protected function houXuBianLiDiGui($genJieDian)
    {
        if ($genJieDian === null) return '';
        if ($genJieDian->jieDianZhi === null) return '';
        $xuLie = '';
        $xuLie .= $this->houXuBianLiDiGui($genJieDian->zuoZhiZhen);
        $xuLie .= $this->houXuBianLiDiGui($genJieDian->youZhiZhen);
        $xuLie .= $genJieDian->jieDianZhi . ';';

        return $xuLie;
    }
```

使用递归还可以计算二叉树的最大深度。

```php
    /**
     * 计算二叉树的最大深度
     * @return int
     */
    public function zuiDaShenDu()
    {
        return $this->zuiDaShenDuDiGui($this->genJieDian);
    }

    /**
     * @param ErChaShuJieDian $genJieDian
     * @return int
     */
    protected function zuiDaShenDuDiGui($genJieDian)
    {
        if ($genJieDian === null) return 0;
        if ($genJieDian->jieDianZhi === null) return 0;
        $zuoZhiZhen = $this->zuiDaShenDuDiGui($genJieDian->zuoZhiZhen);
        $youZhiZhen = $this->zuiDaShenDuDiGui($genJieDian->youZhiZhen);

        return max($zuoZhiZhen, $youZhiZhen) + 1;
    }
```

二叉树的广度优先算法，使用队列实现。

```php
    /**
     * 广度优先遍历
     * @return array
     */
    public function guangDuYouXianBianLi()
    {
        $xuLie = [];
        $duiLie = [];
        // 根结点入队
        array_unshift($duiLie, $this->genJieDian);
        while (!empty($duiLie)) {
            // 持续输出结点，直到队列为空
            // 队列元素出队
            $tempJieDian = array_pop($duiLie);
            // 存放结点数据
            if ($tempJieDian->jieDianZhi !== null) $xuLie[] = $tempJieDian->jieDianZhi;
            // 左结点先入队，先遍历
            if ($tempJieDian->zuoZhiZhen !== null) array_unshift($duiLie, $tempJieDian->zuoZhiZhen);
            // 右结点后入队，后遍历
            if ($tempJieDian->youZhiZhen !== null) array_unshift($duiLie, $tempJieDian->youZhiZhen);
        }

        return $xuLie;
    }
```

广度优先算法分层输出，还是使用队列实现，每一次while循环计算当前层要遍历几个结点，然后for循环会把当前层要遍历的结点全部遍历然后把下一层的结点全部放入队列。这也可以用来计算二叉树的最大深度。

```php
    /**
     * 广度优先遍历，可分层输出结果
     * @return array
     */
    public function guangDuYouXianBianLiFenCeng()
    {
        $xuLie = [];
        $cengShu = 1;
        $duiLie = [];
        // 根结点入队
        array_push($duiLie, $this->genJieDian);
        while ($length = count($duiLie)) {
            // 持续输出结点，直到队列为空
            for ($i = 0; $i < $length; $i++) {
                // 队列元素出队
                $tempJieDian = array_shift($duiLie);
                if ($tempJieDian->jieDianZhi !== null) $xuLie[$cengShu][] = $tempJieDian->jieDianZhi;
                // 左结点先入队，先遍历
                if ($tempJieDian->zuoZhiZhen !== null) array_push($duiLie, $tempJieDian->zuoZhiZhen);
                // 右结点后入队，后遍历
                if ($tempJieDian->youZhiZhen !== null) array_push($duiLie, $tempJieDian->youZhiZhen);
            }
            // 一层遍历结束，层数+1
            $cengShu++;
        }

        return $xuLie;
    }
```

除了广度优先遍历还可以使用深度优先遍历，与广度优先遍历使用队列不同的是，深度优先遍历要用到栈结构。

```php
    /**
     * 深度优先遍历
     * @return array
     */
    public function shenDuYouXianBianLi()
    {
        $xuLie = [];
        $zhan = [];
        // 根结点入栈
        array_push($zhan, $this->genJieDian);
        while (!empty($zhan)) {
            // 持续输出结点，直到栈为空
            // 栈顶元素出栈
            $tempJieDian = array_pop($zhan);
            // 存放结点数据
            if ($tempJieDian->jieDianZhi !== null) $xuLie[] = $tempJieDian->jieDianZhi;
            // 右结点先入栈，后遍历
            if ($tempJieDian->youZhiZhen !== null) array_push($zhan, $tempJieDian->youZhiZhen);
            // 左结点后入栈，先遍历
            if ($tempJieDian->zuoZhiZhen !== null) array_push($zhan, $tempJieDian->zuoZhiZhen);
        }

        return $xuLie;
    }
```

测试代码。

```php
$numList = ['A', 'B', 'C', null, 'D', null, 'E', null, null, 'F', 'I', null, null, 'J', null];
$erChaShu = new ErChaShu();
$erChaShu->setYuanSuList($numList);
echo json_encode($erChaShu->getErChaShu());
// echo json_encode($erChaShu->qianXuBianLi());
// echo json_encode($erChaShu->zhongXuBianLi());
// echo json_encode($erChaShu->houXuBianLi());
// echo json_encode($erChaShu->zuiDaShenDu());
// echo json_encode($erChaShu->guangDuYouXianBianLi());
// echo json_encode($erChaShu->guangDuYouXianBianLiFenCeng());
// echo json_encode($erChaShu->shenDuYouXianBianLi());
```

---

## 参考来源

《大话数据结构》，程杰 著，清华大学出版社，2011年6月第1版，2020年3月第24次印刷。

[百度文库--二叉树](https://baike.baidu.com/item/%E4%BA%8C%E5%8F%89%E6%A0%91)