## 😋6장에서 나온 테스트 방식을 적용

적용 대상: 마르코프 프로그램

여러 프로그래밍 언어로 작성

1. 경계 조건 테스트 ( 조건 분기를 잘 타는지 루프가 의도한 횟수만큼 도는지)

```c
(empty file)
a
a b
a b c
a b c d
```

테이블 초기화 이슈로 off by one 발생

1. 보존 속성 테스트

```c
BEGIN {
while (get1 i ne iARGV[l] > 0)
	for (i = 1; i <= NF; i++) {
		wd[++nw] = Bi # input words
		single[$i]++
	}
for (i = 1; i <nw; i++)
	pair[wd[i] ,wd[i+1]]++
for (i = 1; i < nw-1; i++)
	triple[wd[i] .wd[i+1] ,wd[i+2]]++

while (getline <ARCV[2] > 0) {
	outwd[++ow] = $0 # output words
	if (!(SO in single))
		print "unexpected word". $0
}
for (i = 1; i < ow; i++)
	if ( ! ((outwd[i] , outwd[i+1]) in pair))
		print "unexpected pair" , outwd[i] , outwd[i+1]
for (i = 1; i < ow-1; i++)
	if (!((outwd[i],outwd[i+1],outwd[i+2]) in triple))
		print "unexpected triple ",
			outwd[i] , outwd[i+1] , outwd[i+2]
}
```

입력 → 긴 배열에 저장 → 2개 집합과 3개 집합의 배열을 만듬

마르코프 프로그램의 출력을 읽은 배열과 비교

자바 버전에서 에러 발견 → 

1. 통계적인 테스트

```c
abcabcabc.....abd... 

//abc가 10번 반복하고 abd가 한번 나오는 케이스
```

각 접미사마다 카운트를 몇번 하는지

또 자바..

d가 하나 나올때 c가 20개씩 나왔다

---
