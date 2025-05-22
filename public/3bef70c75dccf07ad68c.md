---
title: "\U0001F536\U0001F537GoogleMapsAPIを利用してひし形のランニングルートを作る\U0001F537\U0001F536"
tags:
  - JavaScript
  - アルゴリズム
  - GoogleMapsAPI
private: false
updated_at: '2025-05-22T18:45:19+09:00'
id: 3bef70c75dccf07ad68c
organization_url_name: null
slide: false
ignorePublish: false
---
# はじめに

私はランニングルートを決めるとき、実際に走りながら新しい道を開拓しています。
もしランニングルートをシミュレーションできる仕組みがあれば面白いと思い、Google Maps APIを使って開発してみたいと思います。

移動ルートは、折り返しではなく、ぐるっと回るような周回コースを作成します。

# 開発

## 1. 事前準備

### APIを有効化

- **Maps JavaScript API**: MAP・Marker・Polylineなどの表示に使用
- **Directions API**: ルート作成に使用

### APIキーを取得しキーをセット

```html:html
<script src="https://maps.googleapis.com/maps/api/js?key=<{$googleMapApiKey}>"></script>
```

これでGoogleマップを使えるようになります。

:::note warn
Google Maps APIキーは、第三者に悪用されると課金される恐れがあります。

HTTPリファラー制限をして、APIキーが指定したドメインからのリクエストにのみ応答するよう制限してください。
:::

## 2. マーカーを設置する

まずは、地図上にマーカーを設置します。

```javascript
// 東京
const LatLng1 = { lat: 35.681236, lng: 139.767125 };

// 地図の初期化
const map = new google.maps.Map(document.getElementById("map"), {
  zoom: 14,
  center: LatLng1
});

// マーカーの設置
const marker = new google.maps.Marker({
  map: map,
  position: LatLng1,
  icon: {
    url: "http://maps.google.com/mapfiles/ms/icons/red-dot.png"
  },
  draggable: true
});
```

また、マーカーオブジェクトの`draggable`を有効化することで、マーカーをドラッグして移動することができるようになります。


<img width="400px;" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/df24a85e-710c-449c-8e9a-e0b2f0faf5b3.png">


## 3. ルートを作る

まずはGoogle Maps API で2点間を結ぶルートを作成します。

markerオブジェクトを2つ作成し、`東京 → 新宿 → 東京`と折り返すルートを作成します。
今回は、スタート地点とゴール地点を東京、2点目の新宿は中継点として`waypoints`に定義します。

```javascript
// 東京
const LatLng1 = { lat: 35.681236, lng: 139.767125 };
// 新宿
const LatLng2 = { lat: 35.6895, lng: 139.6917 };

// 地図の初期化
const map = new google.maps.Map(document.getElementById("map"), {
  zoom: 14,
  center: LatLng1
});

// マーカーの設置
const marker = new google.maps.Marker({
  map: map,
  position: LatLng1,// 東京
  icon: {
    url: "http://maps.google.com/mapfiles/ms/icons/red-dot.png"
  },
  draggable: true
});

const marker2 = new google.maps.Marker({
  map: map,
  position: LatLng2,// 新宿
  icon: {
    url: "http://maps.google.com/mapfiles/ms/icons/blue-dot.png"
  },
  draggable: true
});

const directionsService = new google.maps.DirectionsService();
const directionsRenderer = new google.maps.DirectionsRenderer();
directionsRenderer.setMap(map);

directionsService.route({
    origin: LatLng1, //東京（スタート地点）
    destination: LatLng1, //東京（ゴール地点）
    travelMode: google.maps.TravelMode.WALKING, //徒歩を指定
    waypoints: [
        LatLng2 //新宿（中継）
    ]
  },
  (response, status) => {
    if (status === "OK") {
      directionsRenderer.setDirections(response);
    } else {
      alert("ルートの取得に失敗しました: " + status);
    }
  }
);
```


線が一本で分かりにくいのですが、これで2点を往復するルートができました。

<img width="400px;" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/565e4229-fe39-448f-9674-c6dca2461acf.png">

ここまでがルート作成の基本となります。

## 4. 往復するために4つの点を作る

中継点(`waypoints`)を複数指定して、周遊するルートを作成します。
コードすべてを説明すると長くなるため、ここではロジックの概要のみを説明します。
（記事の最後のほうにGitHubのリンクがあるので気になる方はどうぞ）

### 仕様

- ルートは、A→B→C→D→A を通るルートを作成する
- マーカー配置はひし形で統一し、一辺の長さを同じ距離とする
- 人が設定する箇所としては、マーカーA・Cの座標と移動する全体の距離とし、マーカーBとDはプログラム側で自動計算する
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/85f140e1-27d7-4cbc-b4a4-ba4818a1e6f0.png)

### 全体の距離を4等分する

5Kmを設定した場合、マーカー間の距離は1.25kmとなります。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/90e82ad5-c0ab-456b-93fd-e187afa46661.png)

### 点B・点Dの位置を求める

点B求めるイメージとしては、二等辺三角形を作成し線分ACの距離をGoogleMapsAPIから取得することで点Bを求められます。また、点Dも同様です。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/33da8f24-ed39-4d61-bcd1-fe2fc6c32392.png)

ただしXY座標ではなく地図の座標で計算する必要があり、ACの位置関係的には回転角も考えなければなりません。そのため **ヴィンセンティ法（Vincenty法）** を用いて計算しました。

```js
/**
 * Vincentyの順解法（Direct Method）を使用して、始点から距離と方位角をもとに終点の緯度経度を求める
 *
 * @param {number} lat1 - 始点の緯度（度）
 * @param {number} lng1 - 始点の経度（度）
 * @param {number} azimuthDeg - 始点での方位角（度、北から時計回り）
 * @param {number} distance - 始点からの距離（メートル）
 * @returns {[number, number]} - 終点の [緯度（度）, 経度（度）]
 *
 * @example
 * const [lat2, lng2] = vincenty(35.0, 135.0, 90.0, 10000);
 * console.log(lat2, lng2); // 東方向に10km進んだ地点の緯度経度
 */
function vincenty(lat1, lng1, azimuthDeg, distance) {
    const a = 6378137.0; // 長半径 (WGS84)
    const f = 1 / 298.257222101; // 扁平率
    const b = a * (1 - f); // 短半径

    lat1 = degToRad(lat1);
    lng1 = degToRad(lng1);
    const alpha1 = degToRad(azimuthDeg);

    const U1 = Math.atan((1 - f) * Math.tan(lat1));
    const sigma1 = Math.atan2(Math.tan(U1), Math.cos(alpha1));
    const sinAlpha = Math.cos(U1) * Math.sin(alpha1);
    const cosSqAlpha = 1 - sinAlpha * sinAlpha;

    const uSq = cosSqAlpha * (a * a - b * b) / (b * b);
    const A = 1 + (uSq / 16384) * (4096 + uSq * (-768 + uSq * (320 - 175 * uSq)));
    const B = (uSq / 1024) * (256 + uSq * (-128 + uSq * (74 - 47 * uSq)));

    let sigma = distance / (b * A);
    let sigmaPrev;
    let iterations = 0;

    do {
        sigmaPrev = sigma;
        const twoSigmaM = 2 * sigma1 + sigma;
        const deltaSigma = B * Math.sin(sigma) * (
            Math.cos(twoSigmaM) +
            (B / 4) * (
                Math.cos(sigma) * (-1 + 2 * Math.pow(Math.cos(twoSigmaM), 2)) -
                (B / 6) * Math.cos(twoSigmaM) * (-3 + 4 * Math.pow(Math.sin(twoSigmaM), 2)) * (-3 + 4 * Math.pow(Math.cos(twoSigmaM), 2))
            )
        );
        sigma = distance / (b * A) + deltaSigma;
        iterations++;
    } while (Math.abs(sigma - sigmaPrev) > 1e-12 && iterations < 100);

    const sinSigma = Math.sin(sigma);
    const cosSigma = Math.cos(sigma);
    const sinU1 = Math.sin(U1);
    const cosU1 = Math.cos(U1);

    const lat2 = Math.atan2(
        sinU1 * cosSigma + cosU1 * sinSigma * Math.cos(alpha1),
        (1 - f) * Math.sqrt(
            Math.pow(sinAlpha, 2) +
            Math.pow(sinU1 * sinSigma - cosU1 * cosSigma * Math.cos(alpha1), 2)
        )
    );

    const lambda = Math.atan2(
        sinSigma * Math.sin(alpha1),
        cosU1 * cosSigma - sinU1 * sinSigma * Math.cos(alpha1)
    );

    const C = (f / 16) * cosSqAlpha * (4 + f * (4 - 3 * cosSqAlpha));
    const L = lambda - (1 - C) * f * sinAlpha * (
        sigma + C * sinSigma * (
            Math.cos(2 * sigma1 + sigma) + C * cosSigma * (-1 + 2 * Math.pow(Math.cos(2 * sigma1 + sigma), 2))
        )
    );

    const lng2 = lng1 + L;

    return [radToDeg(lat2), radToDeg(lng2)];
}

/**
 * 度をラジアンに変換する
 *
 * @param {number} x - 変換したい角度（度）
 * @returns {number} ラジアンに変換された角度
 */
function degToRad(x) {
  return x / 180 * Math.PI;
}

/**
 * ラジアンを度に変換する
 *
 * @param {number} x - 変換したい角度（ラジアン）
 * @returns {number} 度に変換された角度
 */
function radToDeg(x) {
  return x * 180 / Math.PI;
}
```

### 描画する

`Polyline`を使ってひし形を表示すると、以下のようになります。

![Animation7.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/181b9212-1006-4c6a-bc0f-216bc47a7fb7.gif)

## 5. ルートを作成する

後は、4つの点を通るようにルートを引いてあげます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/473097/19a360dd-fe08-4268-8073-09afc0a3371f.png)

青い線が作成されたルートです。良い感じですね！
距離は、だいたい指定した距離になります。

# おわりに

今回は、GoogleMapsAPIを用いてランニングルートの作成を紹介しました。
実はこのプログラム4年前に作成し、あまり使わず放置していました。
最近になって改めて見直しており、活用したアイデアなどがあればコメントやご教授いただけると嬉しいです！
（ランニングルートじゃなくてもウェルカムです！）

もしご興味があれば、ぜひ以下のリポジトリをご覧ください。
https://github.com/ishi720/runroute

ここまで読んで頂きありがとうございました。
