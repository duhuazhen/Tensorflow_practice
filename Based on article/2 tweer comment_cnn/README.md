使用的数据集：http://help.sentiment140.com/for-students/ (情绪分析)<br>
数据集包含1百60万条推特，包含消极、中性和积极tweet。<br>
数据格式：移除表情符号的CSV文件，字段如下：<br>


0 – the polarity of the tweet (0 = negative, 2 = neutral, 4 = positive)<br>
1 – the id of the tweet (2087)<br>
2 – the date of the tweet (Sat May 16 23:58:44 UTC 2009)<br>
3 – the query (lyx). If there is no query, then this value is NO_QUERY.<br>
4 – the user that tweeted (robotickilldozr)<br>
5 – the text of the tweet (Lyx is cool)<br>
training.1600000.processed.noemoticon.csv（238M）<br>
testdata.manual.2009.06.14.csv（74K）<br>
1)使用data.py处理数据;<br>
2)使用train.py训练数据并保存模型；<br>
3)使用prediction.py预测模型；<br>
4)使用cnn卷积神经网络训练。<br>
卷积神经网络简介<br>
讲的比较好的一些博客：https://www.zhihu.com/question/39022858<br>
   https://mp.weixin.qq.com/s?__biz=MzA3MzI4MjgzMw==&mid=2650728746&idx=1&sn=61e9cb824501ec7c505eb464e8317915&scene=0#wechat_redirect<br>
   改变某个同学人生的网站(cnn)：http://scs.ryerson.ca/~aharley/vis/conv/<br>
  基于TensorFlow的神经网络演示:https://playground.tensorflow.org/#activation=tanh&batchSize=10&dataset=circle&regDataset=reg-plane&learningRate=0.03&regularizationRate=0&noise=35&networkShape=4,2,2&seed=0.89675&showTestData=false&discretize=false&percTrainData=50&x=true&y=true&xTimesY=true&xSquared=true&ySquared=true&cosX=false&sinX=true&cosY=false&sinY=true&collectStats=false&problem=classification&initZero=false&hideText=false <br>github地址：https://github.com/tensorflow/playground
