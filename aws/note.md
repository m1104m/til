# AWS関連
## cli-alias
- awsコマンドのaliasを作成することができる
- https://github.com/awslabs/awscli-aliases
-
## 具体的な使い道
### default定義alias
``` $ aws whoami ```

``` $ aws sts get-caller-dentity ```　と同義

``` $ aws running-instances ```

``` $ aws ec2 describe-instances --filter Name=instance-state-name, Vlues=running --output table --query 'Reservations[].Instances[].{ID: InstanceId,Hostname: PublicDnsName,Name: Ta    gs[?Key==`Name`].Value | [0],Type: InstanceType, Platform: Platform || `Linux` ```

### 自作alias

``` $ aws uname ```

``` $ aws iam list-account-aliases ``` と同義

- awsアカウントのalias名で表示してくれるのでどのアカウントでログインしているかが分かりやすい

### 追加自作sh

- アカウントの切替の環境変数をいつも忘れるので、コマンド化しました
1. .bashrcに下記を追加
``` 
switch () {
  if [ $# = 1 ]; then
	  export AWS_PROFILE=$1
	fi
}
```

2. 下記で再読み込み
```
source .bashrc
```

3. 下記でアカウントの切替
```
$ switch "profile_name"
```

4. 下記切り替わりを確認
```
$ aws uname
```

※シェルスクリプトでやろうと思ったらシェルスクリプト内での環境変数にしか適用されなかった、、、
```
source test.sh
```
をしてあげてないと反映されない為、.bashrc内に関数という形で埋め込み
