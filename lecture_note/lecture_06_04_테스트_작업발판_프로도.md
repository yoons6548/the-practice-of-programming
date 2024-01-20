## 테스트 방법의 변환 필요. 
- 지금까지는 단일 독립 프로그램 검증 가정. 
- 만약 일부분의 "컴포넌트" 일 경우 어떻게 해야 할까? 
- 다른부분에 대한 인터페이스 제공하고, 지원 역할을 하는 프레임워크나 작업발판(scaffold) 을 필수로만들어야 함. 
- 수학 함수, 문자열 함수, 정렬 루틴 등 테스트는 쉬움 (매개변수 설정, 함수 호출, 결과확인 하면 됨) 
- 복잡한 시퀀스를 가진 스테이트 머신이거나 중간처리 프로그램이면 어떻게 테스트? 
- 이번 장에서는 표준 라이브러리인 mem.. 함수의 하나인 memset함수의 test 구축 작업 해보자. 
	- 참고 : https://en.cppreference.com/w/cpp/string/byte/memset
	- 미리보기 : https://codebrowser.dev/linux/linux/arch/arm64/lib/memset.S.html (for linux)
	- 미리보기 : https://stackoverflow.com/a/20340917 (for x86)
		- 이제 어셈블리가 기억이 안나요.. ㅠㅠ 2003년에 배웠던.... 
	- 속도가 중요하여 어셈블리어로 작성된다. (stack 이나 몇몇것 들은 ucos 포팅 등 해보면 어셈블리어로 함) 
	- 한번에 어셈블리로 바로 작성하는 것은 어렵고, C로 작성 후, 어셈블리로 작성해보자. 
		- C로 작성하고 디컴파일을 이용하여 어셈블리 코드를 보는 것도 방법임. 

## try 1 : C code 작성
```c
/* memset : s 의 첫 n 바이트를 c로 설정한다. */
void *memset(void* s, int c, size_t n)
{
	size_t i;
	char *;

	p = (char *) s;
	for (i = 0; i < n; i++)
		p[i] = c;
	return s;
}
```
- 속도가 중요하지 않으면 위 코드로 해도 충분함. 
- 속도가 중요하다면 한번에 32비트나 64비트워드를 통째로 쓰는 것과 같은 기법을 활용한다. 
	- 32bit processor 는 32bit, 64bit processor 는 64bit 을 한번에 쓰는것이 가장 효율적임. 
	- SIMD (Single Instruction Multiple Data) 를 지원한다면 하나의 stride 크기 (data access 길이)로 하는 것도 효율적이다. 
	- 제가 했던 HVX (Qualcomm) 의 경우 1024bit (128 byte) 의 데이터를 한번에 처리하도록 지원.  
- 속도가 많이 중요하다면 load / store 과정에서 cpu stole 등을 우려하여 미리 L2 fetch 등을 하는 것도 방법이다.
	- 접근하지 않던 대용량의 data 가 호출되고, 이것이 cpu 에 L2 cache 에 load 되어 있지않으면 load 되는 동안 cpu가 놀게 된다. (cpu stole) 
	- 이를 사전에 막는 방법이 미리 L2 cache 에 load (fetch) 해놓는 방법이다. 

## try 2 : Test code 작성
- 경계조건 테스트는 기본. 
	- n = 0, 1, 2 (경계값), 2의 승수나 근처값, 아주 작은 값이나, 2<sup>16</sup> 처럼 아주 큰 값이 모두 경계 조건임. 
	- 2의 승수를 테스트 해야 하는 이유는 memset 을 빠르게 만드는 방법 중 하나가 한번에 여러 바이트를 한꺼번에 쓰는 방법이기 때문임. (특별 명령어나 한번에 워드씩(바이트 아님) 쓰는 방법) 
	- c가 여러값일 경우 : 0, 0x7F (8비트 바이트에서 부호있는 가장 큰 값), 0x80, 0xFF, 1바이트 보다 큰 값 등
- 이를 조합하면 다음 코드를 테스트용 표준 대조군으로 활용할 수 있음. 
```
// big 에 마진 둔 수로 s0, s1 을 alloc 후, 
// memset 두 버전 (직접 버전과 표준 라이브러리버전) 돌려 비교

big = maximum left margin + maximum n + maximum right margin
s0 = malloc(big)
s1 = malloc(big)
for each combination of test parameters n, c, and offset : 
	set all of s0 and s1 to known pattern
	run slow memset(s0 + offset, c, n)
	run fast memset(s1 + offset, c, n)
	check return values
	compare all of s0 and s1 byte by byte
```
- 배열 밖에서 오류 내면 처음이나 맨 끝에서 몇 바이트 사이에 셩향을 줄 가능성이 높음. 
- 버퍼 영역 남겨두고 확인하여 확인 쉽게 하고, 프로그램의 다른 부분을 덮는 에러 생길 가능성 줄임. 
- 그래서 합리적인 테스트는 다음과 같은 모든 조합을 포함. 
```
offset = 10, 11, ...., 20
c = 0, 1, 0x7F, 0x80, 0xFF, 0x11223344
n = 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 15, 16, 17, 
    31, 32, 44, ..., 65535, 65536, 65537
// 65535 : 0xFFFF, 65536 : 0x10000, 65537 : 0x10001
```
- n 은 i가 0 부터 16일때까지 2<sup>i</sup>-1, 2<sup>i</sup>, 2<sup>i</sup>+1 을 포함해야 한다. 
	- 테스트 작업발판의 중심 뼈대에(????) 직접 드러나선 안되지만, 
	- 수작업이나 프로그램으로 생성한 배열에서는나타나야 함. 
		- 그래서 자동 생성 데이터를 쓰는게 나을수도 있음. 
	- 저자의 경우 0x7F 이상의 값을 확인하지 않아서 부호 확장과 관련한 버그 (signed / unsigned? ) 가 있었다고 한다. 
- 검증의 방법은 옳다고 믿어지는 단순한 프로그램을 잘못된 것 같은 새 버전과 대조하는것. 
