---
layout: post
title: "python unittest 사용자 입력 처리, How to test input processing"
date: 2018-10-03 16:26:53 +0900
categories: [python, test]
encoding: UTF-8
---

<br>
<br>

### Python unittet 사용자 입력 처리

<br>

python에서 test를 짜다보면 사용자 입력(input이나 sys.stdin.readline과 같은)이 필요한 경우가 생긴다.

여기서 다룰 예는 [BOJ 9339번 문제](https://www.acmicpc.net/problem/9339)와 같이 입력 값이 많은 알고리즘 문제의 테스트 작성이다. 

<br>

9339번 문제의 입력 예시는 아래와 같다. 

![branch Image](https://raw.githubusercontent.com/lee-seul/lee-seul.github.com/master/static/img/_posts/input_processing_01.png)

<br>

unittest에서 사용자 입력을 처리하는 방법은 unittest.mock.patch를 활용하면 간단하다. 

<br>
테스트 전체 코드
```python


from unittest.mock import patch
import unittest

from p9339 import solution


class P9339TestCase(unittest.TestCase):

    def test_solution(self):
        user_input_1 = [
            '4',
            '123 456 999 73',
            '6',
            '111 5 3',
            '456 -1 -1',
            '123 4 59',
            '73 6 0',
            '520 -1 -1',
            '999 6 0'
        ]
        user_input_2 = [
            '5',
            '3 5 2 7 1',
            '10',
            '5 8 3',
            '4 6 20',
            '9 4 10',
            '10 5 20',
            '1 6 1',
            '2 4 20',
            '3 4 20',
            '6 4 20',
            '7 4 15',
            '8 4 10',
        ]
        right_ans_1 = (123, 3)
        right_ans_2 = (7, 3)
        with patch('builtins.input', side_effect=user_input_1):
            ans = solution()

        self.assertEqual(ans, right_ans_1)

        with patch('builtins.input', side_effect=user_input_2):
            ans = solution()

        self.assertEqual(ans, right_ans_2)


if __name__ == '__main__':
    unittest.main()

```

with patch('builtins.input', side_effect=user_input_1) 형태로 실행하면, 테스트에 필요한 사용자 입력 값을 처리할 수 있다. 

<br>

p9339.solution 코드

```python
# coding: utf-8


def solution():
    k = int(input())
    students = [int(x) for x in input().split()]
    n = int(input())
    su = []
    for _ in range(n):
        num, hour, minute = map(int, input().split())
        if hour == -1:
            continue
        time = hour * 60 + minute
        if num in students and time <= 360:
            su.append((num, time))

    su = sorted(su, key=lambda x: x[1])
    return su[0][0], len(su)


if __name__ == '__main__':
    t = int(input())
    for _ in range(t):
        a, b = solution()
        print(a, b)

```


<br>
<br>