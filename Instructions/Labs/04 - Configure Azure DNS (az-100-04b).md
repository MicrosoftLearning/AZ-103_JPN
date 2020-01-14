---
lab:
    title: 'Configure Azure DNS'
    module: 'Module 04 - Virtual Networking'
---

# 演習 : Azure DNS の構成

この演習のすべての作業には、Azure ポータル (PowerShell クラウド シェル セッションを含む) を使用します。

> **注** : クラウド シェルを使用しない場合は、Azure PowerShell 1.2.0 (またはそれ以降の) モジュールをインストールした演習用仮想マシンが必要となります。 [https://docs.microsoft.com/en-us/powershell/azure/install-az-ps](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps)

演習用ファイル :

- **Labfiles\\Module_04\\Configure_Azure_DNS\\az-100-04b_01_azuredeploy.json**

- **Labfiles\\Module_04\\Configure_Azure_DNS\\az-100-04b_02_azuredeploy.json**

- **Labfiles\\Module_04\\Configure_Azure_DNS\\az-100-04_azuredeploy.parameters.json**


### シナリオ

Adatum Corporation は、独自の DNS サーバーを展開することなく、Azure にパブリックおよびプライベート DNS サービスを実装したいと考えています。


### 目的

この演習を修了すると、次のことができるようになります。

- パブリック ドメイン用に Azure DNS を構成する。

- プライベート ドメイン用に Azure DNS を構成する。


### 演習 1 : パブリック ドメイン用に Azure DNS を構成する

この演習の主な作業は次のとおりです。

1. パブリック DNS ゾーンを作成する。

1. パブリック DNS ゾーンに DNS レコードを作成する。

1. パブリック ドメインの Azure DNS ベースの名前解決を検証する。


#### 作業 1 : パブリック DNS ゾーンを作成する

1. 演習用の仮想マシンで、Microsoft Edge を起動します。[**http://portal.azure.com**](http://portal.azure.com) から Azure ポータル にアクセスします。演習用の Azure サブスクリプションの所有者ロールをもつ Microsoft アカウントでサインインします。

1. Azure ポータル から [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの Azure Marketplace 検索ボックスに「**DNS zone**」と入力します。

1. [**DNS ゾーン**] を選択し、[**作成**] をクリックします。

1. [**DNS ゾーンの作成**] ブレードで、次のような設定で DNS ゾーンを新規作成します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ :「**az1000401b-RG**」という名前のリソース グループを新規作成します。

   - 名前 : 有効な一意の DNSドメイン名 (**.com** 名前空間)。

   - リソース グループの場所 : **(米国) 米国東部** (もしくはお近くのサポートされたリージョン)
 
1. その他は既定値を使用し、[**確認および作成**] をクリックします。
1. [**作成**] をクリックします。


#### 作業 2 : パブリック DNS ゾーンに DNS レコードを作成する

1. Azure ポータル の Cloud Shell ペインから、PowerShell セッションを開始します。

   > **注** : 現在の Azure サブスクリプションで Cloud Shell を初めて起動する場合、Cloud Shell ファイルを維持するための Azure ファイル共有の作成を要求されます。既定のまま設定すると、自動生成されたリソース グループにストレージ アカウントが作成されます。

1. Cloud Shell ペインで次のコマンドを実行して、演習用コンピューターのパブリック IP アドレスを特定します。

   ```pwsh
   Invoke-RestMethod http://ipinfo.io/json | Select-Object -ExpandProperty IP
   ```

   > **注** : IP アドレスを控えておいてください。次の作業で使用します。

1. Cloud Shell ペインで次のコマンドを実行し、パブリック IP アドレス リソースを作成します。

   ```pwsh
   $rg = Get-AzResourceGroup -Name az1000401b-RG

   New-AzPublicIpAddress -ResourceGroupName $rg.ResourceGroupName -Sku Basic -AllocationMethod Static -Name az1000401b-pip -Location $rg.Location
   ```

1. Azure ポータルで [**az1000401b-RG**] リソース グループ ブレードを開きます。

1. [**az1000401b-RG**] リソース グループ ブレードから、新規作成した DNS ゾーンを表示します。

1. DNS ゾーン ブレードから、[**+ レコード セット**] をクリックし、[**レコード セットの追加**] ブレードを開きます。

1. 次のような設定で DNS レコードを作成します。

   - 名前 : **mylabvmpip**

   - 種類 : **A**

   - エイリアスのレコード セット : **いいえ**

   - TTL : **1**

   - TTL の単位 : **時間**

   - IP アドレス : 先ほど確認した演習用 PC のIP アドレス

1. 概要ブレードで再度 [**+ レコード セット**] をクリックし、次のような設定で別のレコードを作成します。

   - 名前 : **myazurepip**

   - 種類 : **A**

   - エイリアスのレコード セット : **はい**

   - エイリアスの種類 : **Azure リソース**

   - サブスクリプションの種類 : この演習で使用している Azure サブスクリプション

   - Azure リソース : **az1000401b-pip**

   - TTL : **1**

   - TTL の単位 : **時間**


#### 作業 3 : パブリック ドメインの Azure DNS ベースの名前解決を検証する

1. DNS ゾーン ブレードで、先ほど作成したネーム サーバーの一覧を確認します。一覧の最初の 1 つを次の手順で命名します。

1. 演習用の仮想マシンで、コマンド プロンプトを起動し、次のコマンドを実行して、新しく作成した 2 つの DNS レコードの名前解決を検証します。(&lt;custom_DNS_domain&gt; には、最初の作業で指定したカスタム DNS 名を、&lt;name_server&gt; には前の作業で指定した DNS サーバー名を入力します。)

   ```
   nslookup mylabvmpip.<custom_DNS_domain> <name_server>

   nslookup myazurepip.<custom_DNS_domain> <name_server>
   ```

1. 返された IP アドレスが、この作業の前半で特定したものと一致していることを確認します。

> **結果**: この演習では、パブリック DNS ゾーンを作成し、パブリック DNS ゾーンに DNS レコードを作成し、パブリック ドメインの Azure DNS ベースの名前解決を検証しました。


### 演習 2 : プライベート ドメイン用に Azure DNS を構成する

この演習の主な作業は次のとおりです。

1. マルチ仮想ネットワーク環境をプロビジョニングする。

1. プライベート DNS ゾーンを作成する。

1. Azure 仮想マシンを仮想ネットワークに展開する。

1. プライベート ドメインの Azure DNS ベースの名前予約と解決を検証する。


#### 作業 1 : マルチ仮想ネットワーク環境をプロビジョニングする

1. Azure ポータル の Cloud Shell ペインから、PowerShell セッションを開始します。

1. Cloud Shell ペインで次のコマンドを実行し、リソース グループを作成します。

   ```pwsh
   $rg1 = Get-AzResourceGroup -Name 'az1000401b-RG'

   $rg2 = New-AzResourceGroup -Name 'az1000402b-RG' -Location $rg1.Location
   ```

1. Cloud Shell ペインで次のコマンドを実行し、2 つの Azure 仮想ネットワークを作成します。

   ```pwsh
   $subnet1 = New-AzVirtualNetworkSubnetConfig -Name subnet1 -AddressPrefix '10.104.0.0/24'

   $vnet1 = New-AzVirtualNetwork -ResourceGroupName $rg2.ResourceGroupName -Location $rg2.Location -Name az1000402b-vnet1 -AddressPrefix 10.104.0.0/16 -Subnet $subnet1

   $subnet2 = New-AzVirtualNetworkSubnetConfig -Name subnet1 -AddressPrefix '10.204.0.0/24'

   $vnet2 = New-AzVirtualNetwork -ResourceGroupName $rg2.ResourceGroupName -Location $rg2.Location -Name az1000402b-vnet2 -AddressPrefix 10.204.0.0/16 -Subnet $subnet2
   ```

#### 作業 2 : プライベート DNS ゾーンを作成する

1. Cloud Shell ペインで次のコマンドを実行し、プライベート DNS ゾーンを作成します。1 つ目の仮想ネットワーク間では登録をサポートし、2 つ目の仮想ネットワーク間では解決をサポートします。

   ```pwsh
   $vnet1 = Get-AzVirtualNetwork -Name az1000402b-vnet1

   $vnet2 = Get-AzVirtualNetwork -name az1000402b-vnet2

   New-AzDnsZone -Name adatum.local -ResourceGroupName $rg2.ResourceGroupName -ZoneType Private -RegistrationVirtualNetworkId @($vnet1.Id) -ResolutionVirtualNetworkId @($vnet2.Id)
   ```

   > **注** : Azure DNS ゾーンに割り当てた仮想ネットワークに、リソースを含むことはできません。

1. Cloud Shell ペインで次のコマンドを実行し、プライベート DNS ゾーンが正常に作成されたことを確認します。

   ```pwsh
   Get-AzDnsZone -ResourceGroupName $rg2.ResourceGroupName
   ```


#### 作業 3 : Azure 仮想マシンを仮想ネットワークに展開する

1. Cloud Shell ペインで、ペイン内の [**ファイルのアップロード/ダウンロード**] をクリックし、**az-100-04b_01_azuredeploy.json**、**az-100-04b_02_azuredeploy.json**、**az-100-04_azuredeploy.parameters.json** ファイルをアップロードします。

1. Cloud Shell ペインで次のコマンドを実行し、最初の仮想ネットワークに、Azure 仮想マシンを展開します。

   ```pwsh
   cd $home

   New-AzResourceGroupDeployment -ResourceGroupName $rg2.ResourceGroupName -TemplateFile "./az-100-04b_01_azuredeploy.json" -TemplateParameterFile "./az-100-04_azuredeploy.parameters.json" -AsJob
   ```

1. Cloud Shell ペインで次のコマンドを実行し、2 つ目の仮想ネットワークに、Azure 仮想マシンを展開します。

   ```pwsh
   New-AzResourceGroupDeployment -ResourceGroupName $rg2.ResourceGroupName -TemplateFile "./az-100-04b_02_azuredeploy.json" -TemplateParameterFile "./az-100-04_azuredeploy.parameters.json" -AsJob
   ```

   > **注** : 両方の仮想マシンの展開が完了するのを待って、次の作業に進みます。Cloud Shell ペインで、`Get-Job` コマンドレットを実行すると、進捗が確認できます。


#### 作業 4 : プライベート ドメインの Azure DNS ベースの名前予約と解決を検証する

1. Azure ポータルで、[**az1000402b-vm2**] ブレードを開きます。

1. [**az1000402b-vm2**] ブレードの [**概要**] ペインから、[**接続**] をクリックして RDP ファイルを生成します。この RDP ファイルを使用して、**az1000402b-vm2** に接続します。

1. プロンプトが表示されたら、次の資格情報を指定して認証します。

   - ユーザー名 : **Student**

   - パスワード : **Pa55w.rd1234**

1. **az1000402b-vm2** へのリモート デスクトップ セッション内で、コマンド プロンプト ウィンドウを開き、次のコマンドを実行します。

   ```
   nslookup az1000402b-vm1.adatum.local
   ```

1. 名前解決されたことを確認します。

1. 演習用仮想マシンに戻ります。Azure ポータルの Cloud Shell ペインを開き、次のコマンドを実行し、プライベート DNS ゾーンに追加の DNS レコードを作成します。

   ```pwsh
   New-AzDnsRecordSet -ResourceGroupName $rg2.ResourceGroupName -Name www -RecordType A -ZoneName adatum.local -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.104.0.4")
   ```

1. 再度 **az1000402b-vm2** へのリモート デスクトップ セッションに戻り、コマンド プロンプト ウィンドウで次のコマンドを実行します。

   ```
   nslookup www.adatum.local
   ```

1. 名前解決されたことを確認します。

> **結果** : この演習では、マルチ仮想ネットワーク環境のプロビジョニング、プライベート DNS ゾーンの作成、Azure 仮想マシンの仮想ネットワークへの展開、プライベート ドメインの Azure DNS ベースの名前の予約と解決を検証しました。

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
