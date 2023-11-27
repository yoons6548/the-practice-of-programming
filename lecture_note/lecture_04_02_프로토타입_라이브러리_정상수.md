# 4.2 프로토타입 라이브러리

## 😋“한 번은 버릴 마음을 먹어라. 어차피 그렇게 될 것이므로.” - 프레드 브룩스

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/a4be28c5-f296-4fce-a873-9a3feb72937d/a250012d-0b44-4db4-9fd0-22f270519306/Untitled.png)

---

## 한번 쓰고 버릴 임시버전(프로토타입)을 먼저 만들어 본다.

## 예시) csvgetline의 프로토타입을 만든다

![스크린샷 2023-11-14 오후 9.43.39.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/a4be28c5-f296-4fce-a873-9a3feb72937d/3bd01fdf-df4d-4365-8e94-8b225810a0cf/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-11-14_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.43.39.png)

동작

1. csv 한 줄을 파일에서 버퍼로 읽고 
2. 각 필드를 분리하여 배열에 저장하고 ‘,’를 없애고
3. 필드 개수를 리턴

```c
char buf [ZOO] ; /a input line buffer a/
char afield[20] ; /a fie1 ds a/
/a csvgetline: read and parse line, return field count a/
/a sample input: "LU",86.25,"11/4/1998","2:19PM",+4.0625 a/
i nt csvgetl i ne(F1LE *fin)
{
	int nfield;
	char *p, aq;

	if (fgets(buf. sizeof (buf) , fin) == NULL)
	return -1;

	nfield = 0;

	for (q = buf; (p=strtok(q, ",\n\rW)) != NULL; q = NULL)
	field [nfiel d++] = unquote(p) :
	return nfield;
}
```

---

## 범용 라이브러리가 되기 위해 고려해야될 것들

1. 입력이 아주 길거나, 필드가 많은 경우
2. 각 필드를 ‘,’로 구분하는데 필드 내용에 ‘,’가 들어간 경우에 대비하지 않음
3. 한 줄을 처리하고 다음 줄로 넘어갈때 이전 데이터를 복사하지 않음
4. 함수를 호출하는 쪽에서 파일을 명시적으로 열고 닫아야 한다. 열린 파일만 읽을 수 있음

---

## 요약

1. 프로토타입을 먼저 만든다
2. 테스트 코드로 여러 케이스를 테스트 해보고 실제 사용도 해보면서 

제대로 설계할수 있을 만큼 관련 쟁점들을 이해한다
