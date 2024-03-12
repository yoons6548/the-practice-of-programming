## 9.5 프로그램을 작성하는 프로그램

generate 함수
- 프로그램을 작성하는 프로그램
- 함수의 출력은 다른 (가상)시스템을 위한 실행 가능한 명령어 스트림

프로그램을 작성하는 프로그램 예시
- HTML 동적 생성
- 고수준 언어를 기계어로 번역하는 컴파일러
- 비주얼 베이직 그래프 인터페이스(마우스 올리면 비주얼베이직 할당문 생성)
- '시각적인' 개발 시스템
- 마우스 클릭으로 사용자 인터페이스 코드를 만들어내는 '마법사' 기능

### Plan 9 운영 체제에서 오류 메시지를 생성
- 프로그램 생성기의 강력함에도 불구하고, 개별 프로그래머가 직접 C나 C++ 코드를 작성하는 것이 때로는 더 자주 사용된다.  
- Plan 9 운영 체제는 이름과 주석이 포함된 헤더 파일에서 오류 메시지를 생성하며, 주석은 기계적으로 열거된 값으로 인덱싱할 수 있는 배열의 따옴표 붙은 문자열로 변환된다.  
- 간단한 프로그램으로 오류 메시지에 대한 선언을 생성할 수 있다.  
- 이 접근 방식의 장점은 enum 값과 문자열 간의 관계가 문서화되고 자연 언어에 독립적이며, 정보가 한 곳에만 나타나기 때문에 최신 상태로 유지하기 쉽다는 것이다다.  
- 헤더 파일이 변경될 때마다 오류 메시지를 자동으로 업데이트하기 위해 .c 파일을 다시 생성하고 컴파일하기 쉽다.  
- 생성기 프로그램은 Perl과 같은 문자열 처리 언어로 쉽게 작성할 수 있다.  

```
enum ErrorMessages {
    EPERM,      /* Permission denied */
    EIO,        /* I/O error */
    EFILE,      /* File does not exist */
    EMEM,       /* Memory limit reached */
    ESPACE,     /* Out of file space */
    EGREG       /* It's all Greg's fault */
};

char *errs[] = {
    "Permission denied", /* Eperm */
    "I/O error", /* Eio */
    "File does not exist", /* Efile */
    "Memory limit reached", /* Emem */
    "Out of file space", /* Espace */
    "It's all Greg's fault", /* Egreg */
};
```

```
# enum.pl: generate error strings from enum+comments

print "/* machine-generated; do not edit. */\n\n";
print "char *errs[] = {\n";

while (<>) {
    chop;                              # remove newline
    if (/^\s*([a-z0-9]+),?/) {         # first word is E...
        $name = $1;                    # save name
        s/.*\/\*.*//;                  # remove up to /*
        s/ *\*\///;                    # remove */
        print "\t\"$_\", /* $name */\n";
    }
}

print "};\n";
```
1. 컴파일러가 프로그램 오류를 잡아내도록 코드 조각에 매직 주석을 사용한다. 각 줄에는 /// (일반 주석과 구별하기 위함)로 시작하고 해당 줄의 진단 메시지와 일치하는 정규식을 입력

2. 두 번째 테스트를 통과하면 예상 메시지가 출력되며, 정규식과 일치

3. 각 코드 조각은 컴파일러에 제공되며, 출력은 예상 진단 메시지와 비교됨. 이 과정은 shell 및 Awk 프로그램의 조합으로 관리됨

4. 컴파일러 출력이 예상과 다른 경우 실패를 나타냅니다. 주석은 정규식이므로 출력을 더 많이 또는 덜 잊어버릴 수 있다.

5. 주석의 아이디어는 새로운 것이 아니다. 그들은 Postscript에 나타나며, 정규 주석은 %로 시작하고 페이지 번호, 경계 상자, 글꼴 이름 등에 대한 추가 정보를 전달할 수 있다.

### Andy Koenig가 개발한 C++ 코드를 테스트하는 방법

이 글은 C++ 컴파일러를 테스트하는 방법에 대해 설명하고 있습다. 컴파일러가 프로그램 오류를 진단하도록 하기 위해 특별한 주석을 사용

주석에는 /// 기호로 시작하는 정규 표현식이 포함되어 있다. 이 정규 표현식은 해당 코드 조각에 대해 컴파일러가 생성해야 하는 진단 메시지와 일치해야 한다.
예를 들어, 다음 두 개의 코드 조각은 각각 다른 진단 메시지를 생성해야 한다:
```
int f() {}
/// warning.* non-void function .* should return a value

void g() {return 1;}
/// error.* void function may not return a value
```

- 첫 번째 코드 조각 int f() {}은 반환 값이 없는 함수가 값을 반환해야 한다는 경고 메시지를 생성해야 한다
- /// 뒤에 있는 warning.* non-void function .* should return a value는 컴파일러가 생성해야 하는 경고 메시지의 패턴을 나타낸다.
- 두 번째 코드 조각 void g() {return 1;}은 void 함수가 값을 반환할 수 없다는 오류 메시지를 생성해야 한다.
- /// 뒤에 있는 error.* void function may not return a value는 컴파일러가 생성해야 하는 오류 메시지의 패턴을 나타낸다.
- 이러한 각 코드 조각은 컴파일러에 제공되며, 컴파일러의 출력은 예상되는 진단 메시지와 비교된다. 이 과정은 shell과 Awk 프로그램의 조합으로 관리된다.
- 컴파일러의 출력이 예상과 다를 경우 실패로 간주된다. 그러나 주석에 사용된 정규 표현식 때문에 출력 결과는 필요에 따라 더 많거나 적게 나타날 수 있다.
- 주석에 의미를 부여하는 아이디어는 새로운 것이 아니다. 이는 Postscript에서도 사용되며, 일반적인 주석은 % 기호로 시작하고 페이지 번호, 경계 상자, 글꼴 이름 등의 추가 정보를 전달할 수 있다.
