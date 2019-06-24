一、 基本文本处理技能

1.分词的概念（分词的正向最大、逆向最大、双向最大匹配法）

（1）分词算法设计原则

- 颗粒度越大越好

- - 用于基于语义分析的文本分词
  - 分词结果颗粒度越大，即单词的字数越多，所表达的含义就越贴切

- 非词典词越少越好

- - 颗粒度大并不是没有上限的，那就会造成不切分的情况，故而此原则对颗粒度进行了一定的限制，即分词最佳情况是在词典中的最大词

- 总体词数越少越好

- - 相同的字数，词数越少语义单元越少，相对的单个语义单元权重越大，准确性越高

（2）最大匹配法：以词典为依据，取词典中最长单词长度作为第一次扫描字串的长度

（3）正向最大匹配法

- 原则：从前向后取词，每次减一个字，直到词典命中或只剩下一个字

- 例子：“今天的学习目标是特征提取”

- - 第一轮：

  - - “今天的学习目标是特征提取”：12字dict不命中
    - “今天的学习目标是特征提”：11字dict不命中
    - ......
    - “今天”：2字dict命中，扫描终止，输出“今天”

  - 第二轮：

  - - “的学习目标是特征提取”：11字dict不命中
    - ......
    - “的”：不命中，扫描终止，非字典词数+1，输出“的”

  - ......

  - 扫描结束，输出：[‘今天’‘的’‘学习目标’‘是’‘特征提取’]

（4）逆向最大匹配法

- 原则：从前往后提取词，其余逻辑与正向相同

（5）双向最大匹配法

- 正/逆向最大匹配法的局限性：“特别提取”逆向提取“特/别提取”
- 原则：两种方法都切一遍，根据颗粒度大和非字典词少原则进行选择

\2. 词、字符频率统计（可以使用Python中的collections.Counter模块，也可以自己寻找其他好用的库）

（1）实例化counter对象，可以像字典一样访问元素并返回出现的词数

from collections import Counter

names = ["Stanley","Lily","Bob","Well","Peter","Bob","Well","Peter","Well",\

​         "Peter","Bob","Stanley","Lily","Bob","Well","Peter","Bob","Bob",\

​         "Well", "Peter", "Bob", "Well"]

\# 实例化Counter对象，可接收任何hashable序列

\#Counter对象可以像字典一样访问元素并返回出现的次数｛“元素名”：出现次数｝

names_counts = Counter(names)

print(names_counts)

![img](https://app.yinxiang.com/FileSharing.action?hash=1/f8033736137755f2905d269942527c7c-66530)

（2）取出出现次数最多的前三个元素

\#取出出现次数最多的三个元素（元素名，出现次数）

top_three = names_counts.most_common(3)  

print(top_three)

![img](https://app.yinxiang.com/FileSharing.action?hash=1/6c787c30080a6377df5abc2fc58748f0-23781)

（3）update，substract，del更新待统计的序列

\#update更新待统计的序列

more_names = ["Stanley", "Lily", "Bob", "Well","Mosion"]

names_counts.update(more_names)

print(names_counts)

\#substract删除序列内容

names_counts.subtract(["Mosion"])

print(names_counts)

\#彻底删除键值

del names_counts['Mosion']

print(names_counts)

![img](https://app.yinxiang.com/FileSharing.action?hash=1/9e0866dbeff564bf726deff04a4f9ad9-69403)

二、n-gram语法模型

\1. 语言模型中unigram、bigram、trigram的概念

（1）n-gram模型：已知前面n-1个词w1,w2......wn-1出现，预测下一个词wn出现的概率，常见的n-gram有unigram,bigaram,trigram

![img](https://app.yinxiang.com/FileSharing.action?hash=1/91b4dcc77d035062056e2de2a6273d2f-3867)

（2）unigram（一元）

![img](https://app.yinxiang.com/FileSharing.action?hash=1/21de9b4eca81b6aa632d82d4728c2773-6515)

（3）bigram（二元）

![img](https://app.yinxiang.com/FileSharing.action?hash=1/954aa19d74f94c130b290d1bac2c78d1-8361)

（4）trigram（三元）

![img](https://app.yinxiang.com/FileSharing.action?hash=1/ffafefab9ab8dc6381ff6d63dce92a12-10431)

\2. unigram、bigram频率统计（可以使用Python中的collections.Counter模块，也可以自己寻找其他好用的库）

from collections import Counter

from functools import reduce

def unigram(cnt):

​    \#计算分词总数N

​    values = reduce(lambda x,y:x+y,list(cnt.values()))

​    unigram_values = zip(cnt,list(map(lambda x:x/values,cnt.values())))

​    return dict(unigram_values)

cnt = Counter(names)

print(cnt)

print(unigram(cnt))

![img](https://app.yinxiang.com/FileSharing.action?hash=1/49ae574c4ff9918d0efbdd4a0f56ca30-73951)



三、文本矩阵化：要求采用词袋模型且是词级别的矩阵化

1.分词（可采用结巴分词来进行分词操作，其他库也可以）

import jieba

test_content = content_list[0]

content = jieba.cut(test_content)

print(" ".join(list(content)))

![img](https://app.yinxiang.com/FileSharing.action?hash=1/46353b3b9b72c98400912239590f8107-121134)

\2. 去停用词；构造词表

\# 创建停用词list  

def stopwordslist(filepath):  

\#     stopwords = [line.strip() for line in open(filepath, 'r', encoding='utf-8').readlines()]  

​    stopwords = ["意外"]

​    return stopwords

\# 对句子进行分词  

def seg_sentence(sentence):  

​    sentence_seged = jieba.cut(sentence.strip())  

​    stopwords = stopwordslist('./test/stopwords.txt')  # 这里加载停用词的路径  

​    outstr = ''  

​    for word in sentence_seged:  

​        if word not in stopwords:  

​            if word != '\t':  

​                outstr += word  

​                outstr += " "  

​    return outstr  





content = seg_sentence(test_content)

print(content)

![img](https://app.yinxiang.com/FileSharing.action?hash=1/e5997cb790835a6880b42fd5027c986f-86904)

\3. 每篇文档的向量化

（1）向量化文本意义：将文本处理问题转化为机器学习问题

（2）举例：

原文：

​    　　句子A：我喜欢看电视，不喜欢看电影。

​    　　句子B：我不喜欢看电视，也不喜欢看电影。

分词结果：

​    　　句子A：我/喜欢/看/电视，不/喜欢/看/电影。

​    　　句子B：我/不/喜欢/看/电视，也/不/喜欢/看/电影。

列出维度：我，喜欢，看，电视，电影，不，也.

统计词频：

​    　　句子A：我 1，喜欢 2，看 2，电视 1，电影 1，不 1，也 0。

​    　　句子B：我 1，喜欢 2，看 2，电视 1，电影 1，不 2，也 1。

转换为向量：

​    　　句子A：[1, 2, 2, 1, 1, 1, 0]

​    　　句子B：[1, 2, 2, 1, 1, 2, 1]

可以看出：词频统计技术直观、简单。但是有明显的缺陷：中文中有的词汇，如：“我”，“的”出现频率很高，因此会赋予较高的权值，但是这些词汇本身无意义。因此若要使用词频统计技术，必须要引入停用词将这些无意义的词汇进行过滤