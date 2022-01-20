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
```
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
```
template<typename Container, typename Index>
auto autoAndAccess(Container& c, Index i)
  -> decltype(c[i])
{
  authenticateUser();
  return c[i];
}
```
