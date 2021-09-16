# 如何科学地打开github

github网页加载或者`git clone`不了, 该怎么办?

最简单的是使用镜像网站[https://hub.fastgit.org/](https://hub.fastgit.org/)

但是这无法登录自己的账号

另一种就是修改github.com的hosts, 直接使用稳定的ip代替域名

那么有哪些稳定的ip和哪些域名需要ping呢?

下面介绍方法

## 步骤

1. 打开[https://ping.cn/ping](https://ping.cn/ping)或[https://ping.chinaz.com/](https://ping.chinaz.com/)

2. 搜索如下的一些网址: 一般只需要搜`github.com`就行了

   ```shell
   # domain: github.com
   github.com   # 最重要
   nodeload.github.com
   api.github.com
   codeload.github.com
   raw.github.com
   training.github.com
   assets-cdn.github.com
   documentcloud.github.com
   help.github.com
   # domain: githubstatus.com
   githubstatus.com
   
   # domain: fastly.net
   github.global.ssl.fastly.net
   
   # domain: githubusercontent.com
   raw.githubusercontent.com
   cloud.githubusercontent.com
   gist.githubusercontent.com
   marketplace-screenshots.githubusercontent.com
   repository-images.githubusercontent.com
   user-images.githubusercontent.com
   desktop.githubusercontent.com
   avatars.githubusercontent.com
   avatars0.githubusercontent.com
   avatars1.githubusercontent.com
   avatars2.githubusercontent.com
   avatars3.githubusercontent.com
   avatars4.githubusercontent.com
   avatars5.githubusercontent.com
   avatars6.githubusercontent.com
   avatars7.githubusercontent.com
   avatars8.githubusercontent.com
   ```

3. 搜索后可以找到对应的最合适的ip

4. 将ip与域名写入hosts文件中, 其中在ubuntu的hosts文件路径是`/etc/hosts`

   ```shell
   13.229.188.59 github.com
   ```

5. 之后就可以快乐的连接github了

## 进阶

可以写一个脚本`github.py`随时刷新 需要`pip install eventlet`

采用的github ip池可以自己爬取

```python
import re
import subprocess
from multiprocessing import Process, Pool, Manager
import eventlet
import random
import copy
github_ips = ["140.82.113.3",
              "140.82.121.3",
              "20.205.243.166",
              "13.250.177.223",
              "215.164.81.167",
              "140.82.112.3",
              "52.74.223.119",
              "140.82.114.4",
              "15.164.81.167",
              "140.82.114.3",
              "140.82.113.4",
              "13.229.188.59",
              "52.192.72.89",
              "140.82.112.4",
              "13.114.40.48",
              "66.249.89.104"]


def check_alive(ip, vlist: Manager().list = None, time_limit=2):
    eventlet.monkey_patch()
    with eventlet.Timeout(time_limit, False):
        try:
            result = subprocess.Popen("ping -c 1 %s | grep '0 received' | wc -l" % ip, stdin=subprocess.PIPE,
                                      stdout=subprocess.PIPE,
                                      stderr=subprocess.PIPE, shell=True).stdout.read()
            if result == b'0\n':
                # print(ip,"能ping通",result)
                if vlist != None:
                    vlist.append(ip)
                return True
            else:
                return False
        except:
            return False


def fine_ips(ips):
    proc_list = []
    vlist = Manager().list()
    for i in ips:
        p = Process(target=check_alive, args=(i, vlist))
        proc_list.append(p)
        p.start()
    for i in proc_list:
        i.join()
    print("可用ip:", vlist)


def find_1st_ip(ips):
    for ip in ips:
        if check_alive(ip, time_limit=1):
            # print(ip, "可用")
            return ip
    # else:
    #     print("所有ip都不能用了:(")

def write_in_ubuntu_hosts():
    ips=copy.deepcopy(github_ips)
    random.shuffle(ips)
    ip = find_1st_ip(ips)
    if ip != None:
        with open("/etc/hosts", "r") as f:
            c = f.read()
            if re.findall("(.*) github.com", c) != None:
                a = re.sub("(.*) github.com", "%s github.com" % ip, c)
            else:
                a = c + "\n%s github.com\n"%ip
    print(a)
    with open("hosts","w") as f:
        print(a,file=f)



if __name__ == '__main__':
    write_in_ubuntu_hosts()
```

运行上述脚本会产生一个`hosts`文件

肉眼观察运行结果是否正确后 输入一下命令 就可以科学打开`github`

```shell
python github.py
sudo mv hosts /etc/hosts
```

# 网站

## 资源网站

1. [MAC萌新网](https://www.macxin.com/blog.html): Mac/Win/Linux计算机/数学/物理/化学软件
2. [hub.fastgit.org](hub.fastgit.org): github镜像网站 可用于 `git clone`
3. [图表可视化](https://zhuanlan.zhihu.com/p/255023280)

## 工具网站

1. [Detexify](https://detexify.kirelabs.org/classify.html): $\LaTeX$符号查询网站

2. [app.diagrams.net](app.diagrams.net): 神经网络绘制网站, 例如如下著名的attension图

   ![例如论文: Attention is all you need 架构图使用该网站绘制](https://connpass-tokyo.s3.amazonaws.com/thumbs/7f/91/7f919a0dd6e46457b46d4cffd214920a.png)

## 学习网站

1. [pytorch](https://pytorch.org/): 科研界主流神经网络架构
2. [tensorflow](https://tensorflow.google.cn/api_docs?hl=zh-cn): 工业界主流神经网络架构之一
3. [JAX](https://hub.fastgit.org/google/jax): Google的新机器学习框架雏形?
4. [pytorch-lightning](https://www.pytorchlightning.ai/): pytorch深度包装
5. [DGL](https://docs.dgl.ai/): torch相关图神经网络库
6. [OpenAI transformers](https://huggingface.co/transformers/index.html): 阅读手册研读transformer
7. [Tokenizers](https://huggingface.co/docs/tokenizers/python/latest/): 词牌分割库
8. [NLTK](http://www.nltk.org/): NLP主流处理库之一
9. [spaCy](https://spacy.io/): NLP主流处理库之一

> NLP处理库很多... 用熟一个即可

8. [openmm](https://openmm.org/): C++/python分子动力学库
9. [pyscf](https://pyscf.org/): python量子化学库

# python进阶与库学习

1. C/C++基础还是要的

   > 主要是面对对象编程思想
   >
   > 重载的思想

2. python进阶

   > 魔法方法
   >
   > 装饰器
   >
   > 封装继承多态
   >
   > 生成器和迭代器
   >
   > 包

3. 基础库

   > 1. multiprocessing
   >
   > 2. logging
   >
   > 3. functools
   >
   > 4. threading
   >
   > 5. time
   >
   > 6. re

4. 常用python库:

   > 1. networkx
   > 2. pandas
   > 3. numpy
   > 4. scipy
   > 5. sympy
   > 6. jupyter



# conda常用命令





