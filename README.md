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

# ② サブネットワークの構築

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
  + 初めから存在するVPCを選択 (IPv4 CIDRが10.0.0.0/16となってるやつ)
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
  + 初めから存在するVPCを選択 (IPv4 CIDRが10.0.0.0/16となってるやつ)
+ サブネット名
  + subnet-test-2
+ アベイラビリティーゾーン
  + アジアパシフィック(東京)/apne1-az1(ap-northeast-1c)
+ IPv4 サブネット CIDR ブロック
  + 10.0.20.0/24
<br>
ここまで入力したら、「サブネットを作成」ボタンを押します。

<br><br>
<img src="https://github.com/user-attachments/assets/1ac89b69-6d6b-4c87-8148-779fdb469f06" width="70%" />
<br>
<img src="https://github.com/user-attachments/assets/0d58f6e8-787a-459a-b06e-a82bde392aed" width="70%" />
<br>
<img src="https://github.com/user-attachments/assets/d27843fd-e419-4128-952e-b1db838c478d" width="70%" />




