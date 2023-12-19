
## 5.6 디버깅 툴

디버거 외에도 버그를 찾는 데 도움이 되는 다양한 프로그램들이 있다.  
이번 절에서는 'strings'라는 프로그램에 대해서 언급되어 있는데, 주로 출력되지 않는 문자들로 이루어진 파일(실행 파일, 이진 포맷 파일 등)을 볼 때 유용하다.  
이러한 파일들 속에는 종종 가치 있는 정보가 숨어있는데, 문서의 텍스트나 오류 메시지, 압축되지 않은 코드, 파일이나 디렉터리의 이름, 함수 호출 등이 숨어 있을 수 있다.  

ex)   
예를 들어 압축 파일 안에는 파일 이름이 포함되어 있을 수 있고, 'strings'는 그런 이름들을 찾을 수 있다. 

### 유닉스 기본 툴킷의 strings
유닉스는 기본 툴킷으로 'strings'를 제공하는데, 여기서 소개하는 strings는 약간 다르다.  
유닉스 기본 strings에서는 주로 프로그램의 텍스트와 데이터 세그먼트(변수에 할당된 실제 값을 저장하는 곳)를 검사하고, 심볼 테이블(이름과 주소를 짝짓는 정보를 담은 테이블)은 무시한다.  
'-a' 옵션을 사용하면 심볼 테이블을 무시하지 않고 전체 파일을 읽을 수 있다고 한다. 

### 요약
**'strings' 프로그램**: 이진 파일로부터 ASCII 텍스트를 추출해서 다른 프로그램이 처리한 텍스트를 읽을 수 있게 해주는 도구.   
오류 메시지가 어떤 프로그램에서 왔는지 알 수 없을 때 이 프로그램을 사용하여 가능한 원인을 찾아볼 수 있다.  
```
% strings *.exe *.dll | grep 'mystery message'
```

### strings 함수
```c
/* strings: extract printable strings from stream */
void strings(char *name, FILE *fin)
{
    int c, i;
    char buf[BUFSIZ];

    do { /* once for each string */
        for (i = 0; (c = getc(fin)) != EOF; ) {
            if (!isprint(c))
                break;
            buf[i++] = c;
            if (i >= BUFSIZ)
                break;
        }
        if (i >= MINLEN) /* print if long enough */
            printf("%s:%.*s\n", name, i, buf);
    } while (c != EOF);
}

```
**'strings'함수**: 파일에서 프린트 가능한 문자열을 추출하는 도구.  
이 예제 함수에서는, 파일에서 읽은 문자가 프린트 가능한지를 확인하고, 최소 길이(여기서는 6글자 이상)를 가진 문자열만을 출력한다.  

**getc함수**: 주어진 파일에서 문자를 하나씩 읽어들이는 함수.   
파일의 끝(EOF)에 도달할 때까지 계속 읽는다.   
문자가 프린트 가능한지 확인한 후(isprint 함수 사용), 프린트 가능한 문자들을 버퍼에 저장한다.  
버퍼가 특정 크기(BUFSIZ)를 넘거나 문자열의 길이가 최소 길이(MINLEN) 이상일 경우, 해당 문자열을 출력한다.  

**printf 함수**: 문자열을 출력할 때 사용되며, %.*s 형식 지정자를 사용하여, 출력할 문자열의 길이를 변수 i에서 받아와서, 버퍼에 저장된 문자열 buf의 알맞은 길이만큼만 출력한다.

(책에서는 코드를 처음 작성할 때 printf 문에 버그가 있었고, 이를 한 곳에서 수정했지만 다른 곳에서 같은 실수를 했는지 확인해야 했다는 이야기와, 코드 중복을 줄이기 위해 전체 구조를 do-while 루프를 사용하는 형태로 다시 작성했다는 경험담 얘기도 한다)

### main 함수

```C
/* strings main: find printable strings in files */
int main(int argc, char *argv[])
{
    int i;
    FILE *fin;
    
    setprogname("strings");
    if (argc == 1)
        eprintf("usage: strings filenames");
    else {
        for (i = 1; i < argc; i++) {
            if ((fin = fopen(argv[i], "rb")) == NULL)
                weprintf("can’t open %s:", argv[i]);
            else {
                strings(argv[i], fin);
                fclose(fin);
            }
        }
    }
    return 0;
}

```
**main함수**: 커맨드 라인에서 입력받은 여러 파일들에서 프린트 가능한 문자열을 찾아내는 목적.

먼저 입력된 파일 이름들을 확인한다.  
파일 이름이 하나도 주어지지 않으면 사용자에게 어떻게 프로그램을 사용해야 하는지 안내 메시지를 출력한다.  
파일 이름이 주어졌다면, 각 파일을 열고 그 안에 있는 프린트 가능한 문자열을 찾아서 출력한 뒤 파일을 닫는다.  

참고)   
argc는 C 프로그램에 전달된 인자의 수를 나타낸다.   
프로그램을 실행할 때, argc 값은 기본적으로 1로 시작한다.  
이는 프로그램 자체의 이름이 첫 번째 인자(argv[0])로 전달되기 때문이다.  
따라서, argc == 1이라는 것은 사용자가 프로그램 이름 외에 추가적인 인자(여기서는 파일 이름)를 전혀 제공하지 않았다는 것을 의미한다.  

파일 이름이 하나도 주어지지 않는다면 표준 입력을 받아들이면 될텐데, 왜 안그랬을까?  
결론은 윈도우와 유닉스 시스템 간의 호환성 문제 때문이다.  
살펴보자~!  

'strings' 프로그램의 일반적인 테스트 케이스는 프로그램 자체를 자기 자신에게 입력으로 넣어 실행하는 것이다.  
이렇게 테스트 했을 떄, 유닉스에서는 문제 없이 잘 동작했지만, 윈도우 95에서는 예상치 못한 결과가 나왔다!  

```
C:\> strings <strings.exe 
```
```
!This program cannot be run in DOS mode
' . rdata
@.data
. i data
. reloc 
```
윈도우에서 실행했을 때는 예상했던 것보다 적은 문자열이 출력되었는데,  
이는 윈도우 시스템에서는 getchar 함수가 Ctrl-Z(0x1A)를 만났을 때 EOF를 리턴하기 때문이다.  
유닉스에서는 이런 일이 없기 때문에, 이 차이점으로 인해 혼란이 생겼다.  

문제를 해결하기 위한 두 가지 방법이 있었다:   
1) 사용자가 파일 이름을 명시적으로 제공하도록 강제하거나, 
2) 조건부 컴파일을 사용하여 시스템에 따라 동작이 달라지게 만드는 것.  
첫 번째 방법은 사용자가 유닉스와 윈도우 모두에서 파일 이름을 제공해야 프로그램이 동작하게 만들며, 이는 동일한 방식으로 일관되게 작동하는 솔루션이지만 사용자에게 추가적인 단계를 요구한다.  
두 번째 방법은 시스템에 따라 프로그램이 다르게 동작하도록 만들지만, 이는 코드의 복잡성을 증가시키고 호환성을 감소시키는 결과를 가져올 수 있다.  

결국, 프로그램이 어디서 실행되든지 같은 방식으로 동작하도록 첫 번째 해결 방법을 선택했다.  
이는 코드의 복잡성과 유지 관리를 줄이고, 사용자가 유닉스와 윈도우 어느 한쪽에서 프로그램을 사용할 때 혼란을 줄이는 데 도움이 되었다.

### 연습문제
연습문제 5-2)  
 'strings' 프로그램에 최소 문자열 길이를 인자로 추가
```C
void strings(char *name, FILE *fin, int minlen) {
    int c, i;
    char buf[BUFSIZ];

    do { /* once for each string */
        for (i = 0; (c = getc(fin)) != EOF; ) {
            if (!isprint(c))
                break;
            buf[i++] = c;
            if (i >= BUFSIZ)
                break;
        }
        if (i >= minlen) /* print if long enough */
            printf("%s:%.*s\n", name, i, buf);
    } while (c != EOF);
}
```

연습문제 5-3)  
입력에서 비출력용 문자들을 특정 형식('\xhh' 형태로)으로 출력하는 기능 (vis)
```C
int main() {
    int c;

    while ((c = getchar()) != EOF) {
        if (isprint(c) || c == '\n') {
            putchar(c);  // 프린트 가능한 문자 또는 개행 문자는 그대로 출력합니다.
        } else {
            printNonPrintable(c);  // 비출력용 문자는 특정 형식으로 출력합니다.
        }
    }

    return 0;
}
```

연습문제 5-4)  
'\x0A'는 16진수로 10을 의미하며, 이는 개행 문자(newline)에 해당한다.   
출력을 명확하게 하기 위해 입력이 \x0A일 때, 프로그램이 \x0A를 출력하도록 한다. 아래 함수 추가.  
```C
void printNonPrintable(int c) {
    if (c == '\n') {
        printf("\\n");  // 개행 문자는 \n으로 출력합니다.
    } else {
        printf("\\x%02X", c);  // 그 외의 비인쇄 가능한 문자는 \xhh 형식으로 출력합니다.
    }
}
```
연습문제 5-5)  
'vis' 프로그램을 확장하여 여러 파일을 처리하고, 긴 줄은 줄바꿈하고, 비출력용 문자를 제거하는 기능을 추가
```C
void printNonPrintable(int c) {
    // 개행 문자를 \n으로 명확하게 표시합니다.
    if (c == '\n') {
        printf("\\n");
    } else {
        printf("\\x%02X", c);
    }
}

void vis(FILE *fin) {
    int c;
    int column = 0;  // 현재 컬럼 위치를 추적합니다.

    while ((c = fgetc(fin)) != EOF) {
        if (isprint(c)) {
            putchar(c);
            column++;
        } else if (c == '\n') {
            printNonPrintable(c);
            column = 0;  // 줄바꿈을 만났으므로 컬럼 위치를 리셋합니다.
        } else {
            printNonPrintable(c);
        }

        // 지정된 최대 컬럼 길이를 넘으면 줄을 접습니다.
        if (column >= MAX_LINE_LENGTH) {
            putchar('\n');
            column = 0;
        }
    }
}

int main(int argc, char *argv[]) {
    if (argc == 1) {
        vis(stdin);  // 파일명이 제공되지 않으면 표준 입력을 사용합니다.
    } else {
        for (int i = 1; i < argc; i++) {
            FILE *fin = fopen(argv[i], "rb");
            if (fin == NULL) {
                fprintf(stderr, "Can't open %s\n", argv[i]);
                continue;
            }

            vis(fin);
            fclose(fin);
        }
    }
    return 0;
}
```
