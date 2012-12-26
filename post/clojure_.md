## clojure知识点

   apply函数的最后一个参数最好不要是lazy-seq, 因为它会把lazyseq转化成object[]对象，这是就会计算出序列中的所有对象，失去了lazyseq的优点，而且可能浪费极大的内存