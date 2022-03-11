---
title: "[Leet Code] Smallest range covering elements from k lists"
categories:
  - coding test
tags:
  - coding test
---

## 문제
정렬된 배열들의 아이템이 포함되는 가장 작은 범위를 찾으세요.

```
Input: nums = [[4,10,15,24,26],[0,9,12,20],[5,18,22,30]]
Output: [20,24]
Explanation: 
List 1: [4, 10, 15, 24,26], 24 is in range [20,24]
List 2: [0, 9, 12, 20], 20 is in range [20,24]
List 3: [5, 18, 22, 30], 22 is in range [20,24]
```

## 해결법
온라인에서 찾아 본 코드를 이용해 해결법을 찾아본다.
각 배열이 정렬되어 있는 상태이기 때문에 배열의 정렬은 따로 필요하지 않다.
각 배열들의 아이템을 순회하면서 세 배열의 아이템을 모두 포함할 수 있는 가장 작은 범위를 찾는다.

```c++
     auto my_comp = [](const pair<vi, vi>& lhs, const pair<vi, vi>& rhs) {
        cout << "In my_comp lhs.first=" << *lhs.first << " rhs.first=" << *rhs.first << endl;
        return *lhs.first > *rhs.first; 
      };
      priority_queue<pair<vi, vi>, vp, decltype(my_comp)> pq(my_comp);
      vector<int> res(2, numeric_limits<int>::min());
```

모든 배열의 아이템들이 포함될 수 있는 범위를 찾는데 priority_queue를 이용한다.
priority_queue는 배열 내 아이템 위치를 가리킬 수 있는 iterator 를 저장한다.
iterator 가 저장될 때 어떤 순서로 저장할 지 결정하는 my_comp 함수를 정의한다.
이 함수에는 iterator pair 두 개가 인자로 들어오는데 두 pair 의 첫 번째 아이템들을 비교해서 더 작은 아이템을 가진 pair 를 앞에 둔다.
그리고 범위를 저장 할 vector<int> res 를 선언하고 integer 의 가장 작은 값으로 초기값을 할당한다.

```c++
      for (auto& array: nums) {
        pq.push(make_pair(array.begin(), array.end()));
        res[1] = max(res[1], *array.begin());
      }
```
nums 는 전달된 배열들을 아이템으로 가지고 있는 vector<vector<int>> 타입의 인자 변수이다.
nums 내 모든 배열의 begin과 end 를 iterator pair 로 만들어 priority_queue에 차곡 차곡 쌓아둔다.
이 때 각 배열의 begin 값 중 가장 큰 값을 res[1]에 넣어둔다.

![image1](/assets/img/coding_test/smallest_range_covering_elements_from_k_lists_1.jpg)

모든 배열을 순환하고 나면 위와 같은 그림이 될 것이다.
iterator 들은 각각 색깔이 다른 화살표로 표현했다.
my_comp 함수가 priority_queue에서 아이템들을 어떻게 정렬할 것인지 정하게 되는데 배열 내의 앞 선 화살표에 위치한 값을 비교하게 되므로 두 번째 배열의 iterator가 가장 첫 아이템으로 저장된다.

```c++
      res[0] = *pq.top().first;
      vector<int> current{res};
```

현재 priority_queue 에 있는 아이템 중 top은 입력의 두 번째 배열이다. 첫 번째 아이템인 0을 res[0] 에 저장한다.
이제 res에는 [0, 5]가 저장 돼 있다. 이 vector 값을 그대로 current에 복사한다.
지금부터 본격적으로 배열들을 순환하며 더 작은 범위가 있는 지 살펴본다.

```c++
    while (!pq.empty()) {
        auto its = pq.top();

        ++its.first;
        pq.pop();

        if (its.first == its.second) break;

        current[1] = max(current[1], *its.first);
        
        pq.push(its);
        current[0] = *pq.top().first;
        if (current[1] - current[0] < res[1] - res[0])
          res = current;
      }
```

![image2](/assets/img/coding_test/smallest_range_covering_elements_from_k_lists_2.jpg)

priority_queue에 더이상 iterator 쌍이 없을 때까지 순환한다.
현재 priority_queue에 있는 가장 top 아이템은 두 번째 배열인 [0, 9, 12, 20].
우선 priority_queue에서 꺼낸다. 그리고 iterator 를 1 추가한다.
그럼 해당 iterator는 9를 가리키게 된다.
iterator가 하나 옮겨간 값이 current[1]에 있는 값보다 클 경우 current[1]의 값을 업데이트한다.
현재 begin iterator의 값이 9이므로 current[1] 의 값은 9이다.

그리고 iterator에 1을 더한 쌍을 다시 priority_queue에 추가한다.
그러면 iterator 의 begin 값 중 가장 작은 값을 가지고 있는 배열이 top 에 있게 된다.
그 값은 begin들 중 가장 작은 값이 되므로 current[0]의 값을 업데이트한다.

res가 가장 적은 범위를 가지고 있기 때문에 current가 가지고 있는 차가 더 작다면 res를 업데이트한다.

![image3](/assets/img/coding_test/smallest_range_covering_elements_from_k_lists_3.jpg)

while 이 한 번 수행되면 위와 같은 그림과 같다.
while 문을 두 번째 돌았을 때는 어떨까요?

![image5](/assets/img/coding_test/smallest_range_covering_elements_from_k_lists_5.jpg)

현재 priority_queue의 top 에 있는 배열은 [4, 10, 15, 24, 26] 이다.
역시 pop을 하고 begin iterator를 뒤로 한 칸 옮긴다.
그러면 현재 begin iterator는 10을 가리키게 된다.

10은 current[1]보다 크지만 current[1]-current[0]은 res[1]-res[0]보다 크기 때문에 res 를 업데이트 하지 않고 그대로 세 번째 while 문을 수행하게 된다.

세 번째 while 문을 수행하면 아래와 같게 된다.

![image6](/assets/img/coding_test/smallest_range_covering_elements_from_k_lists_6.jpg)

이런 식으로 while 문을 모두 돌면 res에는 [20, 24]가 남는다.
