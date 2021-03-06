# 1주차 문제 풀이
## 메뉴 리뉴얼

하나의 `order` 에 대해서 조합을 구한뒤 해당 조합의 개수를 세서 가장 큰 값을 구하면 되는 문제.

1. 먼저 order로 입력되는 배열을 문자열 순서대로 정렬한다. 조합의 결과에서 `AB` 와 `BA` 는 같은 코스 메뉴인데, 이를 다르게 인식할 수 있으므로 처음부터 order의 원소들을 문자열 순서대로 정렬하면 이러한 경우를 방지할 수 있다. 문제의 마지막 테스트 케이스를 참고하자.

2. 정렬된 `order` 값의 원소 하나에 대해서 `course` 하나의 원소 길이에 해당하는 조합을 구한다. 예를 들면 아래와 같다.

```javascript
const order = ['XYZ', 'WXY', 'AWX'];
const course = 2

console.log(combination(order, course));
/*
[
  ['XY', 'XZ', 'YZ'],
  ['WX', 'WY', 'XY'],
  ['AW', 'AX', 'WX']
]
*/
```
3. 2차원 배열 형태로 구해진 값을 javascript의 `flat` 메소드를 이용해서 1차원으로 바꿀 수 있다.
```javascript
console.log(combination(order, course).flat());
// ['XY', 'XZ', 'YZ', 'WX', 'WY', 'XY', 'AW', 'AX', 'WX']
```
4. 이를 key-value 쌍의 객체로 바꿔 하나의 조합에 대한 개수를 counting 한다.
```javascript
const result = combination(order, course).flat();
//['XY', 'XZ', 'YZ', 'WX', 'WY', 'XY', 'AW', 'AX', 'WX']

const obj = countOrders(result)
/*
{
  XY: 2,
  XZ: 1,
  YZ: 1,
  WX: 2,
  WY: 1,
  AW: 1,
  AX: 1, 
}
*/
```
5. 객체에서 가장 큰 value값을 구한 뒤 이 값과 일치하는 다른 key값도 뽑아서 배열로 만든다. 문제에서는 최소 2명 이상의 손님으로부터 주문된 단품 메뉴 조합에 대해서만 포함시키기로 했으므로 이를 조건에 추가한다.

6. 이 과정을 order의 모든 원소에 대해서 반복하여 답을 구한다.

---

## 풍선 터트리기
문제의 조건을 보면 *번호가 더 작은 풍선을 터트리는 행위는 최대 1번만 할 수 있고, 그 이후로는 번호가 더 큰 풍선만 터트릴 수 있다* 라고 한다. 

즉, 숫자가 들어있는 배열의 어떤 한 위치를 정했을 때, 자신의 위치 양 옆으로 **자신보다 더 작은 풍선이 양쪽에 하나씩 있으면 혼자 남을 수 없다.** 왜냐하면 번호가 더 작은 풍선을 터트리는 행위는 최대 1번이기 때문이다. 

그렇다면 현재 위치에서 자신보다 더 작은 풍선을 미리 제거할 수 있는 입력이 주어지면 가능하지 않냐고 생각할 수 있다. 다음의 예를 보자
```
[x, -1, 10, y, -2]
``` 
여기서 숫자 `10`은 왼쪽과 오른쪽에 자신보다 작은 숫자인 `-1, -2`가 존재한다. 그러면 `-1, -2`가 방해되지 않도록 미리 제거할 수 있는 `x, y` 값을 생각해보자.

`-1`을 터트리려면 `(x, -1)` 의 인접 풍선이나 `(-1, 10)`의 인접 풍선에서 터트려야 한다. `(-1, 10)` 일 경우에는 *번호가 더 작은 풍선을 터트리는 기회*를 한번 사용해야 하므로 넘어가고, `(x, -1)`일 경우에는 기회를 사용하지 않은 채 터트리려면 **x가 -1보다 더 작아야 한다.**

x가 -1보다 더 작으면 결국 `(x, 10)` 의 구조가 되는데, 여기서 `10` 풍선을 살리려면 결국 작은 풍선을 터트리는 기회가 사용되고 만다.

오른쪽 또한 같게 생각해보면 결국 `y`값은 `-2`보다 더 작은 값이거나 기회를 사용해야 하기 때문에, **양쪽에 현재 숫자보다 더 작은 숫자가 0개 또는 1개여야 한다.** 그래야 현재 숫자를 마지막까지 남길 수 있게 되는 것이다.

---

## 조이스틱
이 문제는 굉장히 오래 걸렸는데, 오른쪽 끝 문자에서 `▶` 를 누르면 왼쪽 첫 번째 문자로 돌아올 수 있는지에 대한 논란이 많은 문제인 것 같다.

처음에는 돌아올 수 없다는 가정하에 문제를 풀었는데, 제대로 된 답을 도출하지 못했다. 문제의 질문하기 목록을 보고서 여러 테스트 케이스를 적용했을 때 결과적으로는 돌아올 수 있다는 가정하에 푼 풀이가 정답을 도출했다.

가장 논란이 큰 예시인 `BBBAAAB` 를 통해 살펴보자. 내가 정한 테스트 케이스에서는 `BBBAAAB`는 답이 `9`가 나오도록 했는데, 맨 뒤 위치에서 첫번째 위치로 돌아갈 수 있다면 `8`번의 입력만으로도 가능하다.

- 기존에 생각했던 로직
```
  B B B A A A B  : 시작
  A B B A A A B  : 알파벳 1 이동
  ->             : 오른쪽 1 이동
  A A B A A A B  : 알파벳 1 이동
    ->           : 오른쪽 1 이동
  A A A A A A B  : 알파벳 1 이동
    <-           : 왼쪽 1 이동
  <-             : 왼쪽 1 이동
              <- : 왼쪽 1 이동
  A A A A A A A  : 알파벳 1 이동
                  ------------
                  총 9 번 이동
```

- 8번이 나올 수 있는 로직
```
  B B B A A A B   : 시작
  A B B A A A B   : 알파벳 1 이동
               <- : 왼쪽 1 이동
  A B B A A A A   : 알파벳 1 이동
->                : 오른쪽 1 이동
  ->              : 오른쪽 1 이동
  A A B A A A A   : 알파벳 1 이동
    ->            : 오른쪽 1 이동
  A A A A A A A   : 알파벳 1 이동
                  ------------
                   총 8 번 이동
```

아마 현재 위치에서 왼쪽과 오른쪽에 모두 A가 아닌 알파벳이 등장했을 때 어디를 우선 방향으로 잡느냐에 따라 달라지는거라 생각이 든다. 문제의 조건 부족으로 인한 오류인지, 내가 잘못한건지 잘 모르겠다.