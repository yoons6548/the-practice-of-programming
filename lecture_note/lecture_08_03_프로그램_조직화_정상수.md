## 😋호환성에 접근하는 2가지 방식

1. 합집합
    1. 시스템에서 최고의 기능들을 다 사용
    2. 호환이 안되면, 컴파일과 프로세스를 환경마다 다르게 해서 사용
2. 교집합
    1. 모든 환경에서 사용 가능한 기능만 사용

## 어디서나 쓸수 있는 기능만 사용하라

교집합식을 추천

- 어디서나 쓸수 있도록 모든 대상 시스템에 존재하는 기능만 사용

stdlib.h 헤더 파일이 없는 경우에 대처하려는 코드

```c
#if defined (STDC-HEADERS) || defined (_LIBC)
#include<stdlib.h>
#else
	extern void *malloc(unsigned int);
	extern void *realloc(void *, unsigned int);
#endif
```

malloc, free를 정의한 헤더 파일을 소프트웨어에 같이 싣는 편

## 조건 컴파일을 지양하라

```c
#ifdef NATIVE
	char rastring = "convert ASCII to native character set" ;
#else

#ifdef MAC
	char *astring = "convert to Mac textfile format";
#else
#ifdef DOS
	char *astring = "convert to DOS textfile format";
#else
	char *astring = "convert to Unix textfile format";
#endif /* ?DOS r/
#endif /* ?MAC a/
#endif /* ?NATIV */
```

목표했던 것과 달리 호환성이 떨어짐

새로운 환경이 등장할때마다 계속 ifdef 추가해야하는 귀찮음

더 일반적인 표현을 사용해서 호환 가능하게 대체

```c
char rastring = "convert to local text format";
```

컴파일 제어 흐름과 실행 제어 흐름이 섞인 경우

```c
#ifndef DISKSYS
	for (i = 1; i <= msg->dbgmsg.msg-total; i++)
#endif
#ifdef DISKSYS
	i = dbgmsgno;
	if (i <= msg->dbgmsg . msg-total)
#endif
	{
	 if (msg->dbgmsg.msg-total == i)
#ifndef DISKSYS
	break;
#endif
```

---

- 모든 대상 시스템의 공통된 기능만 사용
- 호환성에 문제가 있다면, 조건문을 추가하지 말고 코드를 재작성
