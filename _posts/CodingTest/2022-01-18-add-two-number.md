---
title: "[Leet Code] Add two number"
categories:
  - coding test
tags:
  - coding test
---

## 문제
한 개의 숫자를 멤버 값으로 가지고 있는 링크드 리스트가 두 개 주어진다. 숫자는 역순으로 저장 돼 있다.
이 두 숫자의 합을 나타내는 링크드 리스트를 반환한다. 0부터 시작하는 숫자는 없다.

₩₩₩
Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [7,0,8]
Explanation: 342 + 465 = 807.
₩₩₩

각 숫자의 노드 길이는 1~100 
각 노드 값은 0~9

## 해결법
처음에는 이 문제를 링크드 리스트에서 숫자로 되돌린 후, 두 숫자를 더해 다시 링크드 리스트로 만드는 방법으로 진행했다.
하지만 input 의 길이가 100개까지 가능하므로 엄청나게 큰 자료형을 준비해야 한다.
처음에는 자료형을 수정하다가 결국 검색으로 가장 마음에 드는 해결법을 찾았다.

처음 ListNode 를 지역 변수로 선언하고 이 지역 변수의 next 가 가지고 있는 ListNode 를 돌려주면 코드가 훨씬 깔끔해질 수 있다.

₩₩₩
ListNode r{0};
ListNode *c = &r;
₩₩₩

이 후 두 링크드 리스트가 nullptr 이 될때까지 next 를 타고 링크드 리스트를 순회한다.
두 수의 합이 9보다 클 경우 넘어가는 숫자를 변수에 담아둔다.

₩₩₩
while(i1 || i2 || carry) {
    auto n1 = i1 ? i1->val : 0;
    auto n2 = i2 ? i2->val : 0;
    auto val = n1 + n2 + carry;

    c->next = new ListNode(val%10);
    carry = val/10;

    i1 = i1!=nullptr ? i1->next : nullptr;
    i2 = i2!=nullptr ? i2->next : nullptr;
    c = c->next;
};
₩₩₩

각 자리 수를 더했을 때 10으로 나누어 남는 수를 값으로 저장하고
10으로 나눈 몫은 넘어가는 수로 저장한다.