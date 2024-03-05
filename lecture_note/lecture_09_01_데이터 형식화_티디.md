# 개요
알맞은 언어를 사용하면 프로그램을 쉽게 작성할 수 있는 정도가 완전히 달라진다.
좋은 표기법의 힘은 전통적인 프로그래밍 영역을 뛰어넘어 특수 문제 영역에까지 이른다.

# 9.1 데이터 형식화

컴퓨터에 하고 싶은 말과 일을 시키기 위해 해야 하는 말 사이에는 항상 간극이 존재한다.
-> **이 간극을 좁힐수록 좋다**. 좋은 표기법은 우리가 하고 싶은 말을 더 쉽게 하게 만들고, 실수로 잘못된 말을 하기 어렵게 만든다.

작은 언어(little language)는 좁은 영역을 위한 특수 표기법이다. 좋은 인터페이스를 제공할 뿐 아니라 그 인터페이스를 구현하는 프로그램을 잘 체계화하는 일까지 도와준다.
```c
printf("%d %6.2f %-10.10s\n", i,f,s);
```

이 형식화 문자열의 각 % 기호는 다음 printf 인자의 값이 들어올 자리라는 것을 나타내는 신호다. 선택적 표시 기호와 필드 너비 다음에 나오는 종결 문자는 어떤 종류의 매개변수를 기대하는지 말해준다. 이 표기법은 압축적이고, 직관적이며, 쓰기 쉽고, 구현방식도 정직하고 단순하다.
c++(iostream), java(java.io)의 대체 함수는 비록사용자 정의 타입을 받고 타입 검사를 해주긴 하지만 특수 표기법을 따로 제공하지 않기 때문에 좀 더 불편해 보인다.

## c, c++ 비슷한 예
한 시스템에서 다른 시스템으로 다양한 데이터 타입의 조합을 담은 패킷을 전송한다고 생각하자.

가장 깔끔한 방법은 텍스트 표현형식으로 변환하는 것
but 표준 네트워크 프로토콜에는 효율성이나 크기 면에서 볼 때 바이너리 형식이 적당할 가능성이 높다.
그럼 호환 가능하고 효율적이면서도, 사용하기 쉬운 패킷 처리 코드를 어떻게 만들어야 할까?

![](https://i.imgur.com/x9MxvCr.png)

각 패킷 타입을 묶고 푸는 함수를 작성하면 다음과 같다.
```c
int pack_type1(unsigned char *buf, unsigned short count,
			   unsigned char val, unsigned long data)
{
	unsigned char *bp;

	bp = buf;
	*bp++ = 0x01;
	*bp++ = count >> 8;
	*bp++ = count;
	*bp++ = val;
	*bp++ = data >> 24;
	*bp++ = data >> 16;
	*bp++ = data >> 8;
	*bp++ = data;
	return bp - buf;
}

```
-> 이런 반복적인 코드는
1. 틀리기 쉽다.
2. 가독성이 낮다.
3. 유지보수가 어렵다.

> [!example]
>unsigned char buf[1024];
  unsigned short count = 0x1234;
  unsigned char val = 0x56;
  unsigned long data = 0x78901234;
  >
  -> 01 12 34 56 78 90 12 34

printf에서 아이디어를 빌려 조그만 명세형 언어를 정의해서 각 패킷을 그 레이아웃을 표현한 간단한 문자열로 기술할 수 있다. 
패킷 내 연속적인 원소를 다음과 같이 인코딩한다.
- 8bit 문자는 c
- 16bit short 타입 정수는 s
- 32bit long 타입 정수는 l

> [!example]
> pack1 의 경우 형식화 문자열 "cscl"로 기술할 수 있다.
> ```c
> pack(buf, "cscl", 0x01, count, val, data);
> ```

```c
int pack(unsigned char *buf, char *fmt, ...)
{
  va_list args;
  char *p;
  unsigned char *bp;
  unsigned short s;
  unsigned long l;

  bp = buf;
  va_start(args, fmt);
  for (p = fmt; *p != '\0'; p++) {
    switch (*p) {
    case 'c': // char
      *bp++ = va_arg(args, int);
      break;

    case 's': // short
      s = va_arg(args, int);
      *bp++ = s >> 8;
      *bp++ = s;
      break;
    
    case 'l': // unsigned long
      l = va_arg(args, unsigned long);
      *bp++ = l >> 24;
      *bp++ = l >> 16;
      *bp++ = l >> 8;
      *bp++ = l;
      break;

    default: // long type
      va_end(args);
      return -1;
    }
  }
  va_end(args);
  return bp - buf;
}
```

> [!tip]
> `va_arg(args, type)` 매크로를 사용할 때, `char`와 `short` 타입의 인자는 함수 호출 시 정수형 인자로 자동 확장(promotion)되기 때문에 `va_arg` 매크로를 사용하여 직접 추출할 수 없습니다. 이는 C 언어의 인자 전달 규칙에 따른 것입니다. 함수로 전달되는 `char`와 `short` 타입의 인자는 가변 인자로 처리될 때 `int` 타입으로 자동 확장됩니다. 이러한 규칙은 효율성과 이식성을 높이기 위해 존재합니다. CPU는 `int` 타입을 처리하는 데 최적화되어 있으며, 더 작은 타입을 처리할 때 추가적인 작업을 수행해야 할 수 있습니다.
> ```bash
‘unsigned char’ is promoted to ‘int’ when passed through ‘...’
‘short unsigned int’ is promoted to ‘int’ when passed through ‘...’

> [!output]
> ```bash
> pack_type1 output = 01 12 34 56 78 90 12 34 
pack output = 01 12 34 56 78 90 12 34 
> ```

unpack도 동일하게 하면 된다.
```c
/* unpack : buf에서 묶인 항목들을 풀고 그 길이를 리턴한다 */
int unpack(unsigned char *buf, char *fmt, ...)
{
  va_list args;
  char *p;
  unsigned char *bp, *pc;
  unsigned short *ps;
  unsigned long *pl;

  bp = buf;
  va_start(args, fmt);
  for (p = fmt; *p != '\0'; p++) {
    switch (*p) {
    case 'c':
      pc = va_arg(args, unsigned char*);
      *pc = *bp++;
      break;

    case 's':
      ps = va_arg(args, unsigned short*);
      *ps = *bp << 8;
      *ps != *bp++;
      break;

    case 'l':
      pl = va_arg(args, unsigned long*);
      *pl = *bp++ << 24;
      *pl |= *bp++ << 16;
      *pl |= *bp++ << 8;
      *pl |= *bp++;
      break;
    
    default:
      va_end(args);
      return -1;
    }
  }
  va_end(args);
  return bp - buf;
}
```
unapck 함수를 호출하는 특정 타입용 풀기 루틴은 간단하다.

```c
int unpack_type2(int n, unsigned char *buf)
{
  unsigned char c;
  unsigned short count;
  unsigned long dw1, dw2;

  if (unpack(buf, "csll", &c, &count, &dw1, &dw2) != n)
    return -1;
  assert(c == 0x02);
  return process_type2(count, dw1, dw2);
}

/* unpack : buf에서 묶인 항목들을 풀고 그 길이를 리턴한다 */
int unpack(unsigned char *buf, char *fmt, ...)
{
  va_list args;
  char *p;
  unsigned char *bp, *pc;
  unsigned short *ps;
  unsigned long *pl;

  bp = buf;
  va_start(args, fmt);
  for (p = fmt; *p != '\0'; p++) {
    switch (*p) {
    case 'c':
      pc = va_arg(args, unsigned char*);
      *pc = *bp++;
      break;

    case 's':
      ps = va_arg(args, unsigned short*);
      *ps = *bp << 8;
      *ps != *bp++;
      break;

    case 'l':
      pl = va_arg(args, unsigned long*);
      *pl = *bp++ << 24;
      *pl |= *bp++ << 16;
      *pl |= *bp++ << 8;
      *pl |= *bp++;
      break;
    
    default:
      va_end(args);
      return -1;
    }
  }
  va_end(args);
  return bp - buf;
}

int (*unpackfn[])(int, unsigned char *) = {
  unpack_type0,
  unpack_type1,
  unpack_type2
};

/* receive : 네트워크에서 패킷을 읽어 처리한다 */
void receive(int network)
{
  unsigned char type, buf[BUFSIZ];
  int n;

  while ((n = readpacket(network, buf, BUFSIZ)) > 0) {
    type = buf[0];
    if (type >= NELEMS(unpackfn))
      eprintf("bad packet tpye 0x%x", type);
    if ((unpackfn[type])(n, buf) < 0)
      eprintf("protocoll error, type %x length %d", type, n);
  }
}
```

각 패킷을 처리하는 코드가 압축적이고 한군데 모여 있기 때문에 유지보수하기 쉽다.
수신 측은 프로토콜에 대해 독립성이 커지며, 깔끔하고 빠르기까지 하다.

이 접근방식을 통해 몇천 줄 저어도 되던 반복적이고 에러가 생기기 쉬웠던 코드가 유지보수하기 쉬운 몇백 줄 정도로 줄었다.
표기법이 혼란을 확 잠재운 예시이다.