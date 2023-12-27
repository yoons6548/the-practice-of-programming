## 6.5 부하 테스트
**컴퓨터가 생성하는 대량의 입력도 또 하나의 효과적인 테스트 기법**이다.
- 입력을 대량으로 하면 다양한 TC를 얻을 수 있어 예상하지 못한 이슈를 파악할 수 있음
- ex) 
	- 입력 버퍼, 배열, 카운터에서 넘치는 경우
	- 프로그램에서 고정 크기 공간을 사용하면서 점검되지 않는 부분을 찾아낼 수 있음
	- 사람이 잘 만들지 않는 데이터의 경우
		- 빈 입력이나 순서가 잘못된 입력, 범위가 잘못된 입력
		- 아주 긴 이름이나 천문학적인 데이터 값

임의의 입력을 넣는 것도 뭔가를 망가뜨릴 생각으로 프로그램을 공격할 수 있는 또 다른 방법
- "사람들이 이런 건 하지 않을 거야"의 논리적 확장인 셈
- 문제의 명세서를 활용해서 문법적으로는 유효하지만 괴상한 테스트 데이터를 만드는 프로그램을 구축

### 테스트 목표
**테스트 목표는 분명한 에러를 밝혀내기보다는 '일어날 수 없는' 일과 사고를 유발하는 것**이다. 또 에러 처리 코드를 테스트하는 것도 좋은 방법이다. 태생적으로 버그는 이런 특수한 상황에 숨어있는 경우가 많다.
- 현실에서는 일어나기 힘들어서, 고쳐봤자 별로 효과가 없는 문제를 찾아내는 경우가 있음.
- 한계효용감소의 법칙에 따라 유도리 있게 행동하자.

### Buffer overflow
**직간접적으로, 프로그램 외부에서 값을 받을 수 있는 루틴이라면 그 입력값을 사용하기 전에 반드시 먼저 검증하자**.
- 버퍼를 넘치게하는 오버플로우 공격으로 메모리 공간인 버퍼를 넘치게 한다. 그러면 그 프로그램은 무너지고 적대적인 프로그래머가 컴퓨터를 속여 그 자리에서 악의적인 프로그램을 실행할 수 있다.

#### What is buffer?
메모리를 참조하는 프로그램 변수와 관련하여 특정 경계를 갖는 컴퓨터의 주 메모리 영역
```c
int arr[10]
```
arr의 사이즈는 10x4 = 40 bytes
arr[0]는 left boundary / arr[9]는 right boundary 

#### What is buffer overflow?
메모리 버퍼에 쓰여져야 할 데이터가 버퍼의 left or right boundary를 넘어 쓰여지면 buffer overflow라고 한다.

이 경우 데이터가 버퍼를 참조하는 프로그램 변수에 속하지 않는 메모리의 일부에 기록된다.

```c
arr[10] = 10;
```
index 0~9 까지는 10byte의 버퍼를 참조하는 데 사용할 수 있다.
하지만 위 예시에서 index 10은 10을 저장하는데 사용. 바로 이 때 right boundary를 넘어 데이터가 기록되기 때문에 buffer overrun이 발생한다.
- C 실행 파일을 생성하기 위해 GCC compile process를 이해하는 것이 중요
#### Why are buffer overflows harmful?
**Buffer overflows, if undetected, can cause your program to crash or produce unexpected results.**

##### heap 메모리에 10바이트가 할당된 Case#1
```c
char *ptr  = (char*) malloc(10);
```
아래와 같이 했을 때
```c
ptr[10] = 'c';
```
대부분의 경우 crash가 발생한다. pointer가 자신에게 속하지 않은 heap 메모리에 access 할 수 없기 때문

##### 스택에서 버퍼의 용량을 초과하여 버퍼를 채우려고 하는 다른 Case#2
```c
char buff[10] = {0};
strcpy(buff, "This String Will Overflow the Buffer");
```
데이터가 배열 'buff'의 right boundary를 넘어서 쓰여진다.
이제 사용 중인 컴파일러에 따라 컴파일 중에 이 문제가 발견되지 않고 실행 중에 충돌이 발생하지 않을 가능성이 높다. 그 이유는 스택 메모리가 프로그램에 속하기 때문에 이 메모리의 버퍼 오버플로가 눈에 띄지 않을 수 있기 때문이다.

따라서 이러한 경우 버퍼 오버플로는 주변 메모리를 조용히 손상시키고, 손상된 메모리를 프로그램에서 사용하는 경우 예기치 않은 결과를 초래할 수 있다.
- [How to Avoid Stack Smashing Attacks with GCC (thegeekstuff.com)](https://www.thegeekstuff.com/2013/02/stack-smashing-attacks-gcc/)

#### Buffer Overflow Attacks
attacker가 프로그램의 버퍼 오버플로우에 대해 알고 이를 악용하는 예시
```c
#include <stdio.h>
#include <string.h>

int main(void)
{
    char buff[15];
    int pass = 0;

    printf("\n Enter the password : \n");
    gets(buff);

    if(strcmp(buff, "thegeekstuff"))
    {
        printf ("\n Wrong Password \n");
    }
    else
    {
        printf ("\n Correct Password \n");
        pass = 1;
    }

    if(pass)
    {
       /* Now Give root or admin rights to user*/
        printf ("\n Root privileges given to the user \n");
    }

    return 0;
}
```
위의 프로그램은 프로그램이 사용자에게 비밀번호를 요구하고 비밀번호가 맞으면 사용자에게 루트 권한을 부여하는 시나리오를 시뮬레이션한 것

올바른 비밀번호, 즉 'thegeekstuff'로 프로그램을 실행
```c
$ ./bfrovrflw 

 Enter the password :
thegeekstuff

 Correct Password 

 Root privileges given to the user
```

하지만 이 프로그램에서 버퍼 오버플로우가 발생할 가능성이 있다
gets() 함수는 배열 바운드를 확인하지 않기 때문에 문자열이 기록되는 버퍼의 크기보다 큰 길이의 문자열을 쓸 수도 있다.
attcker는 이 것의 허점을 이용할 수 있음.
```c
$ ./bfrovrflw 

 Enter the password :
hhhhhhhhhhhhhhhhhhhh

 Wrong Password 

 Root privileges given to the user
```

위의 예에서는 비밀번호를 잘못 입력한 후에도 올바른 비밀번호를 입력한 것처럼 프로그램이 작동함.

attcker는 버퍼가 수용할 수 있는 길이보다 더 긴 입력.
특정 길이의 입력에서 버퍼 오버플로가 발생하여 정수 'pass'의 메모리를 덮어썼다.
따라서 비밀번호를 잘못 입력했음에도 불구하고 'pass' 값이 0이 아닌 값이 되어 공격자에게 루트 권한이 부여됐다.

#### To avoid buffer overflow attacks
프로그래머에게 제공되는 일반적인 조언은 올바른 프로그래밍 관행을 따르는 것이다.
- Make sure that the memory auditing is done properly in the program using utilities like [valgrind memcheck](https://www.thegeekstuff.com/2011/11/valgrind-memcheck/)
- Use fgets() instead of gets().
- Use strncmp() instead of strcmp(), strncpy() instead of strcpy() and so on.

#### Example
- Morris Worm 사건 : 단순히 인터넷의 크기를 알고자 하는 순수한 호기심으로 자기 복제를 할 수 있는 웜을 개발하여 네트워크로 전파. 당시 전 세계 인터넷의 10% 가까이를 마비시켰으며 이를 기점으로 정보보안이라는 것이 본격적으로 실체화가 되었을 정도
	- [[정보보안 카페] 악성코드 - 5. 모리스 웜 : 네이버 블로그 (naver.com)](https://blog.naver.com/PostView.naver?blogId=nine01223&logNo=222287912351)
	- MIT의 종신 교수로 재직
- Explosion of first Ariane 5 flight, June 4, 1996
![](https://i.imgur.com/vPPPXG5.png)

### Type casting overflow
타입 간의 변환도 오버플로우의 원인이 될 수 있다.
- ex) 64bit 부동소수값을 16bit signed 정수로 변환 시 overflow가 일어남.

## 정리
- 테스트 목표는 분명한 에러를 밝혀내기보다는 '일어날 수 없는' 일과 사고를 유발하는 것
- 컴퓨터가 생성하는 대량의 입력도 또 하나의 효과적인 테스트 기법
- 직간접적으로, 프로그램 외부에서 값을 받을 수 있는 루틴이라면 그 입력값을 사용하기 전에 반드시 먼저 검증하자
- 타입 간의 변환도 오버플로우의 원인이 될 수 있음
- 좋은 테스트 케이스를 만들어서 다양한 프로그램에서 함께 사용하자.


# Reference
- [Buffer Overflow Attack Explained with a C Program Example (thegeekstuff.com)](https://www.thegeekstuff.com/2013/06/buffer-overflow/)