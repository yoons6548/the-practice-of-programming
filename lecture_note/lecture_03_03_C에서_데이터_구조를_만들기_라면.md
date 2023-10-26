### 3. C에서 데이터 구조를 만들기

1. 앞으로 쓸 상수들을 정의

```c
enum {
    NPREF = 2, /* 접두사의 단어 수 */
    NHASH = 4093, /* 상태를 저장할 해시 테이블의 배열 크기 */
    MAXGEN = 10000 /* 생성할 수 있는 최대 단어 수 */
};
```

NPREF를 실행시간에 정해지는 변수가 아니라 컴파일 시점에 결정되는 상수로 만들면, 저장 공간 관리가 더 쉬워진다.

해시 배열의 크기를 크게 잡은 이유는 많은 분량의 입력 데이터를 처리하기 위해서다.

배열 크기 증가 → 체인 길이 감소 → 검색 시간 단축

배열 크기 감소 → 체인 길이 증가 → 검색 시간 증가 (이 경우 처리할 입력 양을 적절한 시간에 처리하지 못함)

접두사 = 단어의 배열로 저장

해시 테이블의 항복 = state 데이터 타입으로 저장

```c
typedef struct State State;
typedef struct Suffix Suffix;
struct State { /* 접두사 + 접미사 리스트 */
    char* pref[NPREF]; /* 접두사들 */
    Suffix* suf; /* 접미사 리스트 */
    State* next; /* 해시 테이블 안에서 다음 State */
};

struct Suffix { /* 접미사 리스트 */
    char* word; /* 접미사 */
    Suffix* next; /* 접미사 리스트 안에서 다음 Suffix */
}
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/6ad5719f-ad1f-4498-b6dd-57e3e17a4074/3df2fee3-8df7-4f26-8d41-5c806f4c9488/Untitled.png)

2장에서 만든 문자열 해시 함수를 고쳐서 배열 안의 문자열들을 처리하도록 작성

```c
/* hash: NPREF 개의 문자열로 구성된 배열의 해시 값을 계산 */
unsigned int hash(char *s [NPREF]) {
    unsigned int h;
    unsigned char* p; 
    int i; 

    h = 0; 

    for (i=0; i < NPREF; ++i)
        for (p=(unsigned char*)s[i]; *p!='\0'; ++p) 
            h = MULTIPLIER * h + *p;
    return h % NHASH;
}
```

```c
/* lookup: 접두사를 검색, 필요한 경우 접두사를 생성 */
/* 접두사를 찾았거나, 만들었으면 포인터를 리턴, 그 외의 경우에 NULL 리턴 */
/* 새로 만든 문자열은 strdup으로 만든 것이 아니므로 변경하면 안 됨 */
State* lookup(char* prefix[NPREF] , int create) {
    int i;
    int h; 
    State* sp; 
    h = hash(prefix);
    for (sp=statetab[h]; sp!=NULL; sp=sp->next) {
        for (i=0; i<NPREF; ++i) {
            if (strcmp(prefix[i], sp-pref[i]) != 0) 
                break;
        }
        if (i == NPREF) /* 찾았다 */ 
            return sp;
    }
    if (create) {
        sp = (State*)emalloc(sizeof (State)); 
        for (i=0; i<NPREF; ++i) 
            sp->pref[i] = prefix[i]; 
        sp->suf = NULL; 
        sp->next = statetab[h]; 
        statetab[h] = sp;
    }
    return sp;
}
```

lookup은 새 state를 만들 때, 문자열에 대한 사본을 만들지 않음! 

sp→pref[]에 포인터를 저장할 뿐이다.

예를 들어, 문자열이 입출력 버퍼 안에 있다면, lookup을 호출하기 전에 사본을 먼저 만들어야 한다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/6ad5719f-ad1f-4498-b6dd-57e3e17a4074/f68d9aeb-a1aa-4fcd-bb91-eebfc96c702e/Untitled.png)

자원이 인터페이스 경계를 넘어 공유될 때에는 그 자원을 누가 관리할 것인지 결정해야 한다.

이제는 파일 내용을 읽어서 해시 테이블을 채워야 한다.

```c
/* build: 입력을 읽고 해시 테이블에 저장 */
void build(char* prefix[NPREF] , FILE* f) {
    char buf[100];
    char fmt[10];

    /* 포맷 문자열 작성, %s만 쓸 경우 buf가 오버플로우할 가능성이 있음 */
    sprintf(fmt, "%%%ds", sizeof(buf)-1); 
    while(fscanf(f, fmt, buf) != EOF) 
        add(prefix, estrdup(buf));
}
```

sprinf를 쓴 이유 : fscanf를 쓸 때 발생할 수 있는 골치 아픈 문제를 피해가기 위해서다.

fscanf는 %s 포맷을 주면, 파일의 내용을 읽어서 공백으로 구분된 문자를 읽는다. 이 경우, 버퍼의 크기에 제한이 없기 때문에, 단어가 매우 길다면 입력 버퍼가 넘칠 수 있다.

```c
/* add: 단어를 접미사 리스트에 추가하고, 접두사를 갱신한다 */
void add(char* prefix[NPREF] , char* suffix) {
    State* sp; 

    sp = lookup(prefix, 1); /* 없으면 생성한다 */ 
    addsuffix(sp, suffix); 
    /* 접두사 배열에서 단어들을 하나씩 앞으로 당긴다 */ 
    memmove(prefix, prefix+l, (NPREF-l)*sizeof(prefix[0])); 
    prefix[NPREF-1] = suffix;
}
```

위 코드에서 memmove는 접두사 배열의 1번부터 NPREF-1번 원소를 앞으로 당겨 0번부터 NPREF-2번 원소로 만들어 준다.

```c
/* addsuffix: Suffix를 state에 추가. 이때 접미사는 나중에 변경하면 안됨 */
void addsuffix(State asp, char *suffix) { 
    Suffix* suf; 

    suf = (Suffix*)emalloc(sizeof (Suffix)); 
    suf->word = suffix;
    suf->next = sp->suf; 
    sp->suf = suf;
}
```

1. 접미사를 접두사에 추가하는 add 루틴
2. 단어를 접미사 리스트에 추가하는 addsufix