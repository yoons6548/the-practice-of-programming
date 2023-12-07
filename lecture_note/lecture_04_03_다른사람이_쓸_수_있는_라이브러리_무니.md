## 4.3 다른 사람이 쓸 수 있는 라이브러리

범용으로 쓸 만한 라이브러리를 구축해보자

가장 중요한 것: csvgetline 함수를 더 튼튼하게 만들자. 긴 입력이나 필드가 많은 경우도 처리할 수 있게!
다른 사람이 쓸 수 있는 인터페이스를 만들려면 고려해야 할 것들:

- 인터페이스
- 정보 은닉
- 자원 관리
- 에러 처리 

이러한 항목들은 서로 상호작용하고 설계에 영향을 미친다.

## 인터페이스
*어떤 서비스와 접근 권한을 제공할 것인가?*

기본 기능 세 가지 정의:
```C
char *csvgetline(FILE*) : CSV 형식 한 줄을 읽는다.
char *csvfield(int n): 현재 줄의 n번째 필드를 리턴한다.
int csvnfield(void): 현재 줄의 필드 개수를 리턴한다. 
````

**char \*csvgetline(FILE\*)**
입력: 파일
출력(리턴): 원 입력줄을 가리키는 포인터를 리턴, 파일 끝에 이르렀을 땐 NULL을 리턴
리턴한 입력줄의 끝에 있는 개행 문자는 제거
-> 필드 개수를 리턴할 수도 있었고, 그렇다면 개행문자를 포함하는지 안하는지도 정해야 했다.

**char \*csvfield(int n)**
**int csvnfield(void)**
필드를 어떻게 정의해야 할까?  
구분값은 , |, 따옴표 등 다양하다.  
필드의 인덱스는 0부터 시작한다.  
만약 csvfield(-1), csvfield(1000000)으로 존재하지 않는 필드를 호출한다면?   
-> 이런 경우는 ""(빈 문자열)을 리턴 혹은 NULL을 리턴  


### 정보 은닉
*어떤 정보를 드러내고, 어떤 정보를 숨길 것인가?*
오직 csvgetline만이 메모리 관리에 대해 안다. 따라서 csvgetline에 메모리 관리를 숨긴다.  
더 긴 줄이나 더 많은 필드가 들어오면 메모리를 더 할당해야 한다.   
이런 작업이 이루어지는 세부적인 부분은 csv함수에 숨겨져있다.  
세부적인 구현 사항은 사용자에게는 숨긴다. 


### 자원 관리
*메모리나 기타 한정된 자원들을 누가 관리할 것인가?*
누가 공유 정보를 책임질지 결정해야 한다.  
csvgetline은 원본 데이터를 리턴할까, 복사본을 만들까?  
앞에서 csvline의 리터값을 포인터로 결정했었고 복사본 안의 필드에 대한 포인터를 리턴한다.  
-> 사용자가 특정 줄이나 필드를 저장하려면 또다른 복사본을 만들어야 한다.  
더이상 필요하지 않게 되면 메모리 해제도 사용자의 책임이다.  

공유 자원이나 라이브러리와 호출자 사이를 넘나드는 자원을 관리하는 것은 어렵지만 중요하다.  
공유 책임에 대한 오해와 실수는 버그를 만들기 쉽다.  

### 에러 처리
*누가 에러를 감지하고, 누가 이 에러를 보고할 것인가?*
csvgetline은 파일 끝에 도달했을 때 NULL을 리턴하기 때문에 파일 끝에 이른 경우와 메모리 고갈과 같은 에러가 발생했을 경우를 구분할 수 없다.   
존재하지 않는 필드에 대한 접근은 에러를 발생시키지도 않음.  
-> 최근의 에러를 보고하는 csvgeterror를 인터페이스에 추가할 수도 있겠지만 향후 과제로 남김  

에러가 발생했을 때:  
1) 그냥 죽어버리면 안된다.
2) 호출자가 적절한 조치를 취할 수 있게 에러 상태를 리턴해야 한다.
3) 메시지를 출력하거나 팝업 대화상자를 띄워서도 안 된다. -> 다른 것들을 방해함  

에러 처리는 따로 논해야 할 만큼 중요. 4장 후반에서 따로 다룸

### 명세서
위에서 내린 선택을 하나로 모아서 csvgetline이 제공할 수 있는 서비스의 목록, 사용법을 명세서로 만들어야 한다.  

가장 좋은 접근:  
명세서를 일찍 작성한 다음,  
구현하면서 깨달은 것들을 반영해서 계속 갱신한다.

명세서는 다음과 같이 함수 원형과 동작방식, 책임, 가정한 사항들의 상세한 설명을 포함한다.  
(책 129p - 130p)

몇 가지 문제들
- csvgetline이 EOF에 이른 다음에 csvfield와 csvnfield가 호출되면 어떤 값을 리턴해야 할까?
- 잘못된 형식으로 된 필드는 어떻게 처리해야 할까?

이런 복잡한 문제들을 다 파고 들어가는 것은 작은 시스템, 큰 시스템 상관없이 어렵다.  
그러나 중요한 일이므로 시도해볼 가치가 있다.  

<명세서에 맞는 새로운 csvgetline>  
csv.h: 인터페이스의 공용 부분을 나타내는 함수 선언을 담은 헤더 파일  
csv.c: 코드를 담은 구현 파일  
사용자는 자신의 소스코드에 csv.h를 인클루드 해서, csv.c를 컴파일한 바이너리 코드와 자신의 컴파일된 코드를 링크한다.   

csv.c
```C
extern char *csvgetline(FILE*);
extern char *csvfield(int n);
extern int csvnfield(void);

enum C NOMEM = -2 3; /n out of memory signal a/
static char *line = NULL; /n input chars n/
static char asline = NULL; /a line copy used by split a/
static int maxline = 0; /* size of line[] and sline[] a/
static char *afield = NULL; /a field pointers */
static int maxfield = 0; /a size of field[] a/
static int nfield = 0; /a number of fields in field[] a/
static char fieldsep[] = " ,"; /a field separator chars */ 

```
split 과 같은 내부 함수나 내부 변수는 static으로 선언한다   
-> C에서 정보를 숨기는 가장 간단한 방법이다.  

csvgetline  
```C
/* csvgetl i ne: get one line, grow as needed */
/* sample input: "LU".86.25,"11/4/1998","2:19PM",+4.0625 */
char acsvgetl i ne(F1LE afi n)
{
    int i, c;
    char anew1 , anews;
    if (line == NULL) { /a allocate on first call a/
        maxline = maxfield = 1;
        line = (char a) malloc(max1ine);
        sl ine = (char a) malloc(max1ine) ;
        field = (char a*) ma1 loc(maxfieldnsizeof(field[0]));
        if (line == NULL I I sl ine == NULL I I field == NULL) {
            reset 0 ;
            return NULL; /* out of memory */
        }
    }
    for (i=O; (c=getc (f i n)) ! =EOF && ! endofl i ne(fi n , c) ; i++) {
        if (i >= maxline-1) { /n grow line */
            maxline a= 2; /a double current size */
            newl = (char a) real loc(line, maxli ne) ;
            news = (char a) realloc(s1 i ne, maxl ine) ;
            if (newl == NULL ( I news == NULL) {
                reset() ;
                return NULL; /* out of memory */
            }
        }
        line[i] = c;
    }
    line[i] = '\O';
    if (split() == NOMEM) {
        reset (1 ;
        return NULL; /a out of memory */
    }
    return (c == EOF && i == 0) ? NULL : line;
}
```

```C
/* reset: set variables back to starting values */
static void reset(void)
{
    free(1ine) ; /* free(NULL1 permitted by ANSI C */
    free(s1ine) ;
    free (field) ;
    line = NULL;
    sline = NULL;
    field = NULL;
    maxline = maxfield = nfield = 0;
}
```

```C
/* endofline: check for and consume \r, \n, \r\n, or EOF */
static int endofline(FILE *fin, int c)
{
    int eol;
    eol = (c=='\r' || c=='\n');
    if (c == '\r'){
        c = getc(fin);
        if (c !='n' && c != EOF){
            ungetc(c, fin) ; /* read too far; put c back */
        }
    }
return eol;
}
```
표준 입력 함수가 실제 입력값에서 마주칠 여러 잘못된 형태를 처리할 수 없기 때문.

...

```C
/* csvtest main: test CSV library */
int main (voi d)
{
    int i;
    char *line;

    while ((line = csvgetline(stdin)) != NULL) {
        printf ("line = '%sl\n", line) ;
        for (i = 0; i c csvnfieldo; i++)
            printf("field[%d] = '%s'\n", i, csvfield(i));
    }
    return 0;
} 
```

C 언어용 라이브러리 완성!
임의의 큰 입력을 처리할 수 있고, 이상 데이터에도 적절히 반응한다.   
대신 처음 코드보다 네 배나 길어지고 복잡해짐  
그런데 이런 복잡성의 증가는 프로토타입에서 제품 단계로 넘어갈 때 일어나는 전형적인 현상임!
