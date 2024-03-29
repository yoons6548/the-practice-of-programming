
# 7장 성능


``` ad-quote
그의 바람은, 과거에 그랬듯이, 힘이었다.
하지만 그의 능력은, 지금 그렇듯이, 무(無)다
```

- **최적화의 첫 번째 법칙**
	- *하지 않는다* : 프로그램을 최적화할 수 있는 가장 좋은 전략은 과제에 맞는 가장 단순하면서도 가장 분명한 알고리즘과 데이터 구조를 사용하는 것이다. 그리고 성능을 측정해서 수정이 필요한지 확인한다.
- **최적화의 두 번째 법칙** :
	- *측정* : 측정은 성능 개선에서 아주 중요한 부분이다. 성능 개선은 테스트와 통하는 부분이 많다. 자동화, 철저한 기록 유지, 변경 이후에도 정확한 동작을 하며 과거에 개선한 내용에 영향을 주진 않는지 보기 위한 회귀 테스트 등 똑같은 테크닉을 사용할 수 있다.

# 7.4 코드 미세조정

``` ad-info
title: 과열지역(속도가 느려진 구간)을 찾았을 때 **실행시간을 줄일 수 있는 기법** **10가지**
```

**코드 미세조정의 적용 규칙 세가지**
*하나*. 중간 점검,
*둘*. 최소화,
*셋*. 효과 측정

1. **중간 점검** : 회귀 테스트로 코드가 계속 제대로 돌아가는지 확인한다.
2. **최소한의 코드** : (컴파일러가 수행할 수 있는) 불필요한 코드를 작성하지 않는다.
3. **효과 측정** : 효과를 측정하여 정말 그게 도움이 되는지 확인한다.

---

**풀어서 작성하기 5가지 (오히려..!)**
- 공통된 부분 표현식을 하나로 모으라
- 비싼 연산을 싼 연산으로 대체하라
- 루프를 펼치거나 제거하라
- 저수준 언어로 재작성하라
- 근사값을 사용하라

**임시(미리) 저장해서 효율화하기 3가지**
- 빈번히 사용되는 값을 캐싱하라
- 결과를 사전계산하라
- 입력과 출력을 버퍼링하라
  
  **특수한 경우를 예외 처리하기 2가지**
- 특수한 메모리 할당 함수를 작성하라
- 특수한 경우를 따로 처리하라

---
###### 공통된 부분 표현식을 하나로 모으라

``` ad-info
title: 비싼 계산 작업 한 개가 여러 번 반복된다면, 한 군데서만 수행하고 결과를 기록한다.
```

*거리를 계산할 때 연달아 같은 값으로 sqrt를 두 번 호출하는 매크로* :
``` java
? sqrt(dx*dx + dy*dy) + ((sqrt)dx*dx + dy*dy) > 0 ? ...)
// 이 제곱근(sqrt) 함수값은 한 번만 계산하고 그 답을 두 곳에서 쓰면 된다.
```

한 루프 안에서 하는 계산이, 그 루프 안에서 변하는 어떤 값에도 의존성이 없다면, 그 계산을 밖으로 꺼낸다.

*변경 전* :
``` java
for (i = 0; i < nstarting[c]; i++){
```

*변경 후* :
``` java
n = nstarting[c];
for (i = 0; i < n; i++)
```

###### 비싼 연산을 싼 연산으로 대체하라

``` ad-info
title: 연산강도의 감축(reduction in strength)이란 말은 비싼 연산을 싼 연산으로 대체하는 최적화를 의미한다. 
```

- *옛날에 효과적이었던 방법* : 곱셈 연산을 덧셈 연산 또는 시프트 연산으로 대체
	- 오늘날 위의 해결책만으로는 별로 효과가 없다.
- *현재 효과적인 방법* : 
	- **나눗셈**을 ***곱셈의 역연산***으로 대체
	- 나누는 수(제수)가 2의 승수일 때, **나머지 연산**을 ***마스킹 연산***으로 대체
	- C나 C++ 언어에서 배열 자료의 위치를 **인덱스로 찾는 부분**을 ***포인터***로 대체
	- **함수 호출**을 ***단순한 직접 계산***으로 대체

``` ad-danger
title: *주의* : 컴파일러가 자동으로 해주는 부분을 굳이 대체할 필요는 없다.
```


*변경 전* : 평면에서의 두 점 중 어느 쪽이 더 멀리 떨어져 있는지 결정하려면 제곱근 계산을 두 번 해야 함
``` java
sqrt(dx*dx+dy*dy)
```

*변경 후* : 단순히 아래처럼 그 거리의 제곱값만 비교해도 제곱근값을 비교하는 것과 동일한 결과를 얻을 수 있음
``` java
if (dx1*dx1+dy1*dy1 < dx2*dx2+dy2*dy2)
```

###### 루프를 펼치거나 제거하라

``` ad-info
title: 루프를 준비하고 실행하는 데도 분명히 오버헤드(*딜레이?*)가 있다.
```

**case 1.** 만약 루프 본문이 많이 길거나 많이 반복되지 않는다면, *각 반복주기 내용을 쭉 작성*하는 게 더 효율적일 수도 있다.

*변경 전* : 
``` java
for (i = 0; i < 3; i ++)
	a[i] = b[i] + c[i];
```

*변경 후* : 
``` java
a[0] = b[0] + c[0];
a[1] = b[1] + c[1];
a[2] = b[2] + c[2];
// 이렇게 하면 루프, 특히 분기(branching)의 오버헤드가 없어진다. 분기는 실행 흐름에 끼어들어 방해하는 부분이기 때문에 오늘날의 프로세서 구조상 속도를 느리게 만들 수 있다. 
```

**case 2.** 만약 루프가 더 길다 해도, *더 적은 반복횟수를 써서* 오버헤드를 일부 줄일 수 있다. 

*변경 전* : 
``` java
for (i = 0; i < 3*n; i ++)
	a[i] = b[i] + c[i];
```

*변경 후* : 
``` java
for (i = 0; i < 3*n; i +=3){
	a[i+0] = b[i+0] + c[i+0];
	a[i+1] = b[i+1] + c[i+1];
	a[i+2] = b[i+2] + c[i+2];
	}
// 단, 이런 방식은 반복횟수가 각 단계에 수행되는 연산 크기의 배수일 때만 통한다. 그게 아니라면 끝 부분을 조정하기 위해 추가 코드를 넣어야 한다.
```

###### 근사값을 사용하라

``` ad-info
title: 정확성이 중요하지 않은 경우라면 **정밀도가 더 떨어지는 데이터 타입**을 사용한다.
```

###### 저수준 언어로 재작성하라

``` ad-info
title: C++이나 자바 프로그램의 중요한 부분을 C언어로 재작성하거나 어떤 프로그램의 해석 스크립트 부분을 컴파일된 언어로 대체하면 실행 속도가 상당히 빨라질 수 있다.
```

``` ad-danger
title: *주의* : 최후의 수단으로 고려하라. 유지보수 망이 될 수 있다.
```



---

###### 빈번히 사용되는 값을 캐싱하라

``` ad-info
title: 값을 캐싱(*caching - 많이, 자주 쓰는 데이터를 따로 저장해서 속도를 향상시키는 기법*)하면 다시 계산할 필요가 없다.
```

*하드웨어*는 **캐싱**을 광범위하게 이용하여 속도를 향상시키는데, 이는 *소프트웨어*에도 동일하게 적용된다. (*웹브라우저* : 인터넷에서 데이터 전송이 느려질 경우를 위해 웹페이지와 이미지를 캐싱한다. ) 

**캐싱 연산**은 프로그램을 더 빠르게 하는 효과 외에는 나머지 부분에 영향이 없어야 한다. 

*변경 전* : 캐싱 사용 전 
``` java
drawchar(c);
// drawchar 함수의 원 버전은 show(lookup(c))를 호출하는 것이었다.
```

*변경 후* : 캐싱을 사용 - 내부 정적 변수를 써서 앞의 문자와 그 코드를 기록
``` java
if (c != lastc) { /* 캐시를 갱신한다 */ 
	lastc = c;
	lastcode = lookup(c);
}
show(lastcode);
```

###### 결과를 사전계산하라

``` ad-info
title: 자주 쓰는 값을 사전계산해서 필요할 때 바로 쓸 수 있게 준비해두면 프로그램을 더 빠르게 만들 수 있다.
```

###### 입력과 출력을 버퍼링하라

``` ad-info
title: 버퍼링(buffering)을 하면 트랜잭션(주 : 쪼갤 수 없는 데이터 처리의 최소 단위)들을 모아 한번에 처리하기 때문에 잦은 연산을 가능한 한 적은 오버헤드로 수행할 수 있다.
```

**어떤 C 프로그램이 printf를 호출하면..**

1. 문자들을 버퍼에 채우면서 그 버퍼가 꽉 차거나 명시적으로 비워질 때까지는 운영체제로 넘기지 않는다. (*즉, 입력에서 버퍼링을 함*)
2. 운영체제가 받더라도 데이터를 디스크에 쓰는 작업은 또 지연된다. (*그래서 좋다? - 연산을 자주 안하니까 ?*)
3. 문제는 데이터를 보기 위해서 출력 버퍼를 비워야 한다는 것이다. (*비우는게 안좋다는 소리?*)
4. 최악의 경우 프로그램이 죽을 때 버퍼에 남아있던 정보가 사라져 버릴 수도 있다.(*어쩔티비..?*)
   
---

###### 특수한 메모리 할당 함수를 작성하라

``` ad-info
title: 범용 메모리 할당 함수 호출 부분을 *특수 할당 함수*로 대체하면 상당한 속도 개선을 꾀할 수 있다.
```


**case 1.** 대부분의 메모리 요청이 *똑같은 블록 크기*로 들어올 경우
1. 프로그램의 유일한 과열 지역이 *malloc*이나 *new*를 많이 호출하는 메모리 할당 부분인 경우가 많다.
2. **malloc에 대한 호출**을 *여러 메모리 블록으로 구성된 큰 배열을 가져오는 것*으로 대체한다.
3. 필요할 때마다 블록을 한 개씩 나눠준다. (가동비용이 싼 연산을 수행)
4. 해제한 블록은 자유 리스트(free list)에 돌려놓아 빨리 재사용할 수 있다.

**case 2.** 대부분의 메모리 요청이 *비슷한 블록 크기*로 들어올 경우
- 이 경우에는 **공간**을 내주고 *시간*을 취하는 방법으로 **항상 가장 큰 요청에 맞는 크기**를 할당한다. 
	- 짧은 문자열들을 다룰 때 *특정 길이 이하의 모든 문자열*에 **똑같은 메모리 크기**를 쓰면 효과적이다. 

**case 3.** 스택 기반 할당을 사용하는 경우 (모든 할당 작업이 끝난 뒤, 전체를 해제)
- 커다란 메모리 영역을 일단 얻은 뒤, 그것을 스택처럼 사용한다.
	- *push* : 필요할 때마다 할당된 항목들을 넣고
	- *pop* : 마지막에 한꺼번에 꺼낸다.
		- 몇몇 C라이브러리에서 이런 할당 방법을 사용할 수 있는 alloca라는 함수가 있다.

###### 특수한 경우를 따로 처리하라

``` ad-info
title: 똑같은 크기의 객체들을 별도 코드에서 따로 처리하면, 시간 및 공간 오버헤드도 아끼고 조각화 비율도 줄일 수 있다.
```

인페르노 시스템(다양한 플랫폼 및 분산 환경을 구축, 지원하기 위해 개발한 소규모 운영체제)의 그래픽 라이브러리에서 기본 draw 함수는 최대한 단순하고 직관적으로 작성되어있다.
1. 즉, 기본 셋팅을 단순하게 만들어놓고
2. 다양한 경우에 대한 맞춤식 최적화를 해나갔다. 
3. 결국 몇 가지 경우만 최적화 하면 되어서
4. 모든 경우를 고려할 필요가 없었다.

---
