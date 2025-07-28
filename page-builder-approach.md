# ページビルダー実装アプローチ

## 推奨プラグイン

### 1. **Elementor Pro** (推奨)
```
メリット：
✅ 直感的なドラッグ&ドロップ編集
✅ 豊富なウィジェット
✅ カスタムCSS対応
✅ レスポンシブ編集

費用：$49/年（個人プラン）
```

### 2. **Divi Builder**
```
メリット：
✅ 美しいテンプレート
✅ 高度なカスタマイズ
✅ ワイヤーフレーム機能

費用：$89/年
```

## 実装手順

### Step 1: 基本セットアップ
1. WordPressをインストール
2. 軽量テーマを選択（例：Astra、GeneratePress）
3. Elementor Proをインストール
4. カスタムCSSを追加

### Step 2: セクション別実装

#### ヒーローセクション
```css
/* カスタムCSS */
.hero-section {
    background: linear-gradient(rgba(0,0,0,0.5), rgba(0,0,0,0.5)), 
                url('院長写真のURL');
    background-size: cover;
    background-position: center;
    min-height: 600px;
    display: flex;
    align-items: center;
    color: white;
}
```

#### キャンペーンセクション
- Elementorの「投稿」ウィジェットを使用
- カスタム投稿タイプ「campaign」を作成
- カスタムフィールドで価格・説明を管理

#### 症例紹介セクション
- 「ギャラリー」ウィジェットを使用
- Before/After画像を管理
- カスタムフィールドで症例詳細を管理

### Step 3: 管理しやすくする工夫

#### カスタム投稿タイプの作成
```php
// functions.php に追加
function create_campaign_post_type() {
    register_post_type('campaign', [
        'labels' => [
            'name' => 'キャンペーン',
            'singular_name' => 'キャンペーン'
        ],
        'public' => true,
        'has_archive' => true,
        'supports' => ['title', 'editor', 'thumbnail'],
        'menu_icon' => 'dashicons-megaphone'
    ]);
}
add_action('init', 'create_campaign_post_type');
```

#### カスタムフィールドの追加
```php
// Advanced Custom Fields プラグインを使用
// フィールドグループ：キャンペーン情報
- 価格 (text)
- 元価格 (text)
- 施術説明 (textarea)
- キャンペーン画像 (image)
```

## 更新作業の流れ

### キャンペーン更新
1. 管理画面 → キャンペーン → 新規追加
2. タイトル、説明、価格を入力
3. 画像をアップロード
4. 公開

### 症例更新
1. 管理画面 → 症例 → 新規追加
2. Before/After画像をアップロード
3. 施術内容・価格を入力
4. 公開

### 基本情報更新
1. 管理画面 → 外観 → カスタマイズ
2. サイト情報、営業時間を更新
3. 保存

## メリット・デメリット

### メリット
✅ 非技術者でも更新可能
✅ 短期間で実装（3-5日）
✅ 視覚的な編集が可能
✅ テンプレートの再利用が簡単

### デメリット
❌ 完全なデザイン再現が困難
❌ パフォーマンスが劣る可能性
❌ プラグイン依存
❌ カスタマイズに制限がある

## 推奨プラグイン構成
- **Elementor Pro** - ページビルダー
- **Advanced Custom Fields Pro** - カスタムフィールド
- **Yoast SEO** - SEO対策
- **WP Rocket** - キャッシュ・最適化
- **Wordfence** - セキュリティ

## 実装期間
- **基本セットアップ**: 1-2日
- **デザイン実装**: 2-3日
- **コンテンツ移行**: 1-2日
- **テスト・調整**: 1日

**合計**: 約1週間 