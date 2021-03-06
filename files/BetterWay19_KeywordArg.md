## Better Way 19. 키워드 인수로 선택적인 동작을 제공하자

#### 75쪽

* Created : 2016/10/08
* Modified: 2019/05/16  


<br>

## 1. 함수 호출에서의 키워드 인자의 사용


18장에서 확인했다시피 파이썬에서도 다른 프로그래밍 언어와 마찬가지로 함수를 호출할 때 인자를 위치로(Positional arguments) 전달할 수 있다.

```python
# 정수를 다른 정수로 나눈 나머지를 반환하는 함수
def remainder(n, d):
    return n % d


>>> print(remainder(20, 7))

6
```

**위치 인자는 함수 호출 시에 단순히 값만 전달했지만, 인자를 이름과 값 쌍으로 전달하는 키워드 방식도 있다.(Keyword arguments)** 인자의 이름은 함수 정의 시에 정의된 인자 이름을 그대로 사용해야 하고 위치 인자와 키워드 인자를 섞어 쓰는 것도 얼마든지 가능하다.


```python
remainder(20, 7)
remainder(20, d=7)
remainder(n=20, d=7)
remainder(d=7, n=20) # 인자를 모두 키워드로 쓰면 순서를 바꿔도 문제없이 동작한다.
```

만약 위치 인자와 키워드 인자를 섞어쓴다면 **파이썬 문법상 위치 인자는 무조건 키워드 인자 앞에 위치해야 한다.**


```python
remainder(n=20, 7)

SyntaxError: positional argument follows keyword argument
```

또한 각 인자는 한 번만 지정할 수 있다.


```python
remainder(20, n=7)

TypeError: remainder() got multiple values for argument 'n'
```

정의상 함수의 첫 인자(20)는 _n_ 인데 뒤에 키워드 인자로 _n_ 을 한 번 더 받았기 때문에 에러가 출력됐다.  

<br>


## 2. 키워드 인자 사용의 장점

함수 호출 시에 위치 인자를 사용하는 것보다 키워드 인자를 사용하는 것이 비용이 더 많이 든다. 파일의 크기가 커질 것이고, 인자를 입력하는 데 따르는 시간과 개발자의 에너지 비용이 더 소모될 것은 말할 것도 없다. 그럼에도 키워드 인자를 사용하는 데는 몇 가지 장점이 있다.

* **코드를 처음 보는 사람이 함수 호출을 명확하게 이해할 수 있다**

이는 꽤나 명확하다. 협업하게 된 사람이 우리가 만든 기가 막힌 _remainder_ 함수를 보게 됐다고 하자. _remainder(20, 7)_ 이라고 적혀 있는 것보다는 _remainder(n=20, d=7)_ 라고 적혀 있는 것이 이해하기 훨씬 쉽다. 만약 가독성을 좀더 증대시켜야 한다면 함수 정의와 호출에서 인자 이름을 보다 구체적으로 바꿔 _remainder(number=20, divisor=7)_ 처럼 사용한다면 제 3자 입장에서 이해하기 더 쉬울 것이다.

<br>

* **함수를 정의할 때 기본값을 설정할 수 있다**

한 기능이 여러 가지 행동 옵션을 제공하도록 정의할 수 있는데 이런 경우 대부분 사용자들이 원하는 행동 옵션은 정해져있기 마련이다. 이때는 지분이 큰 옵션을 기본 행동으로 정해놓고 사용자가 다른 옵션을 원할 때만 추가로 지정하는 것이 바람직할 것이다.

예를 통해 살펴보자. 큰 통에 들어가는 액체의 유속을 계산하고 싶다고 하자. 큰 통의 무게를 잴 수 있다면, 각기 다른 시각에 측정한 두 무게의 차이를 이용해 유속을 알 수 있다.


```python
def flow_rate(weight_diff, time_diff):
    return weight_diff / time_diff

weight_diff = .5
time_diff = 3
flow = flow_rate(weight_diff, time_diff)

>>> print('%.3f kg per second' % flow)

0.167 kg per second
```

이런 문제는 항상 '단위'에 대한 고민을 해야 하는데 일단 시간 단위는 초 단위였다고 하자. 하지만 때로는 시간이나 날짜처럼 더 큰 단위로 계산하고 싶을 수 있다. **다시 말해 시간 단위의 기본값은 초 단위이고 이 단위가 가장 많이 쓰이지만 때로는 사용자의 수요에 따라 다른 단위 옵션을 제공하는 것이 필요할 수 있다는 뜻.**

함수 정의에서 기간 환산 계수를 추가하면 이런 동작을 쉽게 제공할 수 있다.

```python
def flow_rate(weight_diff, time_diff, period): # period는 초 단위
    return weight_diff / time_diff * period
```

뭐 무난하게 동작한다. **하지만 이 방법의 단점은 함수를 호출할 때마다, 심지어 초당 유속을 사용하는 일반적인 경우(_period_ 가 1)에도 기간을 설정해야 한다는 점이다.**

```python
flow = flow_rate(weight_diff, time_diff, 1)
```

이런 상황을 피하기 위해 **함수 정의에서 키워드 인자를 통해 기본값을 설정해주면 좋다.**

```python
def flow_rate(weight_diff, time_diff, period=1): # period는 초 단위
    return weight_diff / time_diff * period


flow_rate(weight_diff, time_diff)  # 1.
flow_rate(weight_diff, time_diff, period=3600) # 2.
```

첫 번째 예제에서는 _period_ 인자를 주지 않았기 때문에 기본값(1)을 쓴다는 의미가 되고, 두 번째는 사용자가 일반적이지 않은 시간 단위의 계산을 원하기 때문에 인자를 명시적으로 입력해주었다. 이제 _period_ 는 선택적인 인자가 되었으며 함수 사용이 더 유연해졌다.


<br>

* **기존 호출 코드와 호환성을 유지하면서 파라미터를 확장할 수 있다**

앞선 18장에서 위치 인자만 사용할 때는 함수를 확장하면 기존 호출 코드가 비정상적으로 작동할 수 있다는 점을 살펴보았다. 하지만 **키워드 인자를 사용하면 기존 코드와 호환성을 유지하며 함수의 파라미터(인자)를 확장할 수 있다.** 이 방법을 쓰면 코드를 많이 수정하지 않고도 추가적인 기능을 제공할 수 있고, 디버깅하기 힘든 버그가 생길 가능성을 줄일 수 있다.

이전 함수를 한 단계 더 확장해서 이제는 무게 단위도 기존 킬로그램 단위가 아닌 다른 무게 단위로도 유속을 계산한다고 하자. 함수를 확장해서 원하는 측정 단위와 킬로그램의 변환 비율을 선택 파라미터로 추가해야 할 것이다.


```python
def flow_rate(weight_diff, time_diff,
              period=1, units_per_kg=1):
    return weight_diff / units_per_kg / time_diff * period
```

_units\_per\_kg_ 인수의 기본값은 1로 단위를 kg로 쓴다는 의미가 된다. 킬로그램을 무게 단위의 기본 도량형으로 쓰는 한국인 입장에서 적절한 조치라고 할 수 있다. 이렇게 함수를 확장했을 때 장점은 **이 함수 이전에 실행했던 모든 함수 호출이 문제없이 실행됐다는 것이다.**  

물론 이 방법에도 단점은 있는데, **기간이나 무게 단위와 같은 선택적 키워드 인자를 여전히 위치 인자로도 넘길 수 있다는 점이다.**

```python
pounds_rate(flow_rate(wd, td, 3600, 2.2))
```

이런 호출은 3600, 2.2 같은 단순 숫자가 의미하는 바가 명확하지 않아 문맥을 이해하지 못하고 있는 리뷰어들에게는 혼동을 일으킬 수 있다. 이런 경우를 대비하기 위해서는 **항상 키워드 이름으로 선택적인 인수를 지정하고 위치 인수로는 아예 넘기지 않는 것이 바람직하다.**


<br>

## 3. 핵심 정리

* 함수의 인자는 위치뿐만 아니라 키워드로도 지정할 수 있다.
* 위치 인자만으로는 이해하기 어려울 때 키워드 인자를 쓰면 각 인자를 사용하는 목적이 분명해진다.
* 키워드 인자에 기본값을 지정하면 함수에 새 동작을 쉽게 추가할 수 있다. 특히 함수를 호출하는 기존 코드가 있을 때 사용하면 좋다.
* 선택적인 키워드 인자는 항상 위치가 아닌 키워드로 넘기는 것이 좋다.
