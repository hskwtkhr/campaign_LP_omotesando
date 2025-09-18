# VPSサーバー SSL設定確認 - ステップバイステップガイド

## ステップ1: 現在の状況確認

### 1.1 SSL証明書の基本情報確認
```bash
# ドメイン名を指定して証明書情報を確認
echo | openssl s_client -connect bernecomunicacon.net:443 -servername bernecomunicacon.net 2>/dev/null | openssl x509 -noout -dates -subject -issuer
```

**期待される結果：**
- `notBefore=` と `notAfter=` で有効期限が表示される
- `subject=` で証明書の対象ドメインが表示される
- `issuer=` で証明書の発行者が表示される

### 1.2 結果の確認
- 証明書が有効かどうか
- 期限がいつまでか
- 発行者がLet's Encryptかどうか

## ステップ2: Let's Encrypt証明書の確認

### 2.1 Certbotの確認
```bash
# Certbotがインストールされているか確認
which certbot
```

### 2.2 証明書の詳細確認
```bash
# 管理者権限で証明書の一覧を表示
sudo certbot certificates
```

## ステップ3: 自動更新の設定確認

### 3.1 現在のcrontab設定確認
```bash
# rootユーザーのcrontabを確認
sudo crontab -l
```

### 3.2 自動更新の設定追加
```bash
# 自動更新の設定を直接追加
echo "0 2 * * * /usr/bin/certbot renew --quiet --deploy-hook \"systemctl reload nginx\"" | sudo crontab -
```

## ステップ4: 設定の確認

### 4.1 設定が正しく追加されたか確認
```bash
# 設定を確認
sudo crontab -l
```

### 4.2 期待される出力
```
0 2 * * * /usr/bin/certbot renew --quiet --deploy-hook "systemctl reload nginx"
```

## ステップ5: 更新テスト

### 5.1 証明書の更新テスト
```bash
# 証明書の更新をテスト（実際には更新しない）
sudo certbot renew --dry-run
```

## ステップ6: 最終確認

### 6.1 セキュリティヘッダーの確認
```bash
# セキュリティヘッダーを確認
curl -I https://bernecomunicacon.net
```

### 6.2 n8nの設定確認
```bash
# n8nプロセスが実行中か確認
ps aux | grep n8n
```

## トラブルシューティング

### よくある問題と解決法
1. **権限の問題**
   - sudo を使用して実行
   - 適切なユーザー権限を確認

2. **証明書の期限切れ**
   - 手動更新：`sudo certbot renew`
   - 自動更新の設定確認

3. **自動更新の設定失敗**
   - 直接コマンドで追加
   - crontabの書式確認

## 完了チェックリスト

- [ ] SSL証明書が有効
- [ ] 証明書の期限が確認済み
- [ ] Let's Encrypt証明書が確認済み
- [ ] 自動更新が設定済み
- [ ] 更新テストが成功
- [ ] セキュリティヘッダーが確認済み
- [ ] n8nプロセスが確認済み 