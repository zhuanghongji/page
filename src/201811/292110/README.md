# Kotlin Examples - Ranges

## 示例

```kt
for(i in 0..3) {             // 1
    print(i)
}
print(" ")

for(i in 2..8 step 2) {      // 2
    print(i)
}
print(" ")

for (c in 'a'..'d') {        // 3
    print(c)
}
print(" ")

for (c in 'a'..'g' step 2) { // 4
    print(c)
}
print(" ")

for (i in 3 downTo 0) {      // 5
    print(i)
}
print(" ")

val x = 2
if (x in 1..10) {            // 6
    print(x)
}
print(" ")

val y = 3
if (y !in 1..4) {            // 7
    print(y)
}
```

```
0123 2468 abcd aceg 3210 2
```

1. 创建一个从 0 到 3 (包含) 的范围 (range)，并进行迭代。
2. 你可以为连续的元素定义增量步长 (step)。
3. 也可以创建字符范围。
4. 增量步长对字符范围同样有效。
5. 使用 `downTo()` 函数创建反向顺序的范围。
6. 范围在 `if` 语句中也有很大用处。
7. `!in` 与 `in` 相仿。
