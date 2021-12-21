---
title: "Type deduction"
#excerpt_separator: "<!--more-->"
categories:
  - C++
tags:
  - C++
---

## 형식 연역 혹은 타입 추론 (Type deduction)
Type deduction 에서는 형식 연역이라는 단어를 사용한다. 개인적으로 좀 더 자주 본 단어는 타입 추론이다.

### 템플릿 형식 연역
```
template<typename T>
void f(ParamType param);
...
f(expr)
```
일 때, 세 가지 형식 연역 규칙이 존재함

아래와 같은 변수 선언이 존재한다
```
int x = 10;
const int y = x;
const int& z = x;
```
ParamType 형태에 따라 형식 연역되는 경우를 숙지하자.

#### Param type이 포인터 혹은 참조. 하지만 Universal reference (보편 참조)는 아님
| ParamType| 호출 형식 | 형식 연역 |
|----------|---------|---------|
|void f(T& param)| f(x) | T : int, param : int& |
|void f(T& param)| f(y) | T : const int, param : const int& |
|void f(T& param)| f(z) | T : const int, param : const int& |

헷갈리는 부분이 const int& 인 z 호출 시. 이유는 형식 연역 과정에서 참조성이 무시되기 때문


| ParamType| 호출 형식 | 형식 연역 |
|----------|---------|---------|
|void f(const T& param)| f(x) | T : int, param : const int |
|void f(const T& param)| f(y) | T : int, param : const int& |
|void f(const T& param)| f(z) | T : int, param : const int& |


포인터일 경우도 알아보자.
| ParamType| 호출 형식 | 형식 연역 |
|----------|---------|---------|
|void f(T* param)| f(&x) | T : int, param : int* |
|void f(T* param)| const int* pX = &x; f(pX) | T : const int, param : const int* |
책에선 여기까지는 너무 쉬워서 하품이 난다지만 나는 아닌걸...


#### Param type이 universal reference (보편 참조)
이 경우 expr이 왼 값일 때(lvalue) 왼 값으로 연역된다.
| ParamType| 호출 형식 | 형식 연역 |
|----------|---------|---------|
|void f(T&& param)| f(x) | T : int, param : const int& |
|void f(T&& param)| f(y) | T : const int, param : const int& |
|void f(T&& param)| f(z) | T : const int, param : const int& |
|void f(T&& param)| f(10) | T : int, param : int&& |

#### Param type이 포인터도 아니고 참조도 아님
참조성과 const가 무시된다

| ParamType| 호출 형식 | 형식 연역 |
|----------|---------|---------|
|void f(T param)| f(x) | T : int, param : int |
|void f(T param)| f(y) | T : int, param : int |
|void f(T param)| f(z) | T : int, param : int |
|void f(T param)| const char* const ptr = "string"; f(ptr)  | T : const char* , param : const char* |
