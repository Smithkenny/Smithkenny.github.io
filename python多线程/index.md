# python多线程


## python多线程使用

#### 创建线程，入口函数带参数

```python
import threading
import time

print("*******主线程开始**********")
def f1(s):
    print("***********f1start***********")
    time.sleep(s)
    print("***********f1end***********")

def f2(s):
    print("***********f2start***********")
    time.sleep(s)
    print("***********f2end***********")

#创建线程
# target 指定这个先乘执行那个函数中的代码，只写函数名即可，不要写括号。
# args 后面指定的是一个元组，
th1 = threading.Thread(target=f1, args=(2,))
th2 = threading.Thread(target=f2, args=(3,))

th1.start()
th2.start()

print("*******主线程结束**********")
```

#### 另外一种引入包的方式

```python
from threading import Thread
import time

print("*******主线程开始**********")
def f1(s):
    print("***********f1start***********")
    time.sleep(s)
    print("***********f1end***********")

def f2(s):
    print("***********f2start***********")
    time.sleep(s)
    print("***********f2end***********")

#创建线程
# target 指定这个先乘执行那个函数中的代码，只写函数名即可，不要写括号。
# args 后面指定的是一个元组，
th1 = Thread(target=f1, args=(2,))
th2 = Thread(target=f2, args=(3,))

th1.start()
th2.start()

print("*******主线程结束**********")
```

#### 设置等待

```python
# 设置主线程等待子线程执行结束之后在执行
from threading import Thread
import time

print("*******主线程开始**********")
def f1(s):
    print("***********f1start***********")
    time.sleep(s)
    print("***********f1end***********")

def f2(s):
    print("***********f2start***********")
    time.sleep(s)
    print("***********f2end***********")

#创建线程
# target 指定这个先乘执行那个函数中的代码，只写函数名即可，不要写括号。
# args 后面指定的是一个元组，
th1 = Thread(target=f1, args=(2,))
th2 = Thread(target=f2, args=(3,))

th1.start()
th2.start()

# 设置等待
th1.join()
th2.join()

print("*******主线程结束**********")
```

#### 共享内存

给线程上锁。

需求:用户`zhangsan`打车使用支付宝付钱，司机`zhangsan`收到钱。

分析：用户`zhangsan`付钱时，线程上锁，付完钱解锁。司机`zhangsan`账户线程上锁，收到前后解锁。 

 ```python
from threading import Thread, Lock
from time import sleep


# 存储支付宝余额账号
zhifubao = {
    'zhangsan': 10000,
    'liming': 5000,
    'wanghong': 3000,
    'zhaolei': 5000
}

# 申请一把锁
zhifu_lock = Lock()
# 线程1 ：嘀嘀打车处理，参数是用户账户和扣款金额

def thread1_didi_pay(account, amount):
    print('* t1: 即将开始操作')
    # 上锁
    zhifu_lock.acquire()
    balance = zhifubao[account]

    # 下面的sleep(2)表示一些处理过程需要花上2秒钟
    print('* t1: 完成交易需要2s钟')
    sleep(2)
    print('* t1: deduct')
    zhifubao[account] = balance - amount
    # 释放锁
    zhifu_lock.release()
# 线程2： 余额宝可以专区利息，参数是用户账户和扣款金额
def thread2_yue_pay(account,amount):
    print('* t2: 即将开始操作')
    # 上锁
    zhifu_lock.acquire()
    balance = zhifubao[account]
    # 下面的sleep(2)表示一些处理过程需要花上2秒钟
    print('* t2: 完成交易需要2s钟')
    sleep(2)
    print('* t2: deduct')
    zhifubao[account] = balance + amount
    # 释放锁
    zhifu_lock.release()

# 分别创建两个线程
t1 = Thread(target=thread1_didi_pay, args=('zhangsan', 2000))
t2 = Thread(target=thread2_yue_pay, args=('zhangsan', 2000))

# 开始线程
t1.start()
t2.start()

# 设置等待
t1.join()
t2.join()

# 输出账户余额
print("__________________________")
print(zhifubao["zhangsan"])
 ```




