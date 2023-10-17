# 리스트
배열 다음으로 자주 쓰는 데이터 구조.
C++, Java는 라이브러리에서 리스트를 제공한다.
- https://cplusplus.com/reference/list/list/
	- ![](https://i.imgur.com/lwHeJJV.png)



singly-linked list는 각각 데이터와 다음 아이템을 가리키는 포인터를 가지는 아이템들의 집합이다.

리스트의 헤드는 첫 번째 아이템을 가리키는 포인터이며, 리스트의 끝은 `nullptr`로 표시한다.
![](https://i.imgur.com/iHpb3Gw.png)

## 배열과 리스트의 차이점
![](https://i.imgur.com/Fzs31CI.png)
1. 배열은 크기가 고정되어 있지만, 리스트는 언제나 정확히 필요한 만큼만 메모리 공간을 사용하며, 노드마다 다음 노드를 가리킬 포인터를 저장할 공간이 필요하기 때문에, 약간의 오버헤드가 존재한다.
2. 메모리를 블록 단위로 옮겨야 하는 배열에 비해, 리스트는 포인터 몇 개만 바꾸면 순서를 바꿀 수 있으므로 배열에 비해 순서를 바꾸는데 필요한 비용이 적게든다.
3. 리스트에서는 노드를 추가하거나 삭제할 때 다른 노드의 위치를 바꿀 필요가 없으므로, 노드에 대한 포인터를 다른 데이터 구조 안에 저장해 둔 경우라도 리스트가 변경된 다음에 그 포인터들을 그대로 쓸 수 있다.

### 리스트를 사용하면 좋은 경우 
> 이런 차이점들을 보면, 집합의 내용이 자주 바뀌거나 특히 전체 개수를 예상할 방법이 없을 경우 리스트를 쓰는 것이 좋다.

### 배열을 사용하면 좋은 경우
> 데이터가 상대적으로 변경되지 않고 정적이라면 배열이 더 좋다.

기본적인 리스트 관련 연산
- 리스트 맨 앞이나 맨 끝에 새로운 아이템 추가하기
- 특정한 아이템 찾기
- 새로운 아이템을 지정한 아이템 앞이나 뒤에 추가하기
- 때에 따라 아이템을 지우기

## 리스트 정의
C에서는 보통 리스트를 쓸 때, 따로 List 타입을 만들기 보다는 앞에서 만든 원소 타입을 정의해놓고 다음 원소를 가리키는 포인터를 추가한다.
```c
typedef struct Nameval Nameval; 
struct Nameval { 
	char *name; 
	int value;
	Nameval *next; /* in list */ 
};
```
빈 리스트가 아닌, 리스트를 컴파일할 때 초기화하는 것은 어려우므로 배열과 달리 동적으로 만들어야 한다.

가장 직접적인 방법으로 `newitem` 함수를 만들어 공간을 할당하는 방법이 있다.
```c
/* newitem: 주어진 name과 value로 새 아이템을 만듦*/
Nameval *newitem(char* name, int value)
{
	Nameval *newp;

	newp = (Nameval *) emalloc(sizeof(Nameval)); // emalloc: malloc을 불러서 메모리를 할당하고 실패할 경우 에러를 출력하고 프로그램을 끝내는 함수 (4장에서 다룰것)
	newp->name = name;
	newp->value = value;
	newp->next = NULL;
	return newp;
}
```

## 리스트 앞에 항목을 추가
리스트를 작성하는 가장 단순하고 빠른 방법은 새 아이템을 리스트의 앞 부분에 추가하는 것이다.
```c
/*addfront: newp를 listp의 앞쪽에 추가*/
Nameval *addfront(Nameval *listp, Nameval *newp)
{
	newp->next = listp;
	return newp;
}
```
리스트가 변경될 때는 그 리스트의 첫 번째 원소가 달라질 수 있다. 특히 addfront가 호출되었다면 반드시 첫 번째 원소가 바뀐다. 따라서 리스트를 변경하는 함수들은 변경된 첫 번째 아이템을 가리키는 포인터를 리턴해야 하며, 프로그래머는 이 값을 리스트를 가리키는 변수에 저장해야 한다. addfront나 이 함수와 비슷한 함수들은 모두 첫 번째 아이템의 포인터를 리턴한다. 따라서 보통 다음과 같이 쓴다.
```c
nvlist = addfront(nvlist, newitem("smiley", 0x263A));
```
이것은 nvlist가 null인 경우에도 작동하며, 한 수식에서 쉽게 표현할 수 있도록 설계한 것이다.

## 리스트 끝에 항목을 추가
리스트 끝에 항목을 추가하는 연산은 O(n)이며, 리스트의 끝을 찾으려면 리스트 전체를 거쳐야 하기 때문이다.
```c
/* addend: newp를 listp의 끝에 추가 */
Nameval *addend(Nameval *listp, Nameval *newp)
{
	Nameval *p;

	if (listp == null)
		return newp;
	for (p = listp, p->next != NULL; p = p->next);

	p->next = newp;
	return listp;
}
```
addend를 O(1) 연산으로 만들고 싶다면, 리스트 끝을 가리키는 포인터를 따로 가지고 있으면 된다.
이 방식의 단점은 끝 포인터를 유지하는 것이 번거로울 뿐 아니라 리스트를 한 포인터로만 표현하지 못한다는 것이다. 우리는 그냥 단순한 방식을 쓸 것이다.

## 이름을 가진 아이템 리스트 찾기
원하는 이름을 가진 아이템을 찾으려면 next 포인터를 따라가며 찾아보면 된다.
```c
/* lookup: listp에서 이름을 선형 검색으로 찾는다*/
Nameval *lookup(Nameval *listp, char *name)
{
	for ( ; listp != NULL; listp = listp->next)
		if (strcmp(name, listp->name) == 0)
			return listp;
	return NULL; /* 일치하는 것이 없음*/
}
```

**이 연산은 O(n)이 걸리며 더 간단하게 만들 방법은 없다.** 리스트가 정렬된 상태라 해도 특정한 원소에 접근하려면 차례대로 각 아이템을 거쳐가야 한다. 좀 더 나은 성능을 보이는 이진 검색도 리스트에는 쓸 수 없다.

## 리스트를 따라가면서 아이템마다 지정한 함수를 불러주는 apply 함수
apply가 주어진 함수를 호출할 때마다 그 함수에게 어떤 정보를 제공하도록, apply에게 인자를 하나 넘겨주면 더욱 유용하게 쓸 수 있다.
따라서 apply는 세 인자, 즉 작업할 리스트, 각 아이템에 적용될 함수, 이 함수에 넘길 인자를 받는다.
```c
/* apply: listp의 원소마다 fn을 실행 */
void apply(Nameval *listp, void (*fn)(Nameval*, void*), void *arg)
{
	for ( ; listp != NULL; listp = listp->next)
		(*fn)(listp, arg); /* 함수 호출*/
}
```

apply의 두 번째 인자는 인자를 두 개 받고 void를 리턴하는 함수에 대한 포인터다.
함수에 대한 포인터는 아래와 같이 표현한다.
```c
void (*fn)(Nameval*, void*)
```
위 선언은 fn이 void를 리턴하는 함수의 주소를 저장하는 포인터라는 것을 나타낸다.
이 포인터가 가리키는 함수는 리스트 아이템인 Nameval과 정보를 전달받을 포인터인 void* 를 인자로 받는다.
### 각 원소를 출력하기 위한 apply 함수
다음과 같이 포맷 문자열을 받는 함수를 만들 수 있다.
```c
/* printnv: 인자로 받은 출력 형식으로 name과 value를 출력 */
void printnv(Nameval *p, void *arg)
{
	char *fmt;

	fmt = (char *)arg;
	printf(fmt, p->name, p->value);
}
```
```c
// 다음과 같이 apply를 부르면 된다.
apply(nvlist, printnv, "%s: %x\n");
```

### 아이템 개수를 세는 apply 함수
아이템 개수를 세려면, 개수를 저장할 변수에 대한 포인터를 인자로 받는 함수를 만들면 된다.
```c
/* inccounter: 카운터 *arg를 하나씩 늘림 */
void inccounter(Nameval *p, void *arg)
{
	int *ip;
	/* p는 쓰이지 않음 */
	ip = (int *)arg;
	(*ip)++;
}
```
```c
// 이 함수를 다음과 같이 호출한다.
int n;

n = 0;
apply(nvlist, inccounter, &n);
printf("%d elements in nvlist\n", n);
```

## 리스트를 제거할 경우
```c
/* freeall: listp의 모든 아이템에 대한 메모리 할당 해제 */
void freeall(Nameval *listp)
{
	Nameval *next;

	for( ; listp != NULL; listp = next) {
		next = listp->next;
		/* name은 다른 곳에서 해제되었다고 가정함 */
		free(listp);
	}
}
```
메모리는 할당이 해제된 후에 사용될 수 없으므로, listp 포인터가 가리키는 아이템의 메모리 할당이 해제되기 전에, listp->next를 반드시 지역변수(next)에 따로 저장해야 한다. 만약 다른 함수들처럼 반복문이 다음과 같이 되어 있다면,
```c
for( ; listp != NULL; listp = listp->next)
	free(listp);
```
free 때문에 listp->next의 값이 사라지고, 코드는 실패할 것이다.

`freeall`은 listp->name이 가리키는 메모리를 해제하지 않는다는 것에 주의하라. `freeall`은 각 아이템의 name 필드가 다른 곳에서 해제되었거나 아예 처음부터 메모리가 할당되지 않았다고 가정한 것이다. 그리고 원소의 할당과 해제가 올바르게 이루어졌다면, newitem을 부른 횟수와 freeall을 부른 횟수가 같아야 한다. 

> 더 이상 필요하지 않는 메모리를 해제하는 것과, 필요한 메모리가 해제되지 않도록 관리하는 것은 서로 트레이드오프(tradeoff) 관계다.
> 자바 등 다른 언더들에서는 가비지 컬렉션이 이 문제를 해결함. 이런 자원 관리에 대해서는 4장에서 다룸

## 리스트에서 아이템 하나를 없애는 작업
```c
/* delitem: listp에서 처음으로 나오는 name을 제거 */
Nameval *delitem(Nameval *listp, char *name)
{
	Nameval *p, *prev;

	prev = NULL;
	for (p = listp; p != NULL; p = p->next) {
		if (strcmp(name, p->name) == 0) {
			if (prev == NULL)
				listp = p->next;
			else
				prev->next = p->next;
			free(p);
			return listp;
		}
		prev = p;
	}
	eprintf("delitem: %s not in list", name);
	return NULL; /* can't get here */
}
```
`freeall`과 마찬가지로 `delitem`도 name 필드가 가리키는 메모리는 해제하지 않는다.

## 정리
일반적인 프로그램을 작성할 경우, 지금까지 다룬 기본적인 리스트 구조와 연산들만으로도 충분하다. 하지만 다른 방법도 많다.
C++ 의 STL를 포함한 몇몇 library는 doubly-linked list를 지원한다.

중간에 아이템을 추가하거나 삭제하는 상황에 적합하다는 장점 이외에도, 리스트는 크기가 자주 변하고 정렬이 필요없는 데이터, 특히 스택처럼 (데이터 접근 측면에서) LIFO 특성을 보이는 데이터를 관리하기 좋다. 여러 스택이 상호 독립적으로 늘어나고 줄어들어야 하는 상황이라면 리스트가 배열보다 메모리를 더 효율적으로 사용한다. 리스트는 문서를 구성하는 단어들처럼 정보의 구조가 본질적으로 미리 크기를 알 수 없는 항목들의 연쇄 구조인 경우에도 잘 들어맞는다. 하지만 변경이 자주 일어나며 임의 접근 방식도 필요하다면, 선형(linear) 구조가 아닌 tree나 hash table을 쓰는 것이 바람직 할 것이다.

## 연습 문제
### 리스트 연산 만들기
2-7 리스트 복사, 두 리스트 하나로 합치기, 한 리스트 두개로 분리하기, 특정 원소 앞이나 뒤에 새 원소 추가하기 등의 리스트 연산을 만들기.
#### 리스트 복사
```c
Nameval *copy(Nameval *listp)
{
	Nameval *newp = NULL;
	for( ; listp != NULL; listp = listp->next) {
		newp = addend(newp, newitem(listp->name, listp->value));
	}
	return newp;
}
```
#### 두 리스트 하나로 합치기
```c
Nameval *merge(Nameval *listp, Nameval *newp)
{
	listp = addend(listp, newp);
	return newp;
}
```
#### 한 리스트 두개로 분리하기
```c
Nameval *split(Nameval **listp, int point)
{
	Nameval *p = *listp, *prev = *listp;
	if (point == 0) {
		*listp = NULL;
		return p;
	}
	while (point > 0 && p != NULL) {
		prev = p;
		p = p->next;
		point--;
	}
	prev->next = NULL;
	return p;
}
```
#### 특정 원소 앞이나 뒤에 새 원소 추가하기

또, 앞에 추가하는 연산과 뒤에 추가하는 연산 중 어느 것이 더 까다로운지 설명하기.
또, 이 글에서 다룬 함수를 얼마나 많이 써먹을 수 있는지, 또 얼마나 많은 코드를 새로 만들어야 할지 생각해보기
### 2-8 리스트에서 원소의 순서를 거꾸로 만드는 reverse 루틴을 만들어 보라
#### 2-8-1 재귀적 방식
```c
Nameval *reverserecursive(Nameval *prev, Nameval *listp)
{
    if (listp == NULL)
        return prev;
    Nameval *next = listp->next;
    listp->next = prev;
    return reverserecursive(listp, next);
}
```
#### 2-8-1 반복적 방식
```c
Nameval *reverseiteratively(Nameval *listp)
{
    Nameval *prev = NULL;
    while (listp != NULL)
    {
        Nameval *next = listp->next;
        listp->next = prev;
        prev = listp;
        listp = next;
    }
    return prev;
}

```
새 리스트 아이템을 생성하면 안 되며, 기존 아이템만 써서 작업해야 한다.

### 2-9 아무 때나 쓸 수 있는 List 타입을 C 언어로 만들어 보자.
가장 쉬운 방법은, 리스트 아이템마다 아무 데이터나 가리킬 수 있는 void* 를 가지게 하는 것.
또 C++에서 템플릿을 사용해서 똑같은 타입을 만들어 보라.
자바의 오브젝트 타입을 써서 같은 일을 해보라.
그리고 각 언어의 장단점은 무엇인가?
### 2-10 리스트 루틴들이 올바로 작동하는지 검사하는 테스트 여러개 만들기