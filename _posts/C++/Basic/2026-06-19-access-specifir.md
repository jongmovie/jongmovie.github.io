---
title: "클래스와 객체 3: 접근 지정자 및 인라인 함수"
date: 2026-06-17 10:30:00 +0900
categories: ["C++ 프로그래밍", "클래스와 객체"]
tags: ["C++ 프로그래밍", "접근 지정자", "인라인 함수"]
order: 3
math: true
---


# C++ 접근 지정자와 인라인 함수 정리

C++에서 클래스를 제대로 사용하려면 두 가지 개념을 반드시 이해해야 한다.

첫 번째는 **접근 지정자**이다. 접근 지정자는 클래스의 멤버 변수와 멤버 함수에 외부 코드가 어디까지 접근할 수 있는지를 결정한다.

두 번째는 **인라인 함수**이다. 인라인 함수는 아주 짧은 함수에서 발생할 수 있는 함수 호출 오버헤드를 줄이기 위해 사용된다.

이번 글에서는 C++의 접근 지정자 `public`, `private`, `protected`를 정리하고, 이어서 인라인 함수가 왜 필요한지 살펴본다.

---

# 1. 접근 지정자

## 1.1 접근 지정자란?

객체 지향 프로그래밍에서는 객체의 내부 데이터를 외부에서 마음대로 접근하지 못하도록 제한하는 것이 중요하다. 이를 **캡슐화**라고 한다.

C++에서는 클래스의 멤버 변수와 멤버 함수에 대해 접근 범위를 지정할 수 있다. 이를 **접근 지정자**라고 한다.

C++의 대표적인 접근 지정자는 다음 세 가지이다.

1. `public`
2. `private`
3. `protected`

각 접근 지정자는 다음과 같은 의미를 가진다.

| 접근 지정자      | 접근 가능 범위               |
| ----------- | ---------------------- |
| `public`    | 클래스 외부에서도 접근 가능        |
| `private`   | 클래스 내부의 멤버 함수에서만 접근 가능 |
| `protected` | 클래스 내부와 파생 클래스에서 접근 가능 |

---

## 1.2 public

`public`으로 선언된 멤버는 클래스 외부에서도 자유롭게 접근할 수 있다.

즉, 객체를 생성한 뒤 `객체명.멤버명` 형식으로 멤버 변수나 멤버 함수를 사용할 수 있다.

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

class Option
{
public:
    double S;
    double K;

    double payoff();
};

double Option::payoff()
{
    return max(S - K, 0.0);
}

int main()
{
    Option call;

    call.S = 100;
    call.K = 80;

    cout << "payoff is " << call.payoff() << endl;
}
```

실행 결과는 다음과 같다.

```text
payoff is 20
```

위 코드에서 `S`, `K`, `payoff()`는 모두 `public`으로 선언되어 있다.
따라서 `main()` 함수에서도 다음과 같이 직접 접근할 수 있다.

```cpp
call.S = 100;
call.K = 80;
call.payoff();
```

하지만 멤버 변수를 모두 `public`으로 열어두는 방식은 일반적으로 좋은 설계가 아니다.

예를 들어 옵션 가격 계산에서 `S`는 기초자산 가격, `K`는 행사가라고 할 수 있다. 만약 외부에서 이 값들을 아무 제한 없이 수정할 수 있다면, 객체의 상태가 의도하지 않게 바뀔 수 있다.

따라서 중요한 멤버 변수는 보통 `private`으로 숨기고, 필요한 경우 멤버 함수를 통해 접근하도록 설계한다.

---

## 1.3 private

`private`으로 선언된 멤버는 클래스 외부에서 직접 접근할 수 없다.
오직 클래스 내부의 멤버 함수만 접근할 수 있다.

다음 예제를 보자.

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

class Option
{
private:
    double S;
    double K;

public:
    double payoff();
};

double Option::payoff()
{
    return max(S - K, 0.0);
}

int main()
{
    Option call;

    // call.S = 100; // 오류
    // call.K = 80;  // 오류
}
```

위 코드에서 `S`와 `K`는 `private`으로 선언되어 있다.
따라서 `main()` 함수에서는 `call.S`, `call.K`처럼 직접 접근할 수 없다.

반면 `payoff()` 함수는 클래스의 멤버 함수이므로 `private` 멤버인 `S`와 `K`에 접근할 수 있다.

```cpp
double Option::payoff()
{
    return max(S - K, 0.0);
}
```

즉, `private`은 “외부 접근은 막지만, 클래스 내부에서는 사용할 수 있게 하는 접근 지정자”이다.

---

## 1.4 getter와 setter

멤버 변수를 `private`으로 만들면 외부에서 직접 값을 읽거나 바꿀 수 없다.
따라서 필요한 경우 값을 읽는 함수와 값을 변경하는 함수를 따로 제공한다.

값을 읽는 함수를 보통 **getter**, 값을 변경하는 함수를 **setter**라고 한다.

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

class Option
{
private:
    double S;
    double K;

public:
    double get_S();
    double get_K();

    void set_S(double S_);
    void set_K(double K_);

    double payoff();
};

double Option::get_S() { return S;}
double Option::get_K() {return K;}
void Option::set_S(double S_) { S = S_;}
void Option::set_K(double K_) { K = K_;}

double Option::payoff()
{
    return max(S - K, 0.0);
}

int main()
{
    Option call;

    call.set_S(100);
    call.set_K(80);

    cout << "S is " << call.get_S() << endl;
    cout << "K is " << call.get_K() << endl;
    cout << "payoff is " << call.payoff() << endl;
}
```

실행 결과는 다음과 같다.

```text
S is 100
K is 80
payoff is 20
```

이 방식의 장점은 멤버 변수에 대한 접근을 함수로 통제할 수 있다는 점이다.

예를 들어 행사가 `K`가 음수가 되면 안 된다고 가정해보자.
그렇다면 setter 함수 안에서 다음과 같이 조건 검사를 할 수 있다.

```cpp
void set_K(double K_)
{
    if (K_ >= 0)
        K = K_;
}
```

이처럼 `private` 멤버 변수와 `public` 멤버 함수를 함께 사용하면 객체의 내부 상태를 더 안전하게 관리할 수 있다.

---

## 1.5 클래스와 구조체의 기본 접근 지정자

C++에서 `class`와 `struct`는 매우 비슷하지만, 기본 접근 지정자가 다르다.

`class`의 기본 접근 지정자는 `private`이다.

```cpp
class Option
{
    double S; // private
    double K; // private
};
```

반면 `struct`의 기본 접근 지정자는 `public`이다.

```cpp
struct Option
{
    double S; // public
    double K; // public
};
```

즉, 다음 두 코드는 같은 의미이다.

```cpp
class Option
{
private:
    double S;
    double K;
};
```

```cpp
struct Option
{
public:
    double S;
    double K;
};
```

일반적으로 C++에서는 단순히 데이터를 묶는 용도라면 `struct`를 사용하고, 데이터와 동작을 함께 묶고 캡슐화를 적용하려면 `class`를 사용하는 경우가 많다.

---

## 1.6 protected

`protected`는 `private`과 비슷하게 클래스 외부에서는 직접 접근할 수 없다.
하지만 차이점이 있다.

`protected` 멤버는 해당 클래스 내부뿐만 아니라, 그 클래스를 상속받은 파생 클래스에서도 접근할 수 있다.
`protected`는 상속과 관련이 깊으므로, 더 자세한 내용은 상속을 다룰 때 함께 이해하는 것이 좋다.

---

# 2. 인라인 함수

## 2.1 함수 호출 오버헤드

함수는 반복되는 작업을 하나의 단위로 묶어 재사용할 수 있게 해준다.
하지만 함수 호출에는 약간의 비용이 발생한다.

일반적으로 함수가 호출되면 다음과 같은 작업이 필요할 수 있다.

1. 함수 호출 위치 저장
2. 인자 전달
3. 필요한 레지스터 저장
4. 함수 본문 실행
5. 반환값 전달
6. 호출한 위치로 복귀

물론 실제 과정은 컴파일러, CPU 구조, 최적화 옵션, 호출 규약에 따라 달라진다.
하지만 중요한 점은 함수 호출에도 일정한 오버헤드가 존재할 수 있다는 것이다.

특히 다음과 같이 함수 본문이 매우 짧은 경우에는 함수 내부의 연산보다 함수 호출 비용이 더 크게 느껴질 수 있다.

```cpp
int odd(int x)
{
    return x % 2;
}
```

이 함수는 정수 `x`가 홀수인지 확인하기 위해 `x % 2`를 반환한다.
함수 자체의 연산은 매우 작다.

이런 경우 함수 호출 비용을 관찰하기 위해 다음과 같은 실험 코드를 작성할 수 있다.

```cpp
#include <iostream>
#include <chrono>
using namespace std;
using namespace chrono;

#if defined(_MSC_VER)
__declspec(noinline)
#else
__attribute__((noinline))
#endif
int odd(int x)
{
    return x % 2;
}

int main()
{
    const int N = 100000000;

    long long sum = 0;

    auto start1 = steady_clock::now();

    for (int i = 1; i <= N; i++)
    {
        if (odd(i))
            sum += i;
    }

    auto end1 = steady_clock::now();

    long long sum1 = 0;

    auto start2 = steady_clock::now();

    for (int i = 1; i <= N; i++)
    {
        if (i % 2)
            sum1 += i;
    }

    auto end2 = steady_clock::now();

    auto elapsed1 = duration_cast<nanoseconds>(end1 - start1);
    auto elapsed2 = duration_cast<nanoseconds>(end2 - start2);

    cout << "elapsed time(function): " << elapsed1.count() << " ns" << endl;
    cout << "sum: " << sum << endl;

    cout << "elapsed time(direct):   " << elapsed2.count() << " ns" << endl;
    cout << "sum1: " << sum1 << endl;
}
```

여기서 사용한 코드는 컴파일러가 `odd()` 함수를 자동으로 인라인 처리하지 못하도록 하는 코드이다.

```cpp
#if defined(_MSC_VER)
__declspec(noinline)
#else
__attribute__((noinline))
#endif
```

Visual Studio 계열 컴파일러에서는 `__declspec(noinline)`을 사용하고, GCC나 Clang 계열 컴파일러에서는 `__attribute__((noinline))`을 사용한다.

이 코드는 표준 C++ 문법은 아니며, 컴파일러별 확장 기능이다.
즉, “컴파일러에게 이 함수를 인라인으로 확장하지 말라”고 지시하기 위한 코드이다.

함수 호출 비용을 비교하려면 이처럼 인라인 최적화를 막아야 한다.
그렇지 않으면 컴파일러가 `odd(i)`를 사실상 `i % 2`로 바꾸어버릴 수 있기 때문이다.

또한 시간 측정에는 `system_clock`보다 `steady_clock`이 더 적합하다.
`steady_clock`은 시간이 역행하지 않는 단조 증가 clock이므로 벤치마크에 더 안정적이다.

주의할 점도 있다.
이런 간단한 벤치마크는 컴파일러 최적화 옵션, 실행 환경, CPU 상태에 따라 결과가 달라질 수 있다. 따라서 한 번의 실행 결과만으로 절대적인 결론을 내리기보다는 여러 번 실행해 평균적인 경향을 보는 것이 좋다.

---

## 2.2 인라인 함수란?

인라인 함수는 함수 호출로 인한 오버헤드를 줄이기 위해 사용된다.

함수 앞에 `inline` 키워드를 붙이면 인라인 함수로 선언할 수 있다.

```cpp
inline int odd(int x)
{
    return x % 2;
}
```

인라인 함수의 기본 아이디어는 함수가 호출되는 위치에 함수 본문을 직접 삽입하는 것이다.

예를 들어 다음 코드가 있다고 하자.

```cpp
if (odd(i))
    sum += i;
```

컴파일러가 `odd()` 함수를 실제로 인라인 확장하면, 내부적으로는 다음과 비슷한 형태가 될 수 있다.

```cpp
if (i % 2)
    sum += i;
```

이렇게 되면 함수 호출 과정이 사라지므로 호출 오버헤드를 줄일 수 있다.

하지만 중요한 점이 있다.

`inline`은 컴파일러에게 “이 함수를 인라인 처리하면 좋겠다”고 알려주는 요청에 가깝다.
`inline`을 붙였다고 해서 컴파일러가 반드시 함수 호출을 없애는 것은 아니다.

반대로 `inline`을 붙이지 않아도 컴파일러가 필요하다고 판단하면 자동으로 인라인 확장할 수 있다.

즉, 현대 C++에서 `inline`은 단순히 “무조건 빠르게 만들어주는 키워드”라고 이해하면 안 된다.

---

## 2.3 인라인 함수 사용 예제

다음은 `odd()` 함수를 인라인 함수로 선언한 예제이다.

```cpp
#include <iostream>
#include <chrono>
using namespace std;
using namespace chrono;

inline int odd(int x)
{
    return x % 2;
}

int main()
{
    const int N = 100000000;

    long long sum = 0;

    auto start1 = steady_clock::now();

    for (int i = 1; i <= N; i++)
    {
        if (odd(i))
            sum += i;
    }

    auto end1 = steady_clock::now();

    long long sum1 = 0;

    auto start2 = steady_clock::now();

    for (int i = 1; i <= N; i++)
    {
        if (i % 2)
            sum1 += i;
    }

    auto end2 = steady_clock::now();

    auto elapsed1 = duration_cast<nanoseconds>(end1 - start1);
    auto elapsed2 = duration_cast<nanoseconds>(end2 - start2);

    cout << "elapsed time(inline function): " << elapsed1.count() << " ns" << endl;
    cout << "sum: " << sum << endl;

    cout << "elapsed time(direct):          " << elapsed2.count() << " ns" << endl;
    cout << "sum1: " << sum1 << endl;
}
```

이론적으로는 인라인 함수가 직접 코드를 작성한 경우와 비슷한 성능을 보일 수 있다.

하지만 실제 결과는 컴파일러 최적화 옵션에 따라 달라진다.

예를 들어 최적화 옵션을 켜면 컴파일러가 `inline` 키워드가 없어도 자동으로 인라인 처리할 수 있다.
반대로 함수가 너무 복잡하면 `inline` 키워드가 있어도 실제 인라인 확장을 하지 않을 수 있다.

---

## 2.4 클래스 내부에 정의한 멤버 함수와 인라인

C++에서는 클래스 선언부 안에 직접 정의한 멤버 함수는 암묵적으로 `inline` 함수로 취급된다.

예를 들어 다음 코드에서 생성자와 `payoff()` 함수는 클래스 내부에 직접 정의되어 있다.

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

class Option
{
private:
    double S;
    double K;

public:
    Option()
        : S(100), K(80)
    {
    }

    double payoff()
    {
        return max(S - K, 0.0);
    }
};

int main()
{
    Option call;

    cout << call.payoff() << endl;
}
```

위 코드에서 `payoff()` 앞에 `inline` 키워드를 붙이지 않았지만, 클래스 내부에서 정의했기 때문에 암묵적으로 인라인 함수로 취급된다.

즉, 다음과 비슷한 의미를 가진다.

```cpp
inline double payoff()
{
    return max(S - K, 0.0);
}
```

다만 여기서도 주의할 점이 있다.

클래스 내부에 정의된 함수가 “암묵적으로 inline 함수”라는 말은 C++ 문법 차원의 의미이다.
이것이 곧바로 “컴파일러가 반드시 함수 호출을 없애고 코드를 삽입한다”는 뜻은 아니다.

실제 인라인 확장 여부는 컴파일러가 최적화 과정에서 결정한다.

---

## 2.5 getter와 setter는 인라인 함수로 자주 사용된다

클래스에서 멤버 변수를 `private`으로 선언하면 외부에서 직접 접근할 수 없다.
따라서 getter와 setter를 만들어 값을 읽거나 변경하는 경우가 많다.

getter와 setter는 보통 코드가 매우 짧다.

```cpp
double get_S()
{
    return S;
}

void set_S(double S_)
{
    S = S_;
}
```

이런 함수들은 클래스 내부에 직접 정의하는 경우가 많고, 이 경우 암묵적으로 인라인 함수로 취급된다.

```cpp
class Option
{
private:
    double S;
    double K;

public:
    double get_S()
    {
        return S;
    }

    double get_K()
    {
        return K;
    }

    void set_S(double S_)
    {
        S = S_;
    }

    void set_K(double K_)
    {
        K = K_;
    }
};
```

이처럼 짧고 단순한 멤버 함수는 인라인 처리의 좋은 후보가 된다.

하지만 모든 함수를 무조건 클래스 내부에 작성하는 것이 좋은 것은 아니다.
함수가 길어지거나 복잡해지면 선언과 구현을 분리하는 편이 코드 가독성과 유지보수에 더 유리하다.

---

# 3. 정리

C++의 접근 지정자는 클래스의 멤버를 외부에 얼마나 공개할 것인지를 결정한다.

`public` 멤버는 클래스 외부에서 자유롭게 접근할 수 있다.
`private` 멤버는 클래스 내부에서만 접근할 수 있다.
`protected` 멤버는 클래스 내부와 파생 클래스에서 접근할 수 있다.

객체 지향 프로그래밍에서는 멤버 변수를 무조건 `public`으로 열어두기보다는, 필요한 데이터는 `private`으로 숨기고 getter와 setter 같은 `public` 멤버 함수를 통해 접근하도록 만드는 것이 일반적이다.

인라인 함수는 짧은 함수에서 발생할 수 있는 함수 호출 오버헤드를 줄이기 위해 사용된다.
하지만 `inline` 키워드는 컴파일러에게 강제하는 명령이 아니라 요청에 가깝다.

또한 클래스 내부에 직접 정의한 멤버 함수는 암묵적으로 인라인 함수로 취급된다.
따라서 getter, setter처럼 짧은 함수는 클래스 내부에 직접 정의하는 경우가 많다.

정리하면 다음과 같다.

| 개념                 | 핵심 내용                  |
| ------------------ | ---------------------- |
| `public`           | 외부에서 접근 가능             |
| `private`          | 클래스 내부에서만 접근 가능        |
| `protected`        | 클래스 내부와 파생 클래스에서 접근 가능 |
| `class` 기본 접근 지정자  | `private`              |
| `struct` 기본 접근 지정자 | `public`               |
| `inline`           | 함수 호출 오버헤드를 줄이기 위한 힌트  |
| 클래스 내부 정의 함수       | 암묵적으로 `inline` 취급      |

접근 지정자는 클래스의 안전한 설계를 위해 중요하고, 인라인 함수는 짧은 함수의 성능 최적화와 관련이 있다.
두 개념 모두 C++ 클래스를 이해하는 데 꼭 필요한 기본 개념이다.




