---
lab:
    title: 'Azure Site Recovery between Azure regions'
    module: 'Module 07 - Data Protection '
---

# 演習 : リージョン間の ASR

この演習のすべての作業には、Azure ポータルを使用します。

演習用ファイル :

- **Labfiles\\Module_07\\Azure_Site_Recovery_Between_Regions\\az-101-01_azuredeploy.json**

- **Labfiles\\Module_07\\Azure_Site_Recovery_Between_Regions\\az-101-01_azuredeploy.parameters.json**

### シナリオ

Adatum Corporation は、大規模な災害からも Azure 仮想マシンを保護するために、リージョン間の Azure Site Recovery (ASR) を実装したいと考えています。


### 目的

この演習を修了すると、次のことができるようになります。

- Recovery Services コンテナーを実装する。

- Azure Site Recovery を使用して、Azure リージョン間の Azure 仮想マシンのレプリケーションを構成する。


### 演習 1: Azure Site Recovery を構成するための準備を行う

この演習の主な作業は次のとおりです。

1. Azure Resource Manager (ARM) テンプレートを使用して、Azure 仮想マシンを展開する。

1. Recovery Services コンテナーを作成する。


#### 作業 1 : Azure Resource Manager (ARM) テンプレートを使用して、Azure 仮想マシンを展開する

1. 演習用の仮想マシンで、Microsoft Edge を起動します。[**http://portal.azure.com**](http://portal.azure.com) から Azure ポータル にアクセスします。演習用の Azure サブスクリプションの所有者ロールをもつ Microsoft アカウントでサインインします。

1. Azure ポータルから [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Template deployment**」と入力します。

1. 検索結果から [**テンプレートのデプロイ**] ブレードを開き、[<x3/>作成<x4/>] をクリックします。

1. [**カスタム デプロイ**] ブレードで、[**エディターで独自のテンプレートを作成する**] リンクをクリックします。このリンクが確認できない場合は [**テンプレートの編集**] をクリックします。

1. [**テンプレートの編集**] ブレードで、テンプレート ファイルを読み込みます。**Labfiles\\Module_07\\Azure_Site_Recovery_Between_Regions\\az-101-01_azuredeploy.json**.

   > **注** : テンプレートの内容を確認してください。ここでは、Windows Server 2016 Datacenter をホストする Azure 仮想マシンの展開を定義しています。

1. このテンプレートを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードから、[**パラメーターの編集**] ブレードを開きます。

1. [**パラメーターの編集**] ブレードで、パラメーター ファイルを読み込みます。**Labfiles\\Module_07\\Azure_Site_Recovery_Between_Regions\\az-101-01_azuredeploy.parameters.json**.

1. パラメーターを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードで、次の設定を行い、[購入] をクリックして展開を開始します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ : 「**az1010101-RG**」という名前のリソース グループを新規作成します。

   - 場所 : 演習環境に最も近い Azure リージョンの中で、Azure 仮想マシンをプロビジョ二ングできる場所を選択します。

   - Vm Name: **az1010101-vm**

   - Admin Username : **Student**

   - Admin Password : **Pa55w.rd1234**

   - Image Publisher : **MicrosoftWindowsServer**

   - Image Offer : **WindowsServer**

   - Image SKU : **2016-Datacenter-Server-Core-smalldisk**

   - Vm Size : **Standard_DS1_v2**
   > **注** : Azure 仮想マシンをプロビジョニングできる Azure リージョンを確認するには、こちらを参照してください。[**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

   > **注** : 展開が完了するのを待つ必要はありません。次の作業に進んでください。"**az1010101-vm**" という仮想マシンは、次の演習で使います。


#### 作業 2 : Recovery Services コンテナーを作成する

1. Azure ポータルから [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Backup and Site Recovery**」と入力します。

1. 検索結果から [**バックアップおよびサイトの回復**] ブレードを開き、[作成] をクリックし、[**Recovery Services コンテナーの作成**] ブレードを開きます。

1. [**Recovery Services コンテナーの作成**] ブレードで、次のような設定の Recovery Services コンテナーを作成します。

   - サブスクリプション : 前の手順で選択したサブスクリプション

   - リソース グループ : 「**az1010102-RG**」という名前のリソース グループを新規作成します。

   - 資格情報コンテナー名 : **vaultaz1010102**

   - リージョン : サブスクリプション内で有効なリージョンを指定しますが、先ほど Azure 仮想マシンを展開したリージョンとは異なるリージョンを指定します。

> **結果**この演習では、Azure Resource Manager テンプレートを使用して Azure 仮想マシンを展開し、Azure 仮想マシン ディスク ファイルのコンテンツのレプリケートに使用する Azure Site Recovery コンテナーを作成しました。


### 演習 2 : Azure Site Recovery を使用して、Azure リージョン間の Azure 仮想マシンのレプリケーションを構成する

この演習の主な作業は次のとおりです。

1. Azure 仮想マシンのレプリケーションを構成する

1. Azure 仮想マシンのレプリケーション設定を確認する


#### 作業 1 : Azure 仮想マシンのレプリケーションを構成する

> **注** : この作業を開始する前に、演習 1 で開始したテンプレートの展開が完了していることを確認してください。

1. Azure ポータルから [すべてのサービス] を開き、検索ボックスに「Recovery Services コンテナー」と入力し、[Recovery Services コンテナー] ブレードを開きます。[**vaultaz1010102**] を表示します。

1. [**vaultaz1010102**] ブレードで、[レプリケート] をクリックします。

1. [ソース] ブレードで、次のように設定し、[OK] をクリックします。

   - ソース : **Azure**

   - ソースの場所 : 演習 1 で仮想マシンを展開した Azure リージョン

   - Azure 仮想マシンのデプロイメント モデル : **リソース マネージャー**

   - ソース サブスクリプション : 演習 1 で選択したサブスクリプション

   - ソース リソース グループ : **az1010101-RG**

1. [仮想マシン] ブレードで、次のように設定し、[OK] をクリックします。

   - 仮想マシン : **az1010101-vm**

1. [設定の構成] ブレードで、次のように設定し、[**ターゲット リソースの作成**] をクリックします。

   - ターゲットの場所 : サブスクリプション内で有効なリージョンを指定しますが、先ほど Azure 仮想マシンを展開したリージョンとは異なるリージョンを指定します。可能であれば、Azure Site Recovery コンテナーを展開したのと同じ Azure リージョンを使用してください。

   - ターゲット リソース グループ : ** (新規) az1010101-RG-asr**

   - ターゲット 仮想ネットワーク : ** (新規) az1010101-vnet-asr**

   - キャッシュ ストレージ アカウント : 既定の設定のまま

   - レプリカ マネージド ディスク : ** (新規) 1個の Premium ディスク、0 個の Standard ディスク**
  
   - ターゲット可用性セット : **該当なし**

   - レプリケーション ポリシー : [カスタマイズ] をクリックして、"**12-hour-retention-policy**" という名前のポリシーを新規作成します。

   - 復旧ポイントの保持期間 : **12** 時間

   - アプリ整合性スナップショットの頻度 : **6** 時間

   - マルチ VM 整合性 : **いいえ**

1. [**設定の構成**] ブレードで、ターゲット リソースを作成します。[**レプリケーションを有効にする**] ブレードが表示されるまで待ちます。

1. [**レプリケーションを有効にする**] をクリックして、レプリケーションを有効にします。


#### 作業 2 : Azure 仮想マシンのレプリケーション設定を確認する

1. Azure ポータルから、Recovery Service コンテナー [vaultaz1010102] を開き、[**レプリケートされたアイテム**] ブレードを表示します。

1. [**vaultaz1010102 - レプリケートされたアイテム**] ブレードで、仮想マシン "**az1010101-vm**" が表示されていることを確認します。また、**レプリケーション ヘルス**が "**正常**" であり、**状態** が "**プロテクト**" となっていることも確認します。

1. [**vaultaz1010102 - レプリケートされたアイテム**] で、レプリケートされた仮想マシン [**az1010101-vm**] を開きます。

1. レプリケートされたアイテム [**az1010101-vm**] ブレードで、**正常性と状態**、**フェールオーバーの準備**、**最新の回復ポイント**、**インフラストラクチャ ビュー** セクションを確認します。ツール バーには、**フェールオーバー** アイコンと、**テスト フェールオーバー**アイコンもあります。

   > **注** : この後の作業は、オプションであり、採点対象ではありません。

1. 時間があれば、レプリケーションの状態が "**100% 同期済み**" になるのを待ちます。完了までには 90 分程度かかります。

1. レプリケートされた仮想マシンを表示して、**RPO** の値や、[最新の回復ポイント] をクリックして**最新のクラッシュ整合性** と **最新のアプリ整合性**を確認します。

1. [テスト フェールオーバー] をクリックして、仮想ネットワーク **az1010101-vnet-asr** へのフェールオーバーを実行してみます。

> **結果** : この演習では、Azure 仮想マシンのレプリケーションを構成し、Azure 仮想マシンのレプリケーション設定を確認しました。

## 演習 3 : 演習用リソースを削除する

#### 作業 1 : Cloud Shell を開く

1. Azure ポータルの上部にある **Cloud Shell** アイコンをクリックして、Cloud Shell ペインを開きます。

1. Cloud Shell インターフェイスで [**Bash**] を選択します。

1. **Cloud Shell** のコマンド プロンプトで、次のコマンドを入力し [**Enter**] を押します。これにより、この演習で作成したすべてのリソース グループが表示されます。

   ```sh
   az group list --query "[?starts_with(name,'az10101')].name" --output tsv
   ```

1. 出力結果を確認し、リストにはこの演習で作成したリソース グループのみが含まれることを確認します。これらのリソース グループを、次の作業で削除します。

#### 作業 2 : リソース グループを削除する

1. **Cloud Shell** のコマンド プロンプトで、次のコマンドを入力し [**Enter**] を押します。これにより、この演習で作成したリソース グループを削除できます。

   ```sh
   az group list --query "[?starts_with(name,'az10101')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```
   > **注** : "...cannot perform delete operation because following scope(s) are locked..." というエラー メッセージが出た場合は、次の手順でコマンドを実行し、削除を妨げているリソースのロックを解除する必要があります。
   ```sh
   lockedresource=$(az resource list --resource-group az1010101-RG-asr --resource-type Microsoft.Compute/disks --query "[?starts_with(name,'az10101')].name" --output tsv)
   
   az disk revoke-access -n $lockedresource --resource-group az1010101-RG-asr
   
   lockid=$(az lock show --name ASR-Lock --resource-group az1010101-RG-asr --resource-type Microsoft.Compute/disks --resource-name $lockedresource --output tsv --query id)
   
   az lock delete --ids $lockid
   
   az group list --query "[?starts_with(name,'az10101')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. ポータルの下部に表示される **Cloud Shell** プロンプトを閉じます。

> **結果** : この演習では、演習内で使用したリソースをすべて削除しました。
