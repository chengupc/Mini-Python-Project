***From [基于Selenium一键写CSDN博客](http://blog.csdn.net/mrlevo520/article/details/51932076)***



- Python 2.7
- IDE Pycharm 5.0.3
- Firefox 47.0.1

>  至于Selenium等环境配置，则请看[ Python+Selenium+PIL+Tesseract真正自动识别验证码进行一键登录](http://blog.csdn.net/mrlevo520/article/details/51901579)



---



起因
--
> ​	我发现CSDN的博客很奇怪，不知道大家发觉没有，即使是记住账号的，点击自己的博客，然后操作步骤是，写博客，再点击重新输入账号密码(虽然浏览器记住了)，但是还要操作一回，如果没有上述步骤，那也需要再点击‘切换到MarkDown编辑器 ’然后才可以开始写博客，干的事情从点击浏览器开始，然后打开了三个浏览器子页面，一个是登录主页，一个是自己的博客主页，一个就是编辑博客界面了---我TM竟然重复操作了那么多回，我真的好愚钝啊！早该写个脚本了！！

----------

目的
--
1. 一键登录直接撰写CSDN博客
2. 程序健壮性增量，添加其余五个候选项
3. 打包程序，供没有安装python环境的小伙伴使用（firefox必须要有）[如何将python打包成exe请点击这里](http://blog.csdn.net/mrlevo520/article/details/51840217)

----------

实现方案
----
> Python+Selenium自动化脚本+Firefox的联合使用+Pyinstaller打包成exe

----------

实现代码
----

###  第一部分--一键登录写博客

```
# -*- coding: utf-8 -*-
#Author:哈士奇说喵
#CSDN--实现一键写博客

from selenium import webdriver
import time

#shift-tab多行缩进(左)
print 'Please wait...Firefox loading...'
print '---------------------------------'
#reload(sys)

PostUrl = "https://passport.csdn.net/account/login?from=http://my.csdn.net/my/mycsdn"
driver=webdriver.Firefox()#用浏览器实现访问
#driver = webdriver.PhantomJS(executable_path="phantomjs.exe")#没用浏览器
driver.get(PostUrl)


#账号填充输入
elem_user = driver.find_element_by_id('username')
elem_psw = driver.find_element_by_id('password')

#可以自己修改登录名和账户密码，我自己的隐去了
elem_user.send_keys('mrlevo@outlook.com')
elem_psw.send_keys('xxxxxxxx')

#点击登录
click_login = driver.find_element_by_xpath("//input[@class='logging']")
click_login.click()
print 'log in...'
print '---------------------------------'
time.sleep(1)

#先点击写博客图标，不然元素隐藏
click_wbic = driver.find_element_by_xpath("//ul[@class='btns']/li[5]/div")
click_wbic.click()
print 'jumping...'
print '---------------------------------'

click_choice = driver.find_element_by_xpath("//div[@class='wrap clearfix']/dl/dt[4]/a")
click_choice.click()#将点击操作放在内部比较好
#定位新页面元素，将handle重定位即可
driver.switch_to_window(driver.window_handles[1])#定位弹出的第一个页面，也就是当前页面
click_markdown = driver.find_element_by_xpath("//p[@class='subtit']/a")
click_markdown.click()
print '---------------------------------'
print 'here we go!'
#关闭无关页面，也可以根据自己喜好保留无关页面
driver.close()#关闭第二个页面，也就是一般编辑器下的CSDN
driver.switch_to_window(driver.window_handles[0])#关闭第1个页面，也就是登录主页
driver.close()

```
> ​	实现画面我就用动图表示了，最后达到的结果就是，关闭无关页面，（当然你也可以选择不关闭），直接将页面停留在编辑页面下，直接可进行编辑操作，省去中间一大串非人操作。。。

![这里写图片描述](http://img.blog.csdn.net/20160717141027248)

![这里写图片描述](http://img.blog.csdn.net/20160717140442699)

----------

### 第二部分--选择项目操作



```
# -*- coding: utf-8 -*-
#Author:哈士奇说喵
#CSDN--实现一键写博客/传资源/写代码片/etc..

from selenium import webdriver
import time

#shift-tab多行缩进(左)
print 'Please wait...Firefox loading...'
print '---------------------------------'
#reload(sys)

PostUrl = "https://passport.csdn.net/account/login?from=http://my.csdn.net/my/mycsdn"
driver=webdriver.Firefox()#用浏览器实现访问
#driver = webdriver.PhantomJS(executable_path="phantomjs.exe")#没用浏览器
driver.get(PostUrl)


#账号填充输入
elem_user = driver.find_element_by_id('username')
elem_psw = driver.find_element_by_id('password')
elem_user.send_keys('mrlevo@outlook.com')
elem_psw.send_keys('xxxxxxx')

#点击登录
click_login = driver.find_element_by_xpath("//input[@class='logging']")
click_login.click()
print 'log in...'
print '---------------------------------'
time.sleep(1)

#先点击写博客图标，不然元素隐藏
click_wbic = driver.find_element_by_xpath("//ul[@class='btns']/li[5]/div")
click_wbic.click()
print 'jumping...'
print '---------------------------------'
choice = input("what do you want?\n1-put ppt\n2-ask question\n3-write blog\n4-put resource\n5-create project\n6-create codes\nplease enter num:")
click_choice = driver.find_element_by_xpath("//div[@class='wrap clearfix']/dl/dt[%s]/a"%(choice+1))

#根据点击操作后的观察可知，对于除了写博客之外的其余项目，只需要点击一次即可
if choice==3 :
    click_choice.click()#将点击操作放在内部比较好
    #定位新页面元素，将handle重定位即可
    driver.switch_to_window(driver.window_handles[1])#定位弹出的第一个页面，也就是当前页面
    click_markdown = driver.find_element_by_xpath("//p[@class='subtit']/a")
    click_markdown.click()
    print '---------------------------------'
    print 'here we go!'
    #关闭无关页面，也可以根据自己喜好保留无关页面
    driver.close()#关闭第二个页面，也就是一般编辑器下的CSDN
    driver.switch_to_window(driver.window_handles[0])#关闭第1个页面，也就是登录主页
    driver.close()

else:
    click_choice.click()
    print '---------------------------------'
    print 'here we go!'
    driver.switch_to_window(driver.window_handles[0])#关闭第1个页面，也就是登录主页
    driver.close()
```
> ​	这里需要的是在cmd窗口进行选择项目操作，我这里只写了其中的六个项目，好吧，其实我是用不到的，我就象征性的扩充了一下程序，万一有别的小伙伴要用到呢，反正目前我打开博客最多的就是写博客，所以我应该只会用第一部分代码做的exe。

![这里写图片描述](http://img.blog.csdn.net/20160717140049760)

----------


Pay Attention
---

1.点击exe后，cmd窗口出现后，点一下cmd窗口，让它保持在活动窗口最前面，当然你也可以被浏览器覆盖后自己找cmd在哪。

2.注意在操作过程中，不要动浏览器，特别是在选择写博客那一栏，动一下，那一栏就隐藏了，元素找不到就直接报错了，不要动浏览器完事ok

3.请务必安装Firefox浏览器，不然那上哪调用浏览器啊。我的版本是Firefox浏览器：47.0.1的，其余的我没测试过

----------

遇到的问题及解决方案
----------
1.抛出如下错误，元素不可见
```
ElementNotVisibleException: Message: Element is not currently visible and so may not be interacted with
Stacktrace
```
1.解决方案，实行点击操作，先点击一下，那个有一支笔的图标，然后才会有下面的六个选项，才能再进行选择写博客。主要方法就是把自己想象成浏览器，应该怎么一步步操作就达到目的。

----------
2.弹出页面太多，还不够真正一键操作

2.解决方案，使用定位窗口然后关闭浏览器操作，在定位窗口时注意多做测试，才能深刻理解定位句柄的意义，定位窗口请看[解决Selenium弹出新页面无法定位元素问题（Unable to locate element）  
 ](http://blog.csdn.net/mrlevo520/article/details/51926145)

----------

最后
--
> ​	CSDN的元素非常好找，真的非常正规，一切都显得非常顺利，大概写了十几分钟就把脚本写成exe了，大多数时间花在测试上了，其实就是脚本相互套的把戏，成功过几次模拟登陆一切都显得轻车熟路，多练习和解决实际问题才是最快的进步途径啊！

----------

ps
--
本来想写个gui出来的，然后加上用户名密码，但是，这样就脱离了一键操作的轻快感，主要还是给自己用的，如果能给你也带来灵感和提供帮助，我感到非常荣幸！以后就点击这个就可以啦！

![这里写图片描述](http://img.blog.csdn.net/20160717143434656)
​		
**今天就酱紫，大家周末愉快！**

![这里写图片描述](http://img.blog.csdn.net/20160717143007666)
