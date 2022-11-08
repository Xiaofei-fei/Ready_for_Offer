# [LRU缓存](https://leetcode.cn/problems/lru-cache)

> 请你设计并实现一个满足  LRU (最近最少使用) 缓存 约束的数据结构。
> 实现 LRUCache 类：
> LRUCache(int capacity) 以 正整数 作为容量 capacity 初始化 LRU 缓存
> int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
> void put(int key, int value) 如果关键字 key 已经存在，则变更其数据值 value ；如果不存在，则向缓存中插入该组 key-value 。如果插入操作导致关键字数量超过 capacity ，则应该 逐出 最久未使用的关键字。
> 函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

```python
class Dlinklist:
    def __init__(self,key=0,value=0):
        self.key=key
        self.value=value
        self.pre=None
        self.next=None
class LRUCache:
    def __init__(self, capacity: int):
        self.capacity=capacity
        self.cache=dict()
        self.head=Dlinklist()
        self.tail=Dlinklist()
        self.head.next=self.tail
        self.tail.pre=self.head
        self.size=0

    def get(self, key: int) -> int:
        if key not in self.cache:return -1
        node=self.cache[key]
        self.moveTohead(node)
        return node.value
    def put(self, key: int, value: int) -> None:
        if key not in self.cache:
            node=Dlinklist(key,value)
            self.cache[key]=node
            self.addTohead(node)
            self.size+=1
            if self.size>self.capacity:
                remove=self.removeTail()
                self.cache.pop(remove.key)
                self.size-=1
        else:
            node=self.cache[key]
            node.value=value
            self.moveTohead(node)
    def removeNode(self,node):
        node.pre.next=node.next
        node.next.pre=node.pre
    def addTohead(self,node):
        node.pre=self.head
        node.next=self.head.next
        self.head.next.pre=node
        self.head.next=node
    def moveTohead(self,node):
        self.removeNode(node)
        self.addTohead(node)
    def removeTail(self):
        node=self.tail.pre
        self.removeNode(node)
        return node
```

