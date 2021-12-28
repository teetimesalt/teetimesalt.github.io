---
title: "Type deduction 2 : 배열과 포인터"
categories:
  - C++
tags:
  - C++
---

## 배열과 포인터
배열과 포인터를 구분하지 않고 사용할 수 있는 경우가 있지만, 배열 형식은 포인터 형식과 다르다.

```
const char name[] = "name";
const char *ptrName = name;
```

name 은 const char[4] 형식의 배열이지만 ptrName 은 const char* 형식이다.
컴파일이 잘 되지만, 배열이 포인터로 붕괴했다(decay)

### 배열과 템플릿
배열을 템플릿 인자로 전달하면 붕괴하는 특성이 도드라진다.

```
template<typename T>
void f(T param);
f(name);
```
여기에서 꼭 알아야 할 점은, 배열 형식의 함수 매개변수라는 것은 없다는 것.
물론 매개변수에 int param[] 형식으로 사용하지만 사실 포인터와 같은 의미이다.
그러므로, 위 코드의 T 는 const char* 로 연역(타입 추론)된다.

```
template<typename T>
void f(T& param);
f(name);
```

하지만 매개변수를 배열에 대한 참조로 선언할 수는 있다.
이럴 때 T 는 const char[4]가 되고, 매개변수는  const char (&)[4] 로 추론된다.
이런 특성을 이용하면 배열 내부의 원소 개수를 돌려주는 템플릿을 선언할 수 있다.

```
template<typename T, std::size_t N>
constexpr std::size_t arraySize(T (&)[N]) noexcept
{
    return N;
}
```

## 함수 인수
배열이 포인터로 붕괴하듯, 함수도 함수 포인터로 붕괴한다.

```
void someFunc(int, double);

template<typename T>
void f1(T param);

template<typename T>
void f2(T& param);

f1(someFunc);
f2(someFunc);
```

f1의 매개변수 형식은 void(*)(int, double) 이고 f2의 매개변수 형식은 void(&)(int, double) 이 된다.
