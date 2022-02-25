---
title: "decltype의 작동 방식"
#excerpt_separator: "<!--more-->"
categories:
  - C++
tags:
  - C++
---

decltype은 주어진 이름이나 표현식의 형식을 알려줌
대부분의 deltype은 예측한 대로 동작함
```c++
const int i = 0; //decltype(i) const int
bool f(const Widget& w) //decltype(w) const Widget&, decltype(f(w)) bool

struct Point {
  int x, y;
}; //decltype(Point::x) int

template<typename T>
class vector {
public:
  T& operator[](std::size_t index);
};
vector<int> v; //decltype(v) vector<int>
               //decltype(v[0]) int&
```

대체로 operator[] 연산은 T&를 돌려줌.
예외가 있는데, vector<bool>에 대한 operator[]는 bool&가 아님(vector<bool>::reference).
C++에서는 bool에 대한 reference를 사용할 수 없음.

## auto를 반환하는 함수
```c++
template<typename Container, typename Index>
auto authAndAccess(Container& c, Index i)
  -> decltype(c[i])
{
  authenticateUser();
  return c[i];
}
```
C++11 후행 반환 형식은 반환 형식을 매개 변수를 이용해 지정할 수 있다는 장점이 있음.
  
```c++
template<typename Container, typename Index>
auto authAndAccess(Container& c, Index i)
{
  authenticateUser();
  return c[i];
}
```

C++14에서는 후행 반환 형식을 생략할 수 있음
operator[] 는 T&를 반환하기 때문에 아래와 같은 코드를 구현할 때,
```c++
deque<int> d;
authAndAccess(d, 5) = 10;
```

위 코드처럼 수정할 때 컴파일 되지 않음
이유는 auto 반환 형식 연역 과정에서 참조가 제거되기 때문
위 코드를 동작하게 수정하려면 authAndAccess가 c[i]의 반환 형식과 동일한 형식을 반환하게 함.

```c++
template<typename Container, typename Index>
decltype(auto) autoAndAccess(Container&& c, Index i)
{
  authenticateUser();
  return std::forward<Container>(c)[i];
}
```

Container를 보편참조로 받으면 왼 값, 오른 값모두 인자로 입력할 수 있다.
이상 C++14 버전, 아래는 C++11 코드

```c++
template<typename Container, typename Index>
auto autoAndAccess(Container&& c, Index i)
-> decltype(std::forward<Container>(c)[i])
{
  authenticateUser();
  return std::forward<Container>(c)[i];
}
```