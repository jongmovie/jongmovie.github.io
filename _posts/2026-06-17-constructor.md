---
title: "클래스와 객체 2: 생성자 및 소멸자"
date: 2026-06-17 10:30:00 +0900
categories: ["C++ 프로그래밍", "클래스와 객체"]
tags: ["C++ 프로그래밍", "클래스", "객체","생성자", "소멸자"]
order: 2
math: true
---


# C++ 생성자와 소멸자 정리

C++에서 클래스를 사용해 객체를 만들 때, 객체의 멤버 변수를 적절한 값으로 초기화해야 한다.
이때 사용하는 특별한 멤버 함수가 **생성자(Constructor)** 이다.

반대로 객체가 더 이상 사용되지 않고 사라질 때 자동으로 호출되는 함수가 있다.
이를 **소멸자(Destructor)** 라고 한다.

이번 글에서는 생성자, 위임 생성자, 소멸자, 그리고 객체의 생성·소멸 순서를 정리한다.

---

## 1. 생성자

## 1.1 생성자란?

생성자란 **객체가 생성될 때 자동으로 호출되는 특수한 멤버 함수**이다.

객체가 만들어지는 순간 멤버 변수를 초기화하거나, 객체 사용에 필요한 초기 작업을 수행하기 위해 사용한다.

예를 들어 옵션 가격 계산에 필요한 기초자산 가격 `S`와 행사가격 `K`를 가진 `Option` 클래스가 있다고 하자. 객체를 만들 때마다 `S`와 `K`를 직접 설정해주고 싶다면 생성자를 사용할 수 있다.

생성자의 특징은 다음과 같다.

* 생성자 이름은 반드시 클래스 이름과 같아야 한다.
* 생성자는 리턴 타입을 가지지 않는다.
* 생성자는 값을 리턴하지 않는다.
* 객체가 생성될 때 자동으로 한 번 호출된다.
* 생성자는 오버로딩할 수 있다.
* 매개변수의 개수나 타입이 다르면 여러 개의 생성자를 만들 수 있다.

즉, 생성자는 객체가 태어날 때 실행되는 초기화 함수라고 생각하면 된다.

---

## 1.2 생성자 함수 예제

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

class Option
{
public:
    double S;
    double K;

    Option();                  // 기본 생성자
    Option(double S_, double K_); // 매개변수가 있는 생성자

    double payoff();
};

Option::Option()
    : S(100), K(80)
{
    cout << "Stock: " << S << ", Strike: " << K << endl;
}

Option::Option(double S_, double K_)
    : S(S_), K(K_)
{
    cout << "Stock: " << S << ", Strike: " << K << endl;
}

double Option::payoff()
{
    return max(S - K, 0.0);
}

int main()
{
    Option call1;
    double payoff1 = call1.payoff();
    cout << "payoff1: " << payoff1 << endl;

    Option call2(110, 70);
    double payoff2 = call2.payoff();
    cout << "payoff2: " << payoff2 << endl;
}
```

실행 결과는 다음과 같다.

```text
Stock: 100, Strike: 80
payoff1: 20
Stock: 110, Strike: 70
payoff2: 40
```

위 코드에서 `call1` 객체가 생성될 때는 매개변수가 없는 생성자인 `Option()`이 호출된다.

```cpp
Option call1;
```

따라서 `S = 100`, `K = 80`으로 초기화된다.

반면 `call2` 객체가 생성될 때는 매개변수가 있는 생성자인 `Option(double S_, double K_)`가 호출된다.

```cpp
Option call2(110, 70);
```

따라서 `S = 110`, `K = 70`으로 초기화된다.

`call1`과 `call2`는 서로 다른 객체이므로 각각 별도의 메모리 공간을 가진다.
따라서 두 객체의 멤버 변수 `S`, `K` 역시 서로 독립적으로 존재한다.

---

## 1.3 기본 생성자

기본 생성자란 **매개변수가 없는 생성자**이다.

```cpp
Option();
```

클래스에 생성자를 하나도 작성하지 않으면, 컴파일러가 자동으로 기본 생성자를 만들어준다.

예를 들어 다음과 같은 클래스가 있다고 하자.

```cpp
class Option
{
public:
    double S;
    double K;
};
```

이 클래스에는 생성자를 직접 작성하지 않았다.
이 경우 컴파일러는 내부적으로 다음과 같은 기본 생성자를 자동으로 만들어준다.

```cpp
Option::Option() {}
```

하지만 주의할 점이 있다.

클래스에 생성자를 하나라도 직접 작성하면, 컴파일러는 기본 생성자를 자동으로 만들어주지 않는다.

예를 들어 다음과 같은 경우를 보자.

```cpp
class Option
{
public:
    double S;
    double K;

    Option(double S_, double K_);
};
```

이 클래스에는 매개변수가 있는 생성자가 이미 존재한다.
따라서 컴파일러는 `Option()` 기본 생성자를 자동으로 만들지 않는다.

그러므로 다음 코드는 오류가 발생한다.

```cpp
Option call1; // 오류
```

이 코드를 사용하고 싶다면 기본 생성자를 직접 작성해야 한다.

```cpp
class Option
{
public:
    double S;
    double K;

    Option();
    Option(double S_, double K_);
};
```

---

## 1.4 멤버 초기화 리스트

생성자에서 멤버 변수를 초기화할 때는 보통 다음과 같은 형태를 사용한다.

```cpp
Option::Option(double S_, double K_)
    : S(S_), K(K_)
{
    cout << "Stock: " << S << ", Strike: " << K << endl;
}
```

여기서

```cpp
: S(S_), K(K_)
```

부분을 **멤버 초기화 리스트(Member Initializer List)** 라고 한다.

이는 생성자 본문이 실행되기 전에 멤버 변수를 초기화하는 문법이다.

다음처럼 생성자 본문 안에서 대입하는 방식도 가능하다.

```cpp
Option::Option(double S_, double K_)
{
    S = S_;
    K = K_;
}
```

하지만 일반적으로 C++에서는 멤버 초기화 리스트를 사용하는 것이 더 권장된다.

특히 다음과 같은 경우에는 멤버 초기화 리스트가 반드시 필요하다.

* `const` 멤버 변수 초기화
* 참조형 멤버 변수 초기화
* 멤버 객체의 생성자 호출
* 불필요한 기본 생성 후 대입을 피하고 싶을 때

따라서 생성자에서는 가능한 한 멤버 초기화 리스트를 사용하는 습관을 들이는 것이 좋다.

---

# 2. 위임 생성자

## 2.1 위임 생성자란?

한 클래스 안에 여러 개의 생성자가 있을 때, 초기화 코드가 반복되는 경우가 많다.

예를 들어 기본 생성자와 매개변수가 있는 생성자 모두에서 다음 코드가 반복될 수 있다.

```cpp
cout << "Stock: " << S << ", Strike: " << K << endl;
```

이처럼 중복되는 초기화 코드를 줄이기 위해 C++11부터 **위임 생성자(Delegating Constructor)** 를 사용할 수 있다.

위임 생성자란 한 생성자가 같은 클래스의 다른 생성자를 호출하여 초기화 작업을 맡기는 방식이다.

---

## 2.2 위임 생성자 예제

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

class Option
{
public:
    double S;
    double K;

    Option();
    Option(double S_, double K_);

    double payoff();
};

Option::Option()
    : Option(100, 80)
{
}

Option::Option(double S_, double K_)
    : S(S_), K(K_)
{
    cout << "Stock: " << S << ", Strike: " << K << endl;
}

double Option::payoff()
{
    return max(S - K, 0.0);
}

int main()
{
    Option call1;
    double payoff1 = call1.payoff();
    cout << "payoff1: " << payoff1 << endl;

    Option call2(110, 70);
    double payoff2 = call2.payoff();
    cout << "payoff2: " << payoff2 << endl;
}
```

위 코드에서 기본 생성자는 다음과 같다.

```cpp
Option::Option()
    : Option(100, 80)
{
}
```

이 생성자는 직접 `S`와 `K`를 초기화하지 않는다.
대신 다음 생성자를 호출한다.

```cpp
Option::Option(double S_, double K_)
```

즉,

```cpp
Option call1;
```

이 실행되면 먼저 `Option()`이 호출되고, `Option()`은 다시 `Option(100, 80)`을 호출한다.

결과적으로 `call1`은 다음과 같이 초기화된다.

```cpp
S = 100;
K = 80;
```

여기서 실제 초기화 작업을 수행하는 생성자인

```cpp
Option::Option(double S_, double K_)
```

를 **타겟 생성자(Target Constructor)** 라고 한다.

그리고 다른 생성자에게 초기화 작업을 맡기는

```cpp
Option::Option()
```

를 **위임 생성자(Delegating Constructor)** 라고 한다.

위임 생성자를 사용하면 중복 코드를 줄이고, 초기화 로직을 한 곳에서 관리할 수 있다는 장점이 있다.

---

# 3. 소멸자

## 3.1 소멸자란?

소멸자란 **객체가 소멸될 때 자동으로 호출되는 특수한 멤버 함수**이다.

객체가 사라지기 전에 필요한 마무리 작업을 수행하기 위해 사용한다.

예를 들어 다음과 같은 작업을 소멸자에서 처리할 수 있다.

* 동적으로 할당한 메모리 해제
* 파일 닫기
* 네트워크 연결 종료
* 로그 출력
* 기타 자원 정리

소멸자의 이름은 클래스 이름 앞에 `~`를 붙여서 작성한다.

```cpp
~Option();
```

소멸자의 특징은 다음과 같다.

* 클래스 이름 앞에 `~`를 붙인다.
* 리턴 타입을 가지지 않는다.
* 값을 리턴하지 않는다.
* 매개변수를 가질 수 없다.
* 오버로딩할 수 없다.
* 한 클래스에는 소멸자가 하나만 존재할 수 있다.
* 직접 작성하지 않으면 컴파일러가 기본 소멸자를 자동으로 만들어준다.

즉, 생성자가 객체의 시작을 담당한다면 소멸자는 객체의 끝을 담당한다.

---

## 3.2 소멸자 함수 예제

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

class Option
{
public:
    double S;
    double K;

    Option();
    Option(double S_, double K_);
    ~Option();

    double payoff();
};

Option::Option()
    : Option(100, 80)
{
}

Option::Option(double S_, double K_)
    : S(S_), K(K_)
{
    cout << "Stock: " << S << ", Strike: " << K << endl;
}

Option::~Option()
{
    cout << "Stock: " << S << ", Strike: " << K
         << " Option is destructed" << endl;
}

double Option::payoff()
{
    return max(S - K, 0.0);
}

int main()
{
    Option call1;
    double payoff1 = call1.payoff();
    cout << "payoff1: " << payoff1 << endl;

    Option call2(110, 70);
    double payoff2 = call2.payoff();
    cout << "payoff2: " << payoff2 << endl;
}
```

실행 결과는 다음과 같다.

```text
Stock: 100, Strike: 80
payoff1: 20
Stock: 110, Strike: 70
payoff2: 40
Stock: 110, Strike: 70 Option is destructed
Stock: 100, Strike: 80 Option is destructed
```

여기서 주목해야 할 점은 소멸 순서이다.

`main()` 함수 안에서 객체는 다음 순서로 생성된다.

```cpp
Option call1;
Option call2(110, 70);
```

즉, 생성 순서는 다음과 같다.

```text
call1 생성
call2 생성
```

하지만 소멸은 생성의 반대 순서로 이루어진다.

```text
call2 소멸
call1 소멸
```

따라서 실행 결과에서도 `call2`가 먼저 소멸되고, 그 다음 `call1`이 소멸된다.

---

# 4. 생성자와 소멸자의 실행 순서

객체는 선언된 위치에 따라 크게 두 가지로 나눌 수 있다.

## 4.1 지역 객체

함수 안에서 선언된 객체를 **지역 객체(Local Object)** 라고 한다.

```cpp
void local()
{
    Option localcall(80, 100);
}
```

지역 객체는 함수가 실행될 때 생성되고, 함수가 종료될 때 소멸된다.

즉, `local()` 함수가 호출되면 `localcall` 객체가 생성되고, `local()` 함수가 끝나면 `localcall` 객체가 소멸된다.

---

## 4.2 전역 객체

함수 밖에서 선언된 객체를 **전역 객체(Global Object)** 라고 한다.

```cpp
Option globalcall(120, 100);
```

전역 객체는 `main()` 함수가 실행되기 전에 생성된다.
그리고 `main()` 함수가 종료된 뒤, 프로그램이 끝날 때 소멸된다.

즉, 전역 객체는 프로그램 전체 실행 시간 동안 존재한다.

---

## 4.3 생성자와 소멸자 실행 순서 예제

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

class Option
{
public:
    double S;
    double K;

    Option();
    Option(double S_, double K_);
    ~Option();

    double payoff();
};

Option::Option()
    : Option(100, 80)
{
}

Option::Option(double S_, double K_)
    : S(S_), K(K_)
{
    cout << "Stock: " << S << ", Strike: " << K << endl;
}

Option::~Option()
{
    cout << "Stock: " << S << ", Strike: " << K
         << " Option is destructed" << endl;
}

double Option::payoff()
{
    return max(S - K, 0.0);
}

Option globalcall(120, 100);

void local()
{
    Option localcall(80, 100);
}

int main()
{
    Option maincall(100, 110);

    local();

    return 0;
}
```

실행 결과는 다음과 같다.

```text
Stock: 120, Strike: 100
Stock: 100, Strike: 110
Stock: 80, Strike: 100
Stock: 80, Strike: 100 Option is destructed
Stock: 100, Strike: 110 Option is destructed
Stock: 120, Strike: 100 Option is destructed
```

실행 순서를 하나씩 살펴보면 다음과 같다.

먼저 전역 객체 `globalcall`이 생성된다.

```cpp
Option globalcall(120, 100);
```

전역 객체는 `main()`이 실행되기 전에 생성되므로 가장 먼저 출력된다.

```text
Stock: 120, Strike: 100
```

그 다음 `main()` 함수가 시작되고, `maincall` 객체가 생성된다.

```cpp
Option maincall(100, 110);
```

따라서 다음 출력이 발생한다.

```text
Stock: 100, Strike: 110
```

이후 `local()` 함수가 호출된다.

```cpp
local();
```

`local()` 함수 안에서는 지역 객체 `localcall`이 생성된다.

```cpp
Option localcall(80, 100);
```

따라서 다음 출력이 발생한다.

```text
Stock: 80, Strike: 100
```

`local()` 함수가 종료되면 지역 객체 `localcall`이 소멸된다.

```text
Stock: 80, Strike: 100 Option is destructed
```

그 다음 `main()` 함수가 종료되면서 `maincall` 객체가 소멸된다.

```text
Stock: 100, Strike: 110 Option is destructed
```

마지막으로 프로그램이 종료되면서 전역 객체 `globalcall`이 소멸된다.

```text
Stock: 120, Strike: 100 Option is destructed
```

결론적으로 생성과 소멸 순서는 다음과 같다.

```text
globalcall 생성
maincall 생성
localcall 생성
localcall 소멸
maincall 소멸
globalcall 소멸
```

---

# 5. 핵심 정리

## 5.1 생성자

생성자는 객체가 생성될 때 자동으로 호출되는 함수이다.

```cpp
Option();
Option(double S_, double K_);
```

생성자는 주로 멤버 변수를 초기화하거나 객체 생성 시 필요한 작업을 수행할 때 사용한다.

생성자의 핵심 규칙은 다음과 같다.

* 이름은 클래스 이름과 동일하다.
* 리턴 타입이 없다.
* 객체 생성 시 자동으로 호출된다.
* 매개변수에 따라 오버로딩할 수 있다.
* 생성자를 하나도 작성하지 않으면 컴파일러가 기본 생성자를 자동으로 만들어준다.
* 단, 생성자를 하나라도 직접 작성하면 컴파일러는 기본 생성자를 자동으로 만들지 않는다.

---

## 5.2 위임 생성자

위임 생성자는 한 생성자가 같은 클래스의 다른 생성자를 호출하는 방식이다.

```cpp
Option::Option()
    : Option(100, 80)
{
}
```

위임 생성자를 사용하면 중복 초기화 코드를 줄일 수 있다.

초기화 로직을 한 생성자에 모아둘 수 있으므로 코드 유지보수에도 유리하다.

---

## 5.3 소멸자

소멸자는 객체가 사라질 때 자동으로 호출되는 함수이다.

```cpp
~Option();
```

소멸자는 주로 객체가 사용한 자원을 정리할 때 사용한다.

소멸자의 핵심 규칙은 다음과 같다.

* 클래스 이름 앞에 `~`를 붙인다.
* 리턴 타입이 없다.
* 매개변수를 가질 수 없다.
* 오버로딩할 수 없다.
* 클래스마다 하나만 존재할 수 있다.
* 작성하지 않으면 컴파일러가 기본 소멸자를 자동으로 만들어준다.

---

## 5.4 생성자와 소멸자 실행 순서

지역 객체는 함수가 실행될 때 생성되고, 함수가 끝날 때 소멸된다.

전역 객체는 `main()` 함수가 실행되기 전에 생성되고, `main()` 함수가 종료된 뒤 소멸된다.

동일한 영역 안에서 여러 객체가 생성되면, 소멸은 생성의 반대 순서로 이루어진다.

```text
생성 순서: A → B → C
소멸 순서: C → B → A
```

즉, C++ 객체의 생명주기는 다음과 같이 이해할 수 있다.

```text
객체 생성 → 생성자 호출 → 객체 사용 → 소멸자 호출 → 객체 소멸
```

---

# 6. 마무리

생성자와 소멸자는 C++ 클래스의 가장 기본적인 개념 중 하나이다.

생성자는 객체가 생성될 때 필요한 초기화 작업을 담당하고, 소멸자는 객체가 사라질 때 필요한 정리 작업을 담당한다.

특히 C++에서는 객체의 생성과 소멸 시점이 명확하기 때문에, 생성자와 소멸자를 잘 활용하면 안정적인 자원 관리가 가능하다.

또한 C++11부터 제공되는 위임 생성자를 사용하면 여러 생성자 사이의 중복 코드를 줄이고, 초기화 로직을 더 깔끔하게 관리할 수 있다.

따라서 클래스를 설계할 때는 다음 세 가지를 항상 고려하는 것이 좋다.

```text
1. 객체가 생성될 때 어떤 값으로 초기화할 것인가?
2. 여러 생성자 사이에 중복 초기화 코드가 있는가?
3. 객체가 소멸될 때 정리해야 할 자원이 있는가?
```

이 세 가지를 기준으로 생성자와 소멸자를 설계하면 더 안정적이고 유지보수하기 좋은 C++ 코드를 작성할 수 있다.



