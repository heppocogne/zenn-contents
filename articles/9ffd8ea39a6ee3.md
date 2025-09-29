---
title: "単体テスト等で使える便利なmock関数"
emoji: "🍣"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [cpp,UnitTest]
published: true
---
基本的には関数を呼ばれた回数を記録するだけの関数オブジェクトです。  
例えばインターフェイスを抽象化してロジックから分離してある場合に、テストでインターフェイス側がちゃんと動いているかを見ることができます。
```cpp
#include <functional>


template<typename R>
class mock_function;


template<typename R, typename ...ArgTypes>
class mock_function<R(ArgTypes...)>
{
    std::function<R(ArgTypes...)> f;
    int count = 0;

public:
    mock_function() {};
    mock_function(const std::function<R(ArgTypes...)>& _f)
    {
        init(_f);
    };

    void init(const std::function<R(ArgTypes...)>& _f)
    {
        count = 0;
        f = _f;
    }

    void reset_count()
    {
        count = 0;
    }

    int get_count()
    {
        return count;
    }

    bool is_invoked()
    {
        return 0 < count;
    }

    R operator()(ArgTypes... args)
    {
        count++;
        return f(args...);
    }
};
```

使用例:
```cpp
#include <iostream>


int main(void)
{
    auto m0 = mock_function<void(void)>([](){ return; });
    auto m1 = mock_function<void()>([](){ return; });
    auto m2 = mock_function<int(int)>();
    m2.init([](int){ return 0; });

    m0();
    m1();
    m0();
    std::cout << m0.get_count() << std::endl;
    std::cout << m1.is_invoked() << std::endl;
    std::cout << m2(10) << std::endl;

    return 0;
}
```
結果:
```
2
1
0
```

GitHub:
https://github.com/heppocogne/mock_function
