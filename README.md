# My Journal App

Firebase (Firestore & Authentication) と TailwindCSS で動作する、シンプルなシングルページ型のジャーナリング（日記）Webアプリケーションです。

## 🌟 特徴
- **Google認証ログイン:** Googleアカウントを使用して安全に個別の日記データを管理します。
- **カレンダー/リスト表示:** 月ごとのカレンダー表示と時系列のリスト表示を切り替えられます。
- **データ更新機能:** 過去に作成した日記をあとから編集できます。

---

## 🛠️ セットアップ手順

このアプリを自身の環境で動作させるには、Firebaseのプロジェクトを作成し、APIキー等の設定情報をコードに反映させる必要があります。

### 1. Firebase プロジェクトの作成
1. [Firebase Console](https://console.firebase.google.com/) にアクセスし、新しいプロジェクトを作成します。
2. 作成後、ダッシュボードから **「ウェブアプリ (</>)」** を追加し、表示された `firebaseConfig` の値をコピーします。

### 2. Firebase設定の有効化
本アプリでは以下の2つの機能を使用するため、Firebase側で有効化設定を行ってください。

#### A. Authentication (Google認証)
1. Firebase コンソールのメニューから **「Authentication」** > **「始める」** をクリックします。
2. **「Sign-in method」** タブから **「Google」** を選択して有効化します。

#### B. Cloud Firestore
1. メニューから **「Firestore Database」** > **「データベースの作成」** をクリックします。
2. ロケーションなどを選択して作成します。
3. **「ルール」** タブで、ログインしたユーザーのみが自身のデータを読み書きできるようにルールを設定します。
   ```javascript
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /journals/{document} {
         allow read, write: if request.auth != null && request.auth.uid == resource.data.uid;
         allow create: if request.auth != null && request.auth.uid == request.resource.data.uid;
       }
     }
   }
   ```
4. **「インデックス」** の設定:
   日記データは日付順にソートして取得されるため、複合インデックスが必要になります。
   - **コレクションID:** `journals`
   - **インデックスするフィールド:** 
     - `uid` (昇順 または 降順)
     - `createdAt` (降順)
   - **クエリの範囲:** `コレクション`

### 3. APIキーの設定
`index.html` の 84行目付近にある `firebaseConfig` に、手順1でコピーした設定情報を貼り付けます。

```javascript
// index.html
const firebaseConfig = {
   apiKey: "YOUR_API_KEY",
   authDomain: "YOUR_AUTH_DOMAIN",
   projectId: "YOUR_PROJECT_ID",
   storageBucket: "YOUR_STORAGE_BUCKET",
   messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
   appId: "YOUR_APP_ID"
};
```

---

## 🚀 起動方法
本アプリは静的HTMLファイルです。ローカルで動作確認を行うには、簡易的なWebサーバーで起動することをお勧めします。

#### Python を使う場合 (推奨)
1. コマンドプロンプトやターミナルで本ディレクトリを開きます。
2. 以下のコマンドを実行します。
   ```bash
   python -m http.server 8000
   ```
3. ブラウザで [http://localhost:8000](http://localhost:8000) にアクセスします。
