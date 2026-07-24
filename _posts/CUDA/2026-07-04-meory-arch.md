---
title: "Chapter 8: 메모리 계층<스레드 수준 메모리>"
date: 2026-06-13 10:30:00 +0900
categories: ["CUDA", "메모리 계층"]
tags: ["CUDA", "CUDA 메모리 계층"]
order: 8
math: true
---

# CUDA 메모리 계층

## 1. 스레드 수준 메모리

스레드 수준 메모리는 각 스레드 내부에서 사용되기에 다른 스레드에서는 접근할 수 없는 메모리 공간으로 레지스터와 지역 메모리가 있다.

### 1.1. 레지스터

GPU 코어 연산을 위한 데이터를 담아 두고 사용하는 공간으로 스레드가 연산을 위해 데이터를 저장하는 공간이다.
레지스터는 'Kernel' 내부에서 선언된 지역변수를 위해 사용한다.

레지스터는 GPU 메모리 중 가장 빠른 메모리로 일반적으로 레지스터에 대한 접근은 GPU cycle보다 작은 시간이 소요된다.
하지만 GPU에서 가장 크기가 작은 메모리이다. SM 하나당 8K~64K개의 4Byte 레지스터를 가진다.

레지스터는 GPU의 SM 내부에 존재하는 매우 빠른 저장 공간이다.

GPU 스레드는 연산에 필요한 값과 중간 계산 결과를 자신의 레지스터에 저장한다. CUDA 커널 내부에서 선언한 지역 변수는 컴파일러의 판단에 따라 레지스터에 배치될 수 있다.

```cpp
__global__ void add(const float* a, const float* b, float* c)
{
    int index = blockIdx.x * blockDim.x + threadIdx.x;

    float x = a[index];
    float y = b[index];
    float result = x + y;

    c[index] = result;
}
```

위 코드에서 다음 지역 변수들은 컴파일러의 판단에 따라 레지스터에 저장될 수 있다.

```text
index
x
y
result
```

단, 커널 내부의 모든 지역 변수가 반드시 레지스터에 저장되는 것은 아니다. 레지스터가 부족하거나 배열처럼 동적 인덱싱이 필요한 지역 데이터는 Local Memory에 배치될 수 있다.

---

#### 레지스터는 블록이 아니라 SM에 존재한다

레지스터 파일은 각각의 블록 내부에 존재하는 것이 아니라 **SM 내부에 존재한다.**

```text
GPU
├── SM 0
│   ├── CUDA Core
│   ├── Warp Scheduler
│   ├── Register File
│   └── Shared Memory
│
├── SM 1
│   ├── CUDA Core
│   ├── Warp Scheduler
│   ├── Register File
│   └── Shared Memory
│
└── SM 2
    └── ...
```

블록이 SM에 배치되면 블록에 속한 모든 스레드가 사용할 레지스터가 해당 SM의 레지스터 파일에서 예약된다.

```text
SM의 Register File
├── Block 0의 스레드가 사용할 레지스터
├── Block 1의 스레드가 사용할 레지스터
└── Block 2의 스레드가 사용할 레지스터
```

블록마다 독립적으로 전체 레지스터 파일을 가지는 것은 아니다.

---

#### 하나의 블록은 하나의 SM에 배치된다

하나의 블록에 속한 모든 스레드는 동일한 SM에 배치된다.

```text
올바른 구조

Block 0 전체 → SM 0
Block 1 전체 → SM 1
Block 2 전체 → SM 0
```

하나의 블록을 여러 SM으로 나누어서 실행하지는 않는다.

```text
일반적으로 일어나지 않는 구조

Block 0의 절반   → SM 0
Block 0의 나머지 → SM 1
```

같은 블록의 모든 스레드가 동일한 SM에 배치되기 때문에 `Shared Memory`를 공유하고 `__syncthreads()`를 이용해 동기화할 수 있다.

---

#### 블록 할당에는 SM 자원이 필요하다

블록은 사용 가능한 SM에 배치되지만, 아무 조건 없이 배치되는 것은 아니다.

블록 하나를 SM에 배치하려면 다음 자원이 필요하다.

- 블록의 모든 스레드가 사용할 레지스터
- 블록이 요청한 Shared Memory
- 활성 스레드 슬롯
- 활성 워프 슬롯
- 활성 블록 슬롯

이 중 하나라도 부족하면 새로운 블록은 해당 SM에 동시에 배치될 수 없다.

---

#### 블록당 레지스터 사용량

블록 하나가 필요로 하는 레지스터 수는 개념적으로 다음과 같이 계산한다.

```text
블록당 레지스터 사용량
= 블록당 스레드 수 × 스레드당 레지스터 사용량
```

예를 들어 다음과 같이 가정한다.

```text
SM 전체 레지스터 수: 65,536개
블록당 스레드 수: 1,024개
스레드당 레지스터 사용량: 32개
```

블록 하나가 사용하는 레지스터 수는 다음과 같다.

```text
1,024 threads × 32 registers
= 32,768 registers
```

따라서 레지스터 자원만 고려하면 블록 2개가 하나의 SM에 동시에 배치될 수 있다.

```text
SM 전체 레지스터: 65,536개

Block 0
└── 1,024 × 32 = 32,768개

Block 1
└── 1,024 × 32 = 32,768개

전체 사용량
└── 32,768 + 32,768 = 65,536개
```

구조로 표현하면 다음과 같다.

```text
SM
├── Register File: 65,536개
│   ├── Block 0에 32,768개 예약
│   └── Block 1에 32,768개 예약
│
├── 활성 Block 0
│   └── 1,024 threads
│
└── 활성 Block 1
    └── 1,024 threads
```

이 예시는 해당 GPU가 SM당 2,048개의 활성 스레드를 허용하고, Shared Memory나 활성 워프 수 등의 다른 제한에도 걸리지 않는다고 가정한 것이다.

---

#### 레지스터 사용량이 많으면 활성 블록 수가 감소한다

같은 조건에서 스레드 하나가 레지스터를 40개 사용한다고 가정한다.

```text
블록당 레지스터 사용량
= 1,024 × 40
= 40,960개
```

블록 두 개를 동시에 배치하려면 다음 수의 레지스터가 필요하다.

```text
40,960 × 2
= 81,920개
```

하지만 SM에는 65,536개의 레지스터만 존재한다.

```text
필요한 레지스터: 81,920개
사용 가능한 레지스터: 65,536개

81,920 > 65,536
```

따라서 블록 2개를 동시에 배치할 수 없다.

```text
SM
├── 전체 레지스터: 65,536개
│
├── 활성 Block 0
│   └── 40,960개 사용
│
└── 남은 레지스터
    └── 65,536 - 40,960 = 24,576개
```

두 번째 블록도 40,960개의 레지스터가 필요하지만, 남은 레지스터는 24,576개이므로 배치할 수 없다.

```text
SM
└── 활성 Block 0만 배치 가능

Block 1
└── 대기
```

Block 0이 실행을 마치고 자원을 반환하면 대기 중이던 Block 1이 SM에 배치된다.

---

#### 활성 블록 수가 레지스터 수를 줄이는 것은 아니다

다음과 같이 이해하면 안 된다.

```text
블록 1개 배치
→ 스레드당 레지스터 64개 사용

블록 2개 배치
→ 스레드당 레지스터가 자동으로 32개로 감소
```

스레드당 레지스터 사용량은 일반적으로 커널 컴파일 과정에서 먼저 결정된다.

```text
커널 컴파일
    ↓
스레드당 필요한 레지스터 수 결정
    ↓
블록당 필요한 레지스터 수 계산
    ↓
SM 자원 안에서 활성 블록 수 결정
```

따라서 인과관계는 다음과 같다.

```text
스레드당 레지스터 사용량 증가
        ↓
블록당 레지스터 사용량 증가
        ↓
SM에 동시에 배치할 수 있는 블록 수 감소
```

즉, 활성 블록 수가 늘어나서 스레드당 레지스터 수가 줄어드는 것이 아니다.

> 스레드당 레지스터 사용량에 따라 SM에 동시에 배치할 수 있는 활성 블록 수가 결정된다.

---

#### 일반적인 자원 제한식

SM의 레지스터 수를 `R_SM`, 블록당 스레드 수를 `T`, 스레드당 레지스터 사용량을 `R_thread`, 활성 블록 수를 `B`라고 하면 다음 조건을 만족해야 한다.

```text
T × R_thread × B ≤ R_SM
```

예를 들어 다음 조건에서:

```text
R_SM     = 65,536
T        = 1,024
B        = 2
```

스레드당 레지스터 사용량은 다음 조건을 만족해야 한다.

```text
1,024 × R_thread × 2 ≤ 65,536

R_thread ≤ 32
```

이는 각 스레드에 레지스터가 자동으로 32개씩 배분된다는 뜻이 아니다.

> 스레드당 레지스터 사용량이 32개 이하여야 레지스터 자원 측면에서 1,024개 스레드 블록 2개를 동시에 배치할 수 있다는 뜻이다.

---

#### 실제 활성 블록 수를 결정하는 요소

실제 활성 블록 수는 레지스터만으로 결정되지 않는다.

```text
실제 활성 블록 수
= 다음 제한 중 가장 작은 값
```

- 레지스터 제한
- Shared Memory 제한
- SM당 최대 활성 스레드 수
- SM당 최대 활성 워프 수
- SM당 최대 활성 블록 수
- GPU 아키텍처별 자원 할당 단위

예를 들어 레지스터가 충분해도 SM당 최대 활성 스레드 수가 1,536개인 GPU에서는 1,024개 스레드 블록 2개를 동시에 배치할 수 없다.

```text
필요한 활성 스레드
= 1,024 × 2
= 2,048 threads

SM이 허용하는 최대 활성 스레드
= 1,536 threads

2,048 > 1,536
```

이 경우 레지스터가 충분하더라도 활성 블록은 1개로 제한된다.

---

#### 레지스터 스필

컴파일러가 필요한 값을 레지스터에 모두 저장하지 못하면 일부 데이터를 Local Memory로 이동시킬 수 있다.

이를 `Register Spilling`이라고 한다.

```text
필요한 레지스터가 너무 많음
        ↓
일부 값이 레지스터에 들어가지 못함
        ↓
Local Memory로 이동
        ↓
Global Memory 공간을 통한 접근
        ↓
접근 지연 증가 가능
```

따라서 레지스터 사용량을 무조건 줄이는 것이 항상 좋은 것은 아니다.

```text
레지스터를 많이 사용
├── 중간값을 빠르게 유지
└── 활성 블록 수 감소 가능

레지스터를 적게 사용
├── 활성 블록 수 증가 가능
└── Register Spilling 증가 가능
```

레지스터 사용량과 활성 블록 수 사이의 균형을 고려해야 한다.
하드웨어적으로 레지스터는 SM(스트리밍 멀티프로세서) 내부에 있는 메모리이고 이 처럼 SM내부에 있는 메모리를 인-코어 메모리(in-core memory)라고 부른다.

## 1.2. 지역 메모리(Local Meomry)

지역 메모리는 SM 밖에 있는 off-chip-memory로 접근 속도는 레지스터보다 느리지만 사용 가능한 메모리 공간이 크다.
물리적으로 GPU의 디바이스 메모리(DRAM) 공간 일부가 지역 메모리로 사용되고 각 스레드가 자신만의 공간을 갖는다.

지역 메모리 역시 각 스레드의 지역 변수를 위해 사용된다. 특히 레지스터를 사용하기에는 큰 구조체나 배열 등이 지역 메모리 공간을 사용한다.
그리고 일반 변수도 레지스터 공간을 할당 받지 못하면 지역 메모리 공간을 사용한다. 지역 메모리는 스레드당 512KB라는 제한이 있지만 스레드 하나가 지역 변수를 위해 사용하기에는 충분한 양이다.
### 1.2 지역 메모리(Local Memory)

지역 메모리는 각 스레드가 독립적으로 사용하는 전용 메모리 공간이다.

```text
Thread 0 → Thread 0 전용 Local Memory
Thread 1 → Thread 1 전용 Local Memory
Thread 2 → Thread 2 전용 Local Memory
...
```

다른 스레드는 특정 스레드의 지역 메모리에 직접 접근할 수 없다.  
지역 메모리는 레지스터처럼 스레드 전용이지만, 물리적으로는 SM 내부가 아니라 GPU의 Global Memory 주소 공간에 존재한다. 따라서 `Local`이라는 이름은 물리적인 위치가 아니라 데이터의 논리적인 접근 범위를 의미한다. :contentReference[oaicite:0]{index=0}

```text
GPU Device Memory(DRAM)
├── Global Memory 영역
│   ├── 사용자가 cudaMalloc으로 할당한 데이터
│   ├── Thread 0의 Local Memory
│   ├── Thread 1의 Local Memory
│   └── Thread 2의 Local Memory
│
└── ...
```

---

#### 지역 메모리의 물리적 위치

레지스터는 SM 내부에 위치하지만, 지역 메모리의 실제 저장 공간은 Device Memory에 존재한다.

```text
SM 내부
├── Register File
├── Shared Memory
├── L1 Cache
└── 실행 유닛
        │
        │ Local Memory 접근
        ▼
      L1 Cache
        ↓
      L2 Cache
        ↓
GPU Device Memory(DRAM)
```

따라서 지역 메모리는 레지스터보다 접근 지연이 크다. 다만 지역 메모리 접근이 항상 DRAM까지 내려가는 것은 아니며, 필요한 데이터가 L1 또는 L2 캐시에 존재하면 캐시에서 처리될 수 있다. :contentReference[oaicite:1]{index=1}

---

#### 모든 지역 변수가 Local Memory에 저장되는 것은 아니다

커널 내부에 선언된 변수를 C++ 관점에서는 지역 변수라고 부르지만, CUDA의 `Local Memory`에 반드시 저장되는 것은 아니다.

```cpp
__global__ void kernel(double* output)
{
    int tid = blockIdx.x * blockDim.x + threadIdx.x;

    double a = 1.0;
    double b = 2.0;
    double result = a + b;

    output[tid] = result;
}
```

위 코드의 다음 변수들은 대부분 컴파일러의 판단에 따라 레지스터에 저장될 가능성이 높다.

```text
tid
a
b
result
```

```text
C++ 지역 변수
      ↓ 컴파일러 판단
┌──────────────┬────────────────┐
│ 레지스터     │ Local Memory   │
└──────────────┴────────────────┘
```

즉, 다음 두 용어를 구분해야 한다.

```text
지역 변수
└── 함수 또는 커널 내부에 선언된 C++ 변수

Local Memory
└── 컴파일러가 일부 지역 변수를 배치하는 GPU 메모리 공간
```

NVIDIA는 컴파일러가 동적 인덱싱 배열, 큰 구조체나 배열, 레지스터에서 밀려난 변수 등을 Local Memory에 배치할 가능성이 높다고 설명한다. :contentReference[oaicite:2]{index=2}

---

#### 경우 1: 동적 인덱스를 사용하는 지역 배열

다음과 같은 지역 배열을 생각할 수 있다.

```cpp
__global__ void dynamic_index(
    const int* indices,
    double* output)
{
    int tid = blockIdx.x * blockDim.x + threadIdx.x;

    double values[64];

    for (int i = 0; i < 64; ++i) {
        values[i] = static_cast<double>(tid + i);
    }

    int index = indices[tid] & 63;
    output[tid] = values[index];
}
```

여기서 배열의 접근 위치는 실행 중에 결정된다.

```cpp
values[index]
```

컴파일러는 어떤 배열 원소가 사용될지 컴파일 시점에 확정하기 어렵다.

```text
index = 0일 수도 있음
index = 17일 수도 있음
index = 63일 수도 있음
```

GPU 레지스터는 일반 메모리 배열처럼 동적인 주소로 접근하는 구조가 아니므로, 이러한 배열은 Local Memory에 배치될 가능성이 높다. :contentReference[oaicite:3]{index=3}

개념적인 구조는 다음과 같다.

```text
Thread 0
└── Local Memory의 values[64]

Thread 1
└── Local Memory의 values[64]

Thread 2
└── Local Memory의 values[64]
```

모든 스레드가 자신만의 독립된 `values` 배열을 가진다.

---

#### 상수 인덱스 배열은 레지스터에 저장될 수도 있다

다음 배열은 접근 위치가 모두 컴파일 시간에 알려져 있다.

```cpp
__global__ void constant_index(double* output)
{
    int tid = blockIdx.x * blockDim.x + threadIdx.x;

    double values[4];

    values[0] = 1.0;
    values[1] = 2.0;
    values[2] = 3.0;
    values[3] = 4.0;

    output[tid] = values[0] + values[1];
}
```

컴파일러는 배열을 실제 메모리 배열로 만들지 않고 다음처럼 개별 스칼라 값으로 분해할 수 있다.

```text
values[0] → 레지스터
values[1] → 레지스터
values[2] → 사용되지 않으면 제거
values[3] → 사용되지 않으면 제거
```

이를 배열의 스칼라화라고 볼 수 있다.

따라서 지역 배열을 선언했다고 해서 항상 Local Memory를 사용하는 것은 아니다. 최종 배치는 배열 크기, 인덱싱 방법, 최적화 및 대상 GPU 아키텍처에 따라 컴파일러가 결정한다.

---

#### 경우 2: 큰 배열이나 구조체

다음처럼 스레드마다 큰 구조체를 선언할 수 있다.

```cpp
struct ThreadState
{
    double values[128];
    int indices[64];
};

__global__ void large_structure(double* output)
{
    int tid = blockIdx.x * blockDim.x + threadIdx.x;

    ThreadState state;

    for (int i = 0; i < 128; ++i) {
        state.values[i] = tid + i;
    }

    output[tid] = state.values[tid & 127];
}
```

이 구조체 전체를 레지스터에 저장하려면 매우 많은 레지스터가 필요하다.

```text
double values[128]
→ 128 × 8 bytes
→ 1,024 bytes

int indices[64]
→ 64 × 4 bytes
→ 256 bytes

전체 크기
→ 약 1,280 bytes/thread
```

또한 `state.values[tid & 127]`처럼 동적 인덱싱을 사용한다. 이런 큰 구조체나 배열은 레지스터 공간을 지나치게 많이 소비할 수 있으므로 Local Memory에 배치될 가능성이 높다. :contentReference[oaicite:4]{index=4}

---

#### 경우 3: Register Spilling

스레드가 연산을 위해 너무 많은 값을 동시에 유지해야 하면 필요한 레지스터 수가 증가한다.

```text
스레드가 필요로 하는 레지스터: 80개
컴파일러가 사용하도록 제한된 레지스터: 64개
```

부족한 16개의 값을 그대로 버릴 수는 없으므로, 컴파일러가 일부 값을 Local Memory에 저장할 수 있다.

```text
레지스터가 부족함
        ↓
일부 중간값을 Local Memory에 저장
        ↓
나중에 필요할 때 다시 읽음
```

이를 `Register Spilling`이라고 한다. NVIDIA 문서에서도 레지스터에 들어가지 못한 값이 Global Memory 공간으로 기록되고 다시 읽히는 현상으로 설명한다. :contentReference[oaicite:5]{index=5}

예를 들면 다음과 같다.

```cpp
__global__ void register_pressure(
    const double* input,
    double* output)
{
    int tid = blockIdx.x * blockDim.x + threadIdx.x;

    double a0  = input[tid];
    double a1  = a0 + 1.0;
    double a2  = a0 + 2.0;
    double a3  = a0 + 3.0;
    double a4  = a0 + 4.0;
    double a5  = a0 + 5.0;
    double a6  = a0 + 6.0;
    double a7  = a0 + 7.0;

    output[tid] =
        a0 + a1 + a2 + a3 +
        a4 + a5 + a6 + a7;
}
```

이 정도의 코드가 반드시 스필을 발생시키는 것은 아니다. 그러나 동시에 살아 있는 변수와 중간값이 매우 많아지면 레지스터 압력이 증가하며, 컴파일러가 일부 값을 Local Memory로 스필할 수 있다.

```text
레지스터 사용량 증가
        ↓
레지스터 파일 공간 부족 가능
        ↓
Register Spilling
        ↓
Local Memory 읽기·쓰기 증가
        ↓
성능 저하 가능
```

---

#### Local Memory 접근의 메모리 결합

Local Memory는 스레드별 전용 공간이지만, 물리적 데이터 배치는 워프의 연속된 스레드가 동일한 상대 위치에 접근할 때 메모리 요청을 결합할 수 있도록 구성된다. :contentReference[oaicite:6]{index=6}

예를 들어 모든 스레드가 자신의 배열에서 같은 인덱스를 읽는 경우이다.

```cpp
double value = local_array[3];
```

```text
Thread 0 → Thread 0의 local_array[3]
Thread 1 → Thread 1의 local_array[3]
Thread 2 → Thread 2의 local_array[3]
...
```

이 경우 각 스레드의 물리적 주소가 연속적으로 배치되므로 워프 단위 메모리 접근이 효율적으로 결합될 수 있다.

반대로 스레드마다 서로 다른 인덱스를 읽으면 접근 효율이 떨어질 수 있다.

```cpp
double value = local_array[threadIdx.x % 16];
```

```text
Thread 0 → local_array[0]
Thread 1 → local_array[1]
Thread 2 → local_array[2]
...
```

즉, Local Memory도 Global Memory와 마찬가지로 접근 패턴이 성능에 영향을 준다.

---

#### 스레드당 512KB 제한의 의미

현재 NVIDIA의 Compute Capability 표에서는 스레드당 Local Memory의 최대 크기를 512KB로 제시한다. :contentReference[oaicite:7]{index=7}

하지만 이것은 다음 의미가 아니다.

> 각 스레드가 512KB를 부담 없이 자유롭게 사용해도 된다.

예를 들어 블록당 스레드가 1,024개이고, 각 스레드가 Local Memory를 512KB씩 사용한다고 단순 계산하면 다음과 같다.

```text
512KB/thread × 1,024 threads
= 512MB/block
```

블록 하나의 스레드 전용 데이터만으로 약 512MB가 필요해진다.

따라서 512KB는 하드웨어·주소 공간 관점의 최대 제한이며, 일반적인 커널에서 권장되는 사용량을 의미하지 않는다. Local Memory를 많이 사용하면 Device Memory 사용량과 메모리 트래픽이 크게 증가할 수 있다.

다음 표현이 더 정확하다.

```text
기존 표현:
지역 메모리는 스레드당 512KB이므로 지역 변수를 저장하기에 충분하다.

수정 표현:
지역 메모리는 레지스터보다 큰 스레드 전용 데이터를 저장할 수 있지만,
Device Memory를 사용하므로 자주 접근하면 상당한 성능 비용이 발생할 수 있다.
현재 문서상 스레드당 최대 크기는 512KB이지만 실제 사용량은 가능한 한
작게 유지하는 것이 좋다.
```

---

#### 레지스터와 Local Memory 비교

| 구분 | 레지스터 | Local Memory |
|---|---|---|
| 논리적 접근 범위 | 스레드 전용 | 스레드 전용 |
| 물리적 위치 | SM 내부 | Global Memory 주소 공간 |
| 관리 주체 | 컴파일러 | 컴파일러 |
| 접근 속도 | 매우 빠름 | 레지스터보다 느림 |
| 캐시 사용 | 필요 없음 | L1/L2 캐시를 사용할 수 있음 |
| 주요 용도 | 작은 스칼라 변수와 중간값 | 큰 배열·구조체, 동적 인덱싱 배열, 스필된 값 |
| 스레드 간 공유 | 불가능 | 불가능 |
| 최대 용량 | 레지스터 수에 의해 제한 | 현재 문서상 최대 512KB/thread |

---

#### Local Memory 사용량 확인

소스 코드만 보고 Local Memory 사용량을 정확히 판단하기는 어렵다. 최종 결정은 대상 GPU 아키텍처에 맞게 코드를 컴파일하는 과정에서 이루어진다.

다음과 같이 컴파일할 수 있다.

```bash
nvcc -O3 -arch=sm_XX \
    -Xptxas=-v \
    -Xptxas=-warn-lmem-usage \
    -Xptxas=-warn-spills \
    main.cu -o main
```

각 옵션은 다음 용도로 사용할 수 있다.

```text
-Xptxas=-v
└── 레지스터와 Local Memory 등의 자원 사용량 출력

-Xptxas=-warn-lmem-usage
└── Local Memory가 사용되면 경고

-Xptxas=-warn-spills
└── Register Spilling이 발생하면 경고
```

현재 CUDA Programming Guide는 `-res-usage`, `-warn-lmem-usage`, `-warn-spills` 등의 컴파일 옵션을 통해 커널의 자원 사용량과 Local Memory 및 스필 여부를 확인할 수 있다고 설명한다. :contentReference[oaicite:8]{index=8}

실행 코드에서도 확인할 수 있다.

```cpp
cudaFuncAttributes attr{};

cudaFuncGetAttributes(&attr, kernel);

std::printf(
    "registers/thread = %d\n"
    "local memory/thread = %zu bytes\n",
    attr.numRegs,
    attr.localSizeBytes
);
```

`numRegs`는 스레드당 레지스터 수이고, `localSizeBytes`는 해당 커널이 스레드 하나당 사용하는 Local Memory 크기이다. :contentReference[oaicite:9]{index=9}





