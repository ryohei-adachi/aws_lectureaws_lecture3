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
  + インスタンスタイプ: t2.micro
  + ネットワークの設定: パブリックIPの自動割り当てを「無効」から「有効」に変えてください
  + キーペア (ログイン) : 新しいキーペアの作成をクリックして、キーペアを取得してください(ログイン時に使用)
    +  キーペア名: 好きな名前を入力してください
    +  キーペアのタイプ: RSA
    +  プライベートキーファイル形式: 「.pem」
      
<br><br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/05e292b4-55e6-48bd-addd-05e5a4ee682a" width="70%" />

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/26f81d98-07d2-482e-b37f-545b5e72b1cb" width="70%" />

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/ecc10718-aa70-4d39-b9fa-bd925ce0855b" width="70%" />

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/71602f1e-f76d-49af-9ba6-c0a4c516c517" width="70%" />

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/fa4d93e0-3cdd-4857-861f-9e032e38ca76" width="70%" />

<br><br>

必要項目を入力をした後、「キーペアを作成」をクリックしてください。

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/63254121-0875-47eb-a8c0-6c0e7958e3bd" width="70%" />

<br><br>

<img src="https://github.com/ryohei-adachi/aws_lecture/assets/75190594/0a398555-165a-44e3-a2e3-99fe68664a05" width="70%" />

<br><br>
<br>

高度な詳細のタブを開き、