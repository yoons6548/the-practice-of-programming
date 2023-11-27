# 4.4 ) C++ 구현

```cpp
class Csv {
public:
    // 기본 매개변수를 가진 생성자
    Csv(istream& fin = cin, string sep = ",");

    // 멤버 함수들
    int getline(string&);
    string getfield(int n);
    int getnfield() const { return nfield; }

private:
    // 멤버 변수들
    istream& fin;          // 입력 파일 포인터
    string line;           // 입력 라인
    vector<string> field;  // 필드 문자열들
    int nfield;            // 필드의 수
    string fieldsep;       // 구분 문자

    // 비공개 멤버 함수들
    int split();           // 입력 라인을 필드로 분리
    int endofline(char);    // 문자가 라인의 끝을 나타내는지 확인
    int advplain(const string & line, string & fld, int);  // 다음 평범(따옴표로 묶이지 않은) 필드로 진행
    int advquoted(const string & line, string & fld, int);  // 다음 따옴표로 묶인 필드로 진행
};
```

- **`iostream& fin`**: 입력 파일 포인터로, 표준 입력(cin)을 기본값으로 가집니다.
- **`string line`**: 입력 라인을 저장하는 문자열 변수입니다.
- **`vector<string> field`**: 필드를 저장하는 문자열 벡터입니다.
- **`int nfield`**: 필드의 개수를 저장하는 변수입니다.
- **`string fieldsep`**: 필드를 구분하는 문자열로, 기본값은 쉼표(,)입니다.

### C++ 구현의 의미

1. C++ 문자열의 사용은 라이브러리 함수가 메모리를 관리할 것이기 때문에 일부 저장 관리 문제를 자동으로 해결할 수 있다. 
    
    **<C언어는 태어나서 처음 말하는 법을 배우는 언어, C++은 말을 조리있게 잘 하는 법을 배우는 언어>**
    
2. 필드 루틴은 호출자에 의해 수정될 수 있는 문자열을 반환할 것이며, 이는 이전 버전보다 더 유연한 디자인이 가능함
3. **`Csv`** 클래스는 구현의 변수와 함수를 깔끔하게 숨기면서 공개 인터페이스를 정의됨. 
4. 클래스 객체가 한 인스턴스의 모든 상태를 포함하므로 여러 Csv 변수를 인스턴스화할 수 있음. 

```cpp
// getline
int Csv::getline(string& str) {
    char c;
    
    for (line = ""; fin.get(c) && !endofline(c); )
        line += c;

    split();
    str = line;
    return !fin.eof();
}
```

```cpp
// endofline
int Csv::endofline(char c) {
    int eol;

    eol = (c == '\r' || c == '\n');
    if (c == '\r')
        fin.get(c);
    if (!fin.eof() && c != '\n')
        fin.putback(c); // read too far
    return eol;
}
// 줄의 끝을 확인하고 \r, \n, \r\n, 또는 EOF(End of File)를 처리
```

```cpp
// split
int Csv::split() {
    string fld;
    int i, j;
    nfield = 0;
    
    if (line.length() == 0)
        return 0;

    i = 0;
    do {
        if (i < line.length() && line[i] == '"')
            j = advquoted(line, fld, ++i); // skip quote
        else
            j = advplain(line, fld, i);

        if (nfield >= field.size())
            field.push_back(fld);
        else
            field[nfield] = fld;

        nfield++;
        i = j + 1;
    } while (j < line.length());

    return nfield;
}

// 이 함수는 줄을 필드로 나누는 역할을 합니다. 큰따옴표로 둘러싸인 부분은 advquoted 함수로 처리하고,
// 그 외에는 advplain 함수로 처리합니다. 나눈 필드들은 field 벡터에 저장되며
// 필요에 따라 크기가 동적으로 조절됩니다.
```

```c
char* acsvgetline(FILE* fin) {
    int i, c;
    char* newl, *news;

    if (line == NULL) {
        // Allocate memory on the first call
        maxline = maxfield = 1;
        line = (char*)malloc(maxline);
        sline = (char*)malloc(maxline);
        field = (char**)malloc(maxfield * sizeof(field[0]));

        if (line == NULL || sline == NULL || field == NULL) {
            reset();
            return NULL; // Out of memory
        }
    }

    for (i = 0; (c = getc(fin)) != EOF && !endofline(fin, c); i++) {
        if (i >= maxline - 1) {
            // Grow line
            maxline *= 2; // Double current size
            newl = (char*)realloc(line, maxline);
            news = (char*)realloc(sline, maxline);

            if (newl == NULL || news == NULL) {
                reset();
                return NULL; // Out of memory
            }

            line = newl;
            sline = news;
        }

        line[i] = c;
    }

    line[i] = '\0';

    if (split() == NOMEM) {
        reset();
        return NULL; // Out of memory
    }

    return (c == EOF && i == 0) ? NULL : line;
}
```

1. **메모리 할당 및 해제:**
    - **C 코드:** **`malloc`** 및 **`free`** 함수를 사용하여 수동으로 메모리를 할당 및 해제
    - **C++ 코드:** **`new`** 및 **`delete`** 연산자 또는 표준 라이브러리의 스마트 포인터 등을 사용하여 자동 메모리 관리를 수행
2. **문자열 다루기:**
    - **C 코드:** 문자열은 **`char`** 배열로 다루어지며, 길이 등을 직접 관리해야 함.
    - **C++ 코드:** **`std::string`** 클래스를 사용하여 편리하게 문자열을 다룰 수 있음. 문자열 길이 및 메모리 관리에 대한 비용이 감소.
3. **객체 지향 프로그래밍:**
    - **C 코드:** 객체 지향 프로그래밍을 직접적으로 지원하지 않음.
    - **C++ 코드:** 객체 지향 프로그래밍이 가능하며, 클래스를 사용하여 데이터와 관련 함수를 논리적으로 그룹화할 수 있음
4. **연산자 오버로딩:**
    - **C 코드:** 연산자 오버로딩이 지원되지 않음
    - **C++ 코드:** 연산자 오버로딩을 사용하여 사용자 정의 데이터 타입에 대한 연산자 동작을 변경할 수 있음.
5. **표준 라이브러리 활용:**
    - **C 코드:** 표준 라이브러리가 상대적으로 제한적
    - **C++ 코드:** 표준 라이브러리의 다양한 기능 및 컨테이너를 활용하여 코드를 효율적으로 작성할 수 있음

```cpp
// advquoted: 따옴표로 둘러싸인 필드를 처리하며, 다음 구분자의 인덱스를 반환합니다.
int Csv::advquoted(const string& s, string& fld, int i) {
    int j;
    fld = "";
    
    for (j = i; j < s.length(); j++) {
        if (s[j] == '"' && s[++j] != '"') {
            int k = s.find_first_of(fieldsep, j);
            
            if (k > s.length()) // no separator found
                k = s.length();
            
            for (k -= j; k-- > 0; )
                fld += s[j++];
            break;
        }
        
        fld += s[j];
    }
    
    return j;
}
```

```cpp
// advplain: 따옴표로 둘러싸이지 않은 필드를 처리하며, 다음 구분자의 인덱스를 반환합니다.
int Csv::advplain(const string& s, string& fld, int i) {
    int j;
    j = s.find_first_of(fieldsep, i); // look for separator

    if (j > s.length()) // none found
        j = s.length();

    fld = string(s, i, j - i);
    return j;
}
```

### 기능 요약

1. **`j = s.find_first_of(fieldsep, i);`**: 현재 위치 **`i`** 이후에서 첫 번째 구분자의 위치를 탐색
2. **`if (j > s.length()) j = s.length();`**: 구분자를 찾지 못한 경우, 문자열 끝으로 설정
3. **`fld = string(s, i, j - i);`**: 구분자 이전까지의 문자를 필드에 할당
4. **`return j;`**: 다음 구분자의 위치를 반환

### C언어와 주요 차이

1. **문자열 다루기:**
    - **C 코드:** C 스타일의 문자열 및 포인터를 사용하여 문자열을 다룸
    - **C++ 코드:** **`std::string`** 클래스를 사용하여 편리하게 문자열을 다룸
2. **스트링 함수 사용:**
    - **C 코드:** **`strcspn`** 함수를 사용하여 문자열 처리
    - **C++ 코드:** **`find_first_of`** 함수를 사용하여 문자열에서 지정된 문자 집합 중 하나가 처음으로 나타나는 위치를 탐색함
3. **코드 구조:**
    - **C 코드:** 주로 배열과 포인터를 사용한 수동적인 문자열 처리가 이루어짐
    - **C++ 코드:** **`std::string`**의 멤버 함수 및 기능을 활용하여 더 간결한 문자열 처리가 이루어짐
    

```c

// C언어
int main(void) {
    int i;
    char *line;

    while ((line = csvgetline(stdin)) != NULL) {
        printf("line = '%s'\n", line);

        for (i = 0; i < csvnfield(); i++)
            printf("field[%d] = '%s'\n", i, csvfield(i));
    }

    return 0;
}

```

```c
// C++
int main(void) {
    string line;
    Csv csv;

    while (csv.getline(line) != 0) {
        count << "line = '" << line << "'\n";

        for (int i = 0; i < csv.getnfield(); i++) {
            count << "field[" << i << "] = '" << csv.getfield(i) << "'\n";
        }
    }

    return 0;
}
```

C++ 버전은 객체지향적인 접근 / C 버전은 절차지향적인 접근

1. **언어 및 라이브러리**: 
    
    C++ **`<iostream>`** 헤더를 이용하여 입출력을 처리함. C 언어 **`<stdio.h>`** 헤더를 이용하여 입출력을 처리.
    
2. **문자열 및 입출력**: 
    
    C++ 버전은 **`string`** 클래스를 사용하여 문자열을 다루고, **`count`**을 이용하여 출력. C 버전은 문자열을 C 스타일 문자열로 다루고, **`printf`** 함수를 이용하여 출력.
    
3. **클래스 사용 여부**: 
    
    C++ 버전은 **`Csv`** 클래스를 사용하여 CSV 데이터를 처리. C 버전은 라이브러리 함수 **`csvgetline`**, **`csvnfield`**, **`csvfield`**를 사용하여 처리.
    
4. **반복문 변수 선언 위치**: 
    
    C++ 버전에서는 반복문 내부에서 **`int i`**를 선언하여 사용합니다. C 버전에서는 반복문 앞에서 **`int i`**를 선언하여 사용합니다.
    

4-7 C++ / C / Java 세 언어로 CSV 라이브러리를 작성하고 라이브러리 각각의 명료함, 튼튼함, 속도를 비교하라

1. **명료 :**
    - **C 언어:** C는 저수준 언어로 포인터 사용이 많고 메모리 관리가 필요합니다. 이로 인해 코드가 더 복잡성은 높아진다
    - **C++:** C++는 C의 확장이지만 객체 지향 프로그래밍의 특성을 갖추고 있어 높은 수준의 추상화를 허용합니다.
        
        따라서 C에 비해 명료성이 높을 수 있습니다.
        
    - **Java:** Java는 높은 수준의 객체 지향 프로그래밍 언어로 간결하고 명료한 코드를 작성하기 적합
    
2. **튼튼함 :**
    - **C 언어:** C는 포인터 조작 등에서 발생할 수 있는 오류에 노출되기 쉬움. 메모리 오류로부터 보호되지 않음.
    - **C++:** C++는 강력한 타입 검사 및 예외 처리와 같은 기능을 제공하여 더 안전한 프로그래밍을 가능.
    - **Java:** Java는 자동 메모리 관리 및 강력한 예외 처리 기능 등으로 더욱 튼튼한 코드를 작성할 수 있게 합니다.
    
3. **속도 :**
    - **C 언어:** C는 하드웨어에 가까운 저수준 언어이므로 일반적으로 빠른 실행.
    - **C++:** C++는 C에 비해 약간의 오버헤드가 있지만, 최적화된 코드를 작성할 수 있어 높은 성능.
    - **Java:** Java는 가상 머신 위에서 동작하며 JIT(Just-In-Time) 컴파일러를 사용하기 때문에 초기 실행 시에는 느릴 수 있지만,
        
        런타임 중에는 최적화되어 높은 성능
        

연습문제 4-5. [] 연산자를 오버로드해 csv[i]와 같은 방식으로 필드에 접근할 수 있게 C++버전을 다듬어 보라

```cpp
class Csv {
public:
    // 기본 매개변수를 가진 생성자
    Csv(istream& fin = cin, string sep = ",");

    // 멤버 함수들
    int getline(string&);
    string getfield(int n);
    int getnfield() const { return nfield; }

private:
    // 멤버 변수들
    istream& fin;          // 입력 파일 포인터
    string line;           // 입력 라인
    vector<string> field;  // 필드 문자열들
    int nfield;            // 필드의 수
    string fieldsep;       // 구분 문자

    // 비공개 멤버 함수들
    int split();           // 입력 라인을 필드로 분리
    int endofline(char);    // 문자가 라인의 끝을 나타내는지 확인
    int advplain(const string & line, string & fld, int); 
    int advquoted(const string & line, string & fld, int);
};
```

```cpp
// std::string operator[](int n)는 C++ 클래스 Csv에서 배열 subscript 
// ([]) 연산자를 오버로드한 함수입니다. 이 연산자를 통해 Csv 클래스의 객체를 배열처럼 다룰 수 있게 됩니다.

class Csv {
public:
    Csv(std::istream& fin = std::cin, const std::string& sep = ",") 
		: fin(fin), fieldsep(sep) {}
    int getline(std::string&);

    // []연산자를 오버로드하여 인덱스에 접근하기.
    std::string operator[](int n) const {
        if (n < 0 || n >= nfield)
            return "";
        else
            return field[n];
    }

    int getnfield() const { return nfield; }

private:
    std::istream& fin;
    std::string line;
    std::vector<std::string> field;
    int nfield;
    std::string fieldsep;

    int split();
    int endofline(char);
    int advplain(const std::string& line, std::string& fld, int);
    int advquoted(const std::string& line, std::string& fld, int);
};
```

이 코드에서 **`Csv`** 클래스에 **`operator[]`**를 추가하여 필드를 인덱스로 직접 접근할 수 있다.

ex) **`csv[0]`**은 첫 번째 필드를 반환합니다.

```cpp
// 연습문제 4-8 
// C++ 버전을 쓰면 독립적인 CSV 인스턴스를 여러개 생성해서 서로 간섭하지 않고 동시에 돌아가게 할 수 있다
// 그리고 모든 상태를 하나의 객체 안에 간섭하지 않고 동시에 돌아가게 할 수 있다. 그리고 모든 상태를 하나의 객체 안에 캡슐화 한 뒤 독립적인 여러 인스턴스를 만들 수 있다는 장덤도 있다
// C버전을 고쳐서 동일 한 효과를 얻을 수 있도록 해보라. csvnew 함수를 통해 따로 데이터 구조에 공간을 할당하고 초기화하여 전역 데이터 구조를 대신하게 하라

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

struct Csv {
    FILE* fin;
    char* line;
    char** field;
    int nfield;
    const char* fieldsep;
};

struct Csv* csvnew(FILE* fin, const char* sep) {
    struct Csv* csv = (struct Csv*)malloc(sizeof(struct Csv));
    if (!csv) {
        perror("Memory allocation failed");
        exit(EXIT_FAILURE);
    }

    csv->fin = fin;
    csv->line = NULL;
    csv->field = NULL;
    csv->nfield = 0;
    csv->fieldsep = sep;

    return csv;
}

int csvgetline(struct Csv* csv) {
    char c;
    int i;
    for (i = 0; (c = getc(csv->fin)) != EOF && c != '\n'; ++i) {
        if (i == 0) {
            csv->line = (char*)malloc(sizeof(char));
        } else {
            csv->line = (char*)realloc(csv->line, (i + 1) * sizeof(char));
        }

        if (!csv->line) {
            perror("Memory allocation failed");
            exit(EXIT_FAILURE);
        }

        csv->line[i] = c;
    }

    if (c == EOF && i == 0) {
        return 0;  // No more lines
    }

    csv->line = (char*)realloc(csv->line, (i + 1) * sizeof(char));
    if (!csv->line) {
        perror("Memory allocation failed");
        exit(EXIT_FAILURE);
    }

    csv->line[i] = '\0';
    csv->nfield = 0;
    return 1;  // Line read successfully
}

int csvsplit(struct Csv* csv) {
    char* tok;
    int i = 0;
    tok = strtok(csv->line, csv->fieldsep);
    while (tok != NULL) {
        ++csv->nfield;
        if (i == 0) {
            csv->field = (char**)malloc(sizeof(char*));
        } else {
            csv->field = (char**)realloc(csv->field, i * sizeof(char*));
        }

        if (!csv->field) {
            perror("Memory allocation failed");
            exit(EXIT_FAILURE);
        }

        csv->field[i] = strdup(tok);
        if (!csv->field[i]) {
            perror("Memory allocation failed");
            exit(EXIT_FAILURE);
        }

        ++i;
        tok = strtok(NULL, csv->fieldsep);
    }

    return csv->nfield;
}

void csvprint(struct Csv* csv) {
    int i;
    printf("line = '%s'\n", csv->line);
    for (i = 0; i < csv->nfield; ++i) {
        printf("field[%d] = '%s'\n", i, csv->field[i]);
    }
}

void csvfree(struct Csv* csv) {
    int i;
    free(csv->line);
    for (i = 0; i < csv->nfield; ++i) {
        free(csv->field[i]);
    }
    free(csv->field);
    free(csv);
}

int main(void) {
    struct Csv* csv = csvnew(stdin, ",");
    while (csvgetline(csv)) {
        csvsplit(csv);
        csvprint(csv);
    }
    csvfree(csv);
    return 0;
}
```