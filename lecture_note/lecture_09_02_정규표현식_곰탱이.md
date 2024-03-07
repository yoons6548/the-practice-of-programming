# 9.2 정규 표현식

- 정규 표현식은 텍스트의 패턴을 지정하는 방법.
- 정규 표현식에는 여러 종류가 있지만 본질적으로는 모두 동일한 문자, 상수, 숫자 등 같은 문자 계열을 나타내는 약어, 대체기호 반복적 표현들의 패턴을 기술하는 방법.
- 익숙한 방법중 하나로 파일 이름의 패턴을 맞추기 위해 명령줄 프로세서나 셸에서 사용하는 와일드카드 라는 기능이 있다.

- 일반적으로 *(아스테리스크)는 문자로 구성된 모든 문자열의 의미로 사용.
- 정규 표현식은 표현 방식마다 명확한 의미와 공식 문법이 정해져 있는 언어의 일종이다.
- 정규 표현식은 일치하는 문자열 집합을 정의하는 문자의 나열 형태이다.
- 정규 표현식 abc는 입력된 문자열에서 abc가 있는지 체크한다.
- 그리고 메타문자 몇 개로 반복 묶음 위치를 표시한다.

    - ^(caret, circumflex)  : 문자열의 시작
    - $(dollar sign)        : 문자열의 끝

    - . (dot)               : 문자 하나와 일치
    - []                    : 대괄호 안의 문자중 하나와 일치
    - [123456789] -> [1-9]
- 이런 요소들과 결합할 수 있는 메타 문자로 묶음에 괄호, 선택에 |(vertical var), 0번 이상 등장에 *, 0번이나 1번 등장에 ?를 쓸수 있고 \를 붙혀서 특수 의미를 비활성 할수 있다.

- 가장 잘 알려진 정규 표현식 툴은 grep 이다.

    어떤 소스 파일에서 Regexp 클래스를 사용하는가

    % grep Regexp *.java

    어떤 파일이 이것을 구현하는가.

    % grep 'class.*Regexp' *.java

    bob이 보낸 메일을 어디에 저장했더라?

    % grep '^From:.* bob@' mail/*

    이 프로그램 소스 중 공백이 아닌 줄은 몇 줄이나 될까?
    
    % grep '.' *.c++ | wc

- 옵션을 주면 일치하는 줄의 줄 번호, 일치 횟수를 출력하거나 대소문자에 상관없이 찾고 의미를 반전시키는 등 다양한 기본적인 동작을 수행.

- 모든 시스템에는 grep 이나 비슷한게 있는게 아니므로 regex 또는 regexp 라고 부르는 정규 표현식 라이브러리로 직접 grep을 만들수 있다.

- match 함수는 텍스트 문자열이 정규 표현식과 일치하는지 판단하는 것이다.

``` c++
    

    int match(char *regexp, char *text){
        if(regexp[0] == '^')
        return matchhere(regexp+1, text);
        do{
            if(matchhere(regexp,texdt))
            return 1;
        }while(*text++ != '\0');
        return 0;
    }
```

- 만약 정규 표현식이 ^로 시작하면 텍스트는 시작할때 그 표현식의 나머지 부분과 일치해야한다.
- 그렇지 않다면 텍스트를 따라 가면서 matchhere 함수를 호출해 그 텍스트 어느 부분에서인가 일치가 일어나는지 확인한다.

``` c++
// matchhere 텍스트 시작 부분에서 regexp를 찾는다.
int matchhere ( char* regexp, char *text){
    if (regexp[0]=='\0')
    return 1;
    if(regexp[1] == '*')
    return matchstar(regexp[0],regexp+2, text);
    if(regexp[0] == '$' && regexp[1] == '\0')
    return *text == '\0';
    if(*text!='\0' && (regexp[0]=='.' || regexp[0]==*text))
    return matchhere(regexp+1, text+1);
    return 0;
}
```

- 정규 표현식 중간에 나오는 ^나 $는 메타 문자가 아니라 그냥 리터럴 문자로 취급한다.

- 까다로운 경우가 하나 있는 데 그중 하나가 x* 이다.

- 이런 경우에는 matchstar 함수에 별 문자의 피연산자인 x를 첫 인자로 넣고 별문자 뒤의 표현식 패턴과 텍스트를 후속 인자들로 넣어 호출한다.

```c++
// matchstar 텍스트 시작 부분에서 C*regexp를 찾는다.

int matchstar(int c, char *regexp, char *text){
    do{
        if(matchhere(regexp, text))
        return 1;
        
    }while(*text != '\0' && (*text++ == c || c == '.'));
    return 0;
}
```
- 이 루프는 텍스트가 나머지 정규 표현식과 일치하는지 확인하며 첫 문자가 별 문자의 피연산자와 일치하는 한 텍스트의 각 위치에서 계속 시도한다.


- main문은 다음과 같다.

```c++
// grep main : 파일에서 regexp를 찾는다
int main(int argc, char *argv[]){
    int i,nmatch;
    FILE *f;

    setprogname("grep");

    if(argc<2) eprintf("usage : grep regexp [file ...]");
    nmatch = 0;
    if(argc == 2){
        if(grep(argv[1], stdin, NULL))
        nmatch++;

    }else{
        for(i = 2;i <argc; i++){
            f = fopen(argv[i],"r");
            if(f == NULL){
                weprintf("cant open %s : ",argv[i]);
                continue;
            }
            if(grep(argv[1], f, argc>3 ? argv[i]: NULL)>0)
                nmatch++;
            fclose(f);
        }
    }
    return nmatch == 0;
}




```

- 이 프로그램은 한번이라도 일치하면 0을 일치하지 않으면 1을 에러가 발생하는 2를 리턴한다.

- grep 함수는 다음과 같다

```c++
// grep 파일에서 regexp를 검색한다.

int grep ( char *regexp, FILE *f, char *name){
    int n, nmatch;
    char buf[BUFSIZE];

    nmatch = 0;
    while(fgets(buf,sizeof buf, f) != NULL){
        n= strlen(buf);
        if(n>0 && buf[n-1] == '\n')
            buf[n-1] = '\0';
        if(match(regexp,buf)){
            nmatch++;
            if(name != NULL)
                printf("%s:",name);
            printf("%s\n", buf);
        }
    }
    return nmatch;
}
```

- main의 함수에서는 파일을 여는데 실패해도 종료하지 않고 문제를 보고하고 계속 진행한다.

- grep 에서 파일을 하나만 선택하면 파일 이름이 출력이 되지 않지만 찾는 것이 파일 여러개면 파일 명도 같이 출력된다.

- 구현한 match 함수는 일치하는 부분을 찾자마자 리턴하지만 텍스트 에디터의 바꾸기 기능을 구현할 떄는 왼쪽으로 가장긴 것이 일치하는게 더 적합하다.
- 따라서 matchstar를 그리디 하게 만든다.

``` c++
// matchstar : c*regexp와 왼쪽으로 가장 긴 일치 부분을 찾는다

int matchstar (int c, char *regexp, char *text)
{
    char *t;

    for(t = text; *t!='\0' && (*t == c || c == '.'); t++);
    do{
        if(matchhere(regexp,t))
        return 1;
    }while(t-->text);
    return 0;
}
```

- grep이 어떤 식의 일치 판단 방법을 쓰느냐는 중요하지 않다. 그냥 일치하는 부분이 있는지 검사하고 전체 줄을 출력할 뿐이다.