#current_exception
* exception[meta header]
* std[meta namespace]
* function[meta id-type]
* cpp11[meta cpp]

```cpp
namespace std {
  exception_ptr current_exception() noexcept;
}
```
* exception_ptr[link exception_ptr.md]

##概要
現在処理中の例外オブジェクトを指す[`exception_ptr`](exception_ptr.md)を取得する。


##戻り値
現在処理中の例外オブジェクトを指す[`exception_ptr`](exception_ptr.md)を返す。

処理中の例外オブジェクトがない場合は、ヌルを指す[`exception_ptr`](exception_ptr.md)を返す。


##例外
投げない


##備考
この関数は、`catch`節で使用すれば、処理中の例外オブジェクトへの例外ポインタを取得できる。

ただし、例外送出によるスタック巻き戻し中は、取得できないので注意。(スタック巻き戻し中とは、`try`ブロック中で定義されたオブジェクトのデストラクタのこと)


##例
```cpp
#include <iostream>
#include <exception>
#include <stdexcept>

int main()
{
  std::exception_ptr ep;

  try {
    throw std::runtime_error("error!");
  }
  catch (...) {
    std::cout << "catch" << std::endl;
    ep = std::current_exception(); // 処理中の例外ポインタを取得
  }

  if (ep) {
    std::cout << "rethrow" << std::endl;
    std::rethrow_exception(ep); // 再スロー
  }
}
```
* current_exception[color ff0000]

###出力例
```
catch
rethrow

This application has requested the Runtime to terminate it in an unusual way.
Please contact the application's support team for more information.
terminate called after throwing an instance of 'std::runtime_error'
  what():  error!
```

##バージョン
###言語
- C++11

###処理系
- [Clang](/implementation.md#clang): ??
- [GCC](/implementation.md#gcc): 
- [GCC, C++11 mode](/implementation.md#gcc): 4.6.1
- [ICC](/implementation.md#icc): ??
- [Visual C++](/implementation.md#visual_cpp) ??


##参照
- [N2179 Language Support for Transporting Exceptions between Threads](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2007/n2179.html)
- [Can I use `std::current_exception` during stack unwinding? - StackOverflow](http://stackoverflow.com/questions/28267484/can-i-use-stdcurrent-exception-during-stack-unwinding)

