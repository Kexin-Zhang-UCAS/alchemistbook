# 科学打开github

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

## 使用`SwitchHosts`自动更新hosts文件
首先下载`SwitchHosts`并安装: [下载页面](https://hub.fastgit.org/oldj/SwitchHosts/releases/tag/v4.0.3)

ubuntu请选择`*.deb`

之后添加remote, 从指定url获取不断刷新的数据

![](https://windows-cdn.softpedia.com/screenshots/SwitchHosts-oldj_4.png)

可将自动更新改为15min一次

推荐的url:

```shell
https://gitee.com/Mosiki/github/raw/master/github_hosts.txt # 最推荐
https://gitee.com/ineo6/hosts/raw/master/hosts
https://raw.hellogithub.com/hosts
```



