# Week 3. 이항분포의 베이지안 추론

> 핵심 질문: 가장 단순한 모형(성공/실패)에서 베이지안 추론의 전체 흐름(사후분포 → 점추정 → 구간 → 예측 → 두 집단 비교)을 끝까지 따라가 보기

## 1. 모형과 켤레 사전분포

$$
X_1,\dots,X_n\mid\theta\overset{iid}{\sim}Ber(\theta),\qquad\theta\sim Beta(a,b),\qquad y=\sum_{i=1}^n x_i
$$

**사후분포 유도 (커널만 보기)**

$$
\pi(\theta\mid x)\propto f(x\mid\theta)\pi(\theta)\propto\theta^{y}(1-\theta)^{n-y}\cdot\theta^{a-1}(1-\theta)^{b-1}=\theta^{y+a-1}(1-\theta)^{n-y+b-1}
$$

이건 $Beta(y+a,\ n-y+b)$ pdf의 커널이므로

$$
\theta\mid x\sim Beta(y+a,\ n-y+b)
$$

분모 $f(x)$를 감마함수로 끝까지 계산해도 같은 결과가 나오지만, 실제로는 **비례하는 부분이 어떤 분포의 모양인지 알아보는 것**이 핵심이다.

### 켤레 사전분포 (Conjugate prior)

사전분포와 사후분포가 같은 분포족에 속하면 그 사전분포를 켤레라고 한다. 이항 가능도에는 베타가 켤레다.

- **장점**: 사후분포를 바로 쓸 수 있고, 새 데이터가 들어오면 사후분포를 처음부터 다시 계산할 필요 없이 파라미터만 갱신하면 된다. 예: $Beta(a,b)$ → 데이터1 → $Beta(a+y_1, b+n_1-y_1)$ → 데이터2 → $Beta(a+y_1+y_2,\ \dots)$
- **단점**: 사전분포의 형태가 제한된다. 더 유연한 분포를 쓰고 싶어도 못 쓴다.

**예**: $n=40$, $y=15$ ($y/n=0.375$), $Beta(1,1)$ 사전분포 → 사후 $Beta(16,26)$. 평평했던 사전분포가 0.375 근처로 뾰족하게 모인다. 표본이 커질수록 더 뾰족해진다.

## 2. 몬테카를로 (Monte Carlo)

로그오즈 $\eta=\log\frac{\theta}{1-\theta}$처럼 θ의 함수에 관심이 있으면, η의 사후분포를 직접 유도하기 번거롭다.

**아이디어**: 기댓값을 표본평균으로 근사한다.

$$
E\{h(\theta)\}=\int h(\theta)f(\theta)d\theta\approx\frac1N\sum_{i=1}^N h(\theta_i),\qquad\theta_i\overset{iid}{\sim}f
$$

$N$이 클수록 정확해진다 (대수의 법칙).

**사후분포에 적용**: $\theta^{(1)},\dots,\theta^{(N)}\sim\pi(\theta\mid x)$를 뽑고 각각을 $\eta^{(i)}=\log\frac{\theta^{(i)}}{1-\theta^{(i)}}$로 변환하면, 그 $\eta^{(i)}$들이 곧 η의 사후분포 표본이다. 변환이 아무리 복잡해도 "뽑고 → 변환"만 하면 된다.

1차원에선 별 필요 없어 보이지만, 모형이 복잡해지면 사후분포를 요약하는 거의 유일한 방법이 된다.

## 3. 점추정: 사후평균은 가중평균이다

$Beta(y+a, n-y+b)$의 평균을 정리하면

$$
\hat\theta=E(\theta\mid y)=\frac{y+a}{a+b+n}
=\underbrace{\frac{n}{a+b+n}}_{\text{데이터 가중치}}\cdot\underbrace{\frac yn}_{\text{표본평균}}
+\underbrace{\frac{a+b}{a+b+n}}_{\text{사전 가중치}}\cdot\underbrace{\frac{a}{a+b}}_{\text{사전평균}}
$$

- 베이즈 추정량은 **빈도론 추정량(표본평균)과 사전평균의 가중평균**이다.
- $n\to\infty$이면 데이터 쪽 가중치가 1로 가서 $\hat\theta\approx y/n$. 표본이 커지면 가능도가 사전분포를 압도한다.

**초모수 해석**

- $a+b$: **사전 표본 크기 (prior sample size)**. 사전분포가 데이터 몇 개 분량의 확신을 갖고 있는지.
- $a/(a+b)$: θ에 대한 사전 추측값.
- 예: "θ가 0.2쯤일 것 같고, 표본 10개 정도의 확신이 있다" → $a+b=10$, $a/(a+b)=0.2$ → $Beta(2,8)$.

## 4. 예측

### 다음 한 번의 결과

$$
P(X_{n+1}=1\mid x_1,\dots,x_n)=\int_0^1\theta\,\pi(\theta\mid x)\,d\theta=E(\theta\mid x)=\frac{a+\sum x_i}{a+b+n}
$$

θ가 주어지면 $X_{n+1}$은 과거 데이터와 조건부 독립이라는 점을 썼다.

### 앞으로 m번 중 성공 횟수 $Z=X_{n+1}+\dots+X_{n+m}$

$$
P(Z=z\mid x)=\int_0^1\binom mz\theta^z(1-\theta)^{m-z}\pi(\theta\mid x)\,d\theta
$$

이건 **베타-이항 분포**가 된다.

**예**: $n=40$, $y=15$, $Beta(1,1)$ 사전분포에서 다음 10번($m=10$) 예측

| | 예측 평균 | 예측 분산 |
|---|---|---|
| 빈도론 (θ̂ = 0.375 대입한 이항분포) | 3.75 | 2.344 |
| 베이지안 (베타-이항) | 3.810 | 2.852 |

베이지안 예측분산이 더 크다. 빈도론은 θ를 0.375로 **확정된 값처럼** 대입하지만, 베이지안은 θ 자체의 불확실성까지 예측에 반영하기 때문이다.

> 직접 계산해 보니 베타-이항 분산의 정확한 값은 $\frac{m\,\alpha\beta(\alpha+\beta+m)}{(\alpha+\beta)^2(\alpha+\beta+1)}$ ($\alpha=16,\beta=26$) $\approx 2.852$였다. 몬테카를로로 근사하면 이 근처에서 조금씩 흔들린다.

### 몬테카를로로 예측분포 구하기

1. **방법 1 (기댓값 근사)**: $\theta_i\sim\pi(\theta\mid x)$를 뽑아서 $\hat f(z\mid x)=\frac1N\sum_i\binom mz\theta_i^z(1-\theta_i)^{m-z}$
2. **방법 2 (합성 표본)**: $\theta_i\sim\pi(\theta\mid x)$를 뽑고, 그 θ로 $z_i\sim B(m,\theta_i)$를 뽑는다. $\{z_i\}$만 모으면 그 자체가 예측분포의 표본이다.

방법 2가 더 직관적이고, 예측분포를 손으로 못 구하는 복잡한 모형에서도 그대로 쓸 수 있다.

## 5. 신용구간 vs 신뢰구간

$X\mid\theta\sim B(10,\theta)$, $\theta\sim Unif(0,1)$, $x=2$ → $\theta\mid x\sim Beta(3,9)$

| 구간 | 결과 |
|---|---|
| 95% HPD (베이지안) | 약 [0.041, 0.484] |
| 95% 정확 신뢰구간 (Clopper-Pearson) | 약 [0.025, 0.556] |

베이지안 구간이 더 짧다. $Beta(1,1)$ 사전분포가 사전 표본 크기 2만큼의 정보를 더해준 효과로 볼 수 있다.

## 6. 두 비율 비교

$$
Y_i\mid\theta_1\overset{iid}{\sim}Ber(\theta_1),\quad Z_j\mid\theta_2\overset{iid}{\sim}Ber(\theta_2),\quad\theta_k\overset{ind}{\sim}Beta(a_k,b_k)
$$

두 그룹이 독립이므로 사후분포도 각각 따로 구하면 된다.

$$
\theta_1\mid x_1\sim Beta(a_1+x_1,\ b_1+n_1-x_1),\qquad\theta_2\mid x_2\sim Beta(a_2+x_2,\ b_2+n_2-x_2)
$$

**왜 차이 $\theta_1-\theta_2$ 대신 로그오즈비를 쓰나?**
(0.01, 0.001)과 (0.8, 0.809)는 차이가 둘 다 0.009지만 의미가 전혀 다르다. 앞쪽은 10배 차이, 뒤쪽은 거의 같다. 그래서 비율의 스케일을 고려하는 로그오즈비를 쓴다.

$$
\xi=\log\left(\frac{\theta_1/(1-\theta_1)}{\theta_2/(1-\theta_2)}\right)
$$

ξ의 사후분포는 몬테카를로로: $\theta_1, \theta_2$를 각각 사후분포에서 뽑고 ξ로 변환.

**예**: 강의 A 수강생 18명 중 12명, 강의 B 수강생 10명 중 8명이 "도움이 됐다"고 답함. $Beta(1,1)$ 사전분포.

- $\theta_1\mid x\sim Beta(13,7)$, $\theta_2\mid x\sim Beta(9,3)$
- ξ의 95% HPD ≈ [−2.3, 1.1] → **0을 포함하므로 두 그룹의 비율에 뚜렷한 차이가 있다고 보기 어렵다.**
- 덤으로 $P(\theta_1<\theta_2\mid x)\approx0.74$도 바로 구할 수 있다. 이런 "확률로 바로 답하기"가 베이지안의 장점.

(수치는 시뮬레이션 시드에 따라 소수점 둘째 자리 정도에서 조금씩 달라진다.)

## 한 줄 정리

> 베타-이항 켤레 구조 덕분에 사후분포는 "사전 성공/실패 개수 + 관측 성공/실패 개수"로 바로 나온다. 그 뒤의 복잡한 질문(로그오즈, 예측, 두 집단 비교)은 전부 사후표본을 뽑아서 변환하면 해결된다.
