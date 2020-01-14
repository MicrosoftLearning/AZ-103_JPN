---
lab:
    title: 'Azure File Sync'
    module: 'Module 12 - Data Services'
---

# 演習 : Azure File Sync の実装

この演習のすべての作業には、Azure ポータル (PowerShell クラウド シェル セッションを含む) を使用します。ただし、リモート デスクトップからの Azure 仮想マシンでの操作を含む、演習 1 と演習 2 の一部作業は除きます。

演習用ファイル :

- **Labfiles\\Module_12\\Implementing_File_Sync\\az-100-02b_azuredeploy.json**

- **Labfiles\\Module_12\\Implementing_File_Sync\\az-100-02b_azuredeploy.parameters.json**

### シナリオ

Adatum Corporation は、ファイル共有をオンプレミス ファイル サーバーでホストしています。その大部分を Azure に移行する計画があり、Azure のファイル共有にデータを複製する最も効率的な方法を探しています。Adatum は、Azure File Sync の使用を考えています。

### 目的

この演習を修了すると、次のことができるようになります。

- Azure Resource Manager (ARM) テンプレートを使用して Azure 仮想マシンを展開する。

- Azure File Sync インフラストラクチャを準備する。

- Azure File Sync を実装して検証する。


### 演習 0 : 演習環境を準備する

この演習の主な作業は次のとおりです。

1. Azure Resource Manager (ARM) テンプレートを使用して Azure 仮想マシンを展開する。


#### 作業 1 : Azure Resource Manager (ARM) テンプレートを使用して Azure 仮想マシンを展開する

1. 演習用の仮想マシンで、Microsoft Edge を起動します。[**http://portal.azure.com**](http://portal.azure.com) から Azure ポータル にアクセスします。演習用の Azure サブスクリプションの所有者ロールをもつ Microsoft アカウントでサインインします。

1. Azure ポータルから [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Template deployment**」と入力します。

1. 検索結果から [**テンプレートのデプロイ**] ブレードを開き、[作成] をクリックします。

1. [**カスタム デプロイ**] ブレードで、[**エディターで独自のテンプレートを作成する**] をクリックします。

1. [**テンプレートの編集**] ブレードで、テンプレート ファイルを読み込みます。**az-100-02b_azuredeploy.json**.

   > **注** : テンプレートの内容を確認してください。ここでは、単一のデータ ディスクでWindows Server 2016 Datacenter をホストする Azure 仮想マシンの展開を定義しています。

1. このテンプレートを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードから、[**パラメーターの編集**] ブレードを開きます。

1. [**パラメーターの編集**] ブレードで、パラメーター ファイルを読み込みます。**az-100-02b_azuredeploy.parameters.json**.

1. パラメーターを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードで、次の設定で展開を開始します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ : 「**az1000201b-RG**」という名前のリソース グループを新規作成します。

   - 場所 : 演習環境に最も近い Azure リージョンの中で、Azure 仮想マシンをプロビジョ二ングできる場所を選択します。

   - Vm Size : **Standard_DS1_v2**

   - Vm Name : **az1000201b-vm1**

   - Admin Username : **Student**

   - Admin Password : **Pa55w.rd1234**

   - Virtual Network Name : **az1000201b-vnet1**
   > **注** : Azure 仮想マシンをプロビジョニングできる Azure リージョンを確認するには、こちらを参照してください。[**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

   > **注** : 展開が完了するのを待つ必要はありません。次の演習に進んでください。現在展開中の仮想マシンは、次の演習で使用します。

   > **注** : Azure 仮想マシン **az1000201b-vm1** を展開する目的は、シナリオにあるとおり、オンプレミスのファイル サーバーをエミュレートするためです。

> **結果** : この演習では、次の演習で使用する Azure 仮想マシン **az1000201b-vm1** を展開しました。


### 演習 1 : Azure File Sync インフラストラクチャを準備する

この演習の主な作業は次のとおりです。

1. ストレージ アカウントとファイル共有を作成する。

1. Azure File Sync で使用するために Windows Server 2016 を準備する。

1. Azure File Sync 評価ツールを実行する。


#### 作業 1 : ストレージ アカウントとファイル共有を作成する

1. Azure ポータルから [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Storage account**」と入力します。

1. 検索結果から [**ストレージ アカウント**] ブレードを開き、[作成] をクリックします。

1. [**ストレージ アカウントの作成**] ブレードで、次のような設定のストレージ アカウントを新規作成します。

   - サブスクリプション : 前の作業で選択したサブスクリプション

   - リソース グループ : 「**az1000202b-RG**」という名前のリソース グループを新規作成します。

   - ストレージ アカウント名 : 小文字と数字で構成される 3 から 24 文字の有効な一意の名前

   - 場所 : 前の作業で選択した Azure リージョン

   - パフォーマンス : **Standard**

   - アカウントの種類 : **Storage (汎用 v1)**

   - レプリケーション : **ローカル冗長ストレージ (LRS)**

   - 接続方法 : **パブリック エンドポイント (すべてのネットワーク)**

   - 安全な転送が必須 : **無効**

   - BLOB の論理的な削除 : **無効**

   - 階層構造の名前空間 : **無効**
   > **注** : ストレージ アカウントのプロビジョニングが完了するのを待って、次の手順に進みます。

1. Azure ポータルの、[ストレージ アカウント] ブレードをクリックし、新規作成したストレージ アカウントを表示します。

1. 新規作成したストレージ アカウントの [**プロパティ**] ブレードで、File サービスのプロパティを表示します。

1. ストレージ アカウントの [**ファイル共有**] ブレードで、[+ ファイル共有] をクリックして、次のような設定でファイル共有を作成します。

   - 名前 : **az10002bshare1**

   - クォータ : なし


#### 作業 2 : Azure File Sync で使用するために Windows Server 2016 を準備する

> **注** : この作業を開始する前に、演習 0 で開始したテンプレートの展開が完了していることを確認してください。

1. Azure ポータルから、[**az1000201b-vm1**] ブレードを開きます。

1. [**az1000201b-vm1**] ブレードで、[接続] をクリックし、RDP プロトコル経由で Azure 仮想マシンに接続するようにします。サインインが必要な場合は、次の資格情報を入力します。

   - Admin Username : **Student**

   - Admin Password : **Pa55w.rd1234**

1. Azure 仮想マシンへの RDP セッション内の、Server Manager から [**File and Storage Services**] を開き、Azure 仮想マシンに接続されたデータ ディスク (SAS ディスク) を検索し、そのディスクを "**GPT**" ディスクとして初期化 (Initialize) します。また、初期化したディスクを右クリックから [**New Volume**] をクリックし、[**New Volume Wizard**] から、単一ボリュームがディスク全体を占めるように次のような設定にします。

   - Server and Disk : **既定値**

   - Size : **既定値**

   - Drive letter : **S**

   - File system : **NTFS**

   - Allocation unit size : **既定値**

   - Volume label : **Data**

1. RDP セッション内で、Windows PowerShell を管理者として起動します。

1. Windows PowerShell コンソールで、次のコマンドを実行して、ファイル共有を設定します。

   ```powershell
   $directory = New-Item -Type Directory -Path 'S:\az10002bShare'

   New-SmbShare -Name $directory.Name -Path $directory.FullName -FullAccess 'Administrators' -ReadAccess Everyone

   Copy-Item -Path 'C:\WindowsAzure\*' -Destination $directory.FullName –Recurse
   ```

   > **注** : ファイル共有にサンプル データを入れるため、100 MB 程度のファイルを含む *C:\\WindowsAzure* フォルダー内のコンテンツを使用します。

1. Windows PowerShell コンソールで、次のコマンドを実行して、最新の Az PowerShell モジュールをインストールします。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

   > **注** : プロンプトが表示されたら、PSGalleryリポジトリからのインストールを続行することを選択します。


#### 作業 3 : Azure File Sync 評価ツールを実行する

1. RDP セッション内の Windows PowerShell コンソールで、次のコマンドを実行して、Package Management と PowerShellGet の最新バージョンをインストールします。

   ```powershell
   Install-Module -Name PackageManagement -Repository PSGallery -Force

   Install-Module -Name PowerShellGet -Repository PSGallery -Force
   ```

   > **注** : プロンプトが表示されたら、NuGetプロバイダーのインストールを続行することを選択します。

1. PowerShell セッションを再起動します。

1. Windows PowerShell コンソールで、次のコマンドを実行して、Azure File Sync PowerShell モジュールをインストールします。

   ```powershell
   Install-Module -Name Az.StorageSync -AllowClobber -Force
   ```

1. Windows PowerShell コンソールで、次のコマンドを実行して、Azure File Sync PowerShell モジュールをインストールします。

   ```powershell
   Invoke-AzStorageSyncCompatibilityCheck -Path 'S:\az10002bShare'
   ```

1. 出力結果を確認して、互換性のに問題が検出されていないことを確認します。

> **結果**: この演習では、Azure Storage アカウントとファイル共有を作成し、Azure File Sync で使用するために Windows Server 2016 を準備し、Azure File Sync 評価ツールを実行しました。


### 演習 2 : Azure File Sync を実装する

この演習の主な作業は次のとおりです。

1. ストレージ同期サービスを展開する。

1. Azure ファイル同期エージェントをインストールする。

1. Windows Server を ストレージ同期サービスに登録する。

1. 同期グループとクラウド エンドポイントを作成する。

1. サーバー エンドポイントを作成する。

1. Azure File Sync 操作を検証する。


#### 作業 1 : ストレージ同期サービスを展開する

1. Azure 仮想マシンの RDP セッションで、Server Manager の左ペインの [Local Server] を表示し、一次的に [**IE Enhanced Security Configuration**] を無効にします。

1. RDP セッション内で、Internet Explorer を起動し、[**http://portal.azure.com**](http://portal.azure.com) から Azure ポータルを開き、演習で使用している Microsoft アカウントでサインインします。

1. Azure ポータルから [**Create a resource**] ブレードを開きます。

1. [**New**] ブレードの、Azure Marketplace の検索ボックスに「**Azure File Sync**」と入力します。

1. 検索結果から [**Azure File Sync**] ブレードを開き、[Create] をクリックします。

1. [**Azure File Sync のデプロイ**] ブレードで、次のような設定のストレージ同期サービスを展開します。

   - Subscription : 前の作業で選択したサブスクリプション

   - Resource group : 「**az1000203b-RG**」という名前のリソース グループを新規作成します。

   - Storage sync service name : **az1000202b-ss**

   - Region : 演習の前半でストレージ アカウントを作成した Azure リージョン


#### 作業 2 : Azure ファイル同期エージェントをインストールする

1. RDP セッションで、Internet Explorer を別に立ち上げ、[**https://go.microsoft.com/fwlink/?linkid=858257**](https://go.microsoft.com/fwlink/?linkid=858257) から Microsoft Download Center にアクセスします。Azure File Sync Agent Windows Installer ファイル "**StorageSyncAgent_V6_WS2016.msi**" をダウンロードします。

1. ダウンロードが完了したら、[Storage Sync Agent Setup] ウィザードを実行し、既定の設定で Azure ファイル同期エージェントをインストールします。

1. Azure ファイル同期エージェントのインストールが完了したら、[**Azure File Sync - Server Registration**] ウィザードが自動で立ち上がります。


#### 作業 3 : Windows Server を ストレージ同期サービスに登録する

1. [**Azure File Sync - Server Registration**] ウィザードの最初のページで、この演習で使用している Microsoft アカウントでサインインします。

1. [**Azure File Sync - Server Registration**] ウィザードの [**Choose a Storage Sync Service**] 次のような設定で登録を行います。

   - Azure Subscription : この演習で使用しているサブスクリプション

   - Resource group : **az1000203b-RG**

   - Storage Sync Service : **az1000202b-ss**

1. プロンプトが表示されたら、この演習で使用している Microsoft アカウントで再度サインインします。


#### 作業 4 : 同期グループとクラウド エンドポイントを作成する

1. Azure 仮想マシンへの RDP セッションで、Azure ポータルからストレージ同期サービス [**az1000202b-ss**] ブレードを開きます。

1. [**az1000202b-ss**] ブレードで、 [**Sync group**] を開き、次のような設定の同期グループを作成します。

   - Sync group name : **az1000202b-syncgroup1**

   - Azure Subscription : この演習で使用しているサブスクリプション

   - Storage account : 演習 1 で作成したストレージ アカウントのリソース ID

   - Azure File Share : **az10002bshare1**


#### 作業 5 :  サーバー エンドポイントを作成する

1. Azure 仮想マシンへの RDP セッション内の Azure ポータルで、ストレージ同期サービス [**az1000202b-ss**] ブレードを開き、[**az1000202b-syncgroup1**] ブレードを表示します。

1. [**az1000202b-syncgroup1**] ブレードで [**Add server endpoint**] ブレードを開き、次のような設定のサーバー エンドポイントを作成します。

   - Registered server : **az1000201b-vm1**

   - Path : **S:\\az10002bShare**

   - Cloud Tiering : **Enabled**

      - Always preserve the specified percentage of free space on the volume : **15**

      - Only cache files that were accessed or modified within the specified number of days : **30**
   - Offline Data Transfer : **Disabled**



#### 作業 6 : Azure File Sync 操作を検証する

1. Azure 仮想マシンへの RDP セッション内の Azure ポータルで、**az1000202b-syncgroup1** ブレードから、サーバー エンドポイント **az100021b-vm1** の正常性状態を監視します。状態が、**Provisioning** から **Pending** に変わり、緑のチェックマークが表示されます。

   > **注** : 数分後に次の手順に進めます。

1. Azure ポータルから、演習の前半で作成したストレージ アカウントを開き、[**File Shares**] ブレードを表示して、[**az10002bshare1**] をクリックします。

1. [**az10002bshare1**] ブレードで、[**Connect**] をクリックします。

1. [**Connect**] ブレードで、Windows コンピューターからこのファイル共有に接続ための PowerShell コマンドをクリップボードにコピーします。

1. RDP セッション内で Windows PowerShell ISE セッションを起動します。

1. Windows PowerShell ISE セッションで、Script ペインを開き、ローカル PC で先ほどコピーしたコマンドをペーストします。

1. `New-PSDrive` コマンドレットの最後に "` -Persist`" スイッチを追加します。

1. スクリプトを実行し、出力結果から、Z: ドライブから Azure ストレージ同期サービス共有へのマッピングが成功していることを確認します。

1. RDP セッション内でエクスプローラーを開き、Z: ドライブを開き、"S:\\az10002bShare" と同じファイルが表示されていることを確認します。

1. Z: ドライブの各フォルダーのプロパティを表示し、Security タブを確認します。NTFS permissions のエントリを確認し、S: ドライブ上の対応するフォルダーに NTFS アクセスが許可されていることを確認します。


> **結果**: この演習では、ストレージ同期サービスを展開し、Azure ファイル同期エージェントをインスト ールし、Windows Server をストレージ同期サービスに登録しました。同期グループとクラウド エンド ポイントを作成し、サーバー エンドポイントを作成し、Azure File Sync 操作を検証しました。
