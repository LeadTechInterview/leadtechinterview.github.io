# Desc

Determine whether a Sudoku is valid.
The Sudoku board could be partially filled, where empty cells are filled with the character ".".

# Memo

1. one loop is enough, we don't need 3 loops to check cols, rows and boxes
2. `k = 3 * i//3 + j//3` is WRONG, and it's NOT the same as `k = i//3 * 3 + j//3`
3. don't forget to ignore '.'

# Solution

Loop 3 times, not good

```python
    def is_valid_sudoku(self, board: List[List[str]]) -> bool:
        # write your code here
        m, n = len(board), len(board[0])
        assert m == 9 and n == 9

        for i in range(m):
            cnt = [0 for _ in range(n)]
            for j in range(n):
                if board[i][j] == '.': continue
                idx = ord(board[i][j]) - ord('0') - 1
                cnt[idx] += 1
                if cnt[idx] > 1:
                    return False

        for j in range(n):
            cnt = [0 for _ in range(m)]
            for i in range(m):
                if board[i][j] == '.': continue
                idx = ord(board[i][j]) - ord('0') - 1
                cnt[idx] += 1
                if cnt[idx] > 1:
                    return False

        cnt = [[0 for _ in range(9)] for _ in range(9)]
        for i in range(m):
            for j in range(n):
                if board[i][j] == '.': continue
                k = i//3 * 3 + j//3
                idx = ord(board[i][j]) - ord('0') - 1
                cnt[k][idx] += 1
                if cnt[k][idx] > 1:
                    return False

        return True
```

More elegant way:

```python
    def is_valid_sudoku(self, board: List[List[str]]) -> bool:
        # write your code here
        m, n = len(board), len(board[0])
        assert m == 9 and n == 9

        rows = [set() for _ in range(9)]
        cols = [set() for _ in range(9)]
        boxes = [set() for _ in range(9)]

        for i in range(9):
            for j in range(9):
                val = board[i][j]
                if val == '.': continue
                
                if val in rows[i]:
                    return False
                rows[i].add(val)

                if val in cols[j]:
                    return False
                cols[j].add(val)

                idx = i//3 * 3 + j//3
                if val in boxes[idx]:
                    return False
                boxes[idx].add(val)

        return True
```
