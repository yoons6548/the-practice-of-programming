표현식(expression)과 문장(stratement) 이 의미가 최대한 분명히 들어나면 서로 읽기가 좋음. 

## 들여쓰기로 구조를 알아 보기 쉽게 하라

```c++
// step 1. 알아보기 힘듬. 
for(n++;n<100;field[n++]='\0');
*i = '\0'; return('\n');

// step 2. 들여쓰기 조정 -> for 문과 각 문장을 한 줄 단위로 수정 
for(n++;n<10;field[n++]='\0')
    ;
*i = '\0';
return('\n');

// step 3. field[n++] 분리
for (n++; n < 100; n++)
    field[n] = '\0';
*i = '\O';
return '\n';

//// 책에는 여기까지. 무엇을 더 고치면 좋을까요?

// step 4. 제 마음대로 추가 수정.
// for loop 를 도는 부분은 꼭 필요한 것이 아니면 별도의 counter 사용. local 변수를 사용하면 local 변수의 변화를 놓치기 쉬움. 
for (int j = n + 1; j < 100; j++) 
    field[j] = '\0';
*i = '\O';
return '\n';

// 혹은 memset 활용하기 -> 기존 제공되는 함수가 있다면 제공되는 함수를 한번 읽는 것이 for 내용안을 읽는 것 보다 훨씬 효율적임. 
// void * memset ( void * ptr, int value, size_t num );
memset(field + n + 1, '\0', 100);
*i = '\O';
return '\n';
```

## 표현식을 자연스럽게 쓰라
```c++
// step 1. 양쪽 다 부정이 있어서 읽기 어려움.
if (! (block_id < actblks) || !(block_id >= unblocks))  
{
  //
}

// step 2. 연산자를 바꿔 not을 없애보자.
// 읽기 쉬운 코드가 이해하기 쉽고, 그래야 human error 가 발생하지 않음. 
if ((block_id >= actblks) || (block_id < unblocks))  
{
  //
}
```

## 괄호를 써서 애매함을 해소하라
```c++
// 연산자 우선순위로 햇갈리는 코드가 발생할 수 있음.
// 지구최강 세계제일 코딩 잘알 선발대회도 아니고. 햇갈릴 수 있는 부분은 미리 모호함을 없애서 누구나 읽기 편하게 만들어주자.

// 비교연산자(< <= == != >= > 등)는 논리 연산자(&& ||) 보다 우선순위가 높다. 
// 섞여 있을 때는 괄호로 묶어서 쉽게 읽도록 해주자.
while << c = getchar()) != EOF)

// 아래의 경우 햇갈리기 쉽다.
if (x&MASK == BITS) {}
// .1^.....2^.. 의 순서로 실행될 것 같지만, 
if (x & (MASK == BITS)) {}  // 뒷 부분이 먼저 실행된다.

leap_year = y % 4 == 0 && y % 100 != 0 || y % 400 == 0;
//위 코드는 아래와 같이 괄호를 넣고 빈칸을 일부 삭제하면 더 이해하기 쉽다. 
leap_year = ((y % 4 == 0) && (y%100 != 0)) || (y%400 == 0);
```

## 복잡한 표현은 잘게 쪼개라
```c++
// 한줄에 너무 많이 들어가 있으면 힘들다.
*x += (*xp=(2*k < (n-m) ? c[k+1] : d[k--]));

// 이렇게 바꿔보자.
if (2*k < n-m)
    *xp = c[k+1];
else
    *xp = d[k--];
*x += *xp;
```

## 명료하게 쓰라
```c++
// 천하제일 코드자랑대회가 아니다. 간단하고 알기쉽게 쓰자.

subkey = subkey >> (bitoff - ((bitoff >> 3 ) << 3 ));
//........................3^..........1^.....2^..
// 1. right shift 3, 2. left shift 3. 이러면 원래 숫자에 2진수 하위 3자리가 000 인 숫자가 된다.
// 3. 이 숫자를 원래 숫자에서 빼면, 결국 2진수 하위 3자리만 남기는 연산이 된다.
// 그래서, 하위 3자리만 남기려면 bit masking 한번 하는게 더 명확해 보인다.

subkey = subkey >> (bitoff & 0x7);
// 0x7 = 0111 (2) -> bitoff 에 & 연산으로 하위 3자리만 bit masking 하는 연산 -> 위 1, 2, 3 연산 한번에 끝.
// ^...^....^..   자기 변수에 자기 변수 계산한 결과를 대입하는 것이니, 이렇게 바꿀 수도 있다.

subkey >>= bitoff & 0x7;

// 아래것은 중첩된 3항연산인데, 조금 난해하게 보일 수 있다.
child = (!LC&&!RC)?0:(!LC?RC:LC);
// ...............1^....2^..  3항 연산이 2번이나 나왔다.
// ......^..^.^...          부정에 and 연산에 부정.. 어렵다. !A && !B = !(A || B) 로 바꾸면 (드모르간 법칙) 더 쉽지 않을까?

// 하여튼, 이렇게 바꿀 수 있습니다.
if (LC == 0 && RC == 0)
    child = 0;
else if (LC == 0)
    child = RC;
else
    child = LC;

// 이건 책에 없는 내용. 
// 드모르간 법칙을 이용하면 아래와 같이도 바꿀 수 있습니다.
if (LC || RC ) {
    if (LC == 0)
        child = RC;
    else
        child = LC;
} else
    child = 0;

// 삼항 연산자를 쓰지 말라는 것은 아닙니다. 명확히 읽을 수 있는 짧은 식에 적합 합니다.
max = (a > b)? a : b;

// (책에 없는 내용)
// 하지만, 삼항연산자나 define 을 이용한 코딩 등은 실제 내용이 계산 될 때 햇갈릴 수 있으니
// 조금만 길어지면 괄호 등을 잘 사용하거나, 풀어서 작성하도록 하는게 좋습니다. 
```


## 부수효과를 조심하라
```c++
// ++
// 다음은 어떻게 동작 될까?
str[i++] = str[i++] = ' ';
// str 에 i 위치 이후 2개에 공백 입력. 사실 이건 모호하진 않음.

// 이건?
i = 3; // 이건 책에 없음. 
array[i++] = i;

// 변수를 참조 후 증가 함. 따라서 아래와 같이 됨. 
// array[3] = 3;
// i = 4;



// scanf
// 이건?
yr = 1 ; // 이건 책에 없음. 
scanf("%d %d", &yr, &profit[yr]);

// 위 코드에서는 yr 을 읽고 (예를 들어 3이 오면 yr = 3), profit[yr] (예를 들어 profit[3]) 에 동작될 것 같음. 
// 책에 따르면 다음과 같이 동작 됨.
yr = 1 ; // 이건 책에 없음. 
scanf("%d %d", &yr, &profit[1]);

// scanf 를 읽을 때 profit[yr]에 대한 부분을 먼저 위치를 잡기 때문에,
// yr 에 3 을 읽더라도 기존에 있는 값 1을 먼저 참조하여 profilt[1] 에 읽은 값을 작성하게 됨.
// 이렇게.. 값을 위험하게 사용하지 않도록 하자. buffer 를 쓰던가, 끊어서 읽는 것이 좋다.

scanf("%d", &yr);
scanf("%d", &profit[yr]);
```

## 연습 1-4 다음의 코드를 개선해 보자. 
```c++
if( !(c == 'y' || c == 'Y') )
    return;

length = (length < BUFSIZE) ? length : BUFSIZE;

flag = flag ? 0 : 1 ;

quote = (*line == '"') ? 1 : 0;

if (val & 1)
    bit = 1;
else
    bit = 0;
```

```c++
// 개선 1
if( !(c == 'y' || c == 'Y') )
    return;

// not(!) 을 줄여서 내용을 보기 편하게. 
if(c != 'y' && c != 'Y')
    return; 


// 개선 2
length = (length < BUFSIZE) ? length : BUFSIZE;
// -> length < BUFSIZE 일 경우에는 length 그대로임.
// -> length >= BUFSIZE 일 경우에 length = BUFSIZE 가 됨.
if (length >= BUFSIZE)
    length = BUFSIZE;

// 논리적으로는 위가 맞지만, length == BUFSIZE 이면 이 값이든 저 값이든 똑같음.
if (length > BUFSIZE)
    length = BUFSIZE;


// 개선 3
flag = flag ? 0 : 1 ;
// flag 가 true 이면 0
flag = !(flag)

// 사실 flag 가 int 나 float 등 bool 이 아닌 다른 수를 나타내는 변수일 경우에는
// if 를 통하여 값을 직접 지정 해야 하며, 코드가 삼항 연산자 이상 간단해지지 않음.
// 문제가 코드를 개선한다고 표현하는것으로 봐서 bool 이라는 전제를 하고 문제에 접근 함. 



// 개선 4
quote = (*line == '"') ? 1 : 0;
// quote 는 *line 이 " 이면 1, 아니면 0
quote = (*line == '"');

// 개선 3과 같이 bool 이라는 가정하에 가능.
// 값이 있어야 하면 삼항 연산자가 가장 간단함.


// 개선 5
if (val & 1)
    bit = 1;
else
    bit = 0;

// bit 이 bool 이라는 가정 하에,
bit = val & 1;

// 값을 표현한다면 삼항연산자가 더 간단함.
// 대신 연산자 우선순위를 조심하여 삼항연산자의 내부 요소는 각각 괄호로 묶어주는것이 안전함. 
bit = (val & 1) ? 1 : 0;
```

## 연습 1-5 어느 부분이 잘못되었을까? 
```c++
int read(int *ip) {
    scanf("%d", ip);
    return *ip;
}

insert(&graph[vert], read(&val), read(&ch));

// 본문 내용 중, scanf 의 경우 실행 싲머에 값의 모호성에 대한 부분이 있었음.
// compiler 에 따라서 실행되는 순서나, 실행의 순차 보장성에 따라서 값이 달라질 가능성이 있음. 
```

## 연습 1-6 계산 순서에 따라 이 코드의 결과로 나올 수 있는 여러 출력 값을 모두 나열해 보라. 
```c++
n = 1;
printf("%d %d\n", n++, n++);
 
```
위 코드를 최대한 많은 컴파일러로 돌려봐서 실제로 어떻게 되는지 알아보자. 

https://ideone.com/onXogd : 1 2  C (clang 8.0)

https://ideone.com/btk3yQ  :  2 1 C++ (gcc 4.3.2)

https://ideone.com/9rwPMy  :  2 1 C++ (gcc 8.3)

https://ideone.com/MGDof9  :  2 1 C++ (C++14)


