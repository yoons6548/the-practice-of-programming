## 😋1.6 주석

## - 소스코드를 읽는 사람들을 돕기 위한 것

---

## 명확한 코드에는 주석을 달지 말자

```python
/* SUCCESS를 반환 */
return SUCCESS;
```

---

## 함수와 전역 데이터에 주석을 달아라

```python
struct State {
	char *pref[NPREF];    /* 접두어 단어들 */
	Suffix *suf;          /* 접미어 목록 */
	State *next;          /* 해시테이블 다음 노드 */
}

//random: [0,..r-1] 영역에 있는 정수를 반환한다
int random(int r)
{
	return (int)(Math.floor(Math.random()*r));
}
```

---

## 나쁜 코드에 대해 설명하지 말고 코드를 새로 짜라

설명이 긴 코드 예시

```python
 /* "result"가 0이면 맞는 것이 발견되었기 때문에 '참'(0이 아닌 값)을 반환한다.
    그리고 "result"가 0이 아니라면 '거짓'(0)을 반환한다 */ 

 #ifdef DEBUG
 printf ("w* isword returns !result = %d\n" , ! result) ;
 fflush(stdout);
 #endif

 return(! result) ;
```

수정된 예

```python
 #ifdef DEBUG
 printf ("w* isword 리턴. matchfound = %d\n" , matchfound);
 fflush(stdout);
 #endif

 return matchfound;
```

---

## 주석과 코드가 모순되게 하지 말라

코드를 수정할땐 주석도 같이 수정

---

## 혼란스럽게 하지 말고, 명확하게 하라

설명이 혼란스러운 예시

```java
int strcmp(char nsl, char ns2)
/* 이 문자열 비교 루틴은 s1이 오름차순으로 s2 앞에 있는 경우 -1을 반환하고 n/
/* 같으면 0을, a/
/a s2가 s1의 뒤에 있으면 1을 반환한다 */
while(nsl==as2) {
if(*sl=='\O') return(0);
sl++;
s2++;
I
if (nsl>*s2) return(1) ;
return(-1) ;

```

설명이 명확해진 예시

```java
int strcmp(char nsl, char ns2)
/a s1<s2면 음수, s1>s2면 양수, 같으면 0을 반환 */
while(nsl==as2) {
if(*sl=='\O') return(0);
sl++;
s2++;
I
if (nsl>*s2) return(1) ;
return(-1) ;

```
