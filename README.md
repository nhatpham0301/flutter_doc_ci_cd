## CI/CD-FLutter:

- Step 1: install runner on os (MacOS): https://docs.gitlab.com/runner/install/osx.html
- Step 2: đăng ký runner với project hiện tại:
	- Tạo runner với các thông tin như:
		- url project
		- token
		- tag, name runner ,…
		- executor: Đối với macOS nên chọn shell or docker
			- shell là executor mặc định cho gitlab, dễ sử dụng
			- docker thêm vài bước config, có thể chạy chung với các OS khác như linux or Windows
- Step 3: tạo file .gitlab-ci.yml
	- file này dùng để cấu tạo thứ tự chạy lệnh build và làm các hành động đặc biệt.
	- nhập cá lệnh để build file apk hoặc ipa giống như chạy shell dưới local.

### Sử dụng Fastlane để quản lý build và deploy. Cần setup môi trường với các version sau:
- brew: 4.2.16
- Fastlane: 2.219.0
- Ruby: 3.3.0
- Gem 3.5.7
- Bundler: 2.5.7
- Dg (deploygate): 0.9.0
	
### Build Android
- tạo file key.properties và keystore
  - Tạo các biến trên cd/ci sau:
    - $KEY_STORE_PASSWORD
    - $KEY_PASSWORD
    - $KEY_ALIAS
    - tạo file key.properties và viết nội dung sau:
    ```
    touch "key.properties"
    echo "storePassword=$KEY_STORE_PASSWORD\nkeyPassword=$KEY_PASSWORD\nkeyAlias=$KEY_ALIAS\nstoreFile=../keystore" > key.properties
    ```
- Upload to store (fast lane): https://docs.fastlane.tools/actions/upload_to_play_store/#upload_to_play_store
	
### Build iOS (với fastlane):
#### Upload
  - export biến môi trường Xcode-select:
  ```
  export PATH="/usr/local/bin:/usr/bin:/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:$PATH"
  ```
  - Upload to TestFlight: https://docs.fastlane.tools/actions/upload_to_testflight/#upload_to_testflight
  ##### Note: sử dụng p8 để connect API Store: https://docs.fastlane.tools/app-store-connect-api/

### Upload file to Deploygate
- install cmd dg: sudo curl -sSL https://deploygate.com/cli/install | bash
- dg deploy —path

### Build iOS(dev-ad-hoc)
- tạo file export.plist (cấu hình file mobileprovision, teamID, certerficae)
- file example: https://github.com/flutter/flutter/issues/113977
```
flutter build ipa -v --flavor dev -t lib/main_dev.dart --release --export-options-plist=/export.plist
```

### Note:
Có 3 loại tag:
	tag trong config runner (1)
	tag trong file .gitlab-ci.yml (2) 
	tag trong git (3)
- Important: nếu 1 project có nhiều runner thì nên sử dụng tag để phân biệt các runner đó, TH này chỉ có 1 runner ta sẽ sử dụng [Run untagged jobs] mà không cần tag, trong TH này trong (2) không được chứa bất kì tag nào.
- Pipeline sẽ được chạy khi tag trong (2) sẽ được tìm thấy trong (1)

*** FIX BUX ***
Fix path environment: change shell bash to sh
