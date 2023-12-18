# python多线程编程
## 1. 创建线程
```
class MyThread(threading.Thread):
    def run(self):      # 通过继承Thread类的方式创建线程。需要重写run()方法,
    其中包含该线程需要执行的代码。
        print("Thread1, activate!")


def my_function():
    print("Thread2, activate!")


thread1 = MyThread()
thread2 = threading.Thread(target=my_function)      # 通过函数的方式创建
线程，需要执行的函数作为target参数传递给Thread对象。
# 调用start()方法启动线程
thread1.start()
thread2.start()
```

## 2.线程同步
线程之间可能会共享资源，因此需要进行线程同步来保证资源的正确访问。python中提供了多种线程同步机制，互斥锁、信号量、事件。
### 2.1互斥锁
确保同一时刻只有一个线程可以访问共享资源。Python中的T和reading模块提供了Lock类来实现互斥锁。
```
# sharing resource
count = 0

# create a mutex
lock = threading.Lock()


def increment():
    global count
    # get mutex
    lock.acquire()
    try:
        # update sharing resource
        count += 1
    finally:
        # release mutex
        lock.release()


# creates multiple threads and start
threads = []
for _ in range(10):
    thread = threading.Thread(target=increment)
    threads.append(thread)
    thread.start()

# wait for finishment of all threads 
for thread in threads:
    thread.join()   # 用于等待线程完成的函数，可接受一个时间参数(等待时间上限)

print("Count", count)
```
try-finnally(try-catch in C++)语句用于异常处理。
### 2.2 线程间通信
python中线程间通信的机制：使用queue模块实现的队列。
