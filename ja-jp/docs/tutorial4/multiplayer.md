# 75. マルチプレイヤー
Photon SDK を使用して、Siv3D でマルチプレイヤーゲームを作成する方法を説明します。

## 75.1 Photon SDK の準備

### 75.1.1 Photon SDK のダウンロード
1. 開発環境に応じた [Photon Realtime SDK :material-open-in-new:](https://www.photonengine.com/ja/sdks#realtime-cpp){:target="_blank"} (7z 形式で圧縮) をダウンロードします。Siv3D v0.6.15 で検証済みの SDK バージョンは `v5.0.12` です
2. ダウンロードしたファイルを展開し、適当な場所に配置します（これ以降の手順でプロジェクトのインクルード / ライブラリパスをこのフォルダパスに対して設定するため、これ以降は移動させません）

### 75.1.2 Siv3D プロジェクトの準備
1. 通常どおり Siv3D アプリケーションプロジェクトを作成します
2. Siv3D SDK フォルダ内* の `Aaddon/Multiplayer_Photon` フォルダから 3 つのファイル `Multiplayer_Photon.hpp`, `Multiplayer_Photon.cpp`, `PHOTON_APP_ID.SECRET` をコピーして、プロジェクトの Main.cpp があるフォルダに配置します
3. Multiplayer_Photon ライブラリを自分のプロジェクトで使うために、コピーした `Multiplayer_Photon.hpp` と `Multiplayer_Photon.cpp` をプロジェクトに追加し、ビルド対象に含むようにします。ただし、このままでは Photon SDK へのインクルード・ライブラリパスが通っていないため、ビルドには失敗します
4. （Windows の場合）プロジェクトの設定で、**インクルードディレクトリ**と**ライブラリディレクトリ**それぞれに、ダウンロードした Photon SDK フォルダのパス（例: `C:/Users/siv3d/Desktop/libs/Photon-Windows-Sdk_v5-0-12-0s`）を追加します
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1a.png)
5. （macOS の場合）プロジェクトの設定で、ダウンロードした Photon SDK フォルダのパスを Build Settings の **Header Search Paths** に追加し、**Library Search Paths** に `●●●/Common-cpp/lib`, `●●●/LoadBalancing-cpp/lib`, `●●●/Photon-cpp/lib`, `●●●/3rdparty/lib/apple` の 4 つのパスを追加 (●●● は Photon SDK フォルダのパス) したうえで、Build Phases の **Link Binary With Libraries** に、それらのフォルダの中身のうち `libCommon-cpp_release_macosx.a`, `libLoadBalancing-cpp_release_macosx.a`, `libPhoton-cpp_release_macosx.a`, `libcrypto_release_macosx.a` の 4 ファイルを追加します 
6. これでビルドができればプロジェクトの準備は完了です

!!! info "Siv3D SDK フォルダ"
    - Siv3D をインストールしたときに作成されるフォルダです
        - macOS の場合は、ダウンロードした Siv3D SDK 自身です
        - Windows の場合は、デフォルトでドキュメントフォルダに `OpenSiv3D_0.6.*` という名前で作成されます

### 75.1.3 Photon App ID の設定
1. Photon Web サイトログイン後、ダッシュボード画面を開きます
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1b.png)
2. ダッシュボード画面の **CREATE A NEW APP** を押して情報を入力し、**CREATE** から新しい Photon App ID を発行します。**Photon Type** は **Realtime** を選択します。それ以外の入力項目は任意です
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1c.png)
3. 発行される Photon App ID は `"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` のようなランダムな英数字です
4. **75.1.2** でプロジェクトに追加した `PHOTON_APP_ID.SECRET` に書かれているプレースホルダーの App ID `"00000000-0000-0000-0000-000000000000"` を、発行された Photon App ID で置き換えます
5. （プロジェクトを git 管理している場合）この Photon App ID は第三者に知られてはいけません。`.gitignore` を用いて、`PHOTON_APP_ID.SECRET` を管理対象から外すようにしましょう

## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```


## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```


## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```


## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```



## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```



## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```



## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```



## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```



## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```



## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```



## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```



## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```



## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```



## 75.X XXXXX
- XXX
	
![](https://raw.githubusercontent.com/Siv3D/siv3d.site.resource/main/2025/tutorial4/multiplayer/1.png)

```cpp

```


