# lecture_01_05_매직넘버_종민

## 상수 (constant)

- 수식에서 변하지 않는 값을 뜻한다. 변수와 반대 되는 의미이다.
- ex) f(x)=x+1 함수에서 x값은 변수를 의미하고, x값이 어떠하든 간에 변하지 않고 항상 1인 수 즉 상수라고 한다.

## 배열의 크기

```jsx
#include <stdio.h>
#include <stddef.h>

int main() {
    int arr[] = {10, 20, 30, 40, 50};

    // 배열의 크기를 계산하여 요소 개수를 출력
    size_t arrSize = sizeof(arr) / sizeof(arr[0]);

		printf("배열 단일 크기 : %d\n", sizeof(arr[0]));
    printf("배열의 크기: %zu\n", arrSize);

    return 0;
}
```

이 때 sizeof(arr) 은 전체 배열의 바이트 크기를 나타내고, sizeof(arr[0])는 1개의 배열의 크기를 구할 수 있습니다. 따라서 배열의 크기를 구하기 위해서는 sizeof(arr)/sizeof(arr[0]) 사용하여 배열의 크기를 구할 수 있습니다

## 참고

- int형은 4바이트를 나타냅니다 → printf(”%d”, sizeof(arr[0])); 결과 값은 4 를 나타냅니다
- C언어 에서는 데이터 형식에 따라서  ‘d%’(정수), ‘%f’(부동 소수점), ‘%s’(문자열) 출력으로 나뉘게 되는데 ‘%z’ 는 데이터 형식을 출력하기 위한 크기 변환 지정자이다. 크기에 따라 적절한 크기의 문자열을 출력하는데 도움을 준다. ‘u’는 부호 없는 정수를 나타낸다.
- size_t데이터 형식은 <stddef.h> 헤더 파일에서 정의되며 C언어에서 크기와 관련된 여러 가지 데이터 형식을 정의하고, 주로 포인터 연산 및 배열 크기 계산과 같은 작업에 사용된다.
일반적으로 size_t는 배열 크기, 메모리 할당 및 해제, sizeof 연산자와 같은 C프로그램에서 메모리와 크기와 관련된 작업을 수행할 때 사용됩니다.
size_t는 부호 없는 정수 데이터 형식으로 메모리 주소, 배열 요소의 인덱스 또는 데이터 크기를 나타내는데 적합하다.

### 데이터 타입

1. **정수형 (Integer Types)**:
    - **`int`**: 정수를 나타내는 기본 데이터 형식. 일반적으로 시스템에 따라 2 또는 4 바이트입니다.
    - **`char`**: 문자를 나타내는 데이터 형식. 보통 1 바이트입니다.
    - **`short`**: 짧은 정수를 나타내는 데이터 형식. 일반적으로 2 바이트입니다.
    - **`long`**: 긴 정수를 나타내는 데이터 형식. 시스템에 따라 4 또는 8 바이트입니다.
    - **`long long`**: 매우 큰 정수를 나타내는 데이터 형식. 일반적으로 8 바이트입니다.
2. **부동 소수점형 (Floating-Point Types)**:
    - **`float`**: 단정밀도 부동 소수점 수를 나타내는 데이터 형식.
    - **`double`**: 배정밀도 부동 소수점 수를 나타내는 데이터 형식. 일반적으로 **`float`**의 두 배 정도의 정밀도를 가집니다.
    - **`long double`**: 더 높은 정밀도의 부동 소수점 수를 나타내는 데이터 형식.
3. **문자형 (Character Types)**:
    - **`char`**: 문자를 나타내는 데이터 형식.
4. **부울형 (Boolean Type)**:
    - **`_Bool`** 또는 **`bool`** (C99 이상): 참(true) 또는 거짓(false) 값을 나타내는 데이터 형식.
5. **포인터형 (Pointer Types)**:
    - **`T*`**: **`T`** 형식의 변수를 가리키는 포인터 데이터 형식.
6. **배열형 (Array Types)**:
    - **`T[]`** 또는 **`T[N]`**: **`T`** 형식의 요소를 가지는 배열 데이터 형식.
7. **구조체형 (Struct Types)**:
    - **`struct`**: 여러 데이터 형식을 하나로 묶어 사용하는 사용자 정의 데이터 형식.
8. **공용체형 (Union Types)**:
    - **`union`**: 메모리를 공유하는 다른 데이터 형식을 하나의 공용체로 표현하는 데이터 형식.
9. **열거형 (Enum Types)**:
    - **`enum`**: 정수 값에 이름을 부여하는 열거형 데이터 형식.
10. **비트 필드 (Bit-Field Types)**:
    - 비트 필드를 사용하여 특정 비트 수만큼의 메모리를 예약하는 데이터 형식.
11. **void 형식 (Void Type)**:
    - **`void`**: 어떠한 값도 가지지 않는 데이터 형식으로, 주로 함수의 반환 값으로 사용됨.

# 1.5.1 매직넘버에 이름을 달아주라

- 0과1을 제외한 모든 숫자는 매직넘버로 취급하고 이름을 달아줘야 한다.

## 열거형(Enumeration)

```jsx
#include <stdio.h>

// 열거형 정의
enum Day {
    SUNDAY,    // 0
    MONDAY,    // 1
    TUESDAY,   // 2
    WEDNESDAY, // 3
    THURSDAY,  // 4
    FRIDAY,    // 5
    SATURDAY   // 6
};

int main() {
    enum Day today = WEDNESDAY;
    
    printf("오늘은 %d번째 날입니다.\n", today);
    
    return 0;
}
```

- 상수를 정의할 때 사용되는 데이터 형식입니다. 관련된 상수들을 묶어 이름을 부여하고, 이러한 이름을 사용하여 코드를 읽기 쉽게 만들고, 매직 넘버를 피하고 코드를 더 의미 있게 만들 수 있습니다.

## 1.5.2 숫자는 매크로로 쓰지 말고 상수로 정의하라

### 선행 처리자

- 선행 처리자는 컴파일 이전 단계에서 코드를 처리하기 때문에, 컴파일러에게 컴파일러 지시사항을 제공하거나 코드를 변환하는 데 사용됩니다.

1. 매크로(Macro) 처리
#define 지시어를 사용하여 매크로를 정의하고, 코드 내에서 매크로를 사용한 곳에 해당 매크로 값을 대체합니다.

```jsx
#define MAX_VALUE 100
int value = MAX_VALUE;
```

1. 조건 컴파일(Conditional Compilation)
#ifdef, #ifndef, #else, #elif, #endif 등의 지시어를 사용하여 특정 조건이 충족되는 경우에만 코드 블록을 컴파일하도록 할 수 있습니다. 이를 통해 특정 플랫폼 또는 환경에 대한 코드를 컴파일하거나 제외할 수 있습니다.

```jsx
#ifdef DEBUG
    // 디버그 모드에서만 컴파일되는 코드
#endif
```

1. 파일 포함(Header Files)
선행차리자는 #include 지시어를 사용하여 다른 헤더 파일을 현재 파일에 포함시킵니다. 이를 통해 함수 원형과 상수, 구조체 등을 다른 파일에서 가져와 사용할 수 있습니다.

```jsx
#include <stdio.h>
```

1. 기호 상수(Constant Macros)
선행 처리자는 #define을 사용하여 기호 상수를 정의할 수 있습니다. 이러한 기호 상수는 코드 내에서 사용되는 숫자나 문자열을 읽기 쉽게 만들고, 코드를 변경할 때 일관성을 유지하는 데 도움이 됩니다.

```jsx
#define PI 3.14159265359
```

## 1.5.3  아스키 문자는 숫자 코드 말고 문자 상수로 쓰라

### <ctype.h>

- 표준 라이브러리 헤더 파일 중 하나이다.
- 문자 형식(character type)과 관련된 함수와 매크로를 정의하고 있다.
    1. **`isalpha(int c)`**: 주어진 문자 **`c`**가 알파벳 문자인지 여부를 확인합니다.
    2. **`isdigit(int c)`**: 주어진 문자 **`c`**가 숫자 문자인지 여부를 확인합니다.
    3. **`isalnum(int c)`**: 주어진 문자 **`c`**가 알파벳이나 숫자 문자인지 여부를 확인합니다.
    4. **`islower(int c)`**: 주어진 문자 **`c`**가 소문자인지 여부를 확인합니다.
    5. **`isupper(int c)`**: 주어진 문자 **`c`**가 대문자인지 여부를 확인합니다.
    6. **`toupper(int c)`**: 주어진 소문자 문자 **`c`**를 대문자로 변환합니다.
    7. **`tolower(int c)`**: 주어진 대문자 문자 **`c`**를 소문자로 변환합니다.

### 데이터 타입에 따른 0의 표현 방법

1. **정수 (int) 및 부동 소수점 (float, double) 변수:**
    - 정수 변수에 0을 할당하거나 초기화할 때는 그냥 0을 사용합니다.
    
    ```jsx
    int num = 0;
    float floatValue = 0.0;
    double doubleValue = 0.0;
    ```
    
2. **문자 (char) 변수:**
    - 문자 변수에 0을 할당할 때는 문자 상수 '0'을 사용합니다. 이것은 문자 '0'의 ASCII 값인 48을 나타냅니다.
    
    ```jsx
    char character = '0';
    ```
    
3. **포인터 (pointer) 변수:**
    - 포인터 변수에 널 포인터(NULL)를 할당할 때는 **`NULL`** 매크로를 사용합니다.
    
    ```jsx
    int *ptr = NULL;
    ```
    
    *는 포인터 변수를 선언할 때 사용되는 중요한 연산자 입니다.
    NULL은 따로 정의 해주지 않아도 헤더 파일 ‘<stddef.h>’ 또는 ‘<stdio.h>’ 또는 ‘<stdlib.h>’에 저장되어 있다.
    
4.  **불리언 (bool) 변수 (C99 이상):**
    - 불리언 변수는 **`stdbool.h`** 헤더 파일을 포함하고 **`true`** 및 **`false`** 상수를 사용하여 0 또는 1을 나타냅니다.
    
    ```jsx
    #include <stdbool.h>
    bool isTrue = true;
    bool isFalse = false;
    ```
    

## 1.5.4 언어에서 제공하는 것을 써서 객체의 크기를 계산하라

### sizeof()

- **`sizeof()`**는 C 및 C++ 프로그래밍 언어에서 사용되는 연산자입니다. 이 연산자는 주어진 데이터 유형 또는 변수의 크기를 바이트 단위로 반환합니다. 크기를 알고자 하는 변수나 데이터 유형을 **`sizeof()`** 연산자와 함께 사용하면 해당 변수나 데이터 유형의 메모리 크기를 구할 수 있습니다.
    1. C언어일 때
    
    ```jsx
    #include <stdio.h>
    
    int main() {
        int x;
        printf("int 변수의 크기: %zu 바이트\n", sizeof(x));
        return 0;
    }
    ```
    
    1. C++일 때
    
    ```jsx
    #include <iostream>
    
    int main() {
        int x;
        std::cout << "int 변수의 크기: " << sizeof(x) << " 바이트" << std::endl;
        return 0;
    }
    ```
    

## 연습 1-10

- 잠재적으로 발생할 수 있는 에러를 최소화하려면 어떻게 만드는 것이 좋을까?

```jsx
? #define FTZMETER 0.3048
? #define METERZFT 3.28084
? #define MIZFT 5280.0
? #define MIZKM 1.609344
? #define SQMIZSQKM 2.589988
```

- 정답(?)

```jsx
enum Answer{
		FTZMETER = 0.3048
		METERZFT = 3.28084
		MIZFT = 5280.0
		MIZKM = 1.609344
		SQMIZSQKM = 2.589988
}
```

매크로로 지정하는 것 보다 enum을 사용하여 정의한다.
