map

mapPartitions

flatMap

groupby

mapValues

aggregate
```python
seqOp = (lambda x, y: (x[0] + y, x[1] + 1))
combOp = (lambda x, y: (x[0] + y[0], x[1] + y[1]))
sc.parallelize([1, 2, 3, 4]).aggregate((0, 0), seqOp, combOp)
# 输出: (10, 4)
```
zeroValue: 聚合操作初始值，确保它不会改变聚合操作的结果
seqOp函数中: x 代表当前的累积/聚合结果；y 代表当前分区中正在处理的元素。
combOp函数中: x 和 y 都是累积/聚合的结果，分别来自两个不同的分区。

join

cogroup
```python
rdd1 = sc.parallelize([("apple", 1), ("banana", 2), ("cherry", 3)])
rdd2 = sc.parallelize([("apple", 4), ("banana", 5), ("date", 6)])
rdd3 = sc.parallelize([("apple", 7), ("cherry", 8), ("date", 9)])

result = rdd1.cogroup(rdd2, rdd3)
# 输出
# [    ('banana', ([2], [5], [])),
#     ('cherry', ([3], [], [8])),
#     ('apple', ([1], [4], [7])),
#     ('date', ([], [6], [9]))
# ]
```
cogroup 通常用于那些需要按键组合*多个数据集*并处理*每个键的所有值*的场景，例如统计或数据转换。一个键可能对应多个值，所以返回list
outer join 通常用于那些需要合并两个数据集并对每个键的值进行某种操作的场景，例如数据清洗或数据整合。

repartition(num_partitions, "column_name")

```python
codec = "org.apache.hadoop.io.compress.GzipCodec"
sc.parallelize(['foo', 'bar']).saveAsTextFile(path3, codec)
```

