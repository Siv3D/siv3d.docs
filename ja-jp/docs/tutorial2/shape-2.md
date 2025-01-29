# 27. 図形の操作
図形に対して、平行移動・拡大縮小・回転・太らせるなどの操作を行う方法を学びます。

## 27.1 図形を移動させる
- 多くの図形クラスが、自身を**指定したベクトルで平行移動した新しい図形**を作成して返す `.movedBy()` メンバ関数を持っています

| コード | 説明 |
|---|---|
| `.movedBy(X軸方向の移動量, Y軸方向の移動量)` | 指定したベクトルで平行移動した図形を作成して返します |
| `.movedBy(移動量)` | 指定したベクトルで平行移動した図形を作成して返します |

- 図形を直接移動させる場合は、`.moveBy()` メンバ関数を使います
	- 戻り値は `void` です

| コード | 説明 |
|---|---|
| `.moveBy(X軸方向の移動量, Y軸方向の移動量)` | 指定したベクトルで平行移動します |
| `.moveBy(移動量)` | 指定したベクトルで平行移動します |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/1.png)

```cpp

```

## 27.2 図形を拡大縮小する（ピクセル指定）
- `Rect` や `RectF`, `Line` など一部の図形クラスは、自身の**幅や高さを変更した新しい図形**を作成して返す `.stretched()` メンバ関数を持っています

| コード | 説明 |
|---|---|
| `rect.stretched(上下左右)` | 長方形（`Rect` または `RectF`）の上下左右方向の拡大縮小量（ピクセル）を指定して新しい長方形を作成して返します |
| `rect.stretched(左右, 上下)` | 長方形（`Rect` または `RectF`）の左右方向と上下方向の拡大縮小量（ピクセル）を指定して新しい長方形を作成して返します |
| `rect.stretched(上, 右, 下, 左)` | 長方形（`Rect` または `RectF`）の上右下左方向の拡大縮小量（ピクセル）を指定して新しい長方形を作成して返します |
| `line.stretched(両端)` | 線分（`Line`）の始点と終点の拡大縮小量（ピクセル）を指定して新しい線分を作成して返します |
| `line.stretched(始点, 終点)` | 線分（`Line`）の始点と終点の拡大縮小量（ピクセル）を指定して新しい線分を作成して返します |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/2.png)

```cpp

```


## 27.3 図形を拡大縮小する（倍率指定）
- 一部の図形クラスは、自身を**指定した倍率で拡大縮小した新しい図形**を作成して返す `.scaled()`, `.scaledAt()` メンバ関数を持っています

| コード | 説明 |
|---|---|
| `.scaled(倍率)` | 図形を指定した倍率で拡大縮小した新しい図形を作成して返します |
| `.scaled(幅の倍率, 高さの倍率)` | 図形を指定した幅と高さの倍率で拡大縮小した新しい図形を作成して返します |
| `.scaledAt(拡大縮小の基準点, 倍率)` | 図形を指定した倍率で拡大縮小した新しい図形を作成して返します |
| `.scaledAt(拡大縮小の基準点, 幅の倍率, 高さの倍率)` | 図形を指定した幅と高さの倍率で拡大縮小した新しい図形を作成して返します |


`Polygon` は自身を拡大縮小した新しい `Polygon` を返す `.scaled()` や、回転した `Polygon` を返す `.rotated()`, `.rotatedAt()` などのメンバ関数を持ちます。また、`Shape2D` は `Polygon` に変換可能です。

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/3.png)

```cpp

```


## 27.4 図形を回転させる

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/4.png)

```cpp

```



## 27.5 多角形を太らせる
- `Polygon` は、自身を**指定した太さで太らせた新しい多角形**を作成して返す `.calculateBuffer()`, `.calculateRoundBuffer()` メンバ関数を持っています

| コード | 説明 |
|---|---|
| `.calculateBuffer(太さ)` | 多角形を指定した太さで太らせた新しい多角形を作成して返します |
| `.calculateRoundBuffer(太さ)` | 多角形を指定した太さで太らせた新しい多角形を作成して返します（角を丸めます） |

![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial2/shape-2/5.png)

```cpp

```

