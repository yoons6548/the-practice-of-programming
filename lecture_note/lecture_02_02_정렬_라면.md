## 정렬

<aside>
💡 ‘퀵소트(quicksort)’는 어떤 상황에서도 무난한 최고의 정렬 알고리즘 가운데 하나다.

</aside>

이진 검색은 원소가 정렬되어 있을 때만 쓸 수 있다. 데이터 집합이 미리 정해져 있지 않다면, 프로그램 실행 중에 정렬해야 한다. 퀵소트의 정렬 방식은 다음과 같다.

```
배열의 한 원소를 선택한다. (이 원소가 '기준(pivot)'이다.)
다른 원소들을 두 집단으로 분할한다.
		기준보다 작은 값을 갖는 집단
		기준보다 크거나 같은 값을 갖는 집단
각 집단을 이런 식으로 반복해서 정렬한다.
```

퀵소트가 빠른 이유는 일단 어느 원소가 기준 원소의 값보다 작다고 판명되면, 그 원소를 기준 값보다 큰 원소들과 비교하지 않아도 되기 때문이다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/6ad5719f-ad1f-4498-b6dd-57e3e17a4074/ccc6f5b2-0c41-4f39-b1bb-b8de4c0387cf/Untitled.png)

이 때문에 퀵소트가 각 원소를 다른 모든 원소들과 직접 비교하는 삽입 정렬이나 버블 정렬 같은 간단한 정렬 방법보다 훨씬 빠른 것이다.

**quicksort 구현**

```c
void quicksort(int v[], int n)
{
  int i, last;

  if(n<=1)
  {
    return;
  }
  swap(v, 0, rand()%n);
  last = 0;
  for(i=1; i<n; i++)
    if(v[i]<v[0])
    {
      swap(v, ++last, i);
    }
  swap(v, 0, last);
  quicksort(v, last);
  quicksort(v+last+1, n-last-1);
}
```

**swap 구현**

```c
void swap(int v[], int i, int j)
{
  int temp;

  temp = v[i];
  v[i] = v[j];
  v[j] = temp;
}
```

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/6ad5719f-ad1f-4498-b6dd-57e3e17a4074/8ce3975e-32fe-4b95-b7ac-3274f12bd20e/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/6ad5719f-ad1f-4498-b6dd-57e3e17a4074/9d7255c6-7236-4fa2-b6d5-ce2e45e0e960/Untitled.png)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/6ad5719f-ad1f-4498-b6dd-57e3e17a4074/2c0aa721-fe2f-4afe-9a81-ebb7e0c141bd/Untitled.png)

이상적인 경우 n * logn에 비례한다. 평균적으로 이것보다 약간의 시간이 더 걸린다.

**이상적인 상황**

- 첫 번째 분할 단계에서 n개의 원소가 각각 대략 n/2개의 원소를 가지는 두 부분으로 나뉜다.
- 두 번째 분할 단계에서는 두  n/2개 부분이 각각 n/4개의 원소를 가지는 두 부분으로 나뉘어 n/4 부분이 네 개가 된다.
- 이런식으로 반복 된다.

n + 2 * n/2 + 4 * n/4 + 8 * n/8 … (이 식의 항 개수는 logn개)

퀵소트의 약점은 기준을 선택할 때마다 균등하게 분할되지 않는 경우가 빈번하게 발생하면 n^2에 가까운 실행시간이 된다는 점이다. 입력 데이터의 모든 원소 값이 동일한 경우 분할할 때마다 2 집합 중 하나에는 오직 한 원소만 들어가고 이 경우 실행 시간은 n^2에 비례한다. 그러나 좀 더 신경써서 정교하게 만든다면 이 상황이 거의 발생하지 않을 것이다.

## 추가 자료

[카운팅 정렬(Counting Sort, 계수 정렬) 알고리즘](https://8iggy.tistory.com/123)