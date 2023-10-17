# 3 설계와 구현
데이터 구조가 결정된 다음에는 알고리즘도 바로 결정할 수 있고 코딩도 쉬워진다.
이렇게 데이터 구조 설계에서는 프로그래밍 언어의 선택이 상대적으로 중요하지 않다.
따라서 이번에는 프로그램을 설계해보고, 이를 C, java, C++, Awk, Perl을 사용하여 프로그램을 개발해볼 것이다. 각 언어마다 도움이 되는 부분과 도움이 되지 않는 부분들이 있지만, 큰 영향은 없다. 

우리가 만들 프로그램: 그럭저럭 읽을 수 있는 영어 문장을 만드는 프로그램

## 3.1 마르코프 체인 알고리즘

'그럭저럭 읽을 수 있는 영어 문장을 만드는 프로그램'을 만드는 문제를 처리하는 방법 중 하나!

```
원본 글의 첫 두 단어를 w1과 w2로 잡는다.
w1과 w2를 출력한다.
반복문:
    글에서 접두사 w1 w2를 따라 나오는 단어들 가운데서 임의로 w3을 선택한다.
    w3을 출력한다.
    w1과 w2를 W2와 w3으로 바꾼다.
    반복한다.
```

### [예시] 3장 앞머리 인용구 

Show your flowcharts and conceal your tables and I will be mystified. Show your tables and your flowcharts will be obvious. (end)

| 접두사          | 그 접두사에서 나오는 접미사 후보 단어들 |
|-----------------|-----------------------------------------|
| Show your       | flowcharts tables                       |
| your flowcharts | and will                                |
| flowcharts and  | conceal                                 |
| flowcharts will | be                                      |
| your tables     | and and                                 |
| will be         | mystified. obvious.                     |
| be mystified.   | Show                                    |
| be obvious.     | (end)                                   |


단어는 공백 기준으로 자른 것. 따옴표, 괄호, 마침표 등을 그대로 둔다.(words 와 words.는 다른 단어)
-> 문법이 단어를 선택하는 데 간접적인 영향을 주게 되어 출력 글의 품질을 높이는 데 도움이 된다.

### [예시]: 헤밍웨이 "The Sun Also Rises" 7장 인풋 했을 때 아웃풋의 일부

As I started up the undershirt onto his chest black, and big stomach muscles bulging under the light. "You see them?" Below the line where his ribs stopped were two raised welts. "See on the forehead." "Oh, Brett, I love you." "Let's not talk. Talking's all bilge. I'm going away tomorrow." "Tommorow?" "Yes, Didn't I say so? I am." "Let's have a drink, then."

속옷을 걷어 올리자 그의 가슴이 새까맣게 타들어가고 복부 근육이 불룩하게 튀어나왔다. "보여요?" 갈비뼈가 멈춘 선 아래에는 두 개의 융기된 상처가 있었습니다. "이마에 봐요." "오, 브렛, 사랑해." "말하지 말자. 대화는 다 쓸데없는 짓이야. 나 내일 떠날 거야." "내일?" "그래, 내가 말하지 않았어? 그래요." "그럼 한잔하러 가자."
