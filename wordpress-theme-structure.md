# WordPressカスタムテーマ実装ガイド

## 必要なファイル構成
```
elm-clinic-theme/
├── style.css
├── index.php
├── header.php
├── footer.php
├── functions.php
├── page-campaign.php
├── assets/
│   ├── css/
│   ├── js/
│   └── images/
└── template-parts/
    ├── hero-section.php
    ├── campaign-section.php
    ├── case-studies.php
    └── clinic-info.php
```

## 主要な実装ポイント

### 1. カスタム投稿タイプの作成
```php
// functions.php
function create_custom_post_types() {
    // キャンペーン投稿タイプ
    register_post_type('campaign', [
        'labels' => [
            'name' => 'キャンペーン',
            'singular_name' => 'キャンペーン'
        ],
        'public' => true,
        'has_archive' => true,
        'supports' => ['title', 'editor', 'thumbnail', 'custom-fields']
    ]);
    
    // 症例投稿タイプ
    register_post_type('case_study', [
        'labels' => [
            'name' => '症例',
            'singular_name' => '症例'
        ],
        'public' => true,
        'has_archive' => true,
        'supports' => ['title', 'editor', 'thumbnail', 'custom-fields']
    ]);
}
```

### 2. カスタムフィールドの設定
```php
// Advanced Custom Fields (ACF) プラグインを使用
// キャンペーン用フィールド
- campaign_price (text)
- campaign_original_price (text)
- campaign_description (textarea)
- campaign_image (image)

// 症例用フィールド
- case_procedure (text)
- case_price (text)
- case_risks (textarea)
- case_before_after_image (image)
```

### 3. テンプレートファイルの作成
```php
// page-campaign.php
<?php get_header(); ?>

<section class="hero">
    <?php get_template_part('template-parts/hero-section'); ?>
</section>

<section class="campaign-section">
    <?php get_template_part('template-parts/campaign-section'); ?>
</section>

<?php get_footer(); ?>
```

## 更新しやすくするための工夫

### 1. 管理画面での編集項目
- キャンペーン価格・期間
- 症例写真・説明
- 院長プロフィール
- 営業時間・アクセス情報
- FAQ内容

### 2. 画像管理
- メディアライブラリで画像を管理
- 各セクションで画像を選択可能
- レスポンシブ対応の自動生成

### 3. コンテンツ管理
- カスタム投稿タイプで施術メニュー管理
- カスタムフィールドで価格・説明管理
- ウィジェットでサイドバー情報管理

## 開発期間の目安
- **基本実装**: 1週間
- **完全実装**: 2週間
- **テスト・調整**: 3-5日

## 必要なスキル
- PHP基礎知識
- WordPress開発経験
- HTML/CSS/JavaScript
- データベース設計の基礎 