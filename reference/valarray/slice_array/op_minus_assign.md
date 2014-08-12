#operator-=
```cpp
void operator-=(const valarray<T>& xs) const;
```

##概要
減算の複合演算を行う。


##効果
元となる`valarray`オブジェクトから参照によって抽出した各要素から、`xs`の各要素を減算する。


##戻り値
なし


##備考
`valarray`から抽出した要素数と`xs`の要素数が異なる場合、その挙動は未定義。



##例
```cpp
#include <iostream>
#include <valarray>

int main()
{
  std::valarray<int> v = {1, 2, 3, 4, 5, 6};

  const std::size_t start = 1u;  // 開始位置
  const std::size_t length = 3u; // 要素数
  const std::size_t stride = 2u; // 何個置きに処理するか

  std::slice_array<int> result = v[std::slice(start, length, stride)];

  // 抽出した要素から2を減算する
  result -= std::valarray<int>(2, length);

  for (int x : v) {
    std::cout << x << std::endl;
  }
}
```

###出力
```
1
0
3
2
5
4
```

