#ユーザー定義リテラル
* cpp11[meta cpp]

##概要
ユーザー定義リテラル(User-defined literals)は、`123`、`3.14`、`"hello"`といったリテラルに対して付けられるサフィックスをオーバーロードできるようにすることで、ユーザーがリテラルに意味を持たせられるようにする機能である。

これは、リテラルに対して以下のような情報を持たせるために使用できる：

- 単位 : メートル、秒、角度として度数法か弧度法、など
- 型 : `"hello"s`とすることで[`std::string`](/reference/string/basic_string.md)型の文字列リテラル、`1.2i`とすることで[`std::complex<double>`](/reference/complex.md)型のリテラルとするなど

ユーザー定義リテラルは、`operator"" サフィックス名`の演算子をオーバーロードする。`""`とサフィックス名の間にスペースが必要なので注意。

```cpp
std::string operator"" s(const char* str, std::size_t length)
{
  return std::string(str, length);
}

auto x = "hello"s; // xの型はstd::string
```
* std::string[link /reference/string/basic_string.md]
* std::size_t[link /reference/cstddef/size_t.md]

`operator""`は、「リテラル演算子 (literal operator)」という。

ここでは`char`配列の文字列リテラルに対するサフィックスを定義しているが、パラメータの型を`wchar_t`、[`char16_t`](char16_32.md)、[`char32_t`](char16_32.md)とすることで、それらの文字型の文字列に対しても、サフィックスを定義できる。

整数リテラルの場合には、[`unsigned long long`](long_long_type.md)型のパラメータをひとつ受け取るようにする。負数は、演算子のなかでは扱えず、演算子によって返された値を符号反転することで負数が表現される。

浮動小数点数リテラルの場合には、`long double`型のパラメータをひとつ受け取るようにする。


##仕様
###全般的な仕様
- ユーザー定義リテラルのサフィックスと組み込みリテラルのサフィックスが一致した場合でも、組み込みリテラルのサフィックスと型が一致しない場合には、ユーザー定義リテラルが使用される。たとえば、浮動小数点数のユーザー定義リテラルとして`LL`を定義した場合でも、整数リテラルに対して`LL`サフィックスを付けた場合には、組み込みの`LL`サフィックスが使用される：

    ```cpp
std::string operator"" LL(long double x)
{
  return std::to_string(x);
}

long long   a = 123LL;     // 組み込みの整数リテラル
std::string b = 123.456LL; // ユーザー定義リテラル
```
* std::string[link /reference/string/basic_string.md]

- リテラル演算子の名前として、ユニバーサルキャラクタ名を使用することが許可される：

    ```cpp
namespace literals {
  // _ + 小文字のpi (π)
  float operator"" _\u03C0(unsigned long long count)
  {
    return 3.141592f * count;
  }
}

using namespace literals;

float x = 2_\u03C0; // OK
float y = 2_π;     // OK
```

- 文字型と論理値型に対しては、リテラル演算子を定義できない
- リテラル演算子とリテラル演算子テンプレートは、Cリンケージを持ってはならない
- リテラル演算子とリテラル演算子テンプレートは、`inline`と`constexpr`を付けて宣言できる
- リテラル演算子とリテラル演算子テンプレートは、内部リンケージもしくは外部リンケージを持つ可能性がある
- リテラル演算子がデフォルト引数を持つ場合、プログラムは不適格となる


###整数に対するリテラル演算子
整数に対するリテラル演算子は、`unsigned long long`型のパラメータをひとつだけ持つこと。

```cpp
namespace unit_literals {
  // intの大きさを持ち、
  // km (kiro-meter, キロメートル)単位を表すリテラル演算子
  int operator"" _kmi(unsigned long long x)
  {
    return x * 1000;
  }
}

using namespace unit_literals;

// 123km (123,000m)
int distance = 123_kmi;
```

整数リテラルとして負数を記述した場合、リテラル演算子には正数部分のみが渡される。リテラル演算子によって返された値を符号反転することで、負数が表現される：

```cpp
// _kmiリテラル演算子に渡されるのは整数値123LL
int minus_distance = -123_kmi;
```


###浮動小数点数に対するリテラル演算子
浮動小数点数に対するリテラル演算子は、`long double`型のパラメータをひとつだけ持つこと。

```cpp
namespace unit_literals {
  // floatの大きさを持ち、
  // km (kiro-meter, キロメートル)単位を表すリテラル演算子
  float operator"" _kmf(long double x)
  {
    return x * 1000.0f;
  }
}

using namespace unit_literals;

// 123km (123,000m)
float distance = 123.0_kmf;
```

浮動小数点数リテラルとして負数を記述した場合、リテラル演算子には正数部分のみが渡される。リテラル演算子によって返された値を符号反転することで、負数が表現される：

```cpp
// _kmiリテラル演算子に渡されるのは浮動小数点数の値123.0L
float minus_distance = -123.0_kmf;
```


###文字列に対するリテラル演算子
文字列に対するリテラル演算子は、以下のいずれかのパラメータを持つこと：

- `const char*`
- `const char*, std::size_t`
- `const wchar_t*, std::size_t`
- `const char16_t*, std::size_t`
- `const char32_t*, std::size_t`

第1パラメータには文字列リテラルの先頭を指すポインタ、第2パラメータには文字列リテラルの文字配列の要素数が渡される。

```cpp
namespace literals {
  std::u32string operator"" _s(const char32_t* str, std::size_t length)
  {
    return std::u32string(str, length);
  }
}

using namespace literals;
auto str = UR"(こんにちは"世界")"_s;
assert(str.size() == 9);
```
* std::u32string[link /reference/string/basic_string.md]
* str.size()[link /reference/string/basic_string/size.md]
* std::size_t[link /reference/cstddef/size_t.md]
* assert[link /reference/cassert/assert.md]


###リテラル演算子テンプレート
数値リテラルに対してのみ、数値の各文字を分解してコンパイル時定数としてリテラル演算子に渡せる。これは「リテラル演算子テンプレート(literal operator template)」という機能で、非型テンプレートパラメータとして`char`の可変引数テンプレートを受け取るようにすることで、テンプレートパラメータに渡される：

```cpp
namespace literals {
  template <char... Args> // 数値123が文字のシーケンス{'1', '2', '3'}として渡される
  std::string operator"" _s()
  {
    const char str[] = {Args...};
    return std::string(str);
  }
}

using namespace literals;
auto str = 123_s; // strはstd::string型
std::cout << str << std::endl; // "123"
```
* std::string[link /reference/string/basic_string.md]
* std::cout[link /reference/iostream/cout.md]
* std::endl[link /reference/ostream/endl.md] 


###リテラル演算子の規約
注意事項としては、標準C++の規約で、リテラル演算子をユーザーがオーバーロードする場合には以下のことが要求される：

- 非グローバル名前空間にリテラル演算子を定義すること
- リテラル演算子の名前は、アンダースコア `_` で始めること

アンダースコアで始まらないリテラル演算子は、標準C++の将来の拡張のために予約される。


##例
```cpp
#include <iostream>
#include <string>

namespace my_namespace {
inline namespace literals {
  std::string operator"" _s(const char* str, std::size_t length)
  {
    return std::string(str, length);
  }
}}

int main()
{
  using namespace my_namespace::literals;

  auto x = "hello"_s; // xの型はstd::string
  std::cout << x << std::endl;
}
```
* std::string[link /reference/string/basic_string.md]
* std::size_t[link /reference/cstddef/size_t.md]
* std::cout[link /reference/iostream/cout.md]
* std::endl[link /reference/ostream/endl.md]

###出力
```
hello
```


##この機能が必要になった背景・経緯
ユーザー定義リテラルが最初に提案された際の動機は、「`complex<double>(1.1, 1.2)`のようなコンストラクタ呼び出しがあった場合に、それを組み込み型のリテラルと同様にコンパイル時定数としたい」というものだった。この問題に対する解決は[`constexpr`](constexpr.md)機能によって行われたが、ユーザー定義型のためのリテラルを定義できるようにする提案は、動機を変えて残った。

ユーザー定義型に関して、C++には基本的な設計原則がある：

1. ユーザー定義型は、組み込み型と同じ設備(facilities)をサポートできること。C++03では、組み込み型がもつ「リテラル」という機能を、ユーザー定義型に持たせることができなかった
2. C言語との互換性を維持する必要があるが、C99が持つ複素数リテラルを受け入れるための機能がない

ユーザー定義型に対してリテラルを定義できるようにすることで、ユーザー定義型で可能な設計の範囲が増え、C言語が持つ複素数リテラルをC++の機能のなかで実現できるようになる。


##検討されたほかの選択肢
ユーザー定義型に対するリテラルのサポートをする方法は、いくつか段階的に提案された。

まず、リテラルのための特殊なコンストラクタを用意する案：

```cpp
class DecimalFloat {
public:
  DecimalFloat(const char* literalString, double "df", "DF") { … }
};

DecimalFloat f = 12.34df;
```

この案では、先頭のパラメータ`literalString`に`12.34`のようなリテラルの値が文字列として代入され、リテラルに入力される文字を`double`に属する値に限定し、サフィックスとして使用できる文字列として`"df"`と`"DF"`を許可する、というような形式となっていた。

その後の案では、コンストラクタではなく演算子をオーバーロードする形式となった。当時は`DecimalFloat operator"df"(const char*)`のように、サフィックスの名称をダブルクォーテーション内に書くようになっていた。

リテラル名がサフィックスであることを明示的にするために、今日の`DecimalFloat operator"" df(long double)`という形式になった。


##関連項目
- [C++14 リテラル演算子のスペースを省略可能とする](/lang/cpp14/no_whitespace_literal_operators.md)
- [`std::basic_string`の文字列リテラル`s`](/reference/string/basic_string/op_s.md)
- [`std::complex<double`>の浮動小数点数リテラル`i`](/reference/complex/op_i.md)
- [`std::chrono::minutes`の数値リテラル`min`](/reference/chrono/duration/op_min.md)


##参照
- [N1511 Literals for user-defined types](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2003/n1511.pdf)
- [N1892 Extensible Literals](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2005/n1892.pdf)
- [N2282 Extensible Literals](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2007/n2282.pdf)
- [N2378 User-defined literals](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2007/n2378.pdf)
- [N2750 User-defined Literals (aka. Extensible Literals (revision 4))](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2008/n2750.pdf)
- [N2765 User-defined Literals (aka. Extensible Literals (revision 5))](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2008/n2765.pdf)
- [CWG Issue 1479. Literal operators and default arguments](http://www.open-std.org/jtc1/sc22/wg21/docs/cwg_defects.html#1479)

