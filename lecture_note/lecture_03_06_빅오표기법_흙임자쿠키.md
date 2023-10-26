# c++

c++은 c의 상위 집합이다. 즉 c로 작성한것도 c++ 프로그램이라고 할 수 있다.

하지만 진정한 c++을 사용했다고 하기 위해선 아래 조건들이 필요하다.

- 자바와 같이 오브젝트들을 클래스로 정의한다.
    - 이를 통해 자바처럼 구현의 세부내용을 감출 수 있다.
- 표준 템플릿 라이브러리 STL을 사용한다.
    - 필요로 하는 대부분의 기능이 이미 내장되어있다.
    - C++ ISO 표준에는 STL도 언어 정의의 한부분으로 포함된다.

### STL

- 벡터, 리스트, 집합, 덱, 맵과 같은 컨테이너(자료구조)들과 검색, 정렬, 삽입, 삭제를 위한 기본 알고리즘 연산을 제공한다.
- 컨테이너들은 C++의 템플릿으로 표현되고, 특정 데이터 타입용으로 인스턴스화 된다.
    - vector 컨테이너가 있으면 vector<int>, vector<string>으로 사용이 되는 것.
- stl의 Map은 균형 트리 기반이며 O(logN)의 시간이 소요된다.

아래는 최초 접두사를 만들고 NONWORD를 끝에 추가한 후 출력을 생성하는 코드이다.

- cin은 c++ 표준입력이다.

```cpp
typedef deque<string> Prefix;
map<prefix, vector<string> statetab; // 접두사 -> 접미사

int main(void) {
	int nwords = MAXGEN;
	Prefix prefix;

	for (int i = 0; i < NPREF; i++) 
		add(prefix, NONWORD);
	build(prefix, cin);
	add(prefix, NONWORD);
	generate(nwords);
	return 0;
}

/*
buf 함수는 필요한 경우 알아서 크기가 커진다.
*/
void build(Prefix& prefix, istream& in) {
	string buf;

	while(in >> buff) add(prefix, buf);
}

// add: 접미사 리스트에 단어 추가, 접두사 갱신
void add(Prefix& prefix, const string& s) {
	if (prefix.size() == NPREF) {
		statetab[prefix].push_back(s);
		prefix.pop_front();
	}
	prefix.push_back(s);
}

void generate(int words) {
	Prefix prefix;
	int i;

	for (i = 0; i < NPREF; i++) {
		add(prefix, NONWORD);
	}

	for (i = 0; i < nwords; i++) {
		vector<string>& suf = statetab[prefix];
		const string& w = suf[rand() % suf.size()];
		if (w == NONWORD) break;
		cout << w << "\n";
		prefix.pop_front();
		prefix.push_back(w);
	}
}
```

위 함수들은 내부적으로 많은 역할을 하고있다.

- map 컨테이너는 ‘[]’ 연산자를 오버로드 해서 look up 함수처럼 검색 연산을 수행한다.
- statetab[prefix] 는 statetab에서 prefix를 키로 갖는 항목에 대한 레퍼런스를 반환한다. 이때 해당 vector가 존재하지 않는 경우에 새로 생성한다.
- push_back 함수는 새로운 항목을 끝부분에 추가한다.
- pop_front는 첫번째 항목을 얻어온다.

전반적으로 c++의 코드가 명료하고 우아해보인다. 코드도 간결하고 알고리즘의 이해도 쉽다. 하지만 그만큼 c에 비해 매우 느리다는 단점이 있다.