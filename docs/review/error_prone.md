# Error Prone

這裡放我常錯的觀念


## 前後綴分解

## DP 順序/方向

通常會以順序的方向思考 DP 但是如果多個起點會導致較難做思考/計算的話，要嘗試反向的方式定義/思考問題



## 解法簡單，但是起頭/過程推導卡住的題目

---
* [4073. Count Good Strings](https://leetcode.cn/problems/count-good-strings/description/)


    a. 釐清題意
    題目要求將字串拆分，最終可以拆成若干奇數字串

    b. 先找出DP規律，枚舉最後一段奇數字串可以透過加法原理將方案數加起來
    $f(i) = f(i - 1) + f(i - 3) + f(i - 5) ....$


    <details> <summary>hidden</summary>
    c. 優化

    這是最難想到的
    我只有想到聯想到f(1) f(3) f(5)
    $f(i - 2) = f(i - 3) + f(i - 5) ....$
        
    用 $f(i - 2)$ 替換 $f(i - 3) + f(i - 5) ....$ 可以得到 $f(i) = f(i - 1) + f(i - 2)$

    d. 用矩陣快速冪計算fib
    </details>

---