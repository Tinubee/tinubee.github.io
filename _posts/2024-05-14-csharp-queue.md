---
title: Queue
description: Queue에 대해서 자세히 알아보자.
author: tinubee
date: 2024-05-14 18:30:00 +0800
categories: [Study, C#]
tags: [C#, Winform]
pin: true
math: true
mermaid: true
# image:
#   path: /commons/devices-mockup.png
#   lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
#   alt: Responsive rendering of Chirpy theme on multiple devices.
---
## **큐(Queue) 란 ?**

<!-- ![Desktop View](https://blog.kakaocdn.net/dn/uMiOJ/btreKJ5Os5T/AtMSDo5vWJATJQWYxLIKSk/img.png) -->

![Desktop View](https://github.com/Tinubee/TPA/assets/53461370/cee3f74f-f84c-4739-a585-81fa82f1710b)

- Queue<T>는 FIFO(First-In, First-Out) 컬렉션을 구현합니다. 
- 처음 삽입된 요소가 가장 먼저 제거되는 방식입니다. 
- Queue는 기본적으로 데이터가 선형으로 배치된 방식이 아니라 순환 버퍼(Circular Buffer)를 사용하여 효율적으로 관리됩니다.

#### 주요 메서드
1. Enqueue(T item): 큐의 끝에 요소를 추가합니다.
2. Dequeue(): 큐의 시작에서 요소를 제거하고 반환합니다.
3. Peek(): 큐의 시작에 있는 요소를 제거하지 않고 반환합니다.
4. Count: 큐에 있는 요소의 개수를 반환합니다.
5. Clear(): 큐의 모든 요소를 제거합니다.

#### 사용예제

```cs
using System;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        // Queue 생성
        Queue<int> queue = new Queue<int>();
        // 요소 추가
        queue.Enqueue(1);
        queue.Enqueue(2);
        queue.Enqueue(3);
        // 요소 확인
        Console.WriteLine("Peek: " + queue.Peek());  // 출력: Peek: 1
        // 요소 제거
        Console.WriteLine("Dequeue: " + queue.Dequeue());  // 출력: Dequeue: 1
        Console.WriteLine("Dequeue: " + queue.Dequeue());  // 출력: Dequeue: 2
        // 남은 요소 개수
        Console.WriteLine("Count: " + queue.Count);  // 출력: Count: 1
        // 큐 비우기
        queue.Clear();
        Console.WriteLine("Count after Clear: " + queue.Count);  // 출력: Count after Clear: 0
    }
}
```

#### 큐(Queue)의 장점과 단점
>
- 장점 : 단순하고 사용하기 쉽고, 메모리 사용이 효율적이다.
- 단점 : 단일 스레드 환경에서만 안전하다. 다중 스레드 환경에서는 동기화 문제 발생 가능성이 있다.

***

## **ConcurrentQueue**

- ConcurrentQueue<T>는 스레드에 안전한 FIFO 자료구조입니다. 이는 여러 스레드가 동시에 접근할 수 있도록 설계되었습니다.

#### 주요 메서드

1. Enqueue(T item): 큐의 끝에 요소를 추가합니다.
2. TryDequeue(out T result): 큐의 앞에서 요소를 제거하고 반환합니다.
3. TryPeek(out T result): 큐의 앞에 있는 요소를 제거하지 않고 반환합니다.
4. IsEmpty: 큐가 비어 있는지 여부를 반환합니다.
5. Count: 큐에 있는 요소의 개수를 반환합니다 (O(N) 시간 복잡도).

#### 사용예제

```cs
using System;
using System.Collections.Concurrent;
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static void Main()
    {
        // ConcurrentQueue 생성
        ConcurrentQueue<int> concurrentQueue = new ConcurrentQueue<int>();
        // 요소 추가 작업을 하는 메서드
        void EnqueueItems()
        {
            for (int i = 0; i < 10; i++)
            {
                concurrentQueue.Enqueue(i);
                Console.WriteLine($"Enqueued: {i}");
                Thread.Sleep(100); // 작업 지연을 위해 추가
            }
        }
        // 요소 제거 작업을 하는 메서드
        void DequeueItems()
        {
            for (int i = 0; i < 10; i++)
            {
                if (concurrentQueue.TryDequeue(out int result))
                {
                    Console.WriteLine($"Dequeued: {result}");
                }
                else
                {
                    Console.WriteLine("Queue is empty");
                }
                Thread.Sleep(150); // 작업 지연을 위해 추가
            }
        }
        // 두 개의 작업을 각각 다른 스레드에서 실행
        Task enqueueTask = Task.Run(EnqueueItems);
        Task dequeueTask = Task.Run(DequeueItems);
        // 두 작업이 모두 완료될 때까지 대기
        Task.WaitAll(enqueueTask, dequeueTask);
        // 남은 요소 출력
        Console.WriteLine("Remaining items in queue:");
        foreach (var item in concurrentQueue)
        {
            Console.WriteLine(item);
        }
    }
}
```
>이 예제는 여러 스레드가 동시에 ConcurrentQueue에 접근하여 안전하게 요소를 추가하고 제거하는 방법을 보여줍니다. 이를 통해 ConcurrentQueue의 스레드 안전성을 확인할 수 있습니다.