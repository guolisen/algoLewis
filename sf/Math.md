https://www.3blue1brown.com/

# 先验概率 后验概率

https://blog.csdn.net/passball/article/details/5859878

https://zhuanlan.zhihu.com/p/26464206

# 条件概率

https://www.zhihu.com/question/27462939/answer/232983694

![img](D:\code\sf\条件概率.png)

# 贝叶斯

https://zhuanlan.zhihu.com/p/149853224

![img](https://pic3.zhimg.com/80/v2-3a02b5486c758945f9634c2ed49f66aa_720w.jpg)

-   P(A∩B) = P(A)*P(B|A)
-   P(A∩B) = P(B)*P(A|B)

# 中心极限定理

中心极限定理有两个要点：样本的平均值与总体的平均值类似；样本的平均值呈现正态分布。

https://zhuanlan.zhihu.com/p/349480793

1.  如果我们知道了某个群体的信息，就能够推测出从这个群体中抽取的随机样本的情况。

>   比如我们知道一个学校所有学生（1000名）的平均成绩和标准差，那么从中抽取100名学生的平均成绩与所有学生的平均成绩相差不会很大。



\2. 如果我们知道了抽取的样本的信息，就可以对群体信息作出精确的推理。

>   比如我可以根据抽取的100名学生的平均成绩和标准差，去推测全校学生的平均成绩。



\3. 如果我们知道了样本的信息，以及群体的信息，我们就能推断出样本是否从该群体中抽取的样本。

>   比如我们知道100个学生的平均成绩，和某所学校全体学生的平均成绩，那么根据中心极限定理，我们就可以推测中这100个学生属于这个学校的概率有多大，就可以判断这100个样本学生是否该学校的学生。



\4. 如果我们知道两个样本的信息，就能推断出这两个样本是否来自同一个群体。

>   比如我们抽取了一组100名学生，知道了他们的平均成绩和标准差；然后又抽取了另一组100名学生，也知道了他们的平均成绩和标准差，我们就可以推测出这200个学生是否来自同一所学校。



**｜举个例子：**

已知某所学校全部学生（1000名）的平均成绩是90分，标准差是4；假设有100名学生的平均成绩是95分，需要你判断是不是该学校的学生，那么应该如何判断呢？

先计算样本的标准误差SE=4/10=0.4，则根据中心极限定理样本的平均值有68%的可能性在89.6-90.4分之间，有95%的可能性在89.2-90.8分之间，有99.7%的可能性在88.8-91.2分之间。

而现在100个样本的平均值为95分与总体平均值90分的差距，是标准误差的3倍多，也就是说这100个样本只有0.3%的可能性属于整体（即这100个学生不太可能属于这个学校的学生）。





**（1）约有68%的样本平均值会在群体平均值一个标准误差的范围之内；**
**（2）约有95%的样本平均值会在群体平均值的两个标准误差的范围之内；**
**（3）约有99.7%的样本平均值会在群体平均值三个标准误差的范围之内。**

# 大数定理



# 极大似然估计

https://zhuanlan.zhihu.com/p/26614750



# 马尔可夫

**马尔可夫链就是这样一个任性的过程，它将来的状态分布只取决于现在，跟过去无关**

https://www.zhihu.com/question/37588564/answer/2464763647

P(i, j) 是条件概率，p(i ,j) = P(Xn+1 = j | Xn = i) 



# RBM MCMC

https://www.cnblogs.com/peghoty/p/3798500.html

# 向量叉积

![img](D:\code\sf\叉积.jpg)

![img](D:\code\sf\叉积公式.png)

https://zhuanlan.zhihu.com/p/148780358

# word2vec

https://www.cnblogs.com/peghoty/p/3857839.html

https://baijiahao.baidu.com/s?id=1672173952676536197&wfr=spider&for=pc

https://www.cnblogs.com/wujingqiao/p/8978652.html

http://mccormickml.com/2016/04/19/word2vec-tutorial-the-skip-gram-model/

应用

https://www.cnblogs.com/hebin/p/3507609.html

https://www.cnblogs.com/wowarsenal/p/3293586.html

https://blog.csdn.net/zhaoxinfan/article/details/10403917

https://www.cnblogs.com/tina-smile/p/5178549.html



doc2vec

embedding:

https://blog.csdn.net/weixin_44493841/article/details/95341407

https://zhuanlan.zhihu.com/p/164502624



CBOW

http://www.360doc.com/content/20/0301/12/46886353_895906230.shtml



--------------------------------------

https://datawhalechina.github.io/fun-rec/#/ch02/ch2.1/ch2.1.2/word2vec

http://linanqiu.github.io/2015/10/07/word2vec-sentiment/

https://cs.stanford.edu/~quocle/paragraph_vector.pdf

https://radimrehurek.com/gensim/auto_examples/tutorials/run_doc2vec_lee.html

https://blog.csdn.net/lenbow/article/details/52120230?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ETopBlog-1-52120230-blog-88378984.topblog&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ETopBlog-1-52120230-blog-88378984.topblog&utm_relevant_index=1

```
# coding:utf-8
 
import sys
import gensim
import sklearn
import numpy as np
import gensim.models.doc2vec
#from gensim.models.deprecated.doc2vec import LabeledSentence
from gensim.models.doc2vec import TaggedDocument

TaggededDocument = gensim.models.doc2vec.TaggedDocument
 
def get_datasest():
    with open("wangyi_title.txt", 'r', encoding='utf-8') as cf:
        docs = cf.readlines()
        print(len(docs))
 
    x_train = []
    #y = np.concatenate(np.ones(len(docs)))
    for i, text in enumerate(docs):
        word_list = text.split(' ')
        l = len(word_list)
        word_list[l-1] = word_list[l-1].strip()
        document = TaggededDocument(word_list, tags=[i])
        x_train.append(document)
 
    return x_train
 
def getVecs(model, corpus, vector_size):
    vecs = [np.array(model.docvecs[z.tags[0]].reshape(1, vector_size)) for z in corpus]
    return np.concatenate(vecs)
 
def train(x_train, vector_size=200, epoch_num=1):
    model_dm = gensim.models.Doc2Vec(x_train,min_count=1, window = 3, vector_size = vector_size, sample=1e-3, negative=5, workers=4)
    model_dm.train(x_train, total_examples=model_dm.corpus_count, epochs=70)
    model_dm.save('model_dm_wangyi')
 
    return model_dm
 
def test():
    model_dm = gensim.models.Doc2Vec.load("model_dm_wangyi")
    test_text = ['《', '舞林', '争霸' '》', '十强' '出炉', '复活', '舞者', '澳门', '踢馆']
    inferred_vector_dm = model_dm.infer_vector(test_text)
    print(inferred_vector_dm)
    sims = model_dm.docvecs.most_similar([inferred_vector_dm], topn=10)
 
 
    return sims
 
if __name__ == '__main__':
    x_train = get_datasest()
    model_dm = train(x_train)
 
    sims = test()
    for count, sim in sims:
        sentence = x_train[count]
        words = ''
        for word in sentence[0]:
            words = words + word + ' '
        print (words, sim, len(sentence[0]))
```

  \>>> import nltk  

>>> nltk.download('punkt')
>>> 

conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/

conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
https://www.csdn.net/tags/MtjaUgysNzYwNjQtYmxvZwO0O0OO0O0O.html

https://blog.csdn.net/liujh845633242/article/details/101595856?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-101595856-blog-88378984.topblog&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-101595856-blog-88378984.topblog&utm_relevant_index=2

http://linanqiu.github.io/2015/10/07/word2vec-sentiment/

Doc2Vec requires us to build the vocabulary table (simply digesting all the words and filtering out the unique words, and doing some basic counts on them



https://rare-technologies.com/doc2vec-tutorial/

# 特征值和特征向量

从定义出发，Ax=cx：A为矩阵，c为特征值，x为特征向量。

矩阵A乘以x表示，对向量x进行一次转换（旋转或拉伸）（是一种线性转换），而该转换的效果为常数c乘以向量x（即只进行拉伸）。

我们通常求特征值和特征向量即为求出该矩阵能使哪些向量（当然是特征向量）只发生拉伸，使其发生拉伸的程度如何（特征值大小）。这样做的意义在于，看清一个矩阵在那些方面能产生最大的效果（power），并根据所产生的每个特征向量（一般研究特征值最大的那几个）进行分类讨论与研究。

https://www.zhihu.com/question/20507061/answer/16610027



# One-Hot   TF-IDF

https://blog.csdn.net/qq_44186838/article/details/118425070

https://blog.csdn.net/qq_44186838/article/details/117995029?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165228183816782184636242%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165228183816782184636242&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-3-117995029-null-null.142^v9^pc_search_result_control_group,157^v4^new_style&utm_term=word2vec&spm=1018.2226.3001.4187





# 二分类问题

https://www.jianshu.com/p/70bcb4415ed5
