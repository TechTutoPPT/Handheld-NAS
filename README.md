[![](https://github.com/TechTutoPPT/Handheld-NAS/blob/main/IMG_8234.png)](https://youtu.be/K3hJTjUzWzI)

準備材料:
建議一部不少於128GB容量, Android 7.0以上版本的手機
官網下載Termux: https://f-droid.org/zh_Hant/packages/com.termux/

安裝好下載回來的Termux apk後, 先執行以下指令:
更新套件:
```
pkg update && pkg upgrade -y
```
授權Termux存取裝置的內部儲存空間:
```
termux-setup-storage
```
防址Termux被休眠:
```
termux-wake-lock
```

安裝及配置SSH服務(可選, 我是覺得以電腦透過SSH連接手機方便敲代碼):
安裝SSH服務:
```
pkg install openssh
```
啟動SSH服務:
```
sshd
```
假如想啟用Termux時自動啟動SSH服務, 可修改.bashrc檔案:
```
nano .bashrc
```
於檔案內容添加:
```
sshd
```
查看使用者名稱:
```
whoami
```
查看wlan0 IP地址:
```
ifconfig
```
設定登入密碼:
```
passwd
```
電腦透過終端機執行:
```
ssh 做用者名稱@IP地址 -p 8022
```

安裝Nextcloud及相開套件:
```
pkg install php apache2 php-apache sqlite wget unzip
```
配置Apache設定:
```
sed -i 's/DirectoryIndex index.html/DirectoryIndex index.php index.html/g' $PREFIX/etc/apache2/httpd.conf
sed -i 's/#LoadModule mpm_prefork_module/LoadModule mpm_prefork_module/g' $PREFIX/etc/apache2/httpd.conf
sed -i 's/LoadModule mpm_worker_module/#LoadModule mpm_worker_module/g' $PREFIX/etc/apache2/httpd.conf
cat << EOF >> $PREFIX/etc/apache2/httpd.conf
LoadModule php_module /data/data/com.termux/files/usr/libexec/apache2/libphp.so 
<FilesMatch \.php$>
  SetHandler application/x-httpd-php
</FilesMatch> 
ServerName localhost
EOF
```
創建一個PHP資訊頁檔案:
```
echo '<?php phpinfo(); ?>' > $PREFIX/share/apache2/default-site/htdocs/index.php
```
啟動Apache服務:
```
apachectl start
```
測試Apache及PHP是否運作正常, 於瀏覽器中輸入:
```
手機IP:8080
```
查看Nextcloud最新版本:
```
https://download.nextcloud.com/server/releases/?C=S;O=D
```
下載Nextcloud套件:
```
wget https://download.nextcloud.com/server/releases/nextcloud-31.0.9.zip
```
解壓檔案:
```
unzip nextcloud*.zip
```
複製Nextclound所有檔案(包括隱藏檔)到Apache存放網頁的位置:
```
mv -f nextcloud/* nextcloud/.* $PREFIX/share/apache2/default-site/htdocs/
```
刪除安裝後的殘留目錄及檔案:
```
rm nextcloud*.zip
rm -rf nextcloud
```
解決未安裝PHP模組GD錯誤:
```
pkg install php-gd patchelf
patchelf –add-needed libphp.so $PREFIX/lib/php/gd.so
apachectl restart
```
假如想啟用Termux時自動啟動Nextcloud服務, 可修改.bashrc檔案, 於檔案內容尾行添加apachectl start
首次進入Nextcloud頁面, 於瀏覽器中輸入:
```
手機IP:8080
```
輸入新管理員號名稱及密碼>設定資料庫組態選SQLite>其他不變然後點擊安裝,
完成後便會進入Nextcloud的Dashboard, 到此已完成整個安裝過程, 你可以愉快地使用Nextcloud的各種功能了.

