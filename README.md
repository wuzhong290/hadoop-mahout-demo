一、安装mahout
1、下载http://archive.cloudera.com/cdh5/cdh/5/mahout-0.9-cdh5.7.0.tar.gz
由于下载很慢，使用如下
http://download.csdn.net/detail/knight_black_bob/9454519
scp mahout-0.9-cdh5.5.0.tar.gz  root@192.168.120.129:/usr/local/mahout-0.9-cdh5.5.0.tar.gz
2、解压:tar -xvf mahout-0.9-cdh5.5.0.tar.gz
3、改名:mv mahout-0.9-cdh5.5.0 mahout
4、vi /root/.bashrc
export MAHOUT_HOME=/usr/local/mahout
export CLASSPATH=.:$CLASSPATH:$MAHOUT_HOME/lib
export PATH=$PATH:$MAHOUT_HOME/bin

source /root/.bashrc

二、hadoop和mahout整合的例子：
1、准备数据
scp ml-100k.zip  root@192.168.120.129:/home/wuzhong/ml-100k.zip
unzip ml-100k.zip
cd ml-100k
hadoop fs -put u.data u.data
2、执行命令
hadoop jar /usr/local/mahout/mahout-examples-0.9-cdh5.5.0-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob -s SIMILARITY_COOCCURRENCE --input u.data --output cf_output
3、查看结构
hadoop fs -cat output/part-r-00000

[root@master ml-100k]# hadoop fs -cat output/part-r-00000
1     [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
2     [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
3     [284:5.0,508:4.8327136,285:4.828125,248:4.8235292,845:4.769231,319:4.7066326,124:4.7045455,150:4.6862745,311:4.6769233,272:4.6497545]
4     [347:5.0,151:5.0,234:5.0,275:5.0,121:5.0,12:5.0,237:5.0,895:5.0,282:5.0,195:5.0]
5     [275:5.0,79:5.0,514:5.0,282:5.0,121:5.0,12:5.0,258:5.0,237:5.0,234:5.0,117:5.0]
6     [275:5.0,117:5.0,237:5.0,282:5.0,121:5.0,12:5.0,258:5.0,515:5.0,234:5.0,195:5.0]
7     [417:5.0,418:5.0,403:5.0,517:5.0,62:5.0,176:5.0,682:5.0,209:5.0,137:5.0,315:5.0]
8     [275:5.0,79:5.0,514:5.0,282:5.0,121:5.0,12:5.0,515:5.0,237:5.0,234:5.0,117:5.0]
9     [275:5.0,79:5.0,514:5.0,282:5.0,121:5.0,12:5.0,258:5.0,237:5.0,234:5.0,117:5.0]
10    [462:5.0,275:5.0,515:5.0,514:5.0,234:5.0,282:5.0,129:5.0,258:5.0,237:5.0,121:5.0]
11    [895:5.0,282:5.0,515:5.0,514:5.0,462:5.0,234:5.0,129:5.0,88:5.0,237:5.0,275:5.0]
12    [275:5.0,12:5.0,237:5.0,234:5.0,121:5.0,255:5.0,258:5.0,515:5.0,462:5.0,117:5.0]
13    [209:5.0,77:5.0,47:5.0,582:5.0,226:5.0,403:5.0,682:5.0,264:5.0,156:5.0,417:5.0]
14    [462:5.0,275:5.0,515:5.0,514:5.0,234:5.0,282:5.0,129:5.0,258:5.0,237:5.0,121:5.0]
15    [234:5.0,347:5.0,515:5.0,895:5.0,282:5.0,275:5.0,129:5.0,258:5.0,237:5.0,121:5.0]


hadoop fs -getmerge output output.txt

https://chimpler.wordpress.com/2013/02/20/playing-with-the-mahout-recommendation-engine-on-a-hadoop-cluster/




mahout RecommenderJob 参数含义
--input(path): 存储用户偏好数据的目录，该目录下可以包含一个或多个存储用户偏好数据的文本文件；

--output(path): 结算结果的输出目录

--similarityClassname (classname): 向量相似度计算类，可选的相似度算法包括CityBlockSimilarity，CooccurrenceCountSimilarity，CosineSimilarity，CountbasedMeasure，EuclideanDistanceSimilarity，LoglikelihoodSimilarity，PearsonCorrelationSimilarity, TanimotoCoefficientSimilarity。注意参数中要带上包名。

--usersFile (path): 指定一个包含了一个或多个存储userID的文件路径，仅为该路径下所有文件包含的userID做推荐计算 (该选项可选)

--itemsFile (path): 指定一个包含了一个或多个存储itemID的文件路径，仅为该路径下所有文件包含的itemID做推荐计算 (该选项可选)

--filterFile (path): 指定一个路径，该路径下的文件包含了[userID,itemID]值对，userID和itemID用逗号分隔。计算结果将不会为user推荐[userID,itemID]值对中包含的item (该选项可选)

--maxPrefsPerUser (integer): 在最后计算推荐结果的阶段，针对每一个user使用的偏好数据的最大数量，默认为10

--maxSimilaritiesPerItem (integer): 针对每个item的相似度最大值，默认为100

--minPrefsPerUser (integer): 在相似度计算中，忽略所有偏好数据量少于该值的用户，默认为1

--maxPrefsPerUserInItemSimilarity (integer): 在item相似度计算阶段，针对每个用户考虑的偏好数据最大数量，默认为1000

--threshold (double): 忽略相似度低于该阀值的item，默认值为4.9E-324

用hadoop运行hadoop jar mahout-core-0.9-job.jar org.apache.mahout.cf.taste.hadoop.item.RecommenderJob --input /user/mahout/file --output /user/mahout/result --tempDir /tmp --similarityClassname org.apache.mahout.math.hadoop.similarity.cooccurrence.measures.LoglikelihoodSimilarity