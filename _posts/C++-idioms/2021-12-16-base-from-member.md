---
title: "Base from Member"
#excerpt_separator: "<!--more-->"
categories:
  - C++
tags:
  - C++
---

## 목적
파생(상속받은) 클래스 데이터 멤버의 베이스(상속된) 클래스 초기화

## 원인
C++ 에서, 베이스 클래스는 파생 클래스 멤버보다 먼저 초기화된다. 하지만 베이스 클래스를 초기화에 파생 클래스의 데이터 멤버가 필요할 수 있다.
아래 코드는 Boost 라이브러리에 포함되어 있다. 문제를 살펴보자.

```
#include <streambuf>
#include <ostream>

namespace std {
  class streambuf;
  class ostream {
    explicit ostream(std::streambuf * buf);
    //...
  };
}

// A customization of streambuf.
class fdoutbuf : public std::streambuf
{
public:
    explicit fdoutbuf( int fd );
    //...
};

class fdostream : public std::ostream
{
protected:
    fdoutbuf buf;
public:
    explicit fdostream( int fd )
        : buf( fd ), std::ostream( &buf )
        // This is not allowed: buf can't be initialized before std::ostream.
        // std::ostream needs a std::streambuf object defined inside fdoutbuf.
    {}
};
```

위 코드는 개발자가 std::streambuf 클래스를 커스터마이징하면서 흥미로운 점이 시작된다. fdoutbuf 역시 std::streambuf 를 상속받는다. fdoutbuf 클래스는 fdostream 클래스의 멤버로, std::ostream 을 상속받는다. 하지만 std::ostream 클래스는 std::streambuf 클래스 혹은 상속받은 클래스를 필요로 한다. buf 포인터를 넘겨주는게 맞는 것 같다. buf가 초기화 되기만 하면 말이다...
하지만 베이스 클래스가 초기화되기 전까지는 초기화되지 않는다. base-from-member 는 이 문제점을 위한 해결법이다.

## 해결
이 문제를 해결하기 위해서는, 베이스 클래스는 선언된 순서대로 초기화된다는 점을 이용한다. 파생 클래스는 베이스 클래스의 순서를 조절한다. 다시 말하면, 초기화되는 순서를 조절한다. 파생 클래스 멤버를 초기화하기 위한 새 클래스를 추가한다. 새 클래스는 다른 베이스 클래스들보다 먼저 목록에 추가된다. 새 클래스는 가장 먼저 초기화 되어 생성자 매개변수로 넘겨진다.

```
#include <streambuf>
#include <ostream>

class fdoutbuf : public std::streambuf
{
public:
    explicit fdoutbuf(int fd);
    //...
};

struct fdostream_pbase // A newly introduced class.
{
    fdoutbuf sbuffer; // The member moved 'up' the hierarchy.
    explicit fdostream_pbase(int fd)
        : sbuffer(fd)
    {}
};

class fdostream
    : protected fdostream_pbase // This class will be initialized before the next one.
    , public std::ostream
{
public:
    explicit fdostream(int fd)
        : fdostream_pbase(fd),   // Initialize the newly added base before std::ostream.
          std::ostream(&sbuffer) //  Now safe to pass the pointer.
    {}
    //...
};

int main()
{
  fdostream standard_out(1);
  standard_out << "Hello, World\n";
  return 0;
}
```

fdostream_pbase 클래스는 새로 도입되고 sbuffer 멤버를 가지고 있다. fdostream 클래스는 이 새 클래스를 상속받고 베이스 클래스 리스트 목록의 std::ostream 앞에 새 클래스를 상속받는다. 이렇게 하면 sbuffer는 std::ostream 전에 초기화되어 넘겨진다.

## 활용
그리고 이 해결법은 boost 에 적용되어 base_from_member 클래스가 되었다.

```
#include <boost/utility/base_from_member.hpp>

#include <streambuf>  // for std::streambuf
#include <ostream>    // for std::ostream

class fdoutbuf
    : public std::streambuf
{
public:
    explicit fdoutbuf( int fd );
    //...
};

class fdostream
    : private boost::base_from_member<fdoutbuf>
    , public std::ostream
{
    // Helper typedef's
    typedef boost::base_from_member<fdoutbuf>  pbase_type;
    typedef std::ostream                        base_type;

public:
    explicit fdostream( int fd )
        : pbase_type( fd ), base_type( &member )
        {}
    //...
};
```
