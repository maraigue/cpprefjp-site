#add_pointer
* type_traits[meta header]
* std[meta namespace]
* class template[meta id-type]
* cpp11[meta cpp]

```cpp
namespace std {
  template <class T>
  struct add_pointer {
    typedef … type;
  };

  template <class T>
  using add_pointer_t = typename add_pointer<T>::type; // C++14
}
```

##概要
型にポインタを追加する。


##効果
`add_pointer`は、型[`remove_reference`](remove_reference.md)`<T>::type*`を、メンバ型`type`として定義する。  


##例
```cpp
#include <type_traits>

static_assert(std::is_same<std::add_pointer<int>::type, int*>::value, "transform int to int*");
static_assert(std::is_same<std::add_pointer<int&>::type, int*>::value, "transform int& to int*");
static_assert(std::is_same<std::add_pointer<int*>::type, int**>::value, "transform int* to int**");
static_assert(std::is_same<std::add_pointer<int*&>::type, int**>::value, "transform int*& to int**");

int main() {}
```

###出力
```
```

##バージョン
###言語
- C++11

###処理系
- [Clang](/implementation.md#clang): 3.0
- [GCC, C++11 mode](/implementation.md#gcc): 4.3.6
- [Visual C++](/implementation.md#visual_cpp): 9.0 (std::tr1), 10.0, 11.0, 12.0, 14.0
	- `add_pointer_t`は12.0から


##参照
- [N3546 TransformationTraits Redux](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3546.pdf)
- [N3655 TransformationTraits Redux, v2](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2013/n3655.pdf)

