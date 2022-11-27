## マルチスレッド



-----------------------------------


```cpp title="実行環境においてサポートされるスレッド数を返します。"
size_t Threading::GetConcurrency();
```

`戻り値`
:   サポートされるスレッド数


-----------------------------------


```cpp title="条件を満たす配列の要素を並列処理で数えます。"
template <class Fty>
size_t Array<Type>::parallel_count_if(Fty f) const;
```

`f`
:   条件を記述した関数

`戻り値`
:   条件を満たす配列の要素数


-----------------------------------


```cpp title="配列の各要素を引数にした関数の呼び出しを並列実行します。"
template <class Fty>
void Array<Type>::parallel_each(Fty f);

template <class Fty>
void Array<Type>::parallel_each(Fty f) const;
```

`f`
:   非同期で実行する関数

-----------------------------------


```cpp title="配列の各要素を引数にした関数の呼び出しを並列実行し、その結果を同じ順序の配列で返します。"
template <class Fty>
size_t Array<Type>::parallel_map(Fty f) const;
```

`f`
:   非同期で実行する関数

`戻り値`
:   各要素を引数に関数 `f` を実行した戻り値を格納した配列