---
title: "include guard macro"
categories:
  - C++
tags:
  - C++
---

## 목적
헤더 인클루드가 중복되더라도 컴파일 에러를 발생시키지 않도록 함

## 원인
헤더 인클루드가 중복될 경우 헤더에서 정의되거나 선언된 변수 혹은 함수가 재정의 컴파일 에러를 발생


## 해결
전처리 구문을 통해 헤더가 중복돼 인클루드 되더라도 헤더 구현 내용이 컴파일 되지 않도록 함

```
#ifndef MYHEADER_H_ // beginning
#define MYHEADER_H_ 
...
#endif // MYHEADER_H_ // end
```

혹은

```
#pragma once
```

를 이용해 한 번만 컴파일 하도록 함
