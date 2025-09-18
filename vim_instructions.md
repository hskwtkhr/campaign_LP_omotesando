# Vimエディタでのcrontab設定追加手順

## 現在の状況
- Vimエディタが開いている
- rootユーザーのcrontabが空の状態
- 自動更新の設定を追加する必要がある

## 詳細な手順

### 1. 設定行の追加
1. **`i`キー**を押して「挿入モード」に入る
2. 以下の行を入力する：
   ```
   0 2 * * * /usr/bin/certbot renew --quiet --deploy-hook "systemctl reload nginx"
   ```
3. **`Esc`キー**を押して「コマンドモード」に戻る

### 2. ファイルの保存と終了
1. **`:wq`**と入力して**Enter**キーを押す
   - `:w` = ファイルを保存（write）
   - `:q` = エディタを終了（quit）

### 3. 設定の確認
エディタが閉じられたら、以下のコマンドで設定を確認：
```bash
sudo crontab -l
```

## トラブルシューティング

### Vimエディタで迷った場合
- **`Esc`キー**を押してコマンドモードに戻る
- **`:q!`**と入力して**Enter**キーを押す（保存せずに終了）
- 再度`sudo crontab -e`を実行

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