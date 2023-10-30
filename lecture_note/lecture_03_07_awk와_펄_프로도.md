## 스크립트 언어로 동일한 알고리즘을 구현해보기
- Awk, Perl
	- 결합배열, 문자열 연산 제공
- 결합배열
	- 해시 테이블을 사용하기 편한 형태로 만든 것
	- 문자열이나 숫자 또는 쉼표로 구분된 문자열이나 숫자의 리스트를 인덱스로 사용 가능
	- 일종의 map
	- Perl 의 결합배열은 해시라고도 부름. 
- 본 예시에서는 접두사 길이가 두 단어인 경우만 처리할 수 있도록 만들었음. 

## Awk
```awk  
# markov.awk: markov chain algorithm for 2-word prefixes
BEGIN { MAXGEN = 10000; NOWWORD = "\n"; w1 = w2 = NOWWORD }   # 초기화
{
    for (i = 1; i <= NF; i++) { # read all words
        statetab[w1,w2,++nsuffix[w1,w2]] = $i            # 접두사 - 접미사 연결 map 만듬
        w1 = w2
        w2 = $i
    }
}
END {
    statetab[w1,w2,++nsuffix[w1,w2]] = NOWWORD # add tail
    w1 = w2 = NOWWORD
    for (i = 0; i < MAXGEN; i++) {  # generate
        r = int(rand()*nsuffix[w1,w2]) + 1 # nsuffix >= 1
        p = statetab[w1,w2,r]
        if (p == NOWWORD)
            exit
        print p
        w1 = w2 # 접두사 전진
        w2 = p
    }
}
```
- Awk 는 패턴-액션 언어
	- 한줄씩 들어오고 들어오는 줄 마다 패턴 일치 검사 받음. 
	- 패턴이 일치하면 연결된 액션이 실행 됨. 
	- 첫 줄 들어오기 전 BEGIN, 마지막줄 이후 END 실행
	- 입력줄은 필드(공백문자로 구분된 단어들) 로 나누는데, 각 이름은 $1 ~ $NF 까지 붙음. 
- 위 프로그램은...
	- BEGIN 에서 먼저 필요한 초기화를 수행 (MAXGEN, NOWWORD 등 )
	- 그 다음 For 문에서 NF (입력줄 갯수) 만큼 실행 함. 
		- statetab 배열을 만들며 접두사 - 접미사 연결하는 map 을 만듬. 
			- (이라고 되어 있는데, statetab 이 원래 있는 함수도 아니고.. 모르겠...)
		- nsuffix 는 w1, w2 를 가지고 있는 배열로 보이는데, 단어 2개 (접두사-접미사) 연결에 대하여 중복된 것은 count 를 증가하여 동일한 짝의 수를 count 하는 목적으로 보임. 
		- w2 는 w1 으로, 새 문장은 w2 로 넣고, 계속 연결함. 
	- END 에서.. 
		- 종료 되면 맨 끝은 NOWWORD 로 추가 (add tail)
		- w1, w2 도 NOWWORD 로 마크
		- r =  .... 에서 접두-접미 쌍의 count 에서 rand 를 곱하여 hash 화 시킴. 
		- p = .. 에서 접두, 접미, hash 의 값을 저장하고? 가져오고? 
		- if .. 에서는 마지막으로 END 의 시작에서 온 단어가 만나게 되면 종료 함. 
		- 마지막 것이 아니면 
			- 출력 (print p), w1, w2 에 값을 가져와서 값을 순환하며 다음 for 를 진행 함. 


## Perl
```perl
# markov.pl: markov chain algorithm for 2-word prefixes
$MAXGEN = 10000;
$NOWWORD = "\n";
$w1 = $w2 = $NOWWORD;   # 최초 접두사
while (<>) {    # 입력 글을 한 줄씩 처리
    foreach (split) {
        push(@{$statetab{$w1}{$w2}}, $_);
        ($w1, $w2) = ($w2, $_); # 복합 대입
    }
}
push(@{$statetab{$w1}{$w2}}, $NOWWORD); # add tail - 끝에 NOWWORD 추가. 
$w1 = $w2 = $NOWWORD;
for ($i = 0; $i < $MAXGEN; $i++) {
    $suf = $statetab{$w1}{$w2}; # 배열 참조  : 출력할 때 배열 가리키는 레퍼런스
    $r = int(rand @$suf);   # @$suf : 원소 갯수
    exit if (($t = $suf->[$r]) eq $NOWWORD);  # r 번째 접미사 : $suf->[$r]
    print "$t\n";
    ($w1, $w2) = ($w2, $t); # 접두사 전진
}
```

- 앞의 awk 와 유사. 
- map 은 statetab 에 저장함. 
- 핵심 코드 : 새 접미사를 저장된 (익명) 배열 끝에 추가 함. 
```perl
        push(@{$statetab{$w1}{$w2}}, $_);
```
- 출력 할 때 접미사 배열 가리키는 레퍼런스 (위 코드 참조)
- r 번째 접미사 (위 코드 참조)

## 정리
- awk 나 perl 로 작성한 프로그램은 매우 짧음. 
- 하지만 두단어가 아닌 다른 개수의 단어로 된 접두사를 다루도록 고치기는 어려움. 
- 스크립트 언어는 실행 속도는 느리지만, 간단하게 코드를 만들 수 있으니 실험적 프로그램을 작성해보는데는 스크립트 언어도 좋은 방법일 수 있음. 
