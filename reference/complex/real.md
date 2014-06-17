#real
```cpp
namespace std {
  template<typename T>
  constexpr T real(const complex<T>& x);

  FloatingPointType real(ArithmeticType x);	// 追加のオーバーロード：C++11 から
}
```

##概要
複素数の実部を取得する。

なお、C++11 で追加されたオーバーロードは、以下のように規定されている。

- 実引数の型が `long double` の場合、`complex<long double>` にキャストされているかのように振る舞う。
- そうでなくて、実引数の型が `double` か整数型の場合、`complex<double>` にキャストされているかのように振る舞う。
- そうでなくて、実引数の型が `float` の場合、`complex<float>` にキャストされているかのように振る舞う。

また、これらの追加のオーバーロードが関数テンプレートなのか否か、あるいは、引数が参照型なのか否かなどについては、規格では何も言及されていない。


##戻り値
引数に指定した複素数 `x` の実部（`x.`[`real`](complex/real.md)`()`）


##備考
同名のメンバ関数 [`real`](complex/real.md) も存在する。


##例
```cpp
#include <iostream>
#include <complex>

int main()
{
  std::complex<double> c(1.0, 2.0);
  std::cout << c << ", real part = " << std::real(c) << std::endl;
}
```
* real[color ff0000]
* iostream[link /reference/iostream.md]
* complex[link /reference/complex.md]

###出力
```
(1,2), real part = 1
```


##バージョン
###言語
- C++98（追加のオーバーロードは C++11 から）

###処理系
- [Clang](/implementation.md#clang): 3.0, 3.1, 3.2, 3.3, 3.4（追加のオーバーロード含む）
- [GCC](/implementation.md#gcc): 4.3.6, 4.4.7, 4.5.4, 4.6.4, 4.7.3, 4.8.1, 4.8.2, 4.9.0（追加のオーバーロード以外）
- [GCC, C++11 mode](/implementation.md#gcc): 4.3.6, 4.4.7, 4.5.4, 4.6.4, 4.7.3, 4.8.1, 4.8.2, 4.9.0（追加のオーバーロード含む）
- [ICC](/implementation.md#icc): ??
- [Visual C++](/implementation.md#visual_cpp): ??


##参照
|                                    |                                                |
|------------------------------------|------------------------------------------------|
| [`real`](complex/real.md)          | 実部を取得、あるいは、設定する。（メンバ関数） |
| [`imag`](complex/imag.md)          | 虚部を取得、あるいは、設定する。（メンバ関数） |
| [`imag`](imag.md)                  | 虚部を取得する。                               |
| [`abs`](abs.md)                    | 複素数の絶対値を得る。                         |
| [`arg`](arg.md)                    | 複素数の偏角を得る。                           |
| [`norm`](norm.md)                  | 複素数体のノルムを得る。                       |
| [`conj`](conj.md)                  | 共役複素数を得る。                             |
| [`proj`](proj.md)                  | リーマン球面への射影を得る。                   |
| [`polar`](polar.md)                | 指定した絶対値と偏角の複素数値を得る。         |