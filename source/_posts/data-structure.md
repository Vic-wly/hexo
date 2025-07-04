title: 数据结构总结
author: Laiyong Wang
tags:
  - 数据结构
categories: []
date: 2023-05-10 16:07:00
---
#### 本文只介绍数据结构的基本概念、优缺点及适用场景，语言差异或者特殊用法会与本文有区别，例如数组的介绍与 PHP 中数组的区别，队列的只能队尾删除等，因使用场景存在队首插入优先消费等

#### 数组

数组（Array）是一种线性数据结构，用于存储固定大小的相同类型元素的集合。数组中的元素通过索引访问，索引通常从0开始。

**优点：**

1. 快速的随机访问：通过索引，可以直接访问数组中的任意元素，因此访问速度非常快，时间复杂度为O(1)。
2. 内存连续：数组中的元素在内存中是连续存储的，这样可以更高效地利用计算机的缓存机制，提高访问速度。

**缺点：**

1. 大小固定：数组的大小在创建时就确定了，无法动态扩展或缩小。如果需要存储的元素数量超过了数组的大小，就需要重新创建一个更大的数组并进行数据迁移。
2. 插入和删除较慢：在数组中插入或删除元素通常需要移动其他元素，因此这些操作的时间复杂度较高，为O(n)，其中n是数组的大小。

**适用场景：**

1. 快速访问元素：当需要频繁访问数组中的元素，而不需要频繁插入、删除元素时，数组是一个很好的选择。例如，遍历元素、查找最大/最小值、计数等操作。
2. 元素数量固定：当元素数量已知且不会经常变化时，可以使用数组来存储数据，因为数组的大小是固定的。

数组提供了快速的随机访问和连续内存存储的优势，适用于需要快速访问元素且元素数量固定的场景。由于大小固定和插入/删除较慢的特性，需要根据具体需求来选择是否使用数组。

#### 链表

链表（Linked List）是一种由节点组成的线性数据结构，每个节点包含数据和指向下一个节点的指针（或引用）。节点通过指针连接在一起，形成链式结构。

**优点：**

1. 动态大小：链表的大小可以根据需要动态增长或缩小，不像数组需要预先确定大小。
2. 高效的插入和删除：在链表中插入或删除节点非常高效，只需要修改相邻节点的指针，不需要移动其他节点。
灵活性：链表可以轻松地进行节点的插入、删除和重排，适应动态的数据操作。

**缺点：**

1. 随机访问慢：链表的访问需要从头节点开始逐个遍历，因此访问特定位置的节点时，时间复杂度为O(n)，其中n是链表的长度。
2. 额外的内存消耗：链表中每个节点需要额外的指针来指向下一个节点，因此相对于数组，链表会占用更多的内存空间。

**适用场景：**

1. 频繁的插入和删除操作：当需要频繁进行插入和删除操作，而对访问操作的性能要求较低时，链表是一个很好的选择。例如，实现队列、栈，或在某些算法中需要动态维护数据集合的情况。
2. 动态大小：当元素数量不确定或会频繁变化时，链表可以根据需要动态调整大小，而不需要预先分配固定大小的内存空间。

链表在插入和删除操作上具有较高的效率和灵活性，并且支持动态大小的调整。但是链表的随机访问性能较差，占用较多的内存空间。因此，在根据具体需求进行选择时，需要权衡其优点和缺点。

#### 栈

栈（Stack）是一种后进先出（Last-In-First-Out，LIFO）的线性数据结构，仅允许在一端进行插入和删除操作，该端称为栈顶。

**优点：**

1. 简单易实现：栈的操作非常简单，包括入栈（Push）和出栈（Pop）操作，实现起来比较容易。
2. 插入和删除操作快速：由于只能在栈顶进行插入和删除操作，所以这些操作的时间复杂度为O(1)，即常数时间。

**缺点：**

1. 中间元素不可访问：在栈的数据结构中，只有栈顶元素是可访问的，而中间元素需要通过连续的出栈操作才能访问到。
2. 有限容量：栈的容量是有限的，当栈已满时，再进行入栈操作将导致栈溢出的错误。

**适用场景：**

1. 撤销操作：栈常被用于实现撤销操作，例如文本编辑器中的撤销功能，最近的操作存储在栈中，可以通过出栈操作撤销一步步的操作。
2. 表达式求值：在表达式求值中，栈常被用于处理括号匹配和操作符优先级，通过入栈和出栈操作可以按照正确的顺序计算表达式的值。
3. 深度优先搜索（DFS）：在图的遍历算法中，深度优先搜索常常使用栈来实现，通过入栈和出栈操作来控制遍历顺序。

栈是一种简单且高效的数据结构，适用于需要快速插入和删除元素的场景，尤其适用于撤销操作、表达式求值和深度优先搜索等应用。但需注意栈的容量限制和中间元素不可直接访问的特点。

- PHP 简单实现代码
```
class Stack {
    private $stack;

    public function __construct() {
        $this->stack = [];
    }

    public function push($item) {
        array_push($this->stack, $item);
    }

    public function pop() {
        if ($this->isEmpty()) {
            return null;
        }
        return array_pop($this->stack);
    }

    public function peek() {
        if ($this->isEmpty()) {
            return null;
        }
        return end($this->stack);
    }

    public function isEmpty() {
        return empty($this->stack);
    }
}

// 创建一个栈实例
$stack = new Stack();

// 入栈操作
$stack->push('A');
$stack->push('B');
$stack->push('C');

// 出栈操作
$item = $stack->pop();
echo "Popped item: " . $item . "\n";

// 获取栈顶元素
$topItem = $stack->peek();
echo "Top item: " . $topItem . "\n";

// 检查栈是否为空
$isStackEmpty = $stack->isEmpty();
echo "Is stack empty? " . ($isStackEmpty ? "Yes" : "No") . "\n";
```

上述示例中，通过 `Stack` 类实现了栈的基本操作。使用 `$stack` 数组来存储栈中的元素，通过调用相应的函数来进行入栈、出栈、获取栈顶元素以及检查栈是否为空。

在示例中，首先创建了一个空栈实例，然后进行了入栈操作，依次将元素 A、B 和 C 入栈。接着进行出栈操作，弹出了栈顶的元素，并进行了输出。然后获取了栈顶元素，并进行了输出。最后检查了栈是否为空，并进行了输出。

这只是一个简单的示例，用于说明如何用 PHP 实现栈的基本操作。在实际应用中，可以根据需要扩展和优化栈的实现，添加更多的功能和方法，以满足具体的需求。

#### 队列

队列（Queue）是一种先进先出（First-In-First-Out，FIFO）的线性数据结构，类似于现实生活中排队的概念。元素只能从一端（称为队尾）插入，从另一端（称为队首）删除。

**优点：**

1. 简单易实现：队列的操作相对简单，包括入队（Enqueue）和出队（Dequeue）操作，实现起来比较容易。
2. 插入和删除操作快速：由于只能在队尾进行插入，在队首进行删除，这些操作的时间复杂度为O(1)，即常数时间。

**缺点：**

1. 中间元素不可访问：在队列的数据结构中，只能访问队首和队尾的元素，中间的元素需要通过出队操作逐步移除才能访问到。
2. 有限容量：队列的容量是有限的，当队列已满时，再进行入队操作将导致队列溢出的错误。

**适用场景：**

1. 广度优先搜索（BFS）：在图的遍历算法中，广度优先搜索常常使用队列来实现，通过入队和出队操作来控制遍历顺序。
2. 任务调度：队列常用于任务调度的场景，例如多线程或多进程环境中的任务队列，确保任务按照先后顺序依次执行。
3. 缓冲区管理：队列可以用于实现缓冲区，例如网络通信中的消息队列，确保消息按照接收顺序进行处理。

队列是一种简单且高效的数据结构，适用于需要按照先进先出顺序处理元素的场景。它在广度优先搜索、任务调度和缓冲区管理等应用中有广泛的应用。然而，需注意队列的容量限制和中间元素不可直接访问的特点。

- PHP 简单实现代码
```
class Queue {
    private $queue;

    public function __construct() {
        $this->queue = [];
    }

    public function enqueue($item) {
        array_push($this->queue, $item);
    }

    public function dequeue() {
        if ($this->isEmpty()) {
            return null;
        }
        return array_shift($this->queue);
    }

    public function peek() {
        if ($this->isEmpty()) {
            return null;
        }
        return $this->queue[0];
    }

    public function isEmpty() {
        return empty($this->queue);
    }
}

// 创建一个队列实例
$queue = new Queue();

// 入队操作
$queue->enqueue('A');
$queue->enqueue('B');
$queue->enqueue('C');

// 出队操作
$item = $queue->dequeue();
echo "Dequeued item: " . $item . "\n";

// 获取队头元素
$frontItem = $queue->peek();
echo "Front item: " . $frontItem . "\n";

// 检查队列是否为空
$isQueueEmpty = $queue->isEmpty();
echo "Is queue empty? " . ($isQueueEmpty ? "Yes" : "No") . "\n";
```

上述示例中，通过 `Queue` 类实现了队列的基本操作。使用 `$queue` 数组来存储队列中的元素，通过调用相应的函数来进行入队、出队、获取队头元素以及检查队列是否为空。

在示例中，首先创建了一个空队列实例，然后进行了入队操作，依次将元素 A、B 和 C 入队。接着进行出队操作，弹出了队头的元素，并进行了输出。然后获取了队头元素，并进行了输出。最后检查了队列是否为空，并进行了输出。

这只是一个简单的示例，用于说明如何用 PHP 实现队列的基本操作。在实际应用中，可以根据需要扩展和优化队列的实现，添加更多的功能和方法，以满足具体的需求。

#### 树

树（Tree）是一种非线性的数据结构，由节点和边组成，具有层次结构。树的一个节点可以连接到多个子节点，但每个子节点只能有一个父节点。树结构常用于表示具有层次关系的数据。

**优点：**

1. 层次结构：树的结构使得数据的组织和表示更具有层次性，适合表示具有父子关系的数据，例如文件系统的目录结构、组织机构图等。
2. 高效的搜索和插入：树的结构使得在树中搜索和插入元素的操作效率较高，时间复杂度通常为O(log n)，其中n是树中节点的数量。
3. 递归特性：树的递归特性使得树的操作和算法设计更加灵活和简洁。

**缺点：**

1. 树的平衡性：树的平衡性对于维持搜索和插入操作的效率至关重要。如果树的平衡性失衡，可能导致性能下降，需要进行平衡操作。
2. 内存消耗：树结构需要使用额外的指针来连接节点，可能占用较多的内存空间。

**适用场景：**

1. 层次性数据：当数据具有层次性结构时，树是一种常用的数据结构，例如文件系统、XML/JSON解析树等。
2. 搜索和排序：树的结构使得在其中进行搜索和排序操作效率较高，例如二叉搜索树（Binary Search Tree）用于快速的搜索和排序操作。
3. 分类和分类：决策树（Decision Tree）常用于分类和回归问题，树的结构可以帮助组织决策规则。

树是一种具有层次结构和高效搜索插入操作的数据结构。它适用于表示层次性数据、搜索和排序操作以及分类和回归问题等应用场景。但需注意树的平衡性和内存消耗。不同类型的树，如二叉树、红黑树、B树等，可以根据具体需求选择合适的树结构。

- PHP 简单实现代码

```
class TreeNode {
    public $data;
    public $children;

    public function __construct($data) {
        $this->data = $data;
        $this->children = [];
    }

    public function addChild(TreeNode $node) {
        $this->children[] = $node;
    }
}

// 创建一个树的示例
$root = new TreeNode('A');

// 创建子节点
$child1 = new TreeNode('B');
$child2 = new TreeNode('C');
$child3 = new TreeNode('D');

// 将子节点添加到根节点
$root->addChild($child1);
$root->addChild($child2);
$root->addChild($child3);
```

在上述示例中，通过 TreeNode 类实现了树节点的基本操作。每个节点包含一个数据项和一个存储子节点的数组。使用 addChild 方法将子节点添加到父节点的子节点数组中。

示例中创建了一个简单的树，根节点为 A，有三个子节点 B、C 和 D。使用 addChild 方法将子节点添加到根节点的子节点数组中。

这只是一个简单的示例，用于说明如何用 PHP 实现树的基本操作。在实际应用中，可以根据需要扩展和优化树的实现，添加更多的功能和方法，以满足具体的需求。例如，可以实现树的遍历算法（如前序遍历、后序遍历、层次遍历等），以及其他树操作（如查找节点、删除节点等）。

需要注意的是，上述示例只展示了树的基本结构，实际使用中可能需要更多的属性和方法来满足具体的应用场景。树是一种非常灵活的数据结构，可以用于各种场景，例如组织结构、目录结构、分类系统等。

另开文章分别介绍各种树  --todo


#### 图

图（Graph）是一种非线性的数据结构，由节点（顶点）和边组成。图的节点表示实体，边表示节点之间的关系。图可以用于表示各种实际问题中的关联和连接。

简要介绍图的优缺点和适用场景如下：

**优点：**
1. 表示关系：图能够准确地表示实体之间的各种关系和连接，例如社交网络中的用户关系、网络中的节点连接等。
2. 强大的建模能力：图可以用于建模各种复杂的问题，例如路由网络、计算机网络、交通网络等。
3. 多种遍历算法：图上有多种遍历算法，如深度优先搜索（DFS）和广度优先搜索（BFS），可以对图进行各种分析和搜索操作。

**缺点：**
1. 存储和计算复杂性：由于图中节点和边的关系复杂，存储和计算的复杂性也相应增加。
2. 遍历复杂性：在大型图中进行全局遍历操作可能会很耗时，因为可能需要访问大量的节点和边。

**适用场景：**
1. 社交网络：图常用于表示社交网络，其中人或实体作为节点，关系（如好友关系、关注关系）作为边，用于分析社交网络结构、推荐算法等。
2. 路由网络：图可以用于表示路由网络，其中路由器和连接线路作为节点和边，用于优化路由路径、网络拓扑分析等。
3. 组织关系图：图可用于表示组织结构、项目流程等，其中员工或任务作为节点，关系作为边，用于分析组织关系、工作流程等。

图是一种强大的数据结构，适用于表示和解决各种关联和连接的问题。它具有建模能力强、遍历算法丰富等优点，但存储和计算复杂性较高。在具体应用时，需要考虑图的规模和复杂性，并选择适当的图算法和数据结构来处理。

- PHP 简单实现代码
```
class Graph {
    private $vertices;
    private $edges;

    public function __construct() {
        $this->vertices = [];
        $this->edges = [];
    }

    public function addVertex($vertex) {
        $this->vertices[$vertex] = [];
    }

    public function addEdge($vertex1, $vertex2) {
        $this->edges[$vertex1][] = $vertex2;
        $this->edges[$vertex2][] = $vertex1;
    }

    public function getVertices() {
        return array_keys($this->vertices);
    }

    public function getEdges($vertex) {
        return isset($this->edges[$vertex]) ? $this->edges[$vertex] : [];
    }
}

// 创建一个图实例
$graph = new Graph();

// 添加顶点
$graph->addVertex('A');
$graph->addVertex('B');
$graph->addVertex('C');
$graph->addVertex('D');

// 添加边
$graph->addEdge('A', 'B');
$graph->addEdge('B', 'C');
$graph->addEdge('C', 'D');
$graph->addEdge('D', 'A');

// 获取顶点和边
$vertices = $graph->getVertices();
$edges = $graph->getEdges('A');

// 输出结果
echo "Vertices: " . implode(', ', $vertices) . "\n";
echo "Edges from A: " . implode(', ', $edges) . "\n";
```

上述示例中，通过 `Graph` 类实现了图的表示和操作。使用 `$vertices` 数组来存储图的顶点，每个顶点作为数组的键。使用 `$edges` 数组来存储图的边，其中每个顶点作为键，对应的值为与该顶点相连的其他顶点的数组。

通过调用 `addVertex` 方法可以添加顶点，通过调用 `addEdge` 方法可以添加边。`getVertices` 方法用于获取所有顶点，`getEdges` 方法用于获取指定顶点的边。

在上述示例中，创建了一个简单的图，其中包含顶点 A、B、C 和 D，以及相应的边。然后，通过调用相应的方法获取了图的顶点和从顶点 A 出发的边，并进行了输出。

这只是一个简单的示例，用于说明如何用 PHP 来表示和操作图。在实际应用中，可以根据需要扩展和优化图的实现，添加更多的功能和方法，以满足具体的需求。

#### 哈希表

哈希表（Hash Table）是一种基于哈希函数（Hash Function）实现的数据结构，用于存储键值对（Key-Value Pairs）。它通过将键映射到数组中的索引位置来快速访问和操作数据。

**优点：**
1. 快速的数据访问：哈希表使用哈希函数将键转换为唯一的索引位置，因此可以在常数时间复杂度内（O(1)）进行插入、删除和查找操作，即使数据量很大也能保持高效性能。
2. 灵活的键值对存储：哈希表可以存储任意类型的键值对，使其非常适合于各种应用场景。
3. 高效的内存利用：哈希表的大小可以根据需要动态调整，使其能够灵活地利用内存。

**缺点：**
1. 哈希冲突：不同的键可能映射到相同的索引位置，这称为哈希冲突。解决冲突需要使用额外的机制，如开放地址法、链表或再哈希法，这可能会导致性能下降。
2. 空间开销：为了处理哈希冲突，可能需要额外的存储空间来存储链表或其他冲突解决机制，这可能会增加空间开销。

**适用的场景：**
1. 数据存储和检索：哈希表适用于需要快速存储和检索键值对的场景。例如，缓存系统可以使用哈希表来存储经常访问的数据，以提高访问速度。
2. 数据唯一性检查：由于哈希表中的键是唯一的，可以用于检查数据的唯一性。例如，在用户管理系统中，可以使用哈希表来存储已注册的用户名，以确保用户名的唯一性。
3. 查找表和索引结构：哈希表可用于构建查找表和索引结构，以加快数据的查找和检索操作。例如，数据库管理系统中的索引可以使用哈希表实现。
4. 字典和映射：由于哈希表存储键值对，它非常适合实现字典和映射的数据结构。例如，可以使用哈希表存储国家和对应的首都。


**注意点：**

哈希表在极端情况下可能会发生较大的哈希冲突，从而导致性能下降。在设计和使用哈希表时，选择合适的哈希函数和冲突解决策略是非常重要的，以确保哈希表的高效性和稳定性。

- PHP 简单实现代码(PHP数组其实就是一种特殊的散列表，也使用了散列表来进行实现)
```
// 创建一个哈希表
$hashTable = [];

// 向哈希表添加键值对
$hashTable['name'] = 'John';
$hashTable['age'] = 30;
$hashTable['city'] = 'New York';

// 访问哈希表中的值
echo "Name: " . $hashTable['name'] . "\n";
echo "Age: " . $hashTable['age'] . "\n";
echo "City: " . $hashTable['city'] . "\n";
```
在上述示例中，我们通过创建一个空的关联数组 `$hashTable` 来表示哈希表。然后，使用键值对的方式将数据存储到哈希表中，键是唯一的且用于快速查找对应的值。

通过使用 `$hashTable['key']` 的形式，我们可以访问哈希表中特定键的值。在示例中，我们访问了键为 `name`、`age` 和 `city` 的值，并进行了输出。

哈希表的优点在于其快速的插入、删除和查找操作。通过散列函数将键转换为索引，可以在常数时间内访问和修改对应的值。然而，哈希表的缺点是可能存在哈希冲突，即不同的键可能映射到相同的索引位置，需要处理冲突的情况，如使用链表解决冲突或使用更高级的解决方案，例如开放地址法或再哈希法。

哈希表在许多场景中都非常有用，例如缓存系统、索引结构、键值存储等。在 PHP 中，关联数组提供了简单而方便的方式来实现和使用哈希表。

#### 堆

堆（Heap）是一种特殊的树状数据结构，具有以下特点：
- 堆是一个完全二叉树（Complete Binary Tree）或近似完全二叉树，即除了最后一层外，其他层都是完全填充的，且最后一层的节点尽可能靠左排列。
- 堆中的每个节点都满足堆属性（Heap Property），即父节点的值（或优先级）大于等于（或小于等于）其子节点的值，这被称为最大堆（Max Heap）或最小堆（Min Heap）的性质。

**优点：**
- 快速的插入和删除操作：堆的结构使得在插入和删除元素时具有较低的时间复杂度。在最大堆中，插入和删除的时间复杂度为O(log n)，其中n是堆中的元素数量。
- 高效的获取最值：堆可以迅速获取最大值或最小值。在最大堆中，根节点始终是最大值；在最小堆中，根节点始终是最小值。获取最值的时间复杂度为O(1)。
- 适用于动态数据集：堆对于需要在不断变化的数据集中快速查找最值的场景非常有用，例如优先级队列、调度算法等。

**缺点：**
- 不支持快速的查找和遍历：堆的结构不适合快速查找特定元素或进行遍历操作。如果需要频繁进行查找或遍历，使用其他数据结构（如哈希表或平衡二叉搜索树）可能更合适。
- 不支持动态变化优先级：堆中元素的优先级在插入后不易更改。如果需要频繁地修改元素的优先级，堆可能不是最适合的数据结构。

**适用的场景：**
- 优先级队列：堆常用于实现优先级队列，其中元素按照优先级顺序进行插入和删除操作。
- 调度算法：堆可用于调度算法中的任务调度，根据任务的优先级进行排序和选择。
- 图算法：堆可用于图算法中的最短路径算法（如Dijkstra算法）或最小生成树算法（如Prim算法）等。

需要注意的是，堆分为最大堆和最小堆两种类型，具体使用哪种类型取决于问题的要求。在实际应用中，可以根据具体场景选择合适的堆来满足需求。

#### 字典

字典（Dictionary）是一种存储键值对（Key-Value Pairs）的数据结构，也称为映射（Map）、关联数组（Associative Array）或哈希表（Hash Table）。它提供了根据键快速查找对应值的能力，类似于现实生活中的字典，通过查找关键字（键）可以找到对应的定义（值）。

在字典中，每个键都是唯一的，而值可以是任意类型的数据。字典提供了基本的操作，如插入、删除和查找等。

**优点：**
- 快速的查找和访问：字典通过哈希表或其他高效的数据结构实现，可以在常数时间复杂度（O(1)）内查找和访问对应的值，无论字典的大小如何。
- 灵活的数据存储：字典的值可以是任意类型的数据，包括基本类型（如整数、字符串）和复杂类型（如数组、对象），使其非常适合存储和操作各种类型的数据。

**缺点：**
- 不保持顺序：字典通常不保持键的顺序，即插入键值对的顺序和键的排序可能不同。如果需要有序的字典，需要使用特定的数据结构，如有序字典。
- 内存消耗较大：由于字典需要维护键和值的对应关系，可能需要额外的内存空间来存储索引和哈希表等结构，因此相比简单的数组来说，字典可能占用更多的内存。

**适用的场景：**
- 键值对存储：字典适用于需要存储和检索键值对的场景。例如，存储用户信息、配置参数、词汇表等。
- 数据索引：字典可用于构建索引结构，加快对数据的查找和检索操作。例如，在数据库管理系统中，使用字典索引可以快速定位和访问记录。
- 数据映射和转换：字典可用于实现数据的映射和转换。例如，将一个数据集中的某个属性作为键，对应的属性值作为值，可以通过字典快速查找和获取对应的数据。

需要注意的是，不同编程语言和框架中，字典的具体实现方式可能会有所不同，但核心思想是存储键值对并提供高效的查找和访问能力。在选择具体的字典实现时，可以根据语言和应用的需求来选择适合的数据结构。


