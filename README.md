# Becoming-a-Better-Programmer_Code-Practice
> 더 나은 프로그래머 되는 법 한국어 판의 코드 예제를 직접 돌려보고, genAI를 통해서 추가적인 분석 자료를 기록하는 저장소

### **📌 코드 분석 및 문제점**

#### **🔹 코드 원본**
```cpp
bool ok = thisCouldGoWrong();
if (!ok)
    fprintf(stderr, "Error: existing...\n");
    exit(0);
```

---

## **1️⃣ 코드의 의도**
1. `thisCouldGoWrong()` 함수가 실행되고 그 반환값이 `ok` 변수에 저장됨.
2. 만약 `ok`가 `false`라면(`!ok`), **에러 메시지를 출력**하려고 함.
3. 이후 `exit(0);`을 호출하여 프로그램을 종료하려고 함.

💡 **즉, 의도는**  
- `thisCouldGoWrong()` 함수가 실패했을 경우(`false` 반환)  
- `"Error: existing...\n"` 메시지를 `stderr`로 출력한 뒤  
- 프로그램을 종료하는 것.

---

## **2️⃣ 코드의 문제점**
### **(1) `exit(0);` 사용이 적절하지 않음**
- `exit(0);`은 **정상 종료**(Success)를 의미함.
- 하지만, 이 코드에서는 **에러 상황에서 종료하려는 것**이므로 `exit(1);` 또는 다른 **비정상 종료 코드**를 사용해야 함.

✅ **수정 방법**:
```cpp
exit(1);  // 에러 발생 시 비정상 종료
```
또는
```cpp
exit(EXIT_FAILURE);  // C 표준에 정의된 실패 코드
```

---

### **(2) `if` 문의 잘못된 중괄호 사용**
```cpp
if (!ok)
    fprintf(stderr, "Error: existing...\n");
    exit(0);
```
- `if (!ok)` 다음에 `{}` 중괄호가 없음.
- 따라서, `if` 문에 의해 실행되는 코드는 **`fprintf()` 한 줄만 포함됨**.
- `exit(0);`는 **`if` 문과 무관하게 항상 실행됨**.

💡 **실제 동작**
```cpp
if (!ok) 
    fprintf(stderr, "Error: existing...\n");  // if문에 포함됨

exit(0);  // if문과 관계없이 항상 실행됨!
```
즉, `thisCouldGoWrond()`가 `true`를 반환해도 `exit(0);`이 실행되어 **항상 프로그램이 종료됨**.

✅ **올바른 수정**
```cpp
if (!ok) {
    fprintf(stderr, "Error: existing...\n");
    exit(EXIT_FAILURE);
}
```
또는 **한 줄로 처리**
```cpp
if (!ok) { fprintf(stderr, "Error: existing...\n"); exit(EXIT_FAILURE); }
```


---

## **🔹 최종 수정된 코드 (버그 수정 + 개선)**
```cpp
bool ok = thisCouldGoWrong();
if (!ok) {
    fprintf(stderr, "Error: exiting...\n");  // existing → exiting 수정
    exit(EXIT_FAILURE);  // 정상 종료(exit(0)) 대신 비정상 종료 사용
}
```

---

## **📌 정리**
| 문제 | 원인 | 해결 방법 |
|------|------|----------|
| `exit(0);` 사용 | `0`은 정상 종료를 의미하므로, 에러 발생 시 부적절 | `exit(1);` 또는 `exit(EXIT_FAILURE);` 사용 |
| `if` 문 중괄호 없음 | `exit(0);`이 항상 실행됨 | `{}`로 블록을 감싸서 논리 수정 |
| `existing...\n` 오타 | `exiting...\n`이 더 적절함 | `"Error: exiting...\n"`으로 변경 |


