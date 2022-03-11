---
title: "연역된 형식 알아내는 법"
categories:
  - C++
tags:
  - C++
---

# 컴파일러 메시지
컴파일러 오류 메시지에는 문제를 일으킨 형식이 언급 됨.

```c++
template<typename T>
class TD;
```

위 템플릿을 인스턴스화 하려면 인스턴스화 할 템플릿 정의가 없어서 컴파일 오류가 발생

```c++
const int theAnswer = 42;
auto x = theAnswer;
auto y = &theAnswer;

TD<decltype(x)> xType;
TD<decltype(y)> yType;
```

위 예제 코드의 컴파일 에러는 아래와 같이 표시됨

```
error: aggregate ‘TD<int> xType’ has incomplete type and cannot be defined TD<decltype(x)> xType;
error: aggregate ‘TD<const int*> yType’ has incomplete type and cannot be defined TD<decltype(y)> yType;
```

변수 x는 int로, 변수 y는 const int* 로 연역됨.

# 실행시점 출력
```c++
  const int theAnswer = 42;
  auto x = theAnswer;
  auto y = &theAnswer;
  
  std::cout << typeid(x).name() << std::endl;
  std::cout << typeid(y).name() << std::endl;
```

리눅스에서 typeid의 name 함수는 std::type_info::name 정보를 반환하는데 알아볼 수 없는 형식으로 반환 (MicroSoft 컴파일러는 제대로 보여줌)

```
i
PKi
```

std::type_info::name 은 좀 더 복잡한 타입에 대해 정확한 정보를 제공하지 않는다.
다행이도 믿을만한 방법을 제공하는 라이브러리들이 있는데, [boost type_index](https://www.boost.org/doc/libs/1_75_0/boost/type_index.hpp) 이다.

```c++
#include <boost/type_index.hpp>

template<typename T>
void f(const T& param)
{
    using std::cout;
    using boost::typeindex::type_id_with_cvr;

    cout << "T= " << type_id_with_cvr<T>().pretty_name() << '\n';
    cout << "param= " << type_id_with_cvr<decltype(param)>().pretty_name() << '\n';
}
```
위와 같은 템플릿 함수에서 T와 param의 type 을 알아보자.

```c++
std::vector<Widget> createVec();
const auto vw = createVec();

if (!vw.empty()) {
    f(&vw[0]);
}
```

실행했을 때 적절한 결과는 아래와 같다.

```
T= Widget const*
param= Widget const* const&
```