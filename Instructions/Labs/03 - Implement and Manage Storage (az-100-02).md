---
lab:
    title: 'Implement and Manage Storage'
    module: 'Module 03 - Azure Storage'
---

# 演習 : ストレージの実装と管理

この演習のすべての作業には、Azure ポータル (PowerShell クラウド シェル セッションを含む) を使用します。ただし、リモート デスクトップからの Azure 仮想マシンでの操作を含む、演習 2 の作業 2 は除きます。

> **注**: クラウド シェルを使用しない場合は、Azure PowerShell 1.2.0 (またはそれ以降の) モジュールをインストールした演習用仮想マシンが必要となります。 [https://docs.microsoft.com/en-us/powershell/azure/install-az-ps](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps)

演習用ファイル :

- **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.json**

- **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.parameters.json**

### シナリオ

Adatum Corporation は、データをホストするのに Azure Storage を活用したいと考えています。

### 目的

この演習を修了すると、次のことができるようになります。

- Azure Resource Manager テンプレートを使用して Azure 仮想マシンを展開する。

- Azure BLOB ストレージを実装して使用する。

- Azure ファイル ストレージを実装して使用できる。


### 演習 0 : 演習環境を準備する

この演習の主な作業は次のとおりです。

1. Azure Resource Manager テンプレートを使用して Azure 仮想マシンを展開する。


#### 作業 1 : Azure Resource Manager テンプレートを使用して Azure 仮想マシンを展開する

1. 演習用の仮想マシンで、Microsoft Edge を起動します。[**http://portal.azure.com**](http://portal.azure.com)から Azure ポータル にアクセスします。演習用の Azure サブスクリプションの所有者ロールをもつ Microsoft アカウントでサインインします。

1. Azure ポータルから [**サブスクリプション**] ブレードを開きます。

1. [**サブスクリプション**] ブレードから、Azure サブスクリプションのプロパティを表示します。

1. サブスクリプションのプロパティを確認し、[**リソースプロバイダー**] ブレードを開きます。

1. [**リソースプロバイダー**] ブレードで、次のリソースプロバイダーを登録します。(未登録の場合)
- Microsoft.Network
- Microsoft.Compute
- Microsoft.Storage

   > **注 :** このステップでは、Azure Resource Manager Microsoft.Network、Microsoft.Compute、Microsoft.Storage といったリソースプロバイダーを登録します。これは、Azure Resource Manager テンプレートを使用して、これらのリソースプロバイダーによって管理するリソースを展開するときに、(サブスクリプションごとに) 1 回限り必要な操作です (これらのリソース プロバイダーが未登録の場合)。

1. Azure ポータル から [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Template deployment**」と入力します。[**Template deployment (deploy using custom templates)**] を選択します。

1. [**作成**] をクリックします。

1. [**カスタム デプロイ**] ブレードで、[**エディターで独自のテンプレートを作成する**] リンクをクリックします。このリンクが確認できない場合は [**テンプレートの編集**] をクリックします。

1. [**テンプレートの編集**] ブレードで、テンプレート ファイルを読み込みます。**Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.json**

   > **注** : テンプレートの内容を確認してください。ここでは、Windows Server 2016 Datacenter をホストする Azure 仮想マシンの展開を定義しています。

1. このテンプレートを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードから、[**パラメーターの編集**] ブレードを開きます。

1. [**パラメーターの編集**] ブレードで、パラメーター ファイルを読み込みます。**Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.parameters.json**

1. パラメーターを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードで、次の設定で展開を開始します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ :「**az1000201-RG**」という名前のリソース グループを新規作成します。

   - 場所 : 演習環境に最も近い Azure リージョンの中で、Azure 仮想マシンをプロビジョ二ングできる場所を選択します。

   - Vm Size: **Standard_DS1_v2**

   - Vm Name: **az1000201-vm1**

   - Admin ユーザー名 : **Student**

   - Admin パスワード : **Pa55w.rd1234**

   - Virtual Network Name: **az1000201-vnet1**
   > **注** : Azure 仮想マシンをプロビジョニングできる Azure リージョンを確認するには、こちらを参照してください。[**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

   > **注** : 展開が完了するのを待つ必要はありません。次の演習に進んでください。"**az1000201-vm1**" という仮想マシンは、次の演習で使います。

> **結果** :  この演習では、**az1000201-vm1** という仮想マシンの展開が開始しました。この仮想マシンは、この演習の 2 つ 目の演習で使用します。


### 演習 1 : Azure BLOB ストレージを実装して使用する

この演習の主な作業は次のとおりです。

1. Azure ストレージ アカウントを作成する。

1. Azure ストレージ アカウントの構成設定を確認する。 

1. Azure BLOB ストレージを管理する。

1. Azure ストレージ アカウント間でコンテナーと BLOB をコピーする。 

1. Shared Access Signature (SAS) キーを使用して、BLOB にアクセスする。 


#### 作業 1 : Azure ストレージ アカウントを作成する

1. Azure ポータル から [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの Azure Marketplace 検索ボックスに「**Storage account**」と入力します。

1. 検索結果から [**ストレージ アカウント**] ブレードを開き、[作成] をクリックします。

1. [**ストレージ アカウントの作成**] ブレードで、次のような設定のストレージ アカウントを新規作成します。

   - サブスクリプション : 前の作業で選択したサブスクリプション

   - リソース グループ :「**az1000202-RG**」という名前のリソース グループを新規作成します。

   - ストレージ アカウント名 : 小文字と数字で構成される 3 から 24 文字の有効な一意の名前

   - 場所 : 前の作業で選択した Azure リージョン

   - パフォーマンス : **Standard**

   - アカウントの種類 : **Storage (汎用 v1)**

   - レプリケーション : **ローカル冗長ストレージ (LRS)**

1. [**確認および作成**] をクリックし、[**作成**] をクリックします。

1. ストレージ アカウントがプロビジョニングされるのを待たずに、次の手順に進みます。

1. Azure ポータル から [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの Azure Marketplace 検索ボックスに「**Storage account**」と入力します。

1. 検索結果から [**ストレージ アカウント**] ブレードを開き、[作成] をクリックします。

1. [**ストレージ アカウントの作成**] ブレードで、次のような設定のストレージ アカウントを新規作成します。

   - サブスクリプション : 前の作業で選択したサブスクリプション

   - リソース グループ :「**az1000203-RG**」という名前のリソース グループを新規作成します。

   - ストレージ アカウント名 : 小文字と数字で構成される 3 から 24 文字の有効な一意の名前

   - 場所 : 前の作業でストレージ アカウントを作成した Azure リージョンとは異なる場所を選択します。

   - パフォーマンス : **Standard**

   - アカウントの種類 : **StorageV2 (汎用 v2)**

   - アクセス層 : **ホット**

   - レプリケーション : **地理冗長ストレージ (GRS)**

1. [**確認および作成**] をクリックし、[**作成**] をクリックします。

1. ストレージ アカウントのプロビジョニングが完了するまで待ちます。処理は、1 分弱で完了します。


#### 作業 2 : Azure ストレージ アカウントの構成設定を確認する

1. Azure ポータルで、最初に作成したストレージ アカウントを開きます。

1. ストレージ アカウント ブレードから、[**概要**] セクションを開き、パフォーマンス、レプリケーション、アカウントの種類などを確認します。

1. [**アクセス キー**] ブレードを表示します。ストレージ アカウント名の値、および key 1 と key 2 の値をコピーするオプションがあることを確認してください。もしくは、キーを再生成するオプションもあります。

1. ストレージ アカウントの [**構成**] ブレードを表示します。

1. [**構成**] ブレードでは、"**汎用 v2**" アカウントへのアップグレードをするオプションがあります。また、安全な転送を有効にしたり、レプリケーション設定を "**地理冗長ストレージ (GRS)**" または "**読み取りアクセス地理冗長ストレージ (RA-GRS)**" に変更することができます。ただし、パフォーマンス設定は変更することができません。(この設定は、ストレージ アカウント作成時にのみ設定可能です。)

1. 次にストレージ アカウントの [**暗号化**] ブレードを表示します。既定で暗号化が有効になっていますが、独自のキーで暗号化することも可能です。

   > **注** : ストレージ アカウントの構成は変更しないでください。

1. Azure ポータルで、2 つ目に作成したストレージ アカウントを開きます。

1. ストレージ アカウント ブレードから、[**概要**] セクションを開き、パフォーマンス、レプリケーション、アカウントの種類などを確認します。

1. ストレージ アカウントの [**構成**] ブレードを表示します。

1. [**構成**] ブレードでは、安全な転送を無効にすることができます。また、既定のアクセス層を "**クール**" に変更したり、レプリケーションを "**ローカル冗長ストレージ (LRS)**" または "**読み取りアクセス地理冗長ストレージ (RA-GRS)**" に変更することもできます。この場合は、パフォーマンス設定も変更することができます。

1. 次にストレージ アカウントの [**暗号化**] ブレードを表示します。この場合も、既定で暗号化が有効になっており、独自のキーで暗号化することも可能です。


#### 作業 3 : Azure BLOB ストレージを管理する

1. Azure ポータルから最初に作成したストレージ アカウントを開き、[**コンテナー**] ブレードを開きます。

1. 最初のストレージ アカウントの [**コンテナー**] ブレードで、「**az1000202-container**」という新しいコンテナ―を作成し、**パブリック アクセス レベル**を [**プライベート (匿名アクセスはありません)**] に設定します。

1. [**az1000202-container**] ブレードで、コンテナーに **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.json** と **Labfiles\\Module_03\\Implement_and_Manage_Storage\\az-100-02_azuredeploy.parameters.json** をアップロードします。


#### 作業 4 : Azure ストレージ アカウント間でコンテナーと BLOB をコピーする

1. Azure ポータル の Cloud Shell ペインから、PowerShell セッションを開始します。

   > **注** : 現在の Azure サブスクリプションで Cloud Shell を初めて起動する場合、Cloud Shell ファイルを維持するための Azure ファイル共有の作成を要求されます。既定のまま設定すると、自動生成されたリソース グループにストレージ アカウントが作成されます。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   $containerName = 'az1000202-container'
   
   $storageAccount1Name = (Get-AzStorageAccount -ResourceGroupName 'az1000202-RG')[0].StorageAccountName
   
   $storageAccount2Name = (Get-AzStorageAccount -ResourceGroupName 'az1000203-RG')[0].StorageAccountName
   
   $storageAccount1Key1 = (Get-AzStorageAccountKey -ResourceGroupName 'az1000202-RG' -StorageAccountName $storageAccount1Name)[0].Value
   
   $storageAccount2Key1 = (Get-AzStorageAccountKey -ResourceGroupName 'az1000203-RG' -StorageAccountName $storageAccount2Name)[0].Value
   
   $context1 = New-AzStorageContext -StorageAccountName $storageAccount1Name -StorageAccountKey $storageAccount1Key1
   
   $context2 = New-AzStorageContext -StorageAccountName $storageAccount2Name -StorageAccountKey $storageAccount2Key1
   ```
   > **注** : これらのコマンドにより、前の作業でアップロードした BLOB を含む BLOB コンテナ―の名前、2 つのストレージ アカウントとそれらに対応するキー、またそれぞれに対応するセキュリティ 設定を指定する変数の値を設定します。AZ Copy コマンド ライン ユーティリティで、ストレージ アカウント間で BLOB をコピーする際に、これらの値を使用して SAS トークンを生成します。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   New-AzStorageContainer -Name $containerName -Context $context2 -Permission Off
   ```
   > **注** : このコマンドで、2 番目のストレージ アカウントに一致する名前の新しいコンテナーを作成します。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   $containerToken1 = New-AzStorageContainerSASToken -Context $context1 -ExpiryTime(get-date).AddHours(24) -FullUri -Name $containerName -Permission rwdl
   
   $containerToken2 = New-AzStorageContainerSASToken -Context $context2 -ExpiryTime(get-date).AddHours(24) -FullUri -Name $containerName -Permission rwdl
   ```
   > **注** : これらのコマンドは、次の手順で 2 つのコンテナー間で BLOB をコピーするために使用するSASキーを生成します。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   azcopy cp $containerToken1 $containerToken2 --recursive=true
   ```

   > **注** : このコマンドで、AzCopy ユーティリティを使用して、2 つのストレージアカウント間でコンテナーのコンテンツをコピーします。

1. コマンドの出力から、2 つのファイルが転送されたことを確認します。

1. 2 つ目のストレージ アカウントの [**コンテナー**] ブレードを開きます。"**az1000202-container**" という新しいコンテナーの中に、コピーされた 2 つの BLOB が含まれていることが確認できます。


#### 作業 5 : Shared Access Signature (SAS) キーを使用して、BLOB にアクセスする

1. 2 つ目に作成したストレージ アカウントの [**コンテナー**] ブレードから "**az1000202-container**" を開きます。[**az-100-02_azuredeploy.json**] ブレードを開きます。

1. [**az-100-02_azuredeploy.json**] ブレードで、"**URL**" プロパティをコピーします。

1. Microsoft Edgeのウィンドウを新しく開き、先ほどコピーした URL に移動します。

   > **注** : ブラウザーには "**ResourceNotFound**" と表示されます。これは、コンテナーの **パブリック アクセス レベル** が "**プライベート (匿名アクセスはありません)"** に設定されているためです。

1. [**az-100-02_azuredeploy.json**] ブレードで、次のような設定の Shared Access Signature (SAS) と対応する URL を生成します。

   - アクセス許可 : **読み取り**

   - 開始日時 : 現在のタイムゾーンの現在日時を指定します。

   - 有効期限 : 現在日時の 24 時間後の日時を指定します。

   - 使用できる IP アドレス : 空白

   - 許可されるプロトコル : **HTTP**

   - 署名キー : **キー 1**

1. [**az-100-02_azuredeploy.json**] ブレードから、"**Blob SAS URL**" をコピーします。

1. 先ほど開いた Microsoft Edge ウィンドウで、前の手順でコピーした URL に移動します。

   > **注** : 今回は、**az-100-02_azuredeploy.json** を開くか保存するかを尋ねるプロンプトが表示されます。今回は、匿名でこのコンテナーにアクセスしているわけではなく、新規作成した 24 時間有効な SAS キーでアクセスしているため、このような結果になります。 

1. プロンプトが表示されている Microsoft Edge を閉じます。

> **結果** : この演習では、2 つの Azure ストレージ アカウントを作成し、それらの構成設定を確認し、BLOB コンテナーを作成しました。コンテナーに BLOB をアップロードし、ストレージ アカウント間で コンテナーと BLOB をコピーし、BLOB にアクセスするために SAS キーを使用しました。


### 演習 2 : Azure ファイル ストレージを実装して使用する

この演習の主な作業は次のとおりです。

1. Azure ファイル共有を作成する。

1. Azure 仮想マシンから Azure ファイル共有にドライブをマップする。


#### 作業 1 : Azure ファイル共有を作成する

1. Azure ポータルで、前の演習で 2 つ目に作成したストレージ アカウントを開き、プロパティを表示します。

1. ストレージ アカウント ブレードから、[**ファイル共有**] ブレードを開きます。

1. ストレージ アカウントの [**ファイル共有**] ブレードで、次のような設定で新しいファイル共有を作成します。

   - 名前 : **az10002share1**

   - クォータ : **5 GiB**


#### 作業 2 : Azure 仮想マシンから Azure ファイル共有にドライブをマップする

> **注** : この作業を開始する前に、演習 0 で開始したテンプレートの展開が完了していることを確認してください。

1. [**az10002share1**] ブレードから、[**接続**] ブレードを開きます。

1. [**接続**] ブレードで、Windows コンピューターからこのファイル共有に接続ための PowerShell コマンドをクリップボードにコピーします。

1. Azure ポータルから [**az1000201-vm1**] ブレードを開きます。

1. [**az1000201-vm1**] ブレードで、[接続] をクリックし、RDP プロトコル経由で Azure 仮想マシンに接続するようにします。サインインが必要な場合は、次の資格情報を入力します。

   - Admin ユーザー名 : **Student**

   - Admin パスワード : **Pa55w.rd1234**

1. RDP セッション内で Windows PowerShell ISE セッション を起動します。

1. Windows PowerShell ISE セッションで、Script ペインを開き、ローカル PC で先ほどコピーしたコマンドをペーストします。

1. PowerShell ISE セッションでコマンドをペーストしたら、「``-Persist``」を最後に追加して実行します。出力を確認し、Azure Storage File Service 共有の Z: ドライブへのマッピングが成功していることを確認します。

1. エクスプローラーを開き、Z: ドライブに「**Folder1**」というフォルダーを作成します。

1. **Folder1** の中に「**File1.txt**」というテキストを作成します。

   > **注** : 既知のファイル拡張子を表示しないというエクスプローラーの既定の仕様を考慮して、ファイル名が "**File1.txt.txt**" とならないように気を付けてください。

1. PowerShell プロンプトで、「**Z:**」と入力し、マップされたドライブにディレクトリ コンテキストを変更します。

1. PowerShell プロンプトで 「**dir**」と入力し、ドライブの内容をリスト表示します。先ほど作成したディレクトリが表示されます。

1. PowerShell プロンプトで 「**cd Folder1**」と入力し、フォルダーにディレクトリを変更します。「**dir**」を実行し、フォルダー内のファイル リストを確認します。

> **結果** : この演習では、Azure ファイル共有を作成し、Azure 仮想マシンからドライブをファイル共有にマップし、Azure 仮想マシン からエクスプローラーを使用してファイル共有内にフォルダーとファイルを作成しました。

## 演習 3 : 演習用リソースを削除する

#### 作業 1 : Cloud Shell を開く

1. Azure ポータルの上部にある **Cloud Shell** アイコンをクリックして、Cloud Shell ペインを開きます。

1. Cloud Shell インターフェイスで [**Bash**] を選択します。

1. **Cloud Shell** のコマンド プロンプトで、次のコマンドを入力し [**Enter**] を押します。これにより、この演習で作成したすべてのリソース グループが表示されます。

   ```sh
   az group list --query "[?starts_with(name,'az1000')].name" --output tsv
   ```

1. 出力結果を確認し、リストにはこの演習で作成したリソース グループのみが含まれることを確認します。これらのリソース グループを、次の作業で削除します。

#### 作業 2 : リソース グループを削除する

1. **Cloud Shell** のコマンド プロンプトで、次のコマンドを入力し [**Enter**] を押します。これにより、この演習で作成したリソース グループを削除できます。

   ```sh
   az group list --query "[?starts_with(name,'az1000')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. ポータルの下部に表示される **Cloud Shell** プロンプトを閉じます。

> **結果** : この演習では、演習内で使用したリソースをすべて削除しました。
