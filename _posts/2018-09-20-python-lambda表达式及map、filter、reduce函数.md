# lambda表达式
> lambda表达式是一个一行函数。它是一个匿名函数，如果一个函数只使用一次，其他地方不再使用，可以考虑使用它，它使代码更加的简洁。
## 原型
    lambda 参数：操作(参数)
## 示例:列表排序
    a = [(1, 2), (4, 1), (9, 10), (13, -3)]
    a.sort(key=lambda x: x[1])
    print(a)
    #  Output: [(13, -3), (4, 1), (1, 2), (9, 10)]
## 小结
* lambda定义了一个匿名函数
* lambda并不会带来程序运行效率的提高，只会使代码更简洁
* 大多数时候，lambda都可以使用for..in..if语句来代替
* lambda 语句常和map、reduce、filter函数一起使用
# map函数
> map函数会根据提供的函数对指定的序列做映射，function接收一个参数，序列中的每一个元素都调用一次函数。返回值是由每一次调用返回的值组成的list
## 原型
    map(function,sequence)
## 示例
    map(lambda x:x**2,[1,2,3,4])
    # outpupt: [1,4,9,16]
***
    def abc(a, b, c):
        return a*10000 + b*100 + c
    list1 = [11,22,33]
    list2 = [44,55,66]
    list3 = [77,88,99]
    map(abc,list1,list2,list3)
    [114477, 225588, 336699]
# fiter函数
> filter与map类似，执行的是过滤操作。function返回值是True/False。最后的结果里仅包含返回值为True的值。
## 原型
    filter(function,sequence)
## 示例
    def is_even(x):
        reutrn x & 1 !=0
    filter(is_even,[1,2,3,4,5])
    # output: [1,3,5]
# reduce函数
> reduce函数会对参数序列中元素进行累积。function接收两个参数。reduce依次从sequence中取一个元素，和上一次调用function的结果做参数再次调用function。
第一次调用function时，如果提供initial参数，会以sequence中的第一个元素和initial作为参数调用function，否则会以序列sequence中的前两个元素做参数调用function。
## 原型
    reduce(function,sequence),function接收两个参数
## 示例
    reduce(lambda x,y:x+y,[1,2,3,4,5])
    # output: 15
    reduce(lambda x,y:x+y,[1,2,3,4,5],1) # 给了初始值1
    # output: 16
   
