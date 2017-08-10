
* LDA工具<br>
  https://github.com/liuzhiqiangruc/dml/tree/master/tm
* 获取数据<br>
```
hive -e "
select a.user_id, a.item_id, a.preference
from
(
   ...
)
" > input_lda
```

* 数据概况
  * 基础数据获取：见hql
  * 数据整理：cat input_lda | awk -F"\t" '{ print $1"\t"$2 }' > input
  * 数据形式：user_id \t item_id （后期可考虑tf-idf优化）
  * 行数：1849296
  * 用户数：678588
  * 游戏数：3377
* 运行命令
```
./lda -a 0.2 -b 0.01 -k 50 -n 1000 -s 100 -d ./input -o ./output

    参数说明:
     --------------------------------------------
           -t               算法类型1:基本lda，2:lda-collective，3:lda_time
           -r               运行模式，1:建模，2:burn-in
           -a               p(z|d) 的 Dirichlet 参数
           -b               p(w|z) 的 Dirichlet 参数
           -k               Topic个数
           -n               迭代次数
           -s               每多少次迭代输出一次结果
           -d               输入数据
           -o               输出文件目录,实现需要存在

  运行时长：10分钟左右
```
* 关联名称<br>
  * 处理word_topic矩阵，将ID和名称关联起来，为lda.csv
```
Hql如下，
set hive.exec.compress.output=false;
create table xxxx
(
    id  int
) row format delimited
fields terminated by '\t';

load data local inpath '/output/f_word_topic' OVERWRITE  into table xxxx;
```
* Item2Item计算
```
mport sys
import math
import heapq

items_D = {} ## key: id

def load_data():
    global items_D
    inFp = open("lda_norm_10.csv", 'r')
    while True:
        line = inFp.readline()
        if not line:
            break
        items = line.strip().split(',')
        if len(items) != 54:
            continue
        item_D = {}
        item_D['soft_package_name'] = items[0]
        item_D['name'] = items[1]
        item_D['id'] = int(items[2])
        item_D['topics'] = map(float, items[3:53])
        item_D['sum'] = float(items[53])
        items_D[item_D['id']] = item_D


def dis1(A, B):
    return sum( A['topics'][i] * B['topics'][i] for i in range(50))

def dis2(A, B):
    return sum( 100 - abs(A['topics'][i] - B['topics'][i]) for i in range(50))

def search_similar():
    while True:
        line = sys.stdin.readline()
        idx = int(line.strip())
        itemX = items_D[idx]
        sim = -1.0
        for idy, itemy in items_D.items():
            simy = dis1(items_D[idx], items_D[idy])
            if (simy > sim or sim < 0) and idx!=idy:
                sim = simy
                itemY = itemy
        print "%s\tass\t%s"%(itemX['name'], itemY['name'])

load_data()
search_similar()
```

* 效果展示<br>
![Local Image](../../gitbook/images/LDA原理和实践/302.png)<br>
