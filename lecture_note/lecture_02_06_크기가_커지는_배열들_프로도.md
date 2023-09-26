## 원소의 추가의 경우
- 정적배열에서 n개를 하나씩 삽입하는 것은 O(n^2) 연산 -> n 이 크면 피하는 것이 좋음. 
- 배열의 크기 변경은 일정 크기 (chunk) 단위로 이루어져야 하며, 배열과 배열 우지하는 정보 함께 다루는 것이 좋음.
- C++나 자바 쓰면 표준 라이브러리에 있는 클래스, C 를 쓴다면 struct 를 사용할 수도 있음.


```c
typedef struct Nameval nameval;
struct Nameval {
    char *name;
    int value;
};

struct NVtab {
    int nval; /* 현재 원소의 개수 */
    int max; /* 현재 할당된 공간의 크기 */
    Nameval *nameval; /* 이름-값 쌍의 배열 */
} nbtab;

enum { NVINIT = 1, NVGROW = 2};

/* addname: 새 이름(name)과 값(value)을 nvtab에 추가 */
int addname(Nameval newname)
{
    Nameval *nvp;

    if (vtab.nameval == NULL) { /* 처음 호출되었을 경우 */
        nvtab.nameval =
            (Nameval *) malloc(NVINIT * sizeof(Nameval));
        if (nvtab.nameval == NULL)
            return -1;
        nvtab.max = NVINIT;
        nvtab.nval = 0;
    } else if (nvtab.nval >= nvtab.max) { /* 크기를 늘려야 할 경우 */
        nvp = (Nameval *) realloc(nvtab.nameval,
           (NVGROW*nvtab.max) * sizeof(Nameval));
        if (nvp == NULL)
            return -1;
        nvtab.max *= NVGROW;
        nvtab.nameval = nvp;
    }
    nvtab.nameval[nvtab.nval] = newname;
    return nvtab.nval++;
}

```
Link: [cppreference : realloc][realloc_link]

[realloc_link]: https://en.cppreference.com/w/c/memory/realloc

a) expanding or contracting the existing area pointed to by ptr, if possible. The contents of the area remain unchanged up to the lesser of the new and old sizes. If the area is expanded, the contents of the new part of the array are undefined.

b) allocating a new memory block of size new_size bytes, copying memory area with size equal the lesser of the new and the old sizes, and freeing the old block.

(가능하면 현재 메모리 뒤에 붙여 확장하고, 안되면 새로 할당 후 몽땅 복사 함) 



## 잠시... 

C언어는 void* 를 자동으로 원하는 타입으로 형변환하기 때문에, realloc 의 리턴값을 형변환할 필요가 없다.
- void* 형태로 저장을 하려고 하여도 에러는 나지 않는다.
- 이와 관련하여, stack overflow 등에서는 void* 의 return type 을 가지는 malloc, realloc 에 대해서 casting 을 하지 않는것이 맞다고 한다.
- https://stackoverflow.com/questions/605845/do-i-cast-the-result-of-malloc
    - It is unnecessary, as void * is automatically and safely promoted to any other pointer type in this case.
    - It adds clutter to the code, casts are not very easy to read (especially if the pointer type is long).
    - It makes you repeat yourself, which is generally bad.
    - It can hide an error if you forgot to include <stdlib.h>. This can cause crashes (or, worse, not cause a crash until way later in some totally different part of the code). Consider what happens if pointers and integers are differently sized; then you're hiding a warning by casting and might lose bits of your returned address. Note: as of C99 implicit functions are gone from C, and this point is no longer relevant since there's no automatic assumption that undeclared functions return int. 


## 원소의 제거의 경우
- 원소를 제거할 때 생기는 빈 자리는 어떻게해야 할까?
    - 순서와 상관없다면 마지막 원소를가져다 빈자리 채움.
    - 순서를 유지해야 한다면, 빈 자리 이후의 원소들을 모두 한 자리씩 앞당긴다.
        - 차라리 이럴꺼면 linked list 를 쓰는게 더 좋겠다.
        - 아니면 map 을 쓰는 것도 방법이다.
        - 그래서 데이터 설계를 통하여 어떻게 데이터가 취급될 지에 따라서 자료구조를 택하는 것도 최고의 효율을 가지는 프로그래밍 방법 중 하나이다.
        - 그리고 왠만한 사람이 엉성하게 짜는 것 보다 STL 같은 것이 더 훨씬 메모리 관리가 잘 된다.
        - 자신 없으면 열심히 STL 을 바라보자.
        - https://en.cppreference.com/w/

```c
/* delname: nvtab에서 첫 번째로 일치하는 원소를 제거 */
int delname(char *name)
{
    int i;

    for (i = 0 ; i < nvtab.nval; i++)
        if(strcmp(nvtab.nameval[i].name, name) == 0) {
            memmove(nvtab.nameval+i, nvtab.nameval+i+1,
                (nvtab.nval-(i+1) * sizeof(Nameval));
            nvtab.nval--;
            return 1;
        }
    return 0;
}
```
reference. 
- void* memmove( void* dest, const void* src, std::size_t count );
- https://en.cppreference.com/w/cpp/string/byte/memmove

## 잠시 memmove .. 
- 보통 우리가 아는 것은 memcpy 이다.
- memmove 는 왜 쓰는것일까? 왜 다른 것일까?
- memcpy 는....
    - 메모리 번지 기반으로 동작
    - 범위가 벗어나거나 잘못된 동작에 대해서 보장하지 않는다.
    - 대신 빠르다.
- memmove 는...
    - 배열이 valid 한 것에 대해서만 동작 한다. 그래서 안전하다. (물론 dest나 src 에 NULL 등이 들어가거나 하면 알수 없는 난리 발생 가능. ) 
    - memcpy 보다는 느리다.
- 예시. : https://stackoverflow.com/a/4416021
    - memcpy 는 dummy 에 대해서 잘못 복사하는 오류가 발생할 수 있으나, memmove 는 배열 크기 만큼만 동작 하였음.
- 그래서, 가능하면 memmove 를 쓰란다. 

## 연습문제 2-5
- 앞에서 만든 코드에서 delname 은 원소를 삭제하여 남은 메모리를 realloc을 통해 돌려주지 않는다.
- 남은 메모리를 돌려주는 것이 더 나은 설계인지 판단하라.
- 또, 그 판단 근거도 설명해 보라.



```c
struct Nameval {
    char *name;
    int value;
};
///////
    Nameval *nameval; /* 이름-값 쌍의 배열 */
```

a) nameval 은 반환하지 않는 것이 더 좋을 것 같다. 
- nameval 의 경우 할당의 크기를 줄이는 것이 overhead 가 더 커 보인다. 
    - pointer 는 nameval 로 100 개가 있을 경우, nameval 이라는 것 하나로 할당이 되어 있지, 100 개 개별로 할당이 되어 있지 않다.
    - 100 개인 배열 크기를 99 개로 줄일 경우, realloc 을 하거나, free, malloc 을 해야 하는 것인데, 경우에 따라서 memory 연산이 긴 시간을 소모하여 성능을 떨어뜨릴 수도 있다.
    - 또한, 1개의 크기 만큼 줄인다고 하더라도, 그 공간이 꼭 효율적으로 사용되리라는 보장이 없다.
        -  memory 의 경우 효율적인 접근을 위하여 단위 크기로 크기가 할당이 된다.
            -  이른바 word size. 32bit machine 의 경우 32bit, 64bit 의 경우 64bit
        -  즉 반환된 fragment 가 다음 할당 될 때 꼭 재사용 된다는 보장이 없다. 그럴 경우, 이후 사용되는 자원을 이용하여 이전에 불필요한 자원의 위치에 덮어 써버리는 것도 방법일 수 있다.
-  max length 를 관리 하며, memmove 를 이용하여 빠진 배열의 크기 만큼 당겨 쓰고, 나머지 부분은 max length 내에서 새로 삽입이 필요할때 그냥 쓰면 되겠다. 

b) 하지만 name 의 경우에는 free 해주는 것이 좋아 보인다. (memory leak 유발 가능성) 
- Nameval 의 name 은 char* 이다. char 의 pointer 이며, 이는 실제 값은 heap 영역에 new 된 값이다.
- new 된 값을 처리하지 않고 pointer 만 다른 것으로 덮어쓰게 되면 실제 할당된 영역은 heap 영역에서 계속 메모리를 소유하게 된다.
- 이 때문에 장기적으로 반복동작 되면 name 의 크기 만큼 memory leak 이 유발될 수 있다. 


## 연습문제 2-6
- 삭제되었다는 것을 표시하도록 addname 과 delname을 고쳐보라.
- 프로그램의 나머지 부분이 이 변화에서 얼마나 영향을 받을 것인지도 생각해 보라.


```c
/* addname: 새 이름(name)과 값(value)을 nvtab에 추가 */
int addname(Nameval newname)
{
    Nameval *nvp;

    if (vtab.nameval == NULL) { /* 처음 호출되었을 경우 */
        nvtab.nameval =
            (Nameval *) malloc(NVINIT * sizeof(Nameval));
        if (nvtab.nameval == NULL)
            return -1;
        nvtab.max = NVINIT;
        nvtab.nval = 0;
    } else if (nvtab.nval >= nvtab.max) { /* 크기를 늘려야 할 경우 */
        nvp = (Nameval *) realloc(nvtab.nameval,
           (NVGROW*nvtab.max) * sizeof(Nameval));
        if (nvp == NULL)
            return -1;
        nvtab.max *= NVGROW;
        nvtab.nameval = nvp;
    }
    nvtab.nameval[nvtab.nval] = newname;
    return nvtab.nval++;
}

/* delname: nvtab에서 첫 번째로 일치하는 원소를 제거 */
int delname(char *name)
{
    int i;

    for (i = 0 ; i < nvtab.nval; i++)
        if(strcmp(nvtab.nameval[i].name, name) == 0) {
            memmove(nvtab.nameval+i, nvtab.nameval+i+1,
                (nvtab.nval-(i+1) * sizeof(Nameval));
            nvtab.nval--;
            return 1;
        }
    return 0;
}
```

- 사실 이게 최선일까?
    - 삭제할때 잘 삭제하는게 최선이지 않을까?
        - 이렇게 삭제가 빈번하게 발생할 수 있는 것이면, 차라리 linked list 등 삭제해도 그냥 다음꺼 이어붙여도 되는 자료구조로 하는게 맘 편하다. 
    - 굳이 더하기, 삭제에 빈칸을 마커하려면?
        - 삭제할 때 삭제하는 칸에 빈칸임을 마커하고, 그 칸에 더 하기. 
        - 혹은 삭제된 배열을 따로 관리해서 사용하는 것도 방법.. (어쩌피 삭제될 것은 sparse 하게 배포될 것이다. ) 
     
- 인터넷에서 찾은 어느 답변..
```c
int delname(char *name)
{
	int i;
	for (i=0; i < nvtab.nval; i++)
		if (strcmp(nvtab.nameval[i].name, name) == 0){
			nvtab.nameval[i].name=(char*)(NULL); ////////////////////  삭제할 때 name 에 NULL 을 마커한다. 
			nvtab.nval--;
			return 1;
		}
		return 0;
}
```

```c
int addname(Nameval newname)
{
	Nameval *nvp;
	int addpos=0;		/* position where the new element will be addded */
	int emptyslot=FALSE;	/* flag to indicate whether an empty slot is available */
		
	if (nvtab.nameval == NULL){				/* first time */
		nvtab.nameval=(Nameval*) malloc(NVINIT * sizeof(Nameval));
		if (nvtab.nameval == NULL)
			return -1;
		nvtab.max=NVINIT;
		nvtab.nval=1;
		addpos=0;
	}
	else{								
		for (int i=0; i < nvtab.max; i++)	/* look for an empty slot */
			if (strcmp(nvtab.nameval[i].name, (char*)(NULL)) == 0)  ////////////////// 삭제된 칸이 있는지 보고, 
			{
				addpos=i;                                           //////////////////  삭제된 칸은 addpos 로 표시. 
				emptyslot=TRUE;
				break;
			}
		if (!emptyslot)			/* no empty slot found, must grow the array */
		{
			addpos=nvtab.max;                                       /////////////////   삭제된 것이 없으면 max 가 addpos 가 됨. 
			nvp = (Nameval*) realloc(nvtab.nameval,(NVGROW * nvtab.max) * sizeof(Nameval));
			if (nvp == NULL)
					return -1;
			nvtab.max *= NVGROW;
			nvtab.nameval = nvp;
		}
	}
	nvtab.nameval[addpos] = newname;                           ////////////////////////  삭제된 위치 or max 위치에 원소 추가. 
	return nvtab.nval++;
}
```

- 인터넷에서 찾은 답에 대한 고찰
    - for (int i=0; i < nvtab.max; i++)	/* look for an empty slot */
        - 빈칸을 찾을때 최대 max 만큼의 시간 ( O(n) ) 이 발생한다.
        - 삭제된 position을 queue 에 넣고, queue 의 length 가 0 이면 삭제된 것이 없는 것
        - queue length 가 1 이상이면 queue 의 처음 원소를 pop 해서 해당 position 에 바로 write 하면 훨씬 간단하다.
            - 대신 queue 만큼의 추가 메모리 소요가 발생한다.
        - max 까지 검색할 필요가 있을까?
            - nvtab.nval 까지만 검색하면 된다. 현재 원소의 갯수까지 공백이 없으면, nval 이 끝이다.
                - 원소가 5개가 있을 경우, (nval = 5) 5 까지 공백이 없으면 5가 끝이다. 공백이 있으면 6, 7 번째 까지 갈 수 있겠지만, 5까지 중에 꼭 나온다.
            - nval 이후에 max 까지 가버릴 경우, 죽지는 않겠지만,
            - calloc 으로 0 으로 초기화 된 영역에 접근하는 것도 아니고, malloc 으로 어떤 값이 된지 모를 쓰래기 값이 있는 영역에 접근하는 것은 권장하고 싶지 않다. 
