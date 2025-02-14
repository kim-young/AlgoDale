---
title: "Missing Number"
tags:
  - leetcode
  - python
  - array
  - set
  - sort
  - bit-manipulation
date: 2022-01-27
---

<iframe width="560" height="315" src="https://www.youtube.com/embed/FsragroZe3Q" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

LeetCode의 268번째 문제인 [Missing Number](https://leetcode.com/problems/missing-number/)를 함께 풀어보도록 하겠습니다.

## 문제

`[0, 1, ..., n - 1, n]` 범위 내의 중복되지 않은 숫자 `n` 개로 이뤄진 배열이 주어졌을 때, 해당 배열에서 빠져있는 유일한 숫자를 구하라.

## 예제

```py
입력: nums = [3, 0, 1]
출력: 2
```

```py
입력: nums = [0,1]
출력: 2
```

## 풀이 1

숫자 `n`개로 이뤄진 배열에서 빠져있는 숫자를 찾으려면 `0`부터 `n`까지 `1`씩 증가시키면서 해당 숫자가 배열 내에서 존재하는지 체크해야하는데요.
해당 숫자가 배열 내에 존재하지 않는다면 바로 그 숫자를 반환하면 될 것입니다.

이 것을 그대로 코드로 구현해볼까요?

```py
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        for i in range(len(nums)):
            if i not in nums:
                return i
        return len(nums)
```

이 풀이의 시간 복잡도는 `O(n^2)`입니다.
배열 내에서 특정 숫자를 찾으려면 선형 탐색이 필요하기 때문에 `O(n)` 시간이 소모되는데, 이 작업을 `0`부터 `n`까지의 모든 수에 대해서 수행해야하기 때문입니다.
변수 `i` 외에는 추가적인 메모리를 사용하지 않으므로 공간 복잡도는 `O(1)`이 되겠네요.

## 풀이 2

시간 복잡도가 `O(n^2)`인 위 알고리즘은 입력 배열의 크기가 커지게 되면 성능이 현저하게 떨어지게 될텐데요.
어떻게 하면 시간 복잡도를 향상 시킬 수 있을까요?

위 알고리즘에서 가장 시간을 많이 잡아먹는 작업은 바로 특정 배열 내에서 숫자를 찾기위한 선형 탐색인데요.
배열 내의 모든 숫자를 해시 테이블을 기반으로 하는 집합(set) 자료구조에 저장해놓고 찾으면 검색 시간을 상수 시간(constant time)으로 낮출 수가 있을 것입니다.

이 것을 그대로 코드로 구현해보겠습니다.

```py
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        num_set = set(nums)
        for i in range(len(nums)):
            if i not in num_set:
                return i
        return len(nums)
```

이 풀이에서는 공간 복잡도를 `O(1)`에서 `O(n)`으로 올린 댓가로 시간 복잡도를 `O(n^2)`에서 `O(n)`로 대폭 낮출 수 있었습니다.

## 풀이 3

공간 복잡도를 희생하지 않고 시간 복잡도를 개선할 수 있는 방법은 없을까요?

위 2가지 풀이를 관찰해보면 공통적으로 숫자 `0`부터 `1`, `2`, ..., `n - 1`이 배열에 있는지를 체크하고 있는데요.
이는 입력 배열을 정렬하면 단순히 인덱스 `i`에 숫자 `i`가 있는지를 체크하는 것과 동일하다고 볼 수 있습니다.

예를 들어, 첫 번째 예제로 주어진 배열을 정렬한 후에, 인덱스와 비교를 해보겠습니다.

```py
[0, 1, 3]
 0, 1, 2, 3
```

어떤가요? 빠져있는 숫자가 바로 딱 보이죠?

이 것을 그대로 코드로 구현해볼까요?

```py
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        nums.sort()
        for idx, num in enumerate(nums):
            if idx != num:
                return idx
        return len(nums)
```

이 풀이는 공간 복잡도가 `O(1)`로 다시 내려왔지만, 시간 복잡도가 정렬로 인해서 `O(nlog(n))`으로 다소 저하되었습니다.

## 풀이 4

시간 복잡도만 `O(n)`로 향상시킨다면 최적의 알고리즘이 될텐데요... 과연 다른 좋은 방법이 없을까요? 🤔
배열을 정렬하는데 `O(nlog(n))`의 시간이 소모되므로 이 부분을 개선하면 될 것 같은데요.

정렬을 하지않고 단순히 숫자 `i`를 인덱스 `i` 위치로 보내도 같은 효과가 나지 않을까요?

이 게 무슨 말인지 첫 번째 예제로 같이 생각을 해봅시다.

```py
 *
[3, 0, 1]
```

먼저 숫자 `3`은 인덱스 `3`으로 보내야합니다.
그런데 배열의 크기가 `3`이라서 보낼 자리가 없으므로 다음 숫자로 넘어가겠습니다.

```py
    *
[3, 0, 1]
```

숫자 `0`을 인덱스 `0`으로 보내고, 그 자리에 있던 `3`을 현재 위치인 인덱스 `1`로 데려오겠습니다.

```py
    *
[0, 3, 1]
```

숫자 `3`은 보낼 자리가 없으므로 다음 숫자로 넘어가겠습니다.

```py
       *
[0, 3, 1]
```

숫자 `1`을 인덱스 `1`로 보내고, 그 자리에 있던 `3`을 현재 윈치인 인덱스 `2`로 데려오겠습니다.

```py
       *
[0, 1, 3]
```

마찬가지로 숫자 `3`은 보낼 자리가 없으므로 다음 숫자로 넘어가면 배열을 끝에 도달하게 됩니다.

이 과정을 코드로 구현해보겠습니다.

```py
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        idx = 0
        while idx < len(nums):
            val = nums[idx]
            if val < len(nums) and val != idx:
                nums[idx], nums[val] = nums[val], nums[idx]
            else:
                idx += 1

        for idx, num in enumerate(nums):
            if idx != num:
                return idx
        return len(nums)
```

마침내 시간 복잡도를 `O(n)`으로 향상시켰습니다! 🎉

## 풀이 5

놀랍게도 이 문제는 XOR 연산자를 이용해서도 `O(n)` 시간 복잡도로 풀 수 있습니다.
바로 같은 두 숫자를 XOR하면 `0`이 나오고, `0`과 어떤 숫자를 XOR하면 그 숫자가 그대로 나온다는 성질을 이용하는 것인데요.
모든 숫자에 모든 인덱스를 XOR해주면 빠져있는 숫자를 얻을 수 있게 됩니다.

첫 번째 예제에 이 로직을 적용해보면 다음과 같습니디.

```py
입력 배열:  [3, 0, 1]
[0...n]: [0, 1, 2, 3]

XOR: 3 ^ 0 ^ 0 ^ 1 ^ 1 ^ 2 ^ 3 = 2
```

`0`이 2개, `1`이 2개, `3`이 2개여서, 이 6개의 숫자를 XOR해주면 `0`이 되는데 여기에 추가로 `2`를 XOR를 해주면 최종적으로 `2`가 되는 것이지요.

```py
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        xor = len(nums)
        for idx, num in enumerate(nums):
            xor ^= idx ^ num
        return xor
```

사실 이 풀이는 왠만한 내공이 아니면 스스로 생각해내기는 매우 어려울테니 참고만 하시면 좋을 것 같습니다. 😅

## 풀이 6

수학 시간에 배우셨던 `1`부터 `n`까지의 합을 구하는 공식을 기억하신다면 이 문제를 수학적으로도 접근할 수 있습니다.
`n * (n + 1) // 2`을 통해서 `1`부터 `n`까지의 합을 구한 후에 실제 합을 구해서 빼주기만 하면 빠진 숫자가 나올 것입니다.

```py
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        expected_sum = len(nums) * (len(nums) + 1) // 2
        actual_sum = sum(nums)
        return expected_sum - actual_sum
```
