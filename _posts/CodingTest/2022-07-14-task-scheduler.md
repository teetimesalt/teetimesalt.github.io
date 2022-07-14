---
title: "[Leet Code] Task scheduler"
categories:
  - coding test
tags:
  - coding test
---

## 문제
태스크를 나타내는 문자열이 주어지고 자연수 n 이 주어진다.
N 만큼은 중복된 태스크가 수행될 수 없다.
수행할 태스크가 없다면 idle 상태이다.

주어진 문자열에 포함된 모든 태스크가 수행되는 가장 적은 수행 횟수를 찾아라.

```
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: 
A -> B -> idle -> A -> B -> idle -> A -> B
There is at least 2 units of time between any two same tasks.
```

## 해결법
문자열에 속한 태스크들이 정렬되어 있지 않다.
A, B, C, D, B, A 식의 태스크 열을 A, A, B, B, C, D 로 정렬해주면 중복되지 않은 태스크를 찾기 쉬워진다.
수행해야 할 A 태스크가 몇 개인지 정리해둔다. 그러기 위해 구조체를 정의한다.

```
typedef struct Task {
  char c;
  int size;
} Task;

struct TaskCompare {
    bool operator() (Task &a, Task &b) {
        return a.size < b.size;
    }
};
```
태스크를 정렬하는 순서는 수행해야 할 태스크의 개수이다.
아래처럼 늘 정렬되게 Priority queue 에 넣어둔다.

|Task|Num|
|---|:---|
|A| 2|
|B| 2|
|C| 1|
|D| 1|

N 만큼의 수행동안 동일한 태스크가 겹치게 하지 않으려면
1. Priority queue 가 빌 때까지 반복하며 수가 많은 태스크를 하나하나 꺼낸다.
2. Priority queue 에서 태스크를 꺼낼 때마다 N 반복을 계산한다.
3. 꺼낸 태스크는 수를 -1 해준다.
4. 현재 반복이 N 만큼 도달하면 다시 모든 태스크를 priority queue 에 채운채로 반복한다.
5. 현재 반복이 N 에 도달하지 못하면 idle로 간주하고 priority queue 에 모든 태스크를 채우고 반복한다.

```c++
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
      if (n == 0)
        return tasks.size();
      int ans = 0;
      sort(tasks.begin(), tasks.end());

      Task currentTask{tasks[0], 1};
      struct TaskCompare {
        bool operator() (Task &a, Task &b) {
          return a.size < b.size;
        }
      };
      priority_queue <Task, vector<Task>, TaskCompare> pq;

      for(int i = 1 ; i < tasks.size() ; i++) {
        if (currentTask.c == tasks[i]) {
          currentTask.size++;
        } else {
          pq.push(currentTask);
          currentTask = Task{tasks[i], 1};
        }
      }
      pq.push(currentTask);

      vector<Task> sched;
      int offset = 0;

      while (!pq.empty()) {
        Task t = pq.top();
        pq.pop();
        t.size -= 1;
        if (t.size > 0) {
          sched.push_back(t);
        }
        offset++;
        ans++;
        if (offset == n+1 || pq.empty()) {
          if (offset != n+1) {
            if (sched.empty()) {
              break;
            }
            ans += (n+1-offset);
          }
          offset = 0;
          vector<Task>::iterator it;
          for(it = sched.begin() ; it != sched.end() ; it++) {
            pq.push(*it);
          }
          sched.erase(sched.begin(), sched.begin() + sched.size());
          continue;
        }
      }

      return ans;
    }
};
```