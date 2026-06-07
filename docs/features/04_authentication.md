## 4章.認証機能
- [ログイン画面](#ログイン画面)
- [ログイン状態確認](#ログイン状態確認)
- [新規アカウント作成画面](#新規アカウント作成画面)
- [パスワード再設定画面](#パスワード再設定画面)
- [ログアウト](#ログアウト)
- [認証機能で使う主なデータ](#認証機能で使う主なデータ)

### ログイン画面
ログイン画面では、ユーザー名とパスワードを入力してログイン処理を行う<br>

- ログインボタンを押下した場合<br>
React側<br>
　`LoginPageコンポーネント`:<br>
　　ユーザー名とパスワードを入力する<br>
　　ログインボタン押下後、ログイン処理を呼び出す<br>
　　入力内容を`Appコンポーネント`へ渡す<br>
　`Appコンポーネント`:<br>
　　POST /api/loginでリクエスト送信<br>
<br>
Java側<br>
　`SecurityConfig`:<br>
　　POST /api/loginのログイン処理が動く<br>
　　`AppUserDetailsService`へユーザー検索を依頼する<br>
　`AppUserDetailsService`:<br>
　　ユーザー名をもとにユーザーを検索する<br>
　　利用可能なユーザーか確認する<br>
　`SecurityConfig`:<br>
　　パスワードを確認する<br>
　　ログインできるか判定する<br>

- ログインできた場合<br>
React側<br>
　`Appコンポーネント`:<br>
　　ログイン中ユーザーの情報を取得する<br>
　　GET /api/auth/statusでリクエスト送信<br>
<br>
Java側<br>
　`AuthController`:<br>
　　ログイン状態を確認する<br>
　　ユーザー名、権限、表示名を`AuthStatusResponse`で返す<br>
<br>
React側<br>
　`Appコンポーネント`:<br>
　　ログイン情報をauthに保存する<br>
　　管理者ユーザーは商品管理画面へ移動する<br>
　　一般ユーザーは商品選択画面へ移動する<br>

- ログインに失敗した場合<br>
React側<br>
　`Appコンポーネント`:<br>
　　POST /api/auth/login-checkでリクエスト送信<br>
<br>
Java側<br>
　`AuthController`:<br>
　　ユーザー名とパスワードが一致するか確認する<br>
　　利用可能なユーザーか確認する<br>
　　matchedとenabledを返す<br>
<br>
React側<br>
　`Appコンポーネント`:<br>
　　利用停止中の場合は専用メッセージを表示する<br>
　　それ以外の場合はログイン失敗メッセージを表示する<br>

- ここで確認すること<br>
　ログイン本体はSpring Securityが判定している<br>
　ログイン成功後にログイン中ユーザー情報を取得している<br>
　利用停止中かどうかの表示はlogin-checkで補足している<br>

- 参照ファイル<br>
React側<br>
　mobileorder-react/src/pages/LoginPage.jsx<br>
　mobileorder-react/src/App.jsx<br>
<br>
Java側<br>
　config/SecurityConfig.java<br>
　service/AppUserDetailsService.java<br>
　controller/AuthController.java<br>
　dto/AuthStatusResponse.java<br>
　dto/LoginCheckRequest.java<br>

### ログイン状態確認
ログイン状態確認では、画面表示時に現在ログインしているユーザー情報を取得する<br>

- 画面表示時<br>
React側<br>
　`Appコンポーネント`:<br>
　　ログイン状態確認処理を実行する<br>
　　GET /api/auth/statusでリクエスト送信<br>
<br>
Java側<br>
　`AuthController`:<br>
　　ログイン状態を確認する<br>

- ログインしていない場合<br>
Java側<br>
　`AuthController`:<br>
　　ログインしていない状態として返す<br>
<br>
React側<br>
　`Appコンポーネント`:<br>
　　authをnullにする<br>
　　ログイン画面、新規アカウント作成画面、パスワード再設定画面だけ表示できる<br>

- ログイン済みの場合<br>
Java側<br>
　`AuthController`:<br>
　　ログイン中のユーザー名を取得する<br>
　　`AppUserRepository`でユーザー情報を取得する<br>
　　ユーザー名、権限、表示名を`AuthStatusResponse`で返す<br>
<br>
React側<br>
　`Appコンポーネント`:<br>
　　authにログイン情報を保存する<br>
　　権限に応じて表示できる画面を切り替える<br>

- ここで確認すること<br>
　authがあるかどうかでログイン状態を判断している<br>
　auth.roleがadminかuserかで画面を切り替えている<br>

- 参照ファイル<br>
React側<br>
　mobileorder-react/src/App.jsx<br>
<br>
Java側<br>
　controller/AuthController.java<br>
　dto/AuthStatusResponse.java<br>
　repository/AppUserRepository.java<br>

### 新規アカウント作成画面
新規アカウント作成画面では、ユーザー管理番号を使って一般ユーザーを登録する<br>

- 登録ボタンを押下した場合<br>
React側<br>
　`SignupPageコンポーネント`:<br>
　　ユーザー管理番号、ユーザー名、パスワード、確認用パスワードを入力する<br>
　　新規アカウント作成処理を呼び出す<br>
　　入力内容を`Appコンポーネント`へ渡す<br>
　`Appコンポーネント`:<br>
　　POST /api/signupでリクエスト送信<br>
<br>
Java側<br>
　`AccountController`:<br>
　　POST /api/signupのリクエスト受取<br>
　　`SignupRequest`で入力内容を受け取る<br>
　　`AccountService`へ処理を渡す<br>
　`AccountService`:<br>
　　パスワードと確認用パスワードが一致するか確認する<br>
　　同じユーザー名が既に使われていないか確認する<br>
　　ユーザー管理番号が存在するか確認する<br>
　　USER-CODEから始まる管理番号か確認する<br>
　　未使用の管理番号か確認する<br>
　　パスワードをBCryptで暗号化する<br>
　　`AppUser`を一般ユーザーとして登録する<br>
　　ユーザー管理番号を使用済みにする<br>

- 登録できた場合<br>
React側<br>
　`SignupPageコンポーネント`:<br>
　　ログイン画面へ移動する<br>

- 登録に失敗した場合<br>
React側<br>
　`SignupPageコンポーネント`:<br>
　　Java側から返されたメッセージを表示する<br>

- ここで確認すること<br>
　新規登録にはUSER-CODEから始まる未使用のユーザー管理番号が必要になる<br>
　パスワードはそのまま保存せず、BCryptで暗号化して保存している<br>

- 参照ファイル<br>
React側<br>
　mobileorder-react/src/pages/SignupPage.jsx<br>
　mobileorder-react/src/App.jsx<br>
<br>
Java側<br>
　controller/AccountController.java<br>
　service/AccountService.java<br>
　dto/SignupRequest.java<br>
　entity/AppUser.java<br>
　entity/UserManagementCode.java<br>

### パスワード再設定画面
パスワード再設定画面では、使用済みのユーザー管理番号とユーザー名を使ってパスワードを更新する<br>

- 再設定ボタンを押下した場合<br>
React側<br>
　`PasswordResetPageコンポーネント`:<br>
　　ユーザー管理番号、ユーザー名、新しいパスワードを入力する<br>
　　パスワード再設定処理を呼び出す<br>
　　入力内容を`Appコンポーネント`へ渡す<br>
　`Appコンポーネント`:<br>
　　POST /api/password-resetでリクエスト送信<br>
<br>
Java側<br>
　`AccountController`:<br>
　　POST /api/password-resetのリクエスト受取<br>
　　`PasswordResetRequest`で入力内容を受け取る<br>
　　`AccountService`へ処理を渡す<br>
　`AccountService`:<br>
　　新しいパスワードと確認用パスワードが一致するか確認する<br>
　　ユーザー管理番号が存在するか確認する<br>
　　ユーザー管理番号が使用済みであることを確認する<br>
　　管理番号に紐づくユーザー名と入力されたユーザー名が一致するか確認する<br>
　　対象ユーザーを取得する<br>
　　新しいパスワードをBCryptで暗号化して保存する<br>

- 再設定できた場合<br>
React側<br>
　`PasswordResetPageコンポーネント`:<br>
　　ログイン画面へ移動する<br>

- 再設定に失敗した場合<br>
React側<br>
　`PasswordResetPageコンポーネント`:<br>
　　Java側から返されたメッセージを表示する<br>

- ここで確認すること<br>
　パスワード再設定では、すでに使用済みのユーザー管理番号を使う<br>
　管理番号とユーザー名が一致しない場合は再設定できない<br>

- 参照ファイル<br>
React側<br>
　mobileorder-react/src/pages/PasswordResetPage.jsx<br>
　mobileorder-react/src/App.jsx<br>
<br>
Java側<br>
　controller/AccountController.java<br>
　service/AccountService.java<br>
　dto/PasswordResetRequest.java<br>
　entity/UserManagementCode.java<br>
　entity/AppUser.java<br>

### ログアウト
ログアウトでは、共通ヘッダーから確認モーダルを表示してログイン状態を解除する<br>

- ログアウトボタンを押下した場合<br>
React側<br>
　`Headerコンポーネント`:<br>
　　ログアウト処理を呼び出す<br>
　`Appコンポーネント`:<br>
　　確認モーダルを表示する<br>

- ログアウトを確定した場合<br>
React側<br>
　`ConfirmModalコンポーネント`:<br>
　　確定処理を呼び出す<br>
　`Appコンポーネント`:<br>
　　POST /api/logoutでリクエスト送信<br>
<br>
Java側<br>
　`SecurityConfig`:<br>
　　ログアウト処理が動く<br>
　　ログアウト成功時は204を返す<br>
<br>
React側<br>
　`Appコンポーネント`:<br>
　　authをnullにする<br>
　　cartを空にする<br>
　　ログイン画面へ移動する<br>

- ここで確認すること<br>
　ログアウト前に共通の確認モーダルを使っている<br>
　ログアウト後はログイン状態とカート情報をリセットしている<br>

- 参照ファイル<br>
React側<br>
　mobileorder-react/src/components/Header.jsx<br>
　mobileorder-react/src/components/ConfirmModal.jsx<br>
　mobileorder-react/src/App.jsx<br>
<br>
Java側<br>
　config/SecurityConfig.java<br>

### 認証機能で使う主なデータ
- `AppUser`<br>
　app_userテーブルに対応する<br>
　ユーザー名、暗号化済みパスワード、表示名、利用状態、権限を持つ<br>
<br>
- `UserManagementCode`<br>
　user_management_codeテーブルに対応する<br>
　USER-CODEまたはADMIN-CODEから始まる管理番号を持つ<br>
　新規アカウント作成や管理者登録で使用する<br>
<br>
- `Role`<br>
　ROLE_USERとROLE_ADMINを定義する<br>
　DBテーブルではなく、ユーザー権限を表すenum<br>
<br>
- `SignupRequest`<br>
　新規アカウント作成時の入力内容を受け取る<br>
<br>
- `PasswordResetRequest`<br>
　パスワード再設定時の入力内容を受け取る<br>
<br>
- `AuthStatusResponse`<br>
　ログイン状態確認APIからReact側へ返す情報を表す<br>
<br>
- `LoginCheckRequest`<br>
　ログイン失敗時に、利用停止中かどうかを確認するための入力内容を受け取る<br>
