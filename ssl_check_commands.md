# VPSサーバー SSL設定確認コマンド集

## 1. SSL証明書の基本情報確認

### 証明書の詳細情報を表示
```bash
# ドメイン名を指定して証明書情報を確認
openssl s_client -connect your-domain.com:443 -servername your-domain.com < /dev/null 2>/dev/null | openssl x509 -noout -dates -subject -issuer
```

### 証明書の有効期限を確認
```bash
# 証明書の有効期限のみを表示
echo | openssl s_client -connect your-domain.com:443 -servername your-domain.com 2>/dev/null | openssl x509 -noout -dates
```

### 証明書の詳細情報（包括的）
```bash
# 証明書の全情報を表示
openssl s_client -connect your-domain.com:443 -servername your-domain.com < /dev/null 2>/dev/null | openssl x509 -noout -text
```

## 2. n8nのSSL設定確認

### n8nの設定ファイルを確認
```bash
# n8nの設定ディレクトリを探す
find / -name ".env" -path "*/n8n*" 2>/dev/null
find / -name "config" -path "*/n8n*" 2>/dev/null

# 設定ファイルの内容を確認
cat /path/to/n8n/.env
cat /path/to/n8n/config
```

### n8nプロセスの確認
```bash
# n8nプロセスが実行中か確認
ps aux | grep n8n

# n8nが使用しているポートを確認
netstat -tlnp | grep n8n
```

## 3. セキュリティヘッダーの確認

### HTTPヘッダーの確認
```bash
# セキュリティヘッダーを確認
curl -I https://your-domain.com

# より詳細なヘッダー情報
curl -v https://your-domain.com
```

## 4. SSL/TLS設定の確認

### サポートされている暗号化方式を確認
```bash
# サポートされている暗号化方式を表示
nmap --script ssl-enum-ciphers -p 443 your-domain.com
```

### SSL/TLSバージョンの確認
```bash
# SSL/TLSバージョンを確認
openssl s_client -connect your-domain.com:443 -servername your-domain.com -tls1_2 < /dev/null
openssl s_client -connect your-domain.com:443 -servername your-domain.com -tls1_3 < /dev/null
```

## 5. 外部診断ツールの使用

### SSL Labsでの診断
```bash
# コマンドラインからSSL Labs APIを使用（オプション）
curl -s "https://api.ssllabs.com/api/v3/analyze?host=your-domain.com"
```

## 6. よくある問題と解決方法

### 証明書の期限切れ
```bash
# 証明書の有効期限を確認
echo | openssl s_client -connect your-domain.com:443 -servername your-domain.com 2>/dev/null | openssl x509 -noout -enddate
```

### 証明書のドメイン名不一致
```bash
# 証明書のSAN（Subject Alternative Names）を確認
echo | openssl s_client -connect your-domain.com:443 -servername your-domain.com 2>/dev/null | openssl x509 -noout -text | grep -A1 "Subject Alternative Name"
```

## 7. Let's Encrypt証明書の自動更新確認

### Certbotの設定確認（管理者権限が必要）
```bash
# Certbotがインストールされているか確認
which certbot

# 管理者権限で証明書の一覧を表示
sudo certbot certificates

# rootユーザーのcrontabを確認（自動更新設定）
sudo crontab -l | grep certbot

# Certbotの設定ファイルを確認
sudo ls -la /etc/letsencrypt/
sudo cat /etc/letsencrypt/renewal/your-domain.com.conf
```

### 権限問題の解決方法
```bash
# 方法1: sudoを使用してcertbotを実行
sudo certbot certificates

# 方法2: 特定のディレクトリを指定して実行
certbot --config-dir ~/.certbot --work-dir ~/.certbot --logs-dir ~/.certbot certificates

# 方法3: rootユーザーに切り替え
sudo su -
certbot certificates
exit
```

### 手動更新のテスト
```bash
# 証明書の更新をテスト（実際には更新しない）
sudo certbot renew --dry-run

# 実際に更新する場合
sudo certbot renew
```

## 8. 自動更新の設定

### 自動更新のcron設定を確認・設定
```bash
# rootユーザーのcrontabを確認
sudo crontab -l

# 自動更新のcron設定を追加（例：毎日午前2時に実行）
sudo crontab -e
# 以下の行を追加：
# 0 2 * * * /usr/bin/certbot renew --quiet --deploy-hook "systemctl reload nginx"

# または、既存の設定を確認
sudo cat /etc/cron.d/certbot
```

### 自動更新設定の詳細手順
```bash
# 1. 現在の証明書情報を確認
sudo certbot certificates

# 2. 証明書の更新テスト
sudo certbot renew --dry-run

# 3. 自動更新のcron設定を追加
sudo crontab -e

# 4. 以下の行を追加（毎日午前2時に実行）
0 2 * * * /usr/bin/certbot renew --quiet --deploy-hook "systemctl reload nginx"

# 5. 設定を確認
sudo crontab -l

# 6. ログの確認
sudo tail -f /var/log/letsencrypt/letsencrypt.log
```

### トラブルシューティング
```bash
# 証明書の更新に失敗した場合の対処法
sudo certbot renew --force-renewal

# 証明書の手動更新
sudo certbot certonly --standalone -d your-domain.com

# ログファイルの確認
sudo tail -n 50 /var/log/letsencrypt/letsencrypt.log

# 証明書の詳細情報
sudo certbot certificates --verbose
```

## 9. 自動化スクリプト

### SSL設定確認スクリプト
```bash
#!/bin/bash
# ssl_check.sh

DOMAIN="your-domain.com"

echo "=== SSL設定確認レポート ==="
echo "ドメイン: $DOMAIN"
echo ""

echo "1. 証明書の有効期限:"
echo | openssl s_client -connect $DOMAIN:443 -servername $DOMAIN 2>/dev/null | openssl x509 -noout -dates

echo ""
echo "2. 証明書の発行者:"
echo | openssl s_client -connect $DOMAIN:443 -servername $DOMAIN 2>/dev/null | openssl x509 -noout -issuer

echo ""
echo "3. セキュリティヘッダー:"
curl -I https://$DOMAIN 2>/dev/null | grep -E "(Strict-Transport-Security|X-Frame-Options|X-Content-Type-Options)"

echo ""
echo "4. SSL/TLSバージョン:"
echo "TLS 1.2: $(openssl s_client -connect $DOMAIN:443 -servername $DOMAIN -tls1_2 < /dev/null 2>/dev/null | grep -c 'Verify return code: 0' || echo 'Not supported')"
echo "TLS 1.3: $(openssl s_client -connect $DOMAIN:443 -servername $DOMAIN -tls1_3 < /dev/null 2>/dev/null | grep -c 'Verify return code: 0' || echo 'Not supported')"

echo ""
echo "5. Let's Encrypt証明書の状態:"
if command -v certbot &> /dev/null; then
    if sudo certbot certificates 2>/dev/null | grep -A5 "$DOMAIN"; then
        echo "証明書情報を取得しました"
    else
        echo "証明書情報の取得に失敗しました（権限の問題の可能性）"
    fi
else
    echo "Certbotがインストールされていません"
fi

echo ""
echo "6. 自動更新の設定:"
if sudo crontab -l 2>/dev/null | grep -q certbot; then
    echo "自動更新が設定されています"
    sudo crontab -l | grep certbot
else
    echo "自動更新が設定されていません"
fi

echo ""
echo "7. 推奨アクション:"
if ! sudo crontab -l 2>/dev/null | grep -q certbot; then
    echo "⚠️  自動更新が設定されていません。以下のコマンドで設定してください："
    echo "   sudo crontab -e"
    echo "   0 2 * * * /usr/bin/certbot renew --quiet --deploy-hook \"systemctl reload nginx\""
fi
``` 