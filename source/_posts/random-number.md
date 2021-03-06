---
title: 亂數
date: 2019-01-31 22:22:37
tags: [math, random number, entropy]
categories: math
mathjax: true
---

這篇是 jserv 的課堂作業的一部分，當時因為後面的部分沒寫完所以沒交上去，現在回來看覺得亂數這段寫得還不錯，所以把他移到部落格來。[當時的 hackmd](https://hackmd.io/s/r1A3kFPjz)。

### 亂數

在討論[Random Number Generator](http://stattrek.com/statistics/random-number-generator.aspx)亂數表夠不夠亂之前，可能我們要先回答兩個問題：

1. What is random?
2. How to measure the randomness?

亂數直覺而言是個很簡單的概念，一般來說，我們認爲一個事件「亂」，表示我們無法預測該事件的結果，但統計學卻讓所謂的「亂」有跡可循，透過機率分佈的概念，我們即便無法準確的預測下一次的事件結果爲何，卻可以很「確定」的表示隨機事件的結果落在某個範圍內的機率是多少，或長期而言結果的統計量會趨近於某個確定的值 -- 我們可以用機率分佈來「描述」隨機事件。

另一個角度來看，我們可能認爲一段文字是隨機的，如果我們無法從該段文字中讀出資訊，例如「woif ejxv nwe」可能是一個隨機的字串而「what the fuck」則不是隨機 -- 資訊的含量也可以用來描述隨機性。

從上面的兩個觀點中，我們可以截取出兩個概念，並討論看看這他們與亂數的關係 -- probability & information

一個事件的結果無跡可循，換句話說結果發生在任何位置的機率是均等的，這樣的性質可以用 uniform distribution 來表達，但光是 uniform 是不夠的，考慮一個數列: $f(i) = a + (i\ mod\ (b-a)), i=1, 2, 3, ...$ 可以預期結果會是分佈在 $[a, b]$ 的正整數，且發生的頻率均等，但我們不會認爲這樣的數列是隨機的，因爲我們可以很容易的發現事件之間有前後關係，所以還要再多加上一個條件，事件發生的機率互相獨立。

在此觀點下，理想的亂數應符合以下分佈

$$
X \sim^{iid} U[a, b]
$$

那麼以文字的資訊量切入的話什麼是理想的亂數呢？

首先我們得先談論何爲「資訊(information)」，考慮剛才的例子，爲何我們有辦法解讀出「what the fuck」的含義？首先從單字來看，我們知道 what 按順序出現時可表達出某個意思，換句話說當出現 wha 時，有很高的機率接下來會出現 t；從句子來看，當我們看到 what 知道他是疑問詞，根據文法結構可以預期接下來會出現名詞的機率很高，相反如果出現的不是名詞我們將難以判斷該文句的意義，我們就認爲該句子是隨機而無意義的。

上述過程我們可以發現一件事情，當我們預期某個事件發生的機率很高，而他真的發生的時候，我們獲得的資訊其實沒有變多，也就是說上面這句話裏面事實上有許多部分是多餘的，即便我們將一部分的字元移除仍可以傳達相等的資訊（缺失的部分可以很容易預測），例如「wat th fk」即使沒有一個單字拼對我們仍可讀出一樣的訊息，兩個句子每個字元平均所含的資訊量是不相等的，這也是資訊壓縮的基本原理，要怎麼用最少的字元數表達相同的資訊量。

總結來說，當一個事件發生的機率越低，而他真的發生時，我們獲得的資訊會越多，這就是資訊含量 Entropy 的基本想法，Entropy 定義是某個隨機事件平均每個結果發生時所能傳達出的資訊量，公式如下

$$
S=- \sum^{n}_{i=1} P(x_i) log_b P(x_i)=\sum^{n}_{i=1} P(x_i)I(x_i)
$$

Entropy 實際上是 Self-information $I(x_i)$ 的期望值，Self-information 即每個隨機事件發生所傳達的資訊量，定義上資訊量與隨機事件發生的機率成反比，且機率爲 1 時資訊量爲 0，根據定義我們發現 log 有這樣的性質，因此

$$
I(x_i)=log_b (\frac{1}{P(x_i)})
$$

帶入 P 值可以發現 P 越小 I 會越大，符合我們對資訊量的定義。

此外使用 log 還有一個性質：資訊是可加的，兩個獨立事件發生的資訊量為個別資訊量相加。

$$
\begin{split}
I(A+B) &= log_b (\frac{1}{P(A)P(B)}) \\
       &= log_b (\frac{1}{P(A)}) + log_b (\frac{1}{P(B)}) \\
       &= I(A)+I(B)
\end{split}
$$

觀察 Entropy 公式可以發現到，對於一分佈而言，因 $\sum_{i=1}^{n} P(x_i) = 1$，Entropy 的極大值發生在 $P(x_1)=P(x_2)=...=P(x_n)=\frac{1}{n}$ 時 $(S=log_b n)$， 此分佈即爲 uniform distribution，因爲每個事件的發生的機率都一樣，不論發生哪個事件我們都可以獲得最大的資訊量，這就是資訊觀點的亂數，從剛剛文法的例子去想也很直覺，若我們難以從前面的資訊預測接下來會出現的詞，我們更有可能認為該句子是亂數產生的。

#### 小結

上面我們從兩個不同的觀點切入並獲得相同的結果，並有以下結論，隨機具有以下特性

1. 每個隨機事件的發生機率相同
2. 每個事件發生的機率互相獨立

並且我們可以用 Entropy 來量測亂度

$$
S=- \sum^{n}_{i=1} P(x_i) log_b P(x_i)
$$

#### PRNG

在現實世界中是否存在真正理想的亂數，目前仍有爭議，因為古典的科學建立在任何事件發生皆有因果關係之上，如果我們能夠獲取夠全面的資訊的話，就應該可以預測接下來會發生的事情，若存在一事件發生與過去所有狀態無關，則會打破這樣的因果關係；但近代的量子物理開始有發現到一些自然界的隨機性。

儘管如此我們可以透過一些手段，讓亂數雖然是經過一連串確定性的過程產生，卻可在我們使用的範圍內「不可預測」，這就是「偽亂數」(pseudo-random number)，產生偽亂數的產生器即為 pseudo-random number generator (PRNG)。

PRNG 的數學描述如下

考慮一函數集合

$$
\mathcal A = \{A: \{0, 1\}^n \to \{0,1\}^*\}
$$

為一系列檢測一組長度為 n 的序列是否為亂數的統計試驗，稱為 Adversaries，則有一函數

$$
G: \{0, 1\}^\ell\to\{0,1\}^n \text{ with } \ell \leq n
$$

稱為 Generator，試圖欺騙所有 $\mathcal A$ 裡面所有的 $A$，並容許偏差 $\epsilon$，即對於所有 $\mathcal A$ 裡面的 $A$，$A(G(U_\ell))$ 和 $A(U_n)$ 的統計距離不大於 $\epsilon$，其中 $U_k$ 為 $\{0,1\}^k$ 的 uniform distribution。

> (似曾相似...這不就是 GAN 的數學描述嬤？！只是 G 和 A 在 GAN 裏面是兩個類神經網路，而這邊是PRNG和統計試驗) [name=DanielChen]

Adversaries 的例子可參考 [Statistical randomness - Wikipedia#Tests](https://en.wikipedia.org/wiki/Statistical_randomness#Tests)

#### Randomness test

linux 底下有一指令 [`ent`](http://www.fourmilab.ch/random/) 可計算一輸入字串的 Entropy。

嘗試計算 linux 內建的 PRNG `/dev/random`

```
$ head -c 10M /dev/random | ent
Entropy = 7.999982 bits per byte.

Optimum compression would reduce the size
of this 10485760 byte file by 0 percent.

Chi square distribution for 10485760 samples is 255.57, and randomly
would exceed this value 47.82 percent of the times.

Arithmetic mean value of data bytes is 127.4605 (127.5 = random).
Monte Carlo value for Pi is 3.144157846 (error 0.08 percent).
Serial correlation coefficient is 0.000057 (totally uncorrelated = 0.0).
```

1 byte char 的大小為 $2^8$，則最大的 entropy 為 $S=lg(2^8)=8$，可觀察到這次取出的結果相當接近這個理論值。

輸出的第二的部分為最佳壓縮率計算，這個值是從 entropy 計算而來的。

輸出的第三部分為卡方檢定的結果，卡方中的適合度檢定(Goodness of Fit)可用來檢定資料是否符合某種分佈，我們可以用卡方來檢驗我們的資料是否符合 Uniform distrubution，卡方檢定的 null hypothesis 如下

$H\_0: p\_i=p\_{i0}, i=1,...,k$
$H\_1: 不是所有p\_i都等於p\_{i0}$

經由卡方檢定計算出的 p 值若小於設定的信心水準 (如0.05) 表示拒絕此虛無假說，則可宣稱此組資料不符合指定分佈，反之則宣稱無證據顯示此資料不符合該分佈。

> 若沒學過統計或對統計不熟的同學要注意一下，當我們拒絕 null hypothesis 時我們明確表示 $H_1$ 爲真，但若檢定結果爲不顯著，結論並非「$H_0$ 爲真」，而是「無證據顯示 $H_1$ 爲真」[name=DanielChen]

以這次執行的結果而言 p 值為 47.82%，離 5% 信心水準相當遙遠，因此判斷這次產生的亂數有可能符合 Uniform distribution。

最後三個部分分別為平均值檢定、蒙地卡羅法計算Pi值、及序列相關係數(可檢驗序列上每個值前後的相關性)，也是檢驗該序列是否是亂數的依據。

除了 `ent` 之外，還有一個很爲人所知的工具叫做 [Diehard tests](https://en.wikipedia.org/wiki/Diehard_tests)，他是一系列 Randomness test 的集合，最早由 George Marsaglia 開發，之後釋出的 linux 指令 [`dieharder`](https://github.com/seehuhn/dieharder) 還有後續加入其他的亂數測試。

節錄部分執行結果

```
#=============================================================================#
#            dieharder version 3.31.1 Copyright 2003 Robert G. Brown          #
#=============================================================================#
   rng_name    |rands/second|   Seed   |
   /dev/urandom|  2.16e+07  | 670031799|
#=============================================================================#
        test_name   |ntup| tsamples |psamples|  p-value |Assessment
#=============================================================================#
   diehard_birthdays|   0|       100|     100|0.80323410|  PASSED  
      diehard_operm5|   0|   1000000|     100|0.99712783|   WEAK   
  diehard_rank_32x32|   0|     40000|     100|0.13150830|  PASSED  
    diehard_rank_6x8|   0|    100000|     100|0.60447291|  PASSED  
   diehard_bitstream|   0|   2097152|     100|0.14568798|  PASSED  
        diehard_opso|   0|   2097152|     100|0.49901549|  PASSED  
        diehard_oqso|   0|   2097152|     100|0.08646324|  PASSED  
         diehard_dna|   0|   2097152|     100|0.75489321|  PASSED  
diehard_count_1s_str|   0|    256000|     100|0.95499444|  PASSED  
diehard_count_1s_byt|   0|    256000|     100|0.59438482|  PASSED  
 diehard_parking_lot|   0|     12000|     100|0.00000000|  FAILED  
    diehard_2dsphere|   2|      8000|     100|0.00000000|  FAILED  
    diehard_3dsphere|   3|      4000|     100|0.00000000|  FAILED  
     diehard_squeeze|   0|    100000|     100|0.00000000|  FAILED  
        diehard_sums|   0|       100|     100|0.00000000|  FAILED  
        diehard_runs|   0|    100000|     100|0.96701019|  PASSED  
        diehard_runs|   0|    100000|     100|0.80232209|  PASSED  
...
```

#### Stat Trek RNG

我用 javascript 簡單寫了一支小程式來計算 [Random Number Generator](http://stattrek.com/statistics/random-number-generator.aspx) 這個網站的 entropy 以及卡方值，新增一個我的最愛並貼上貼上以下代碼，名稱任意（或直接將右邊這個連結拖曳到書籤列 <a href="javascript:(function(){var script=document.createElement('SCRIPT');script.src='https://raw.githack.com/d4n1elchen/RandomnessTest/master/test-compress.js';document.body.appendChild(script);})()">RandomnessTest</a>），進入[網站](http://stattrek.com/statistics/random-number-generator.aspx)並產生亂數表之後執行剛剛新增的那個我的最愛，結果會顯示在 browser console 裡面

```
javascript:(function(){var script=document.createElement('SCRIPT');script.src='https://raw.githack.com/d4n1elchen/RandomnessTest/master/test-compress.js';document.body.appendChild(script);})()
```

執行結果(範圍0~99999,產生1000個)

```
Calculated entropy = 9.955784284662016
Ideal entropy for N=100000 is 16.609640474436812

Chi Square = 99999.99999987465
```

就 entropy 而言這次產生出來的亂數表距離理想值還有一段距離。

因為卡方分佈的 CDF 不太好實做，這邊只算了卡方值，可用[查表](https://homepage.divms.uiowa.edu/~mbognar/applets/chisq.html)的方式查出自由度 99999，$\mathcal X^2= 99999.99999987465$ 時，$p=0.49851=49.8\%$，$H_0$不顯著，無證據說此分佈不均勻。

但這邊有一個問題，取樣的數目可能太少而範圍太大，發生 local randomness 不足的可能很高，調整亂數範圍為 0~99

```
Calculated entropy = 6.559046277841441
Ideal entropy for N=100 is 6.643856189774724

Chi Square = 117.6
```

發現 entropy 與理想值相當接近，表示該亂數產生器產生出來的亂數有一定程度的亂度。

卡方檢定部分，$p=0.09787=9\%$，$H_0$ 不顯著，無證據說此分佈不均勻。

因此結論此 RNG 通過 entropy 與卡方檢定，我們能在統計上確保一定程度的亂度。

#### Cryptographically secure pseudorandom number generator

在上一部分所談到的亂數度檢定如 entropy 及卡方檢定僅能檢驗是否符合統計的亂數分佈，但並不保證該 PRNG 不會被破解(被找到產生的 pattern 進而預測下一個產生的亂數)，因此在電腦科學領域有一更加嚴格要求的偽亂數產生器，是為 [Cryptographically secure pseudorandom number generator (CSPRNG)](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator)，CSPRNG 首先必須先通過統計亂數檢定，除此之外還有一些額外的要求，例如：已知亂數序列前 k 項，在多項式時間的計算預估 k+1 項猜中的機率不可高於 50%。
