#reset(C++11)
```cpp
void reset() noexcept;
```

##概要
監視対象をクリアする。


##効果
[`weak_ptr()`](./weak_ptr.md)`.`[`swap`](./swap.md)`(*this)`と同等の効果を持つ。


##戻り値
なし


##例
```cpp
#include <cassert>
#include <memory>

int main()
{
  // wpはspを監視する
  std::shared_ptr<int> sp(new int(3));
  std::weak_ptr<int> wp = sp;

  // spの監視をやめる
  wp.reset();

  assert(wp.use_count() == 0);
}
```

###出力
```
```

##バージョン
###言語
- C++11

###処理系
- [GCC, C++11 mode](/implementation#gcc.md): 4.3.6
- [Clang libc++, C++11 mode](/implementation#clang.md): 3.0
- [ICC](/implementation#icc.md): ?
- [Visual C++](/implementation#visual_cpp.md): ?