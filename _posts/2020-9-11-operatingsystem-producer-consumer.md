---
title: 操作系统-消费者生产者问题
categories: [操作系统, 消费者生产者]
tags: ['Linux','互斥']
---

# Linux-信号量互斥

##     1. 消费者生产者问题

通过创建两个线程，一个代表生产者，另一个代表消费者，申请一段固定大小的内存区域表示缓冲区，向缓冲区末尾插入字符和取出字符来模拟生产消费的过程，

其中生产过程是随机生产一个小写字母放入**缓冲区**中，而且用nextin，nextout表示下一个要插入以及要取出的下标位置，方便下一次操作。

​    为了保证消费者线程在插入字符时，不被生产者线程堵塞，应该通过信号量机制防止此情况发生，设置**三个信号量**：

* mutex:：初始值为1，该信号量作用为控制一次只有一个线程可以访问缓冲区，保证消费过程和生产过程始终互斥。

* empyt：初始值为缓冲区大小，每生产一次该信号量就减1，每消费一次该信号量就加1，当缓冲区已满时，该信号量会减小到0，这样在生产者线程开头的

  sem_wait(&empty)就会堵塞生产过程，保证缓冲区已满时不再进行生产。

* full：初始值为0，每消费一次该信号量就减1，每生产一次该信号量就加1，当缓冲区为空时，在消费者线程开头的sem_wait(&full)就会堵塞消费过程，保证缓冲区为空时不再进行消费。

## 2.代码如下：

```c
#include <unistd.h>
#include <semaphore.h>
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>
#define N 10
char buff[N];
int count, nextin, nextout;
sem_t mutex;
sem_t empty;
sem_t full;
int speed_producer = 1, speed_consumer = 4;

void init() {
    srand(time(NULL));
    sem_init(&mutex, 0, 1);
    sem_init(&empty, 0, N);
    sem_init(&full, 0, 0);
    nextin = 0;
    nextout = 0;
    count = 0; 
}

void show() {
    int j = count, i = nextout;
    printf("buff:");
    while (j--)
    {
        printf("%c", buff[i]);
        i++;
        i %= N;
        if (j != 0)
        {
            printf(",");
        }
    }
    printf("\n\n");
}

void *producerThread() {
    while (1)
    {
        sem_wait(&empty);
        sem_wait(&mutex);

        buff[nextin] = 'a' + rand()%26;
        printf("%c生产\n", buff[nextin]);
        nextin++;
        nextin %= N;
        count++;
        show();

        sem_post(&mutex);
        sem_post(&full); 
        sleep(speed_producer);

        
    }
}
void *consumerThread() {
    while (1)
    {
        sem_wait(&full);
        sem_wait(&mutex);

        printf("%c消费\n", buff[nextout]);
        nextout++;
        nextout %= N;
        count--;
        
        show();

        sem_post(&mutex);
        sem_post(&empty);
        sleep(speed_consumer);
       
    }
}
int main() {
    pthread_t producer, consumer;

    init();
    pthread_create(&producer, NULL, producerThread, NULL);
    pthread_create(&consumer, NULL, consumerThread, NULL);
    
    pthread_join(producer, NULL);
    pthread_join(consumer, NULL);

    sem_destroy(&mutex);
    sem_destroy(&empty);
    sem_destroy(&full);
    
    exit(0);
}
```

   通过调整两个线程的延时快慢，speed_producer或者speed_consumer越大表示延时越长，速度越慢，修改这两个值即可得到不同运行效果。

>  运行输出解释：每次执行完生产与消费过程，程序将输出缓冲区当前**所有不为空的字符**

    * 当speed_consumer与speed_producer均为1，或者speed_consumer大于speed_producer时，结果均为生产一个消费一个，缓冲区始终不满。

​       ![wUeqsO.png](https://s1.ax1x.com/2020/09/11/wUeqsO.png)



* 当speed_consumer为4，speed_producer均为1，每1秒生产一个字符，每4秒消费一个字符，因此缓冲区会逐渐填满。

  ![](https://s1.ax1x.com/2020/09/11/wUmPQf.png)

  ## 3. 实验总结

  * 掌握多线程编程方法，了解程序创建多线程的方法的原理。
  * 通过运用信号量机制进行线程互斥，对临界区原理有了进一步的认识，并且对消费者生产者问题有了进一步的理解。