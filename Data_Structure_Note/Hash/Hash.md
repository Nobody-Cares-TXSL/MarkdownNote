# HashMap
## **哈希表的简单实现**
## 一.哈希冲突
**哈希冲突改良方法“链式地址”和“开放寻址”。**

1. 链式地址（separate chaining）将单个元素转换为链表，将键值对作为链表节点，将所有发生冲突的键值对都存储在同一链表中。
- 链式地址的局限性：占用空间大，查询效率低

***
2. 开放寻址（open addressing）线性探测
- 线性探测采用固定步长的线性搜索来进行探测，通过线性探测，它们被依次存储在该桶以及之下的桶中。
- 线性探测容易产生“聚集现象”：数组中连续被占用的位置越长，这些连续位置发生哈希冲突的可能性越大，从而进一步促使该位置的聚堆生长，形成恶性循环，最终导致增删查改操作效率劣化。
- 不能在开放寻址哈希表中直接删除元素，会使数组内产生一个空桶（None），当进行线性探测查询到该空桶会返回，导致该空桶一下的元素都无法访问到，可能会导致误判这些元素不存在。
- 采用懒删除（lazy deletion）机制：它不直接从哈希表中移除元素，而是利用一个常量 TOMBSTONE 来标记这个桶。在该机制下，None 和 TOMBSTONE 都代表空桶，都可以放置键值对。但不同的是，线性探测到 TOMBSTONE 时应该继续遍历，因为其之下可能还存在键值对。
- 懒删除可能会加速哈希表的性能退化。这是因为每次删除操作都会产生一个删除标记，随着 TOMBSTONE 的增加，搜索时间也会增加，因为线性探测可能需要跳过多个 TOMBSTONE 才能找到目标元素。
- 优化懒删除：在线性探测中记录遇到的首个 TOMBSTONE 的索引，并将搜索到的目标元素与该 TOMBSTONE 交换位置。这样做的好处是当每次查询或添加元素时，元素会被移动至距离理想位置（探测起始点）更近的桶，从而优化查询效率。    
- 总结: 开放寻址固定步长线性搜索,不能直接删除元素,用常量TOMBSTONE标记要删除的桶,代表空桶,可以遍历,探测时记录首个删除标记,并将搜索到的目标元素桶位置与之交换位置.  

```cpp {.line-numbers}
    //搜索key对应的桶索引
    int findBucket(int key)
    {
        int index = hashFunc(key);
        int firstTombstone = -1;
        //线性探测,遇到空桶跳出
        while(buckets[index] != nullptr)
        {
            //遇到key值,返回桶索引
            if(buckets[index]->key == key)
            {
                //若之前遇到了删除标记，则将键值对移动至该索引处
                if(firstTombstone != -1)
                {
                    buckets[firstTombstone] = buckets[index];
                    buckets[index] = TOMBSTONE;
                    return firstTombstone; //返回移动后桶索引
                }
                return index; //返回桶索引
            }
            //记录遇到的首个删除标记
            if(firstTombstone == -1 && buckets[index] == TOMBSTONE)
            {
                firstTombstone = index;
            }
            //计算桶索引,越过尾部则返回头部
            index = (index + 1) % capacity;
        }
        //若key不存在,则返回添加点的索引
        return firstTombstone == -1 ? index : firstTombstone;
    }
```
