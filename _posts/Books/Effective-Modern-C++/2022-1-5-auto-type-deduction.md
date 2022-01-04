---
title: "auto 의 형식 연역 규칙 (타입 추론)"
#excerpt_separator: "<!--more-->"
categories:
  - C++
tags:
  - C++
---

*auto 형식 연역이 곧 템플릿 형식 연역이다*

* case1 : 형식 지정자가 포인터나 참조이지만, 보편 참조는 아닌 경우. expression이 참조일 경우 참조성을 무시한다
* case2 : 형식 지정자가 보편 참조인 경우, expression이 왼값(lvalue)이면 왼값 참조로 연역된다
* case3 : 형식 지정자가 포인터도 아니고 참조도 아닌 경우. expression의 const와 참조가 무시된다

```
auto x = 27; //int x (case 3)
const auto cx = x; //const int cx(case 3)
const auto& rx = x; //const int& rx (case 1)

auto&& uref1 = x; //int& uref1 (case 2)
auto&& uref2 = cx; //const int& uref2 (case 2)
auto&& uref3 = 27; //int&& uref3
```

## 템플릿과 auto 형식 연역의 차이점
변수의 초기화를 지원하는 방법이 다양해지면서 다음과 같은 문제가 생김

```
int x1 = 27;
int x2(x1);
int x3 = {27};
int x4{27};
// 위 코드 모두 int

auto a1 = 27; ///int
auto a2(a1); //int
auto a3 = {27}; //std::initializer_list<int>
auto a4{27}; //std::initializer_list<int>
```

auto로 선언된 변수를 중괄호 초기치로 초기화하는 경우, 연역된 형식은 std::initializer_list 임
하지만 중괄호를 이용해 초기화하는 것은 템플릿 함수에서는 컴파일에 실패함
