# ① EC2インスタンスの構築

+ AWSにログインしてください。

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/4c8cec3b-b46a-4e04-a90c-f5378e9f63a6" width="70%" />

<br><br>

+ AWSのマジメントコンソールを開き、リージョンを選択してください。
  + 本手順書では、「東京」リージョンに選択したうえで進めます。

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/a5490b76-9d0d-4306-a100-3adc8b9fc3b4" width="70%" />

<br><br>

+ マネジメントコンソールの検索部分に「EC2」と入力・検索を行い、EC2サービスを選択してください。

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/2704af17-6537-492f-bd55-61f00c010825" width="70%" />

<br><br>

+ 左メニューの「インスタンス」を選択して、「インスタンスを起動」をクリックしてください。

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/17c66e10-3095-41e4-9baf-dd799a6c5549" width="70%" />

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/6025e6ac-d8d2-4207-a74b-96a10b8a4af5" width="70%" />

<br><br><br>

+ 「Launch an instance」では下記の内容を入力・選択する
  + 名前: 好きなサーバ名を入力してください
  + Amazon マシンイメージ (AMI): Amazon Linux 2023
  + インスタンスタイプ: t3.micro
  + ネットワークの設定: パブリックIPの自動割り当てを「無効」から「有効」に変えてください
  + キーペア (ログイン) : 新しいキーペアの作成をクリックして、キーペアを取得してください(ログイン時に使用)
    +  キーペア名: 好きな名前を入力してください
    +  キーペアのタイプ: RSA
    +  プライベートキーファイル形式: 「.pem」
      
<br><br><br>

<img src="https://github.com/user-attachments/assets/4317984c-63db-48af-830e-0aa34528a768" width="70%" />


<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/26f81d98-07d2-482e-b37f-545b5e72b1cb" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/92f77a14-411b-4627-921e-a75e4fea3d97" width="70%" />


<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/71602f1e-f76d-49af-9ba6-c0a4c516c517" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/e7fe97c6-f4d1-48ff-bec4-50f14571c795" width="70%" />

<br><br>

必要項目を入力をした後、「キーペアを作成」をクリックしてください。

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/63254121-0875-47eb-a8c0-6c0e7958e3bd" width="70%" />

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/0a398555-165a-44e3-a2e3-99fe68664a05" width="70%" />

<br><br>
<br>

さらに、下部にある高度な詳細のタブを開きます。

<br><br>

<img src="https://github.com/user-attachments/assets/912e3ea8-65fa-4cc1-a969-15208e556517" width="70%" />

<br><br>

「ユーザーデータ - オプション」下記の記載を入れます。

```
#!/bin/bash

dnf update -y
dnf install nginx -y

systemctl enable nginx
systemctl start nginx

HOSTNAME=$(hostname)
INSTANCE_ID=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)

cat << EOF > /usr/share/nginx/html/index.html
<h1>Auto Scaling Demo</h1>
<p>Hostname : ${HOSTNAME}</p>
<p>Instance ID : ${INSTANCE_ID}</p>
EOF
```
<br><br>
<img src="https://github.com/user-attachments/assets/0a258c79-79ba-410d-898c-dda9fb5857ce" width="70%" />

<br>

※169.254.169.254は、自身のEC2インスタスの情報を取得できる専用IPアドレス
<br>
※「169.254.169.254/latest/meta-data/instance-id」にアクセスし、EC2情報を取得して、ドキュメントルートに記載している

<br><br>

ここまでの設定が完了すれば、「インスタンスを起動」ボタンを押す

<br>

<img src="https://github.com/user-attachments/assets/4ffe9a4b-f6d2-4e47-8a12-aaf8d01cfbc0" width="70%" />

<br>
<img src="https://github.com/user-attachments/assets/6fc55fa8-5c54-4cf3-9522-9d1cfd79a3a0" width="70%" />

<br>
<img src="https://github.com/user-attachments/assets/8759579f-804f-44b8-96c3-1e9e9f275465" width="70%" />

<br><br>

インスタンスの一覧を開き、作成したインスタンス(実行中の状態)が存在すればOKです

<br>
<img src="https://github.com/user-attachments/assets/58dabfa6-ec13-46b3-83f4-08ad68d65502" width="70%" />

# ② VPC内にサブネットを追加する

<br><br>
ELBでリクエスト通信を振り分けるには、2つ以上のサブネットワークを用意する必要があります。
<br>
そこで、今回の演習用のサブネットワークを2つ構築します。
<br><br>

+ 1つ目のサブネットワーク
  + 10.0.10.0/24

<br>

+ 2つ目のサブネットワーク
  + 10.0.20.0/24

<br><br>
左上の検索ボックスに「VPC」と入力して、VPCサービスを選択します。

<img src="https://github.com/user-attachments/assets/fe4e9cc5-adc3-4d40-a3f0-d2db95ba78ef" width="70%" />

<br><br>
左のサイドメニューから「サブネット」を選択します。

<img src="https://github.com/user-attachments/assets/67eeb0b3-908c-4aa9-8509-16f1e591a2cd" width="70%" />

<br><br>

左上の「サブネットを作成」ボタンをクリックします。

<img src="https://github.com/user-attachments/assets/70062765-269c-496c-8c17-54bcfedd0c87" width="70%" />

<br><br>

1つめのサブネットを作成します。
<br>
以下の内容を入力・選択してください。
<br>
+ サブネットID
  + ①で作成したEC2と同じVPCを選択してください
+ サブネット名
  + subnet-test-1
+ アベイラビリティーゾーン
  + アジアパシフィック(東京)/apne1-az4(ap-northeast-1a)
+ IPv4 サブネット CIDR ブロック
  + 10.0.10.0/24
<br>
ここまで入力したら、「サブネットを作成」ボタンを押します。

<br><br>

<img src="https://github.com/user-attachments/assets/cb8c3841-3f84-4948-b9f9-2c6d6a725646" width="70%" />
<br>
<img src="https://github.com/user-attachments/assets/e8e50273-1fe5-40fc-af8a-e467f43e6ef6" width="70%" />


<br><br>
<img src="https://github.com/user-attachments/assets/46dc07d6-b601-44d1-8199-08335062e9cd" width="70%" />

<br>
再度、「サブネットを作成」ボタンをクリックし、2つ目のサブネットを作成します。
<br>
<img src="https://github.com/user-attachments/assets/de79f6c4-43a7-40f0-928a-1bb369127a5d" width="70%" />


<br>
以下の内容を入力・選択してください。

<br>

+ サブネットID
  + ①で作成したEC2と同じVPCを選択してください
+ サブネット名
  + subnet-test-2
+ アベイラビリティーゾーン
  + アジアパシフィック(東京)/apne1-az1(ap-northeast-1c)
+ IPv4 サブネット CIDR ブロック
  + 10.0.20.0/24

<br><br><br>

ここまで入力したら、「サブネットを作成」ボタンを押します。

<br><br><br>

<img src="https://github.com/user-attachments/assets/1ac89b69-6d6b-4c87-8148-779fdb469f06" width="70%" />

<br><br><br>

<img src="https://github.com/user-attachments/assets/0d58f6e8-787a-459a-b06e-a82bde392aed" width="70%" />

<br><br><br>

<img src="https://github.com/user-attachments/assets/d27843fd-e419-4128-952e-b1db838c478d" width="70%" />

<br><br><br>

# ③ ターゲットグループの作成

<br><br><br>

ELBに振り分け先のEC2インスタンスのグループを作成します。

<br><br><br>

左上の検索ボックスに、「EC2」と入力し、EC2サービスに移動します。

<br><br><br>

<img src="https://github.com/user-attachments/assets/c810bf1c-ad74-4ef4-bded-3a7670155e6c" width="70%" />

<br><br><br>

左のサイドメニューの「ロードバランシング」の箇所にある「ターゲットグループ」をクリックします。

<img src="https://github.com/user-attachments/assets/43021ab6-54b9-4130-b923-fb8ddd1015a1" width="70%" />

<br>

「ターゲットグループの作成」を選択します。

<br>

<img src="https://github.com/user-attachments/assets/bc28b027-7f6e-40f9-9613-170c19844ecc" width="70%" />

<br>
<br>
以下の内容を入力・選択してください。

<br>

+ ターゲットの種類
  + インスタンス
+ ターゲットグループ名
  + test-target-group
+ プロトコル
  + HTTP
+ ポート
  + 80
+ IP アドレスタイプ
  + IPv4
+ VPC
  + ①で作成したEC2と同じVPCを選択してください
+ プロトコルバージョン
  + HTTP1
+ ヘルスチェックプロトコル
  + HTTP
+ ヘルスチェックパス
  + /


<br><br><br>

<img src="https://github.com/user-attachments/assets/bf23f3ed-2321-4d96-9932-b60ee82acfc2" width="70%" />

<br><br><br>

<img src="https://github.com/user-attachments/assets/9152a90d-c940-4081-9c5c-3536357d44ee" width="70%" />

<br><br><br>

<img src="https://github.com/user-attachments/assets/6ecf7db5-35b0-4c89-8ff8-09070b916aea" width="70%" />

<br><br><br>

<img src="https://github.com/user-attachments/assets/b68cae88-6ad6-48ea-bb3c-cb062c0b440c" width="70%" />

<br><br><br>

ここまで入力できたら、「次へ」をクリックします。

<br>
使用可能なインスタンスに、①で構築したEC2インスタンスが表示されます。(表示されない場合、EC2インスタンスが停止状態になっている可能性があります)

<br><br><br>

<img src="https://github.com/user-attachments/assets/1c93f7c5-cf6c-46ff-8f66-42f1154609fc" width="70%" />

<br><br><br>

構築したEC2インスタンスを選択し、「保留中として以下を含める」を選択します。

<br><br><br>

<img src="https://github.com/user-attachments/assets/93c2d275-4132-4cbc-abb6-bf7adef3aeb0" width="70%" />

<br><br><br>

「次へ」をクリックします。

<br><br><br>

<img src="https://github.com/user-attachments/assets/5a1aea15-6c21-4e70-afd7-4c795fd0722e" width="70%" />

<br><br><br>

確認画面に遷りますので、最後「ターゲットグループの作成」ボタンをクリックします。

<br><br><br>

<img src="https://github.com/user-attachments/assets/deb22774-f5be-47c8-b7af-d049ae19d894" width="70%" />

<br><br><br>

<img src="https://github.com/user-attachments/assets/ac00fe9d-0964-4fd8-816b-e32df0ff10da" width="70%" />

<br><br><br>

<img src="https://github.com/user-attachments/assets/7e2f7b0e-3cab-4da4-8b08-114669461f54" width="70%" />

<br><br><br>

# ④ ロードバランサー(ELB)の作成

<br><br>
EC2サービスの左メニューから、ロードバランシングのロードバランサーを選択します。

<br><br><br>

<img src="https://github.com/user-attachments/assets/47a808a2-8c66-4572-8977-349fc6da1bd8" width="70%" />

<br><br><br>

左上の「ロードバランサーの作成」を選択します。

<br><br><br>

<img src="https://github.com/user-attachments/assets/ac149d19-54a5-4eb1-bf37-05b0b0aa6327" width="70%" />

<br><br><br>

ロードバランサータイプは、左の「ALB」(Application Load Balancer)を選択してください。

<br><br><br>

<img src="https://github.com/user-attachments/assets/a564ef69-bdd1-4fba-a9cf-d02cebe14b58" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/0bd07fa5-89c7-4939-907b-23f0ba6320d5" width="70%" />


<br><br><br>

<br>

以下の内容を入力・選択してください。

<br><br>

+ ロードバランサー名
  + test-elb
+ スキーム
  + インターネット向け
+ ロードバランサーの IP アドレスタイプ
  + IPv4
+ VPC
  + ①で作成したEC2と同じVPCを選択してください
+ IP アドレスタイプ
  + IPv4
+ VPC
  + ①で作成したEC2と同じVPCを選択してください
+ アベイラビリティーゾーンとサブネット
  + ap-northeast-1a (apne1-az4)とap-northeast-1c (apne1-az1)にチェック
  + サブネットは、②で作った「subnet-test-1」と「subnet-test-2」を選択
+ セキュリティグループ
  + いったんはデフォルトのままでOK (後の⑤で設定変更します)
+ プロトコル
  + HTTP (ポート: 80)
+ アクションのルーティング
  + ターゲットグループへ転送
+ ターゲットグループ
  + test-target-group

<br><br>

以降はデフォルトの設定でOKで、一番下の「ロードバランサーの作成」ボタンをクリックする。

<br><br><br>

<img src="https://github.com/user-attachments/assets/e593b4f1-cd62-4bf9-8426-078ab1d3793b" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/f24c9e16-3d56-4e41-aafd-b7de2a7114cf" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/293910d1-dd35-4ebd-a919-4942287dd2e1" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/291cc8f1-5d6e-4bf3-b6da-3ad199f031eb" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/4b1cb9ea-50da-4462-8dde-f5a4ec1b77f5" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/eb851829-7e29-4d18-9c11-eda1a5e968bc" width="70%" />

<br><br>

# ⑤ セキュリティグループの作成

<br>

## ELB用とEC2インスタンス用のセキュリティグループ作成

<br><br>

ELB用とEC2インスタンス用のセキュリティグループを作成します。

<br><br>

左メニューからセキュリティグループを選択し、右上の「セキュリティグループの作成」をクリックします。

<br><br>

<img src="https://github.com/user-attachments/assets/1fd946d5-170b-4cc8-a818-2ef0e8582562" width="70%" />

<br><br>

まず、ELBに割り当てるセキュリティグループを作成します。

<br><br>

以下の内容を入力してください。

<br>

+ セキュリティグループ名
  + ElbSecurityGroup
+ 説明
  + ElbSecurityGroup
+ VPC
  + ①で作成したEC2と同じVPCを選択してください

<br><br>

インバウンドルールのルールを追加をクリックします。

<br><br>

<img src="https://github.com/user-attachments/assets/8b7503a7-f4e0-4fa5-836a-36ceaf7ca057" width="70%" />

<br><br>

インバウンドルールには、下記の内容を入力してください。

<br><br>

+ タイプ
  + HTTP
+ ソース
  + Anywhere-IPv4 (0.0.0.0/0)

<img src="https://github.com/user-attachments/assets/134cfcce-2e73-4546-8180-61400082836f" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/b9a3be9b-a7e8-45ec-9e46-f9e8196b0957" width="70%" />

<br><br>

アウトバウンドルールはデフォルトの設定で、ここまで記入できれば、「セキュリティグループを作成」ボタンをクリックする。

<img src="https://github.com/user-attachments/assets/9de5944b-5883-4e8e-95b0-d3e78373e1ac" width="70%" />

<br><br><br>

続いて、EC2に割り当てるセキュリティグループを作成します。

<br><br>

<img src="https://github.com/user-attachments/assets/89351190-23a8-4d8d-af24-b970b353af22" width="70%" />

<br><br>

以下の内容を入力してください。

<br>

+ セキュリティグループ名
  + ElbToEc2SecurityGroup
+ 説明
  + ElbToEc2SecurityGroup
+ VPC
  + ①で作成したEC2と同じVPCを選択してください

<br><br>

<img src="https://github.com/user-attachments/assets/693b52a9-232a-4156-83a9-e1c2e2ea885b" width="70%" />

<br><br>

インバウンドルールには、下記の内容を入力してください。

<br><br>

▼1つ目のルール

<br>

+ タイプ
  + HTTP
+ ソース
  + 「カスタム」を選択し、ELB用のセキュリティグループを選択する(「ElbSecurityGroup」で検索し、表示されたセキュリティグループを選択)

<br>

▼2つ目のルール

<br>

+ タイプ
  + SSH
+ ソース
  + マイIP
 
<img src="https://github.com/user-attachments/assets/cd168122-3253-4706-90ba-104ee7a2a331" width="70%" />

<br>

<img src="https://github.com/user-attachments/assets/c30b8581-fd88-4d57-b99f-2fec02d4bb99" width="70%" />

<br><br>

ここまで記入できれば、「セキュリティグループを作成」ボタンをクリックする。

<br><br>

<img src="https://github.com/user-attachments/assets/69742e40-765f-4603-b699-bf008e0cbb47" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/78a4be6b-7887-48f8-9d3b-613788e90c15" width="70%" />

<br><br>

## セキュリティグループをELBに割り当てる

<br>

セキュリティグループを④で作成したELBに割り当てる。

<br><br>

左メニューから「ロードバランサー」を開き、作成したELBを選択し、「セキュリティ」タブを開きます。

<br><br>

そして、「セキュリティグループ」の「編集」ボタンをクリックします。


<img src="https://github.com/user-attachments/assets/807ca975-c58b-4e9d-ae03-191a5d4a13d2" width="70%" />

<br><br>

セキュリティグループの編集画面にうつり、デフォルトで設定設定されているセキュリティグループ(default)を削除します。

<br><br>

<img src="https://github.com/user-attachments/assets/9f041285-2bff-44f1-ab92-cd8f371670ad" width="70%" />

<br><br>

そして、「ElbSecurityGroup」を追記して、「変更内容の保存」ボタンをクリックする。

<br><br>

<img src="https://github.com/user-attachments/assets/d9b8a29b-f13b-46d9-b03c-8d1598cf26a4" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/43d73d0b-50b2-48b7-a89c-91aa5430ecea" width="70%" />

<br><br>

<img src="https://github.com/user-attachments/assets/bfef1644-8e93-4603-bade-e9a3002db42a" width="70%" />

<br><br>
