---
title: "솔리디티 배우기"
categories:
  - solidity, dapp, ethereum
tags:
  - solidity, dapp, ethereum
---

# 시작
DAPP 을 만들어보고자 솔리티디 문법을 살펴보기로 하자!
솔리디티를 배우는 여정을 쉽게 해주는 좋은 사이트가 있다.
만약 솔리디티를 배우고자 한다면 [크립토좀비](https://cryptozombies.io/ko) 에 등록해보자.

솔리디티는 sol 확장자를 가지는 파일을 컴파일 해야 한다.
아직은 문법만 익히기로 했으므로 크립토좀비가 제공하는 에디터를 사용한다.

# 솔리디티 시작
sol 확장자를 가지는 파일과 컴파일러를 마련했다면 솔리디티를 시작할 수 있다.
pragma 키워드를 이용하고, 솔리디티 버전을 명시해주면 솔리디티 구현을 시작할 수 있다.
크립토 좀비의 솔리디티 최신 버전은 0.4.19 이다.
2022년 3월을 눈 앞에 둔 지금은 0.8.12 버전까지 있다.

```
pragma solidity ^0.4.19;
```

# 문법
## 컨트랙트
컨트랙트는 블록 체인에 기록되는 데이터이다.
```
pragma solidity ^0.4.19;

contract ZombieFactory {
}
```

C++ 언어의 class 정도로 생각할 수 있겠다.

## 데이터 타입
크립토좀비 약 10단계까지는 string, int, uint (unsigned int) 정도가 있다.
구조체도 있는데, 다른 언어들과 유사하다.

배열은 vector와 유사한 면이 있다.
[] 사이에 명확한 사이즈를 명시해서 유한한 배열을 선언할 수도 있고, 명확한 사이즈를 명시하지 않고 무한한 배열을 선언할 수도 있다.
배열에 아이템을 추가 할 때는 push를 사용한다.

public은 contract 의 내외부에서 호출할 수 있고, private은 contract의 내부에서만 접근 가능하다.

```
pragma solidity ^0.4.19;

contract ZombieFactory {

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;

    ...
    zombies.push(Zombie(_name, _dna));
}
```

## 함수
함수 선언은 java script와 유사하다.
function 키워드로 시작하고, private 함수의 경우 암묵적으로 함수명을 _로 시작한다.
함수 인자의 경우도 인자명을 _로 시작한다. 반드시 그러할 필요는 없지만 암묵적인 규칙이다.

함수의 반환값은 함수명 뒤에 returns (type) 형식으로 명시한다.
함수가 getter 로만 사용될 경우 C++ 등에서는 const를 붙이지만 솔리디티에서는 view 키워드를 사용한다.

```
pragma solidity ^0.4.19;

contract ZombieFactory {

    uint dnaDigits = 16;
    uint dnaModulus = 10 ** dnaDigits;

    struct Zombie {
        string name;
        uint dna;
    }

    Zombie[] public zombies;

    function _createZombie(string _name, uint _dna) private {
        zombies.push(Zombie(_name, _dna));
    } 

    function _generateRandomDna(string _str) private view returns (uint) {
    }
}
```