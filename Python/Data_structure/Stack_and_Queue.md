### 스택(Stack) 자료구조

- 먼저 들어 온 데이터가 나중에 나가는 형식(선입후출)의 자료구조
- **입구와 출구가 동일한 형태**로 스택을 시각화할 수 있음

#### 스택 구현 예제

```python
stack = []

# 삽입(5) - 삽입(2) - 삽입(3) - 삽입(7) - 삭제() - 삽입(1) - 삽입(4) - 삭제()
stack.append(5)
stack.append(2)
stack.append(3)
stack.append(7)
stack.pop()
stack.append(1)
stack.append(4)
stack.pop()

print(stack[::-1])  # 최상단 원소부터 출력
print(stack)  # 최하단 원소부터 출력

'''
[Output]
[1, 3, 2, 5]
[5, 2, 3, 1]
'''
```

### 큐(Queue) 자료구조

- 먼저 들어 온 데이터가 먼저 나가는 형식(선입선출)의 자료구조
- 큐는 **입구와 출구가 모두 뚫려 있는 터널과 같은 형태**로 시각화 할 수 있음

#### 큐 구현 예제

```python
from collections import deque

# 큐(Queue) 구현을 위해 deque 라이브러리 사용
queue = deque()

# 삽입(5) - 삽입(2) - 삽입(3) - 삽입(7) - 삭제() - 삽입(1) - 삽입(4) - 삭제()
queue.append(5)
queue.append(2)
queue.append(3)
queue.append(7)
queue.popleft()
queue.append(1)
queue.append(4)
queue.popleft()

print(queue)  # 먼저 들어온 순서대로 출력
queue.reverse()  # 역순으로 바꾸기
print(queue)  # 나중에 들어온 원소부터 출력

'''
[Output]
deque([3, 7, 1, 4])
deque([4, 1, 7, 3])
'''
```
