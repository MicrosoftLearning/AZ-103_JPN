---
lab:
    title: 'Deploy and Manage Virtual Machines'
    module: 'Module 02 - Azure Virtual Machines'
---

# 演習 : 仮想マシンの展開と管理

この演習のすべての作業には、Azure ポータル (PowerShell クラウド シェル セッションを含む) を使用します。ただし、リモート デスクトップからの Azure 仮想マシンでの操作を含む、演習 2 の作業 2 と作業 3 は除きます。

> **注** : クラウド シェルを使用しない場合は、Azure PowerShell モジュールをインストールした演習用仮想マシンが必要となります。[**https://docs.microsoft.com/en-us/powershell/azure/install-Az-ps**](https://docs.microsoft.com/en-us/powershell/azure/install-Az-ps)

演習用ファイル :

- **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_azuredeploy.json**

- **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_azuredeploy.parameters.json**

- **Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_install_iis_vmss.zip**

### シナリオ

Adatum Corporation は、Azure 仮想マシンとスケールセットを使用してワークロードを実装したいと考えています。


### 目的

この演習を修了すると、次のことができるようになります。

- Azure ポータル、Azure PowerShell、ARM テンプレートを使用して Azure 仮想マシンを展開する。

- Windows および Linux OS を実行する Azure 仮想マシンのネットワーク設定を構成する。

- スケール セットを展開して構成する。


### 演習 1 : Azure ポータル、Azure PowerShell、ARM テンプレートを使用して Azure 仮想マシン を展開する

この演習の主な作業は次のとおりです。

1. Azure ポータル を使用して、Windows Server 2016 Datacenter を実行する Azure 仮想マシンを可用性セ ットに展開する。

1. Azure PowerShell を使用して、Windows Server 2016 Datacenter を実行する Azure 仮想マシンを既存の可用性セットに展開する。

1. ARM テンプレートを使用して、Linux を実行する 2 つの Azure 仮想マシンを可用性セットに展開する。


#### 作業 1 : Azure ポータル を使用して、Windows Server 2016 Datacenter を実行する Azure 仮想マシンを可用性セ ットに展開する

1. 演習用の仮想マシンで、Microsoft Edge を起動します。[**http://portal.azure.com**](http://portal.azure.com)から Azure ポータル にアクセスします。演習用の Azure サブスクリプションの所有者ロールをもつ Microsoft アカウントでサインインします。

1. Azure ポータル から [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Windows Server**」と入力します。検索結果のリストから、[**Windows Server**] を選択します。

1. Windows Server ページで、ドロップダウン メニューから **[smalldisk] Windows Server 2016 Datacenter** を選択し、[**作成**] をクリックします。

1. [**仮想マシンの作成**] ブレードから、次のような設定の仮想マシンを展開します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ : 「**az1000301-RG**」 という名前のリソース グループを新規作成します。

   - 仮想マシン名 : **az1000301-vm0**

   - 地域 : **(米国) 米国東部** (もしくはお近くの地域)

      > **注** : Azure 仮想マシンをプロビジョニングできる Azure リージョンを確認するには、こちらを参照してください。[**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

   - 可用性オプション : **可用性セット**

   - 可用性セット : **新規作成** をクリックし、「**az1000301-avset0**」という名前の可用性セットを作成します。障害ドメインは "**2**"、更新ドメインは "**5**" とします。[**OK**] をクリックします。

   - イメージ : **[smalldisk] Windows Server 2016 Datacenter**

   - サイズ : **Standard DS2_v2**

   - ユーザー名 : **Student**

   - パスワード : **Pa55w.rd1234**

   - パブリック受信ポート : **なし**

   - Windows Server ライセンスを既にお持ちの場合 : **いいえ**

1. **次: ディスク >** をクリックします。

   - OS ディスクの種類 : **Standard HDD**

1. **次: ネットワーク >** をクリックします。

1. ネットワーク タブで 仮想ネットワークの [**新規作成**] をクリックします。既に割り当てられた仮想ネットワーク名を使用し、次のとおり設定します。

   - アドレス範囲 : **10.103.0.0/16**

   - サブネット名 : **subnet0**

   - サブネットのアドレス範囲 : **10.103.0.0/24**

1. [**OK**] をクリックします。

1. **次: 管理 >** をクリックします。

   - ブート診断 : **オフ**

1. その他は既定値を使用し、[**確認および作成**] をクリックします。
1. [**作成**] をクリックします。

   > **注** : 演習 2 では、作成したネットワーク セキュリティ グループの設定を行います。

   > **注** : 展開が完了するのを待って、次の作業に移ってください。処理の完了までには、5 分ほどかかります。


#### 作業 2 : Azure PowerShell を使って、Windows Server 2016 Datacenter を実行している Azure 仮想マシンを既存の可用性セットに展開する

1. Azure ポータル の Cloud Shell ペインから、PowerShell セッションを開始します。

   > **注** : 現在の Azure サブスクリプションで Cloud Shell を初めて起動する場合、Cloud Shell ファイルを維持するための Azure ファイル共有の作成を要求されます。既定のまま設定すると、自動生成されたリソース グループにストレージ アカウントが作成されます。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   $vmName = 'az1000301-vm1'
   $vmSize = 'Standard_DS2_v2'
   ```

   > **注** : このコマンドで Azure 仮想マシンの名前とサイズを指定する変数の値を設定します。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   $resourceGroup = Get-AzResourceGroup -Name 'az1000301-RG'
   $location = $resourceGroup.Location
   ```

   > **注** : これらのコマンドで ターゲットとなるリソース グループとその場所を指定する変数の値を設定します。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pswh
   $availabilitySet = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup.ResourceGroupName -Name 'az1000301-avset0'
   
   $vnet = Get-AzVirtualNetwork -Name 'az1000301-RG-vnet' -ResourceGroupName    $resourceGroup.ResourceGroupName
   
   $subnetid = (Get-AzVirtualNetworkSubnetConfig -Name 'subnet0' -VirtualNetwork $vnet).Id
   ```

   > **注** : これらのコマンドで新しい Azure 仮想マシンを展開する可用性セット、仮想ネットワーク、およびサブネットを指定する変数の値を設定します。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   $nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -Name "$vmName-nsg"
   
   $pip = New-AzPublicIpAddress -Name "$vmName-ip" -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -AllocationMethod Dynamic 
   
   $nic = New-AzNetworkInterface -Name "$($vmName)$(Get-Random)" -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -SubnetId $subnetid -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
   ```

   > **注** : これらのコマンドで、新しい Azure 仮想マシンで使用する新しいネットワーク セキュリティ グループ、パブリック IP アドレス、およびネットワーク インターフェイスを作成します。

   > **注** : 演習 2 では、作成したネットワーク セキュリティ グループの設定を行います。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   $adminUsername = 'Student'
   
   $adminPassword = 'Pa55w.rd1234'
   
   $adminCreds = New-Object PSCredential $adminUsername, ($adminPassword | ConvertTo-SecureString -AsPlainText -Force)
   ```

   > **注** : これらのコマンドで、新しい Azure 仮想マシンのローカル管理者アカウントの資格情報を指定する変数の値を設定します。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   $publisherName = 'MicrosoftWindowsServer'
   
   $offerName = 'WindowsServer'
   
   $skuName = '2016-Datacenter'
   ```

   > **注** : これらのコマンドで、新しい Azure 仮想マシンのプロビジョニングに使用される Azure Marketplace イメージのプロパティを指定する変数の値を設定します。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   $osDiskType = (Get-AzDisk -ResourceGroupName $resourceGroup.ResourceGroupName)[0].Sku.Name
   ```

   > **注** : このコマンドで、新しい Azure 仮想マシンのオペレーティング システム ディスクの種類を指定する変数の値を設定します。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $availabilitySet.Id
   
   Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
   
   Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $adminCreds 
   
   Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version 'latest'
   
   Set-AzVMOSDisk -VM $vmConfig -Name "$($vmName)_OsDisk_1_$(Get-Random)" -StorageAccountType $osDiskType -CreateOption fromImage
   
   Set-AzVMBootDiagnostic -VM $vmConfig -Disable
   ```

   > **注** : これらのコマンドで、仮想マシンのサイズ、可用性セット、ネットワーク インターフェイス、コンピューター名、ローカル管理者の資格情報、ソース イメージ、オペレーティング システム ディスク、ブート診断設定など、新しい Azure 仮想マシンのプロビジョニングに使用されるAzure 仮想マシン構成オブジェクトのプロパティを設定します。

1. Cloud Shell ペインで、次のコマンドを実行します。

   ```pwsh
   New-AzVM -ResourceGroupName $resourceGroup.ResourceGroupName -Location $location -VM $vmConfig
   ```

   > **注** : このコマンドで、新しい Azure 仮想マシンの展開を開始します。

   > **注** : 展開が完了するのを待つ必要はありません。次の作業に進んでください。


#### 作業 3 : ARM テンプレートを使用して、Linux を実行する 2 つの Azure 仮想マシンを可用性セットに展開する

1. Azure ポータル から [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Template deployment**」と入力します。[**Template deployment (deploy using custom templates)**] を選択します。

1. [**作成**] をクリックします。

1. [**カスタム デプロイ**] ブレードで、[**エディターで独自のテンプレートを作成する**] リンクをクリックします。このリンクが確認できない場合は [**テンプレートの編集**] をクリックします。

1. [**テンプレートの編集**] ブレードで、テンプレート ファイルを読み込みます。**Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_azuredeploy.json**

   > **注** : テンプレートの内容を確認してください。このテンプレートでは、Linux Ubuntu をホストする 2 つの Azure 仮想マシンを、可用性セットと既存の仮想ネットワーク **az1000301-vnet0** に展開しています。この仮想ネットワークは現在の展開に存在していません。以下のパラメーターで仮想ネットワーク名を変更します。

1. このテンプレートを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードから、[**パラメーターの編集**] ブレードを開きます。

1. [**パラメーターの編集**] ブレードで、パラメーター ファイルを読み込みます。**Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_azuredeploy.parameters.json**

1. パラメーターを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードで、次の設定を行ったら、[**上記の使用条件に同意する**] をチェックし、[**購入**] をクリックして展開を開始します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ : 「**az1000302-RG**」という名前のリソース グループを新規作成します。

   - 場所 : この演習の最初に設定した Azure リージョンと同じ場所を指定します。

   - Vm Name Prefix: **az1000302-vm**

   - Nic Name Prefix: **az1000302-nic**

   - Pip Name Prefix: **az1000302-ip**

   - Admin Username: **Student**

   - Admin Password: **Pa55w.rd1234**

   - Virtual Network Name: **az1000301-RG-vnet** _(テンプレートの既定値から変更します。)_

   - Image Publisher: **Canonical**

   - Image Offer: **UbuntuServer**

   - Image SKU: **16.04.0-LTS**

   - Vm Size: **Standard_DS2_v2**
   > **注** : 展開が完了するのを待って、次の作業に移ってください。処理の完了までには、5 分ほどかかります。

> **結果** : この演習では、Azure ポータル を使用する方法で、Windows Server 2016 Datacenter を実行する Azure 仮想マシン を可用性セットに展開し、Azure PowerShell を使用する方法で、同じ可用性セットに Windows Server 2016 Datacenter を実行する別の Azure 仮想マシン を展開し、ARM テンプレートを使用する方法で、Linux Ubuntu を実行する 2 つの Azure 仮想マシン を可用性セットに展開しました。

> **注** : テンプレートを使用して Linux Ubuntu サーバーをホストする 2 つの Azure 仮想マシンを展開したように、テンプレートを使用して、Windows Server 2016 Datacenter をホストする 2 つの Azure 仮想マシンを 1 操作で展開することもできます。作業 1 と 2 で、これらの Azure 仮想マシンを別々の作業で展開したのは、Azure ポータル と Azure PowerShell を使用した展開を習得していただくためです。


### 演習 2 : Windows および Linux OS を実行する Azure 仮想マシンのネットワーク設定を構成する

この演習の主な作業は次のとおりです。

1. Azure 仮想マシンの静的プライベートおよびパブリック IP アドレスを構成する。

1. パブリック IP アドレスを介して、Windows Server 2016 Datacenter を実行する Azure 仮想マシンに接続する。

1. プライベート IP アドレスを介して、Linux Ubuntu サーバーを実行する Azure 仮想マシンに接続する。


#### 作業 1 : Azure 仮想マシンの静的プライベートおよびパブリック IP アドレスを構成する

1. Azure ポータル から [**az1000301-vm0**] ブレードを開きます。

1. [**az1000301-vm0**] ブレードから [**ネットワーク**] ブレードを開きます。ここから、ネットワーク インターフェイスに割り当てられた **az1000301-vm0-ip** というパブリック IP アドレスの設定が確認できます。

1. [**ネットワーク**] ブレードで、パブリック IP アドレスを表すリンク (NIC パブリック IP のリンク) をクリックします。

1. [az1000301-vm0-ip] ブレードで、[**構成**] をクリックします。

1. パブリック IP アドレスの割り当てを [**静的**] に変更し、[**保存**] をクリックします。

   > **注** : この **az1000301-vm0** のネットワーク インターフェイスに割り当てられたパブリック IP アドレスは控えておいてください。この演習の後半で使用します。

1. Azure ポータルから [**az1000302-vm0**] ブレードを開きます。

1. [**az1000302-vm0**] ブレードで [**ネットワーク**] ブレードを開きます。

1. [**az1000302-vm0 - ネットワーク**] ブレードで、ネットワーク インターフェイスを表すリンク (ネットワーク インターフェイスに接続:) をクリックします。

1. **az1000302-vm0** のネットワーク インターフェイスのプロパティが表示されます。[**IP 構成**] ブレ―ドを開きます。

1. [**IP 構成**] ブレードで、**ipconfig1** のプライベート IP アドレス を「静的」、「**10.103.0.100**」に設定し、[**保存**] をクリックします。

   > **注** : プライベート IP アドレスの割り当てを変更すると、Azure 仮想マシンの再起動が必要となります。

   > **注** : Azure 仮想マシンには、静的または動的に割り当てられたパブリックおよびプライベート IP アドレスを介して接続できます。静的 IP 割り当ての選択は、これらの IP アドレスが IP フィルタリング、ルーティングと組み合わせて使用されるシナリオ、または DNS サーバーとして機能する Azure 仮想マシンのネットワークインターフェイスに割り当てられる場合によく行われます。


#### 作業 2 : パブリック IP アドレスを介して、Windows Server 2016 Datacenter を実行する Azure 仮想マシンに接続する

1. Azure ポータル から [**az1000301-vm0**] ブレードを開きます。

1. [**az1000301-vm0**] ブレードで、[**ネットワーク**] ブレードを開きます。

1. [**az1000301-vm0 - ネットワーク**] ブレードで、**az1000301-vm0** のネットワーク インターフェイスに割り当てられたネットワーク セキュリティ グループの受信ポート規則を確認します。

   > **注** : 組み込み規則で構成される既定の設定では、インターネット経由の接続はすべて拒否されます。(RDP ポート TCP 3389 経由の接続を含みます。)

1. [受信ポートの規則を追加する] をクリックして、既存のネットワーク セキュリティ グループに、次のような設定の受信セキュリティ規則を追加します。

   - ソース : **Any**

   - ソース ポート範囲 : **\***

   - 宛先 : **Any**

   - 宛先ポート範囲 : **3389**

   - プロトコル : **TCP**

   - アクション : **許可**

   - 優先度 : **100**

   - 名前 : **AllowInternetRDPInBound**

1. Azure ポータルで [**az1000301-vm0**] ブレードの [**概要**] ペインを開きます。

1. **az1000301-vm0** ブレードの [**概要**] ペインから、[**接続**] をクリックして RDP ファイルを生成します。この RDP ファイルを使用して、**az1000301-vm0** に接続します。

1. プロンプトが表示されたら、次の資格情報を指定して認証します。

   - ユーザー名 : **Student**

   - パスワード : **Pa55w.rd1234**


#### 作業 3 : プライベート IP アドレスを介して、Linux Ubuntu サーバーを実行する Azure 仮想マシン に接続する

1. **az1000301-vm0** への RDP セッション内で、**コマンド プロンプト** を起動します。

1. コマンド プロンプトで、次のコマンドを実行します。

   ```
   nslookup az1000302-vm0
   ```

1. 出力結果を確かめ、名前解決がこの演習の最初の作業で割り当てた IP アドレスであることを確認します。(**10.103.0.100**)

   > **注** : これが、正しい状態です。Azure は、仮想ネットワーク内に組み込みの DNS 名前解決を提供します。

1. **az1000301-vm0** への RDP セッション内で、サーバー マネージャーから [**Local Server**] をクリックします。そして [**IE Enhanced Security Configuration**] を [Off] にします。

1. **az1000301-vm0** への RDP セッション内で、Internet Explorer を起動し、"**putty.exe**" を次のページからダウンロードします。[**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. **putty.exe** を使用して、**SSH** プロトコル (TCP 22) 経由で、**az1000302-vm0** のプライベート IP アドレスに接続できることを確認します。

1. プロンプトが表示されたら、次の値を指定して認証します。

   - ユーザー名 : **Student**

   - パスワード : **Pa55w.rd1234**
   > **注** : ユーザー名もパスワードも大文字小文字を正確に入力する必要があります。

1. 認証に成功したら、**az1000301-vm0** への RDP セッションを終了します。

1. 演習用の 仮想マシンで、Azure ポータルから [**az1000302-vm0**] ブレードを開きます。

1. [**az1000302-vm0**] ブレードで、[**ネットワーク**] ブレードを開きます。

1. [**az1000302-vm0 - ネットワーク**] ブレードで、**az1000301-vm0** のネットワーク インターフェイスに割り当てられたネットワーク セキュリティ グループの受信ポート規則を確認して、なぜプライベート IP アドレスを介した SSH 接続が可能であったかを考えてみてください。

   > **注** : 組み込み規則で構成される既定の設定では、Azure 仮想ネットワーク環境内の受信接続はすべて許可されています。 (SSH ポート TCP 22 を含みます。)

> **結果**:この演習では、Azure 仮想マシンの静的プライベートとパブリック IP アドレスを構成し、パブリック IP アドレスを介して Windows Server 2016 Datacenter を実行する Azure 仮想マシンに接続し、プライベート IP を介して Linux Ubuntu サーバーを実行する Azure 仮想マシンに接続しました。


### 演習 3 : Azure 仮想マシンのスケール セットを展開して構成する

この演習の主な作業は次のとおりです。

1. Azure 仮想マシン スケールセット展開に使用可能な DNS 名を特定する

1. Azure 仮想マシン スケール セットを展開する

1. DSC 拡張機能を使用してスケールセット VM に IIS をインストールする


#### 作業 1 : Azure 仮想マシン スケールセット展開に使用可能な DNS 名を特定する

1. Azure ポータル の Cloud Shell ペインから、PowerShell セッションを開始します。

1. Cloud Shell ペインで、次のコマンドを実行します。その際、&lt;custom-label&gt; を任意の一意な文字列 (英小文字) と置き換えて下さい。

   ```pwsh
   $rg = Get-AzResourceGroup -Name az1000301-RG
  
   Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location $rg.Location
   ```

1. コマンドが検証され "**True**" が返されます。"**True**" が返されるまで、&lt;custom-label&gt; の値を変えながら、同じコマンドを実行してください。

1. 成功した &lt;custom-label&gt; の値を控えておいてください。次の作業で使用します。


#### 作業 2 : Azure 仮想マシン スケール セットを展開する

1. Azure ポータル から [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Virtual machine scale set**」と入力します。

1. 検索結果から [**Virtual machine scale set**] を選択します。

1. [**Virtual machine scale set**] ブレードで、[作成] をクリックして、次のようなスケールセットを設定します。

   - 仮想マシンのスケール セット名 : **az1000303vmss0**

   - オペレーティング システムのディスク イメージ : **Windows Server 2016 Datacenter**

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ : 「**az1000303-RG**」という名前のリソース グループを新規作成します。

   - 場所 : この演習の最初に設定した Azure リージョンと同じ場所を指定します。

   - 可用性ゾーン : **なし**

   - ユーザー名 : **Student**

   - パスワード : **Pa55w.rd1234**

   - インスタンス数 : **1**

   - インスタンス サイズ : **DS2 v2**

   - 低優先度で配置する (プレビュー) : **いいえ**

   - マネージド ディスクを使用 : **はい**

   - 自動スケーリング : **無効**

   - 負荷分散オプションの選択 : **ロード バランサー**

   - パブリック IP アドレス名 : **az1000303vmss0-ip**

   - ドメイン名ラベル : 前の作業で指定した &lt;custom-label&gt; の値を入力します。

   - 仮想ネットワーク :「**az1000303-vnet0**」という名前の新しい仮想ネットワークを作成し、次のように設定します。

      - アドレス範囲 : **10.203.0.0/16**

      - サブネット名 : **subnet0**

      - サブネット アドレス範囲 : **10.203.0.0/24**
   - 各インスタンスのパブリック IP アドレス : **オフ**
   > **注** : 展開が完了するのを待って、次の作業に移ってください。処理の完了までには、5 分ほどかかります。


#### 作業 3 : DSC 拡張機能を使用してスケールセット VM に IIS をインストールする

1. Azure ポータルから [**az1000303vmss0**] ブレードを開きます。

1. [**az1000303vmss0**] ブレードで、[拡張機能] ブレードを開きます。

1. [**az1000303vmss0 - 拡張機能**] ブレードで、[**PowerShell Desired State Configuration**] を検索して追加し、次のように設定します。

   > **注** : DSC の Configuration Module は次のファイルをアップロードしてください。**Labfiles\\Module_02\\Deploy_and_Manage_Virtual_Machines\\az-100-03_install_iis_vmss.zip**このモジュールには、Web Server (IIS) ロールをインストールした設定スクリプトが記述されています。

   - Configuration Modules or Script : **"az-100-03_install_iis_vmss.zip"**

   - Module-qualified Name of Configuration : **az-100-03_install_iis_vmss.ps1\IISInstall**

   - Configuration Arguments : 空白

   - Configuration Data PSD1 File : 空白

   - WMF Version : **latest**

   - Data Collection : **Disable**

   - Version : **2.76**

   - Auto Upgrade Minor Version: **Yes**

1. [**az1000303vmss0 - インスタンス**] ブレードを開き、"**az1000303vmss0_0**" インスタンスのアップグレードを開始します。

   > **注** : アップグレードにより、DSC 構成スクリプトの適用がトリガーされます。アップグレードが完了するのを待ちます。処理の完了までには、5 分ほどかかります。[**az1000303vmss0 - インスタンス**] ブレードで、進行状況を確認できます。

1. アップグレードが完了したら、[**az1000303vmss0_0**] をクリックし、[**ネットワーク**] ブレードを開きます。

1. [az1000303vmss0_0 - ネットワーク] ブレードで、[**受信ポートの規則を追加する**] をクリックして、次のような設定の受信セキュリティ規則を追加します。

   - ソース : **Any**

   - ソース ポート範囲 : **\***

   - 宛先 : **Any**

   - 宛先ポート範囲 : **80**

   - プロトコル : **TCP**

   - アクション : **許可**

   - 優先度 : **100**

   - 名前 : **AllowHttpInBound**

1. [**az1000303-RG**] リソースグループから [**az1000303vmss0-ip**] ブレードを開き、"**az1000303vmss0**" に割り当てられたパブリック IP アドレスを控えておきます。

1. Microsoft Edge を起動し、前の手順で指定したパブリック IP アドレスに移動します。

1. ブラウザーで、デフォルトの IIS のホームページが表示されていることを確認します。

> **結果** : この演習では、スケール セットの展開に使用可能な DNS 名を特定し、スケール セットを展開し、DSC 拡張機能を使用してスケール セット仮想マシン に IIS をインストールしました。

## 演習 4 : 演習用リソースを削除する

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
