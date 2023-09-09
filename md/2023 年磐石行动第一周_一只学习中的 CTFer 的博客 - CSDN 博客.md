> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/weixin_44394805/article/details/131393789?spm=1001.2014.3001.5502)

### 1、MISC-trainee

题目如下：

```
新来的实习生将VIP客户的名单录入系统的时候有几处错误，请你帮他校对纠正一下。database.txt中是公司的所有客户名单，customer.txt是VIP客户。
举例：admin（正确的单词）—>adcin（错误的单词），找到的答案为c
将找到的所有答案拼接，并用md5加密，即为flag。

```

exp 如下：

```
import string
import hashlib

with open('./database.txt','r') as f:
    database_buff = f.readlines()

with open('./username_vip.txt','r') as f:
    vip_buff = f.readlines()


databases = []
vips = []

for database in database_buff:
    databases.append(database.strip())

for vip in vip_buff:
    vips.append(vip.strip())

#step1:找到不在database中的用户名
error_name_vips = []

for vip in vips:
    if vip not in databases:
        error_name_vips.append(vip)

#step2:找出错误的字母

char_buff = string.ascii_letters
flag = ''

for vip in error_name_vips:
    find_one = False
    for index in range(len(vip)):
        first_name = vip[:index]
        last_name = vip[index+1:]
        for mid_name in char_buff:
            right_name = first_name+mid_name+last_name
            if right_name in databases:
                flag += vip[index]
                find_one = True
                break
        if find_one:
            break

print(hashlib.md5(flag.encode()).hexdigest())

```

### 2、WEB-zhimale

题目为众筹的网站  
![](https://img-blog.csdnimg.cn/64620826ef624619b354abe3ba82e2d9.png#pic_center)  
开始查了芝麻乐 CMS，发现并没有公开的漏洞，然后开始研究网站的功能

登录部分发现三个功能为登录、注册、修改密码

可以给项目投资，不过需要有钱

同时还有转入和转出功能，发现转入和转出功能有问题，估计考点不在这个地方

然后随便注册一个手机号登录进去，18812341234

![](https://img-blog.csdnimg.cn/179439de5a924bffa79fb7e14cc31975.png#pic_center)  
然后发现这个提示，那么需要通过 18888888888 账号进行登录才能获得 flag

那么需要知道 1888888888 的密码，想起来修改密码的那个功能

![](https://img-blog.csdnimg.cn/bbc86621a56848e591d5fab3a4ff928f.png#pic_center)

然后发现直接使用 188 手机号无法获取到验证码，但是自己的手机号可以获取到验证码，

![](https://img-blog.csdnimg.cn/b62865e2a5e140a99fdf659c47e3fae4.png#pic_center)

然后这个时候把用户的密码修改，就可以将任意账号的密码修改，这里存在一个[逻辑漏洞](https://so.csdn.net/so/search?q=%E9%80%BB%E8%BE%91%E6%BC%8F%E6%B4%9E&spm=1001.2101.3001.7020)，可以任意用户密码

![](https://img-blog.csdnimg.cn/217f66969afd4b54be889332a8499270.png#pic_center)  
成功将用户的密码修改为`qwe@123`

这个地方会自动登录进去，但是不显示用户名，需要退出之后重新登录一下，否则后面看不到 flag  
![](https://img-blog.csdnimg.cn/6673a83be3034dbd9ba695bf51570472.png#pic_center)  
这个地方正常显示了`18888888888`用户名

![](https://img-blog.csdnimg.cn/ad762b268dff48b6ac9cff6bc5d9f7b1.png#pic_center)  
从资金明细发现投资过三个爸爸 1000 元。

![](https://img-blog.csdnimg.cn/9a9d5513a89d4ff7aa4c169b9b11cd1d.png#pic_center)  
然后继续投资两次后发现没钱之后就不可以继续投资

然后分析下相关逻辑

```
投资份数*金额 < 目前资金
剩余资金 =  目前资金 - 投资份数*金额 

```

那么如果校验不严格，可以通过负数的投资份额和金额使得剩余的资金增加

通过测试发现份数不能为负数，同时不能超过项目剩余的份数

三个爸爸项目总共需要 4500000 元，一份是 1000 元，那么共计 4500-2=4498 份，同时将金额设置为 - 1000，应该就可以得到 4500000 元。

![](https://img-blog.csdnimg.cn/04cfbf04376649de8e650aa196e8da09.png#pic_center)

发现用户已经有 450 万元。

![](https://img-blog.csdnimg.cn/a7fe9fb038614b4d9597e74603d43f5e.png#pic_center)

![](https://img-blog.csdnimg.cn/82381d8e9e2b4cbb838b28524a460d8b.png#pic_center)  
然后得到 flag，`DASCTF{08009800269248961777982938438909}`