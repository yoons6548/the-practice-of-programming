## 7.7 요약

올바른 알고리즘을 선택하자. 
그 후 성능 최적화를 하자.
>성능 최적화란 보통 프로그램을 짜면서 제일 나중에 걱정할 만한 수준의 문제다. 그래도 최적화를 수행해야만 한다면 그 기본 절차는, **측정하고 조금의 변경으로 가장 큰 차이를 만들 수 있는 적은 부분에 집중하고, 변경한 내용이 옳은지를 검증하고, 그 다음에 다시 측정하기를 반복하는 것**이다. 그리고 가능한 한 빨리 그만두고 가장 단순한 그 버전을 시간과 정확성을 평가하는 기준선으로 유지한다.

어떤 프로그램의 속도나 공간 소모를 개선하려고 한다면, **벤치마크 테스트**와 문제를 만들어서 **스스로 성능을 추정**하고 추정을 기록하는 것도 좋은 방법이다. 자신이 하고 있는 작업에 이미 표준 벤치마크 테스트가 있다면, 그것도 같이 활용하라. 프로그램이 상대적으로 자급자족형일 때 취할 수 있는 접근방식으로 '전형적인' 입력군을 찾거나 만드는 방법이 있다. 물론 이것을 테스트 집합의 일부로 만들 수도 있다. 이 방법이 바로 컴파일러, 컴퓨터와 같은 학술 시스템이나 상용 시스템을 위한 벤치마크 집합의 기원이다. 

벤치 마킹은 6장에서 테스트에 추천한 방식과 똑같은 작업발판을 통해 다룰 수 있다. 시간 측정 테스트를 자동으로 실행하고, 출력에는 그 출력을 이해하고 재현할 수 있을만한 충분한 정보를 포함시키고, 결과를 계속 기록해서 변화 경향과 중대한 변경을 관찰할 수 있어야 한다.

## 더 읽어보기
- [Programming Pearls: Jon Bentley: 9788177588583: Amazon.com: Books](https://www.amazon.com/Programming-Pearls-Jon-Bentley/dp/8177588583)
- [More Programming Pearls: Confessions of a Coder: Confessions of a Coder: Bentley, Jon: 9780201118896: Amazon.com: Books](https://www.amazon.com/More-Programming-Pearls-Confessions-Coder/dp/0201118890)

- [C++ 최적화 - 예스24 (yes24.com)](https://www.yes24.com/Product/Goods/74971458)
- [동시성 프로그래밍 - 예스24 (yes24.com)](https://www.yes24.com/Product/Goods/108570426)

### BLAS
![](https://i.imgur.com/Ww8DW0W.png)
![](https://i.imgur.com/3PYc0hi.png)
- [GitHub - michael-lehn/ulmBLAS: ulmBLAS](https://github.com/michael-lehn/ulmBLAS?tab=readme-ov-file)

### Cache
![](https://i.imgur.com/c2KSUoQ.png)
### DMA
![](https://i.imgur.com/nrRXhjR.png)

### Pipelining
![](https://i.imgur.com/2vRpePt.png)

### Intrinsic code
![](https://i.imgur.com/1DYrPJa.png)

### DSP
#### H/W architecture
![](https://i.imgur.com/jl58Rpw.png)

#### Code Development Flow to Increase Performance
![](https://i.imgur.com/4Vb7SKj.png)

#### H/W supported layers
![](https://i.imgur.com/7MnV4hm.png)

### H/W accelerator
- DOF
- MSC
	- Scaler
- DSP
	- Vector Processing
- ARM
	- Neon