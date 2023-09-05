# 1.3 일관성과 관용 표현
일관성은 좋은 프로그램으로 향하는 길이다.
일관성이 없다면 프로그램이 도대체 어떻게 작동하는지 알아보기 힘들 것이다.
- 코드 스타일을 예측할 수 없을 경우
- 배열을 도는 루프가 앞에서 뒤로 돌았다가 뒤에서 앞으로 도는 경우
- 문자열을 여기서는 strcpy로 복사했다가 저기서는 for 루프로 복사하는 경우
동일한 연산은 매번 동일한 방식으로 규칙을 정한다면, **변화가 있는 부분은 정말로 다른, 주목할 가치가 있는 부분이라는 것을 파악할 수 있다.**

## 들여쓰기와 중괄호 '{ }'를 쓰는 스타일에서는 일관성을 지켜라
중요한 것은 일관성 있는 코드지 세부적인 스타일이 어떤 것이냐가 아니다.
**한 스타일을 선택해 일관되게 사용하자.**

괄호와 마찬가지로 중괄호는 코드의 애매함을 해결해주며 일반적으로 코드를 더 명료하게 한다.

```c
if (month==FEB) { 
	if (year%4 == 0) 
		if (day > 29) 
			legal = FALSE;
	else
		if (day > 28)
			legal = FALSE;
}
```

- { } 추가
```c
if (month==FEB) { 
	if (year%4 == 0) {
		if (day > 29) 
			legal = FALSE;
	} else {
		if (day > 28)
			legal = FALSE;
	}
}
```

```c
if (month == FEB) {
	int nday;
	
	nday = 28;
	if (yearOA == 0)
		nday = 29;
	if (day > nday)
		legal = FALSE;
}
```
**이 코드도 여전히 잘못되어 있지만 그래도 이 구조가 본격적으로 고쳐 나가기에 훨씬 편하다.**
여러분이 작성하지 않은 프로그램을 손 볼때는 **원래 코드의 스타일을 그대로 유지하는 것이 좋다**. 코드를 수정할 때, 자신만의 스타일은 절대 쓰지 않도록 한다. 코드의 일관성은 개발자 자신의 스타일보다 중요하다. 이 규칙을 따르면 삶이 더 편해지기 때문이다.
## 일관성을 위해 관용 표현을 사용하라
자연언어와 마찬가지로 프로그래밍 언어에도 관용 표현과 경험 많은 프로그래머들이 일반적으로 사용하는 상용 표현들이 있다.

### 루프 예제
```c
i=O;
while (i <= n-1)
	array[i++] = 1.0;
```

```c
for (i = 0; i < n; )
	array[i++] = 1.0;
```

```c
for (i = n; --i >= 0; )
	array[i] = 1.0;
```

```c
for (i = 0; i < n; i++)
	array[i] = 1.0;
```

위 코드들은 모두 맞지만, 관용적인 형식은 다음과 같다.
```c
for (int i = 0; i < n; i++)
	array[i] = 1.0;
 ```
아무렇게나 선택한 형식이 아니다.
- 0 ~ n-1 까지 원소 n개를 돈다.
- for 문만 사용해서 루프를 제어한다.
- 오름차순으로 돈다.
- 루프 변수를 갱신할 때 사용하는 매우 관용적인 방법인 ++ 연산자를 사용한다.
- 루프가 돈 마지막 루프 변수의 다음 값을 루프 변수에 남긴다.
-> 영어를 아는 사람이면 별다른 노력을 하지 않아도 루프가 어떻게 작동하는지 알아챌 수 있고 기능 변경을 하려고 할 때 쉽게 고칠 수 있다.

### linked list 예제
```c
for (p = list; p != NULL; p = p->next) 
 ...
```
링크드 리스트를 도는 루프는 위와 같이 쓰는게 표준이다.

### 무한 루프 예제
아래 두가지 형식 외 다른 것은 쓰지 않도록 한다.
```c
for (;;)
	...
```
```c
while(1)
	...
```

### 들여쓰기 예제
다음과 같이 수직으로 맞춰서 쓴 특이한 레이아웃은 가독성을 떨어뜨리며 루프가 하나가 아니라 세 문장처럼 보인다.
```c
for (
	ap = arr;
	ap < arr + 128;
	*ap++ = 0
	)
{
	;
}
```

다음과 같이 보다 표준적인 방법으로 루프를 쓰는 것이 훨씬 읽기 편하다.
```c
for (ap = arr; ap < arr+128; ap++) 
	*ap = 0;
```
길게 펼쳐서 쓴 코드는 화면에서 여러 페이지로 나뉘어 보이기 때문에 가독성을 떨어뜨린다.

### 루프 조건안에 대입문을 넣는 경우
```c
while ((c = getchar()) != EOF) 
	putchar(c);
```
do-while 문은 항상 적어도 한 번은 실행되며 조건 검사가 루프의 처음이 아니라 마지막에 일어나기 때문에 for 문과 while 문보다 훨씬 더 적게 쓰인다.

```c
do {
	c = getchar();
	putchar(c);
} while (c != EOF);
```
do-while 문은 루프 안의 블록이 적어도 한 번은 반드시 실행되어야 하는 경우에만 써야 한다.
관용 표현을 일관되게 사용할 때 좋은 점 하나는 **일반적이지 않은 루프를 사용해서 발생하는 문제점이 쉽게 눈에 뛴다는 점이다.**

### off by one 예제
다음에 있는 메모리를 건드리지만 프로그램이 죽을 정도로 치명적인 여파가 오는 것은 보통 한참 더 진행된 뒤이기 때문에 정확히 어느 곳에서 문제가 발생했는지 찾기 매우 어렵다.
```c
int i, *iArray, nmemb; 

iArray = malloc(nmemb * sizeof(int));
for (i =O; i <= nmemb; i++)
	iArray[i] = i;
```
루프의 계속 조건을 <= 연산자로 검사했기 때문에 루프가 배열을 벗어나서 그 다음 메모리의 내용이 뭐든지 그냥 덮어써 버린다. 안타깝게도 이런 오류는 손상이 발생한지 한참 뒤에서야 발견되는 편이다.


#### off by one
일반적으로 반복문, 배열 인덱싱, 범위 지정 등에서 발생한다. 이 오류는 보통 한 단계나 한 요소를 놓치거나 초과하는 경우에 발생하며, 이름에서 알 수 있듯이 "하나 차이나는" 오류이다.
##### example
```c
int array[10];
for (int i = 0; i <= 10; ++i) {
    array[i] = i;
}
```
```c
// 수정 코드
int array[10];
for (int i = 0; i < 10; ++i) {
    array[i] = i;
}
```


### 문자열을 저장할 공간을 할당하는 관용 표현 예제
이를 사용하지 않는 코드는 대게 버그를 수반한다.
```c
char tp, buf C[256];
gets(buf);
p = malloc(strlen(buf));
strcpy(p, buf);
```
gets는 입력이 들어오는 양을 조절할 방법이 없기 때문에 절대로 사용해서는 안된다. (6장과 관련)
항상 gets보다 fgets를 사용하는 편이 더 좋다.
strcpy는 문자열의 끝을 표시하는 '\0'를 복사하지만, strlen은 '\0'을 문자열의 길이로 세지 않는 것이 문제다. 따라서 공간이 충분히 할당되지 않으며, strcpy는 결국 할당된 공간을 지나쳐서 덮어쓴다. 이 경우는 보통 이렇게 쓴다.
```c
p = malloc(strlen(buf)+1);
strcpy(p, buf);
```
```c
p = new char[strlen(buf)+l];
strcpy(p, buf);
```
+1이 없는 곳을 발견하면 주의하자.
## 다중결정이 필요할 떄는 else-if를 사용하라
많은 갈래로 갈라지는 다중 결정에는 보통 if ... else if ... else를 엮어서 쓴다.
```c
if (condition 1 ) 
	statement 
else if (condition2) 
	statement 2 
... 
else if (condition n)
	statement n
else 
	default-statemen
```
default로 실행되어야 할 동작이 없는 경우에는 마지막 else를 생략할 수 있지만, **에러 메시지를 뿌리도록** default를 넣어두면 발생해서는 안되는 상황이 발생하는걸 알아채는데 도움이 된다.

```c
if (argc==3)
	if ((fin = fopen(argv[l] , "r")) != NULL)
		if ((fout = fopen(argv[2], "w")) != NULL) {
			while ((c = getc(fin)) != EOF)
				putc(c, fout); ? fclose(fin);
			fclose(fout);
		} else
			printf ("Can't open output file %s\n", argv[Z]);
	else
		printf ("Can't open input file %s\nW ,argv[l]");
else 
	printf ("Usage: cp inputfi le outputfi le\n");
```
조건문들을 머리에 스택을 쌓아가면서 생각해야한다. (한번에 해석하기 힘듬)

```c
if (argc != 3) 
	printf ("Usage: cp inputfile outputfile\n"); 
else if ((fin = fopen(argv[l] , "r")) == NULL) 
	printf("Can't open input file %s\nM, argv[l]"); 
else if ((fout = fopen(argv[2], "w")) == NULL) 
	printf ("Can't open output file %s\nn, argv[2]");
	fclose(fin);
else {
	while ((c = getc(fin)) != EOF) 
		putc(c, fout); 
	fclose(fin);
	fclose(fout);
}
```
코드가 명료해졌다. 
맨 처음 참이라고 평가되는 조건과 그 조건에서 실행되는 동작들을 본 뒤에, 마지막 else 다음으로 넘어가면 된다.
각각의 조건문과 그 조건문에서 유발되는 동작들은 가능한 가까이 붙여서 써야 하는 것이 원칙이다. 아니면 아예 매번 조건 안에서 동작을 실행해버리는 방법도 있다.

### switch case 예제
코드를 재활용하려고 하다 보면 다음과 같이 지나치게 꽉 짜인 프로그램이 되곤 한다.
```c
switch (c) {
case '-': sign = -1;
case '+': c = getchar();
case '.': break;
default: if (! isdigit(c))
			return 0;
}
```
여기서는 코드 한 줄이 중복되는 것을 절약하기 위해서 case를 break로 닫지 않고 아래로 쭉 내리는(fall-through) 트릭을 사용한다. **이 방식은 관용 표현으로 볼 수 없다.**
아주 가끔 주석으로 표시를 달고 생략하는 경우를 제외하면, case에는 항상 break로 닫는 것이 일반적이기 때문이다.

다음과 같이 쓰면, 약간 길어지기는 하지만 보다 전통적인 형식이기도 하고 읽기도 쉽다.
```c
switch (c) { 
case '-': 
	sign = -1; 
	/* fall through */ 
case '+' : 
	c = getchar(); 
	break; 
case '.': 
	break; 
default : 
	if (!isdigit(c)) 
		return 0; 
	break;
}
```
그러나, 코드가 명료해진 것 이상으로 길어져 버렸다. 이런 특이한 구조보다는 그냥 else-if 문이 더 깔끔하다.
```c
if (c == '-') { 
	sign = -1; 
	c = getchar(); 
else if (c == '+') { 
	c = getchar(); 
} else if (c != '.' && !isdigit(c)) { 
	return 0;
}
```
블록 안에 문장이 한 개밖에 없는데도 중괄호로 감싼 것은 앞의 블록과 병렬적이라는 것을 강조하기 위해서다.

break를 쓰지 않는 case문을 써도 괜찮은 경우로는 완전히 똑같은 코드를 여려 경우에 쓰는 방법이 있는데, 일반적으로 이런 형식으로 쓴다.
```c
case '0':
case '1':
case '2':
	...
	break;
```
이 경우에는 break를 쓰지 않는 코드라고 주석을 달아줄 필요가 없다.
## 연습 문제
### 1-7 세 c/c++ 코드를 좀 더 명료하게 고쳐보자
if문을 해석하면 아래와 같이 명료하게 고칠 수 있다.
```c
if (istty(stdin));
	else if (istty(stdout)); 
		else if (istty(stderr)); 
			else return(0);
```
```c
if (!istty(stdin) && !istty(stdout) && !istty(stderr)) {
	return 0;
}
```

쓸모 없는 코드를 제거하자.
```c
if (retval != SUCCESS) 
{
	return (retval);
}
/* All went well ! */
return SUCCESS;
```
```c
return retval;
```

관용적인 for문 구조로 변경하자.
```c
for (k = 0; k++ < 5; x += dx) 
	scanf("%lf" , &dx);
```
```c
for (k = 0; k < 5; k++)
{
	scanf("%lf", &x);
	x+=dx;
}
k++;
```
### 1-8 이 자바 코드에서 오류를 찾아내 루프를 관용 표현으로 고쳐보자
- off by one 오류 발생 가능
	- count++를 증가 시키고 찾기 때문에
- while -> for 문으로 변경
- count -> i 로 변경한다.
```Java
int count = 0;
while (count < total) {
	count++;
	if (this.getName(count) == nametable.userName()) {
		return(true);
	}
}
```
```Java
for(i = 0; i < total; i++) {
	if(this.getName(i) == nametable.userName()) {
		return true;
	}
}
