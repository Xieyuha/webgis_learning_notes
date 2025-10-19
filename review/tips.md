#简单是结果，复杂是过程
ArrayBuffer:只读的、空间连续的、定长字节数组
数组新api
    arr.toSorted((a,b)=>a-b)//不改变原数组，返回一个新排序后的数组
    arr.toReversed()//不改变原数组，返回一个新反转后的数组
    arr.with(index,value)//不改变原数组，返回一个新数组，index位置替换为value
    arr.toSpliced(start,deleteCount,...items)//不改变原数组，返回一个新数组，从start位置删除deleteCount个元素，并插入items元素
URL本质:获取资源的地址字符串

