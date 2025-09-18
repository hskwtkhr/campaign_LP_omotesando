# nanoエディタでのcrontab設定追加手順

## 現在の状況
- nanoエディタが起動している
- rootユーザーのcrontabが空の状態
- 自動更新の設定を追加する必要がある

## 詳細な手順

### 1. 設定行の追加
1. **直接入力**を開始する（nanoエディタは挿入モードがデフォルト）
2. 以下の行を入力する：
   ```
   0 2 * * * /usr/bin/certbot renew --quiet --deploy-hook "systemctl reload nginx"
   ```
3. **Enterキー**を押して改行する

### 2. ファイルの保存と終了
1. **`Ctrl+X`**を押す（終了）
2. **`Y`**を押す（保存するかどうかの確認）
3. **`Enter`キー**を押す（ファイル名の確認）

## nanoエディタの基本操作

### よく使うキー
- **`Ctrl+X`** = 終了
- **`Ctrl+O`** = 保存
- **`Ctrl+G`** = ヘルプ
- **`Ctrl+K`** = 行を切り取り
- **`Ctrl+U`** = 貼り付け

### 画面下部の表示
- `^X Exit` = Ctrl+Xで終了
- `^O WriteOut` = Ctrl+Oで保存
- `^G Get Help` = Ctrl+Gでヘルプ

## トラブルシューティング

### nanoエディタで迷った場合
1. **`Ctrl+X`**を押して終了
2. **`N`**を押して保存しないで終了
3. 再度`sudo EDITOR=nano crontab -e`を実行

### 設定が正しく追加されたか確認
```bash
# 設定を確認
sudo crontab -l

# 期待される出力：
# 0 2 * * * /usr/bin/certbot renew --quiet --deploy-hook "systemctl reload nginx"
```

## 設定の説明

### cron設定の意味
```
0 2 * * * /usr/bin/certbot renew --quiet --deploy-hook "systemctl reload nginx"
```

- `0 2 * * *` = 毎日午前2時に実行
- `certbot renew` = 証明書の更新
- `--quiet` = 静默モード（ログを最小限に）
- `--deploy-hook "systemctl reload nginx"` = 更新後にNginxを再読み込み

## 次のステップ

1. 設定追加後、`sudo crontab -l`で確認
2. `sudo certbot renew --dry-run`で更新テスト
3. ログの確認：`sudo tail -f /var/log/letsencrypt/letsencrypt.log` 