# 09. C++ 프로그래밍

---

## 1. 기본 문법

### Namespace

이름 충돌 방지를 위한 논리적 그룹화

```cpp
namespace MyLib {
    void func() { }
}

MyLib::func();       // 명시적 사용
using namespace MyLib;  // 전체 사용 (권장하지 않음)
using MyLib::func;   // 특정 항목만 사용
```

### Overloading

같은 이름의 함수를 매개변수로 구분

```cpp
void print(int x);
void print(double x);
void print(const char* s);
```

### ⭕ Overloading과 Overriding의 차이는?

| 구분 | Overloading | Overriding |
|------|-------------|------------|
| 위치 | 같은 클래스 | 상속 관계 |
| 시그니처 | 다름 (매개변수) | 같음 |
| 결정 시점 | 컴파일 타임 | 런타임 (virtual) |

### References (참조자)

```cpp
int a = 10;
int& ref = a;  // ref는 a의 별명
ref = 20;      // a도 20이 됨
```

### ⭕ 포인터와 참조의 차이는?

| 구분 | 포인터 | 참조 |
|------|--------|------|
| NULL 가능 | O | X |
| 재할당 | O | X |
| 초기화 | 나중에 가능 | 선언 시 필수 |

참조가 더 안전하다.

### Range-based for

```cpp
vector<int> v = {1, 2, 3};
for (int x : v) { }        // 복사
for (int& x : v) { x++; }  // 참조 (수정 가능)
for (const auto& x : v) { } // 읽기 전용
```

### Inline 함수

```cpp
inline int square(int x) { return x * x; }
```

- 함수 호출 오버헤드 제거
- 컴파일러가 최종 결정 (힌트일 뿐)

---

## 2. 객체지향 프로그래밍

### 생성자와 소멸자

```cpp
class MyClass {
public:
    MyClass();                    // 기본 생성자
    MyClass(int x);               // 매개변수 생성자
    MyClass(const MyClass& other); // 복사 생성자
    MyClass(MyClass&& other);     // 이동 생성자 (C++11)
    ~MyClass();                   // 소멸자
};
```

### 초기화 리스트

```cpp
MyClass::MyClass(int x, int y) : m_x(x), m_y(y) { }
```

- 멤버 변수를 직접 초기화 (대입보다 효율적)
- const, 참조 멤버는 반드시 초기화 리스트 사용

### 상속

```cpp
class Derived : public Base {
    // public 상속: public → public, protected → protected
};
```

| 상속 유형 | public | protected | private |
|----------|--------|-----------|---------|
| public | public | protected | 접근 불가 |
| protected | protected | protected | 접근 불가 |
| private | private | private | 접근 불가 |

### ⭕ 가상 함수란? 가상 소멸자는 왜 필요한가?

```cpp
class Base {
public:
    virtual void func() { }      // 가상 함수
    virtual void pure() = 0;     // 순수 가상 함수
    virtual ~Base() { }          // 가상 소멸자 (중요!)
};
```

가상 함수는 런타임에 동적 바인딩되어 다형성을 구현한다. 기본 클래스 포인터로 파생 객체 삭제 시 가상 소멸자가 없으면 파생 클래스 소멸자가 호출되지 않아 메모리 누수가 발생한다.

**가상 함수 테이블 (vtable):**
- 가상 함수 주소를 저장하는 테이블
- 런타임에 올바른 함수 호출 (동적 바인딩)

---

## 3. 메모리 관리

### 동적 할당

```cpp
int* p = new int(10);    // 단일 객체
int* arr = new int[10];  // 배열

delete p;      // 단일 객체 해제
delete[] arr;  // 배열 해제
```

### ⭕ 스마트 포인터의 종류와 차이는?

| 타입 | 소유권 | 용도 |
|------|--------|------|
| `unique_ptr` | 단독 소유 | 대부분의 경우 |
| `shared_ptr` | 공유 소유 | 여러 곳에서 참조 |
| `weak_ptr` | 비소유 참조 | 순환 참조 방지 |

```cpp
auto p1 = std::make_unique<MyClass>();   // unique_ptr
auto p2 = std::make_shared<MyClass>();   // shared_ptr
```

### ⭕ RAII란?

Resource Acquisition Is Initialization

자원 획득을 객체 초기화에, 해제를 소멸자에 맡겨 자원 누수를 방지하는 패턴이다. 스마트 포인터, lock_guard 등이 이 패턴을 따른다.

---

## 4. 템플릿

### 함수 템플릿

```cpp
template <typename T>
T max(T a, T b) {
    return (a > b) ? a : b;
}
```

### 클래스 템플릿

```cpp
template <typename T>
class Container {
    T data;
public:
    void set(T value) { data = value; }
    T get() { return data; }
};
```

### 템플릿 특수화

```cpp
template <>
class Container<bool> {
    // bool에 대한 특수 구현
};
```

---

## 5. STL

### STL 컨테이너

| 컨테이너 | 특징 | 시간 복잡도 (접근/삽입/삭제) |
|----------|------|---------------------------|
| `vector` | 동적 배열 | O(1) / O(1)* / O(n) |
| `deque` | 양방향 큐 | O(1) / O(1) / O(n) |
| `list` | 이중 연결 리스트 | O(n) / O(1) / O(1) |
| `set/map` | 레드-블랙 트리 | O(log n) |
| `unordered_set/map` | 해시 테이블 | O(1) 평균 |

### 반복자 (Iterator)

```cpp
vector<int>::iterator it;
for (it = v.begin(); it != v.end(); ++it) {
    cout << *it << endl;
}
```

### 알고리즘

```cpp
#include <algorithm>

sort(v.begin(), v.end());
find(v.begin(), v.end(), target);
count(v.begin(), v.end(), value);
transform(v.begin(), v.end(), result.begin(), func);
```

---

## 6. 모던 C++

### C++11 주요 기능

- `auto` 키워드
- Range-based for
- Lambda 표현식
- 스마트 포인터
- `nullptr`
- `constexpr`
- 이동 의미론 (Move semantics)

### Lambda 표현식

```cpp
auto lambda = [capture](params) -> return_type {
    // body
};

// 예시
auto add = [](int a, int b) { return a + b; };
```

### 이동 의미론

```cpp
std::vector<int> v1 = {1, 2, 3};
std::vector<int> v2 = std::move(v1);  // v1의 자원을 v2로 이동
```

---

## 7. 빌드 시스템

### Make

```makefile
target: dependencies
    commands

main: main.o utils.o
    g++ -o main main.o utils.o

main.o: main.cpp
    g++ -c main.cpp
```

### CMake

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

add_executable(main main.cpp utils.cpp)
target_link_libraries(main ${LIBS})
```

---

## 8. 타입 캐스팅

| 캐스팅 | 용도 |
|--------|------|
| `static_cast` | 일반적인 타입 변환 |
| `dynamic_cast` | 다형성 타입의 안전한 다운캐스팅 |
| `const_cast` | const 제거/추가 |
| `reinterpret_cast` | 비트 레벨 재해석 (위험) |

---

## 9. 예외 처리 ◑

### try-catch-throw

```cpp
try {
    if (error) throw std::runtime_error("Error message");
} catch (const std::exception& e) {
    std::cerr << e.what() << std::endl;
} catch (...) {
    // 모든 예외 처리
}
```

### ⭕ 예외 안전성 (Exception Safety) ◑

| 수준 | 보장 내용 |
|------|----------|
| No-throw | 예외 발생 안 함 (noexcept) |
| Strong | 실패 시 원래 상태로 복원 |
| Basic | 불변 조건 유지, 자원 누수 없음 |
| No guarantee | 보장 없음 |

**RAII**를 사용하면 예외 발생 시에도 자원이 안전하게 해제된다.

---

## 10. 동시성 프로그래밍 ◑

### std::thread ◑

```cpp
#include <thread>

void task(int id) {
    std::cout << "Thread " << id << std::endl;
}

int main() {
    std::thread t1(task, 1);
    std::thread t2(task, 2);

    t1.join();   // 완료 대기
    t2.join();

    // 또는
    t1.detach(); // 백그라운드 실행 (주의: 프로그램 종료 시 문제)
}
```

### ⭕ Mutex와 Lock ◑

```cpp
#include <mutex>

std::mutex mtx;
int counter = 0;

void increment() {
    // 방법 1: lock/unlock (비권장)
    mtx.lock();
    ++counter;
    mtx.unlock();

    // 방법 2: lock_guard (RAII, 권장)
    {
        std::lock_guard<std::mutex> lock(mtx);
        ++counter;
    } // 스코프 종료 시 자동 unlock

    // 방법 3: unique_lock (더 유연)
    {
        std::unique_lock<std::mutex> lock(mtx);
        ++counter;
        lock.unlock();  // 수동 제어 가능
        lock.lock();
    }
}
```

| 타입 | 특징 |
|------|------|
| `lock_guard` | 단순, RAII, 중간에 unlock 불가 |
| `unique_lock` | 유연, 조건 변수와 함께 사용 |
| `scoped_lock` | (C++17) 여러 mutex 동시 락 |

### 조건 변수 (Condition Variable) ◑

```cpp
#include <condition_variable>

std::mutex mtx;
std::condition_variable cv;
bool ready = false;

void worker() {
    std::unique_lock<std::mutex> lock(mtx);
    cv.wait(lock, []{ return ready; });  // ready가 true가 될 때까지 대기
    // 작업 수행
}

void signal() {
    {
        std::lock_guard<std::mutex> lock(mtx);
        ready = true;
    }
    cv.notify_one();  // 대기 중인 하나의 스레드 깨움
    // cv.notify_all();  // 모든 대기 스레드 깨움
}
```

### Atomic ◑

락 없이 스레드 안전한 연산을 수행한다.

```cpp
#include <atomic>

std::atomic<int> counter(0);

void increment() {
    ++counter;  // 원자적 연산
    counter.fetch_add(1);  // 명시적
}
```

| 연산 | 설명 |
|------|------|
| `load()` | 값 읽기 |
| `store()` | 값 쓰기 |
| `exchange()` | 값 교환 |
| `compare_exchange_weak/strong()` | CAS 연산 |
| `fetch_add()`, `fetch_sub()` | 산술 연산 |

### async와 future ◑

```cpp
#include <future>

int compute() {
    return 42;
}

int main() {
    // 비동기 실행
    std::future<int> result = std::async(std::launch::async, compute);

    // 결과 대기
    int value = result.get();  // 블로킹

    // 상태 확인
    if (result.wait_for(std::chrono::seconds(1)) == std::future_status::ready) {
        // 준비됨
    }
}
```

| launch 정책 | 설명 |
|-------------|------|
| `std::launch::async` | 새 스레드에서 즉시 실행 |
| `std::launch::deferred` | get() 호출 시 현재 스레드에서 실행 |
| 기본값 | 구현에 따라 결정 |

### promise와 future ◑

```cpp
void producer(std::promise<int>& prom) {
    prom.set_value(42);  // 값 설정
}

int main() {
    std::promise<int> prom;
    std::future<int> fut = prom.get_future();

    std::thread t(producer, std::ref(prom));
    int value = fut.get();  // 값 수신

    t.join();
}
```

### ⭕ 데드락 방지 ◑

```cpp
// 잘못된 예: 데드락 발생 가능
void func1() {
    lock_guard<mutex> lock1(mtx1);
    lock_guard<mutex> lock2(mtx2);  // 순서 불일치 시 데드락
}

void func2() {
    lock_guard<mutex> lock1(mtx2);  // 역순!
    lock_guard<mutex> lock2(mtx1);
}

// 올바른 예: scoped_lock (C++17)
void safe_func() {
    std::scoped_lock lock(mtx1, mtx2);  // 동시에 락, 데드락 방지
}

// 또는 std::lock 사용 (C++11)
void safe_func2() {
    std::lock(mtx1, mtx2);  // 데드락 방지 알고리즘
    std::lock_guard<std::mutex> lock1(mtx1, std::adopt_lock);
    std::lock_guard<std::mutex> lock2(mtx2, std::adopt_lock);
}
```

### 동시성 관련 주의사항 ◑

| 문제 | 설명 | 해결 |
|------|------|------|
| 데이터 레이스 | 동시 접근 중 최소 하나가 쓰기 | mutex, atomic |
| 데드락 | 상호 대기 | 락 순서 일관성, scoped_lock |
| 라이브락 | 진행 없이 상태만 변경 | 백오프 전략 |
| 기아 | 특정 스레드가 자원 획득 못함 | 공정한 스케줄링 |

---

## 면접 대비 체크리스트 ◑

- [ ] Overloading vs Overriding
- [ ] 포인터 vs 참조
- [ ] 가상 함수와 vtable
- [ ] 가상 소멸자가 필요한 이유
- [ ] 스마트 포인터 (unique_ptr, shared_ptr, weak_ptr)
- [ ] RAII 패턴
- [ ] 복사 vs 이동 의미론
- [ ] Lambda 표현식
- [ ] STL 컨테이너별 특징
- [ ] C++ 캐스팅 종류
- [ ] std::thread, mutex, lock_guard
- [ ] 조건 변수 (condition_variable)
- [ ] atomic 연산
- [ ] async/future/promise
- [ ] 데드락 방지 방법
