---
title: "[Leet Code] Maximum average subarray"
categories:
  - coding test
tags:
  - coding test
---

## 문제
10진수 수를 포함한 배열과 자연수 k가 전달된다.

인접한 k 길이의 부분 배열이 가장 큰 평균 값을 찾고 반환
10-5 정도의 계산 오류는 허용된다.

```
Input: nums = [1,12,-5,-6,50,3], k = 4
Output: 12.75000
Explanation: Maximum average is (12 - 5 - 6 + 50) / 4 = 51 / 4 = 12.75
```

## 해결법
평균을 낼 아이템의 시작과 끝을 가리키는 start, end 변수를 정의.
그리고 현재 더한 아이템의 개수를 나타내는 count 변수를 준비

가장 큰 평균 값을 저장 할 max 변수를 선언하고 동시에 그 값을 아이템이 가질 수 있는 가장 낮은 값인 -100000.0으로 초기 값을 설정

```
double MIN = -100000.0;
int start = 0, end = 0, count = 0;
double sum = 0.0;
double max = MIN;
```

start와 end 모두 0에서 출발.
end 는 전달받은 배열인 vector의 끝까지 +1하며 해당 아이템을 sum에 더해나감.
아이템을 sum에 더할수록 count도 1씩 증가

```
while (end < nums.size()) {
    sum += nums[end++];
    count++;
}
```

count 의 값이 k 와 같아지면, 현재까지 start와 end 사이의 평균이 가장 큰 평균인지 확인
start 를 뒤로 움직여, 부분 배열을 뒤로 옮김
배열에는 음수와 양수가 섞여 있기 때문에 start 의 아이템이 0보다 작다면 아이템 값만큼 더함

```
while (end < nums.size()) {
    sum += nums[end++];
    count++;
    while (count == k) {
        if (max < sum/count) {
            max = sum/count;
        }
        if (nums[start] < 0) {
            sum += abs(nums[start++]);
        } else {
            sum -= nums[start++];
        }
        count--;
    }
}
```

## 최종 코드
```
class Solution {
public:
    double findMaxAverage(vector<int>& nums, int k) {
        double MIN = -100000.0;
        int start = 0, end = 0, count = 0;
        double sum = 0.0;
        double max = MIN;

        while (end < nums.size()) {
          sum += nums[end++];
          count++;
          while (count == k) {
            if (max < sum/count) {
              max = sum/count;
            }
            if (nums[start] < 0) {
              sum += abs(nums[start++]);
            } else {
              sum -= nums[start++];
            }
            count--;
          }
        }

        return max;
    }
};
```