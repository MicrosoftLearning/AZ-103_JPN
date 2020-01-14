---
lab:
    title: 'Load Balancer and Traffic Manager'
    module: 'Module 08 - Network Traffic Management'
---

# 演習 : Load Balancer と Traffic Manager

この演習のすべての作業には、Azure ポータル (PowerShell クラウド シェル セッションを含む) を使用します。ただし、リモート デスクトップからの Azure 仮想マシンでの操作を含む、演習 1 の作業 3 は除きます。

演習用ファイル :

- **Labfiles\\Module_08\\Load_Balancer_and_Traffic_Manager\\az-101-03_01_azuredeploy.json**

- **Labfiles\\Module_08\\Load_Balancer_and_Traffic_Manager\\az-101-03_01_1_azuredeploy.parameters.json**

- **Labfiles\\Module_08\\Load_Balancer_and_Traffic_Manager\\az-101-03_01_2_azuredeploy.parameters.json**

### シナリオ

Adatum Corporation は、Azure 仮想マシンでホストされている Web ワークロードの可用性を、Azure Load Balancer の負荷分散とネットワーク アドレス変換 (NAT) 機能を利用して、向上させ、子会社である Contoso Corporation の管理を容易にしたいと考えています。


### 目的

この演習を修了すると、次のことができるようになります。

- Azure Resource Manager テンプレートを使用して Azure 仮想マシンを展開する。

- Azure Load Balancer を実装する。

- Azure Traffic Manager の負荷分散を実装する。


### 演習 0 : Azure Resource Manager テンプレートを使用して Azure 仮想マシンを展開する

この演習の主な作業は次のとおりです。

1. Azure Resource Manager テンプレートを使用して、Web サーバー (IIS) の役割がインストールされた Windows Server 2016 Datacenter を実行している Azure 仮想マシンを最初の Azure リージ ョンの可用性セットに展開する。

1. Azure Resource Manager テンプレートを使用して、Web サーバー (IIS) の役割がインストールされた Windows Server 2016 Datacenter を実行している Azure 仮想マシンを 2 番目の Azure リージョンの可用性セットに展開する。


#### 作業 1 : Azure Resource Manager テンプレートを使用して、Web サーバー (IIS) の役割がインストールされた Windows Server 2016 Datacenter を実行している Azure 仮想マシンを最初の Azure リージ ョンの可用性セットに展開する

1. 演習用の仮想マシンで、Microsoft Edge を起動します。[**http://portal.azure.com**](http://portal.azure.com) から Azure ポータル にアクセスします。演習用の Azure サブスクリプションの所有者ロールをもつ Microsoft アカウントでサインインします。

1. Azure ポータルから [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Template deployment**」と入力します。

1. 検索結果から [**テンプレートのデプロイ**] ブレードを開き、[作成] をクリックします。

1. [**カスタム デプロイ**] ブレードで、[**エディターで独自のテンプレートを作成する**] リンクをクリックします。このリンクが確認できない場合は [**テンプレートの編集**] をクリックします。

1. [**テンプレートの編集**] ブレードで、テンプレート ファイルを読み込みます。**Labfiles\\Module_08\\Load_Balancer_and_Traffic_Manager\\az-101-03_01_azuredeploy.json**

   > **注** : テンプレートの内容を確認してください。ここでは、Windows Server 2016 Datacenter Core をホストする 2 つの Azure 仮想マシンの可用性セットへの展開を定義しています。

1. このテンプレートを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードから、[**パラメーターの編集**] ブレードを開きます。

1. [**パラメーターの編集**] ブレードで、パラメーター ファイルを読み込みます。**Labfiles\\Module_08\\Load_Balancer_and_Traffic_Manager\\az-101-03_01_1_azuredeploy.parameters.json**.

1. パラメーターを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードで、次の設定を行い、[購入] をクリックして展開を開始します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ : 「**az1010301-RG**」という名前のリソース グループを新規作成します。

   - 場所 : 演習環境に最も近い Azure リージョンの中で、Azure 仮想マシンをプロビジョ二ングできる場所を選択します。

   - Admin Username : **Student**

   - Admin Password : **Pa55w.rd1234**

   - Vm Name Prefix: **az1010301w-vm**

   - Nic Name Prefix: **az1010301w-nic**

   - Image Publisher : **MicrosoftWindowsServer**

   - Image Offer : **WindowsServer**

   - Image SKU: **2016-Datacenter**

   - Vm Size: **Standard_D2s_v3**

   - Virtual Network Name: **az1010301-vnet**

   - Address Prefix: **10.101.31.0/24**

   - Virtual Network Resource Group: **az1010301-RG**

   - Subnet0Name: **subnet0**

   - Subnet0Prefix: **10.101.31.0/26**

   - Availability Set Name: **az1010301w-avset**

   - Network Security Group Name: **az1010301w-vm-nsg**

   - Modules Url: **https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-iis-server-windows-vm/ContosoWebsite.ps1.zip**

   - Configuration Function: **ContosoWebsite.ps1\\ContosoWebsite**
   > **注** : Azure 仮想マシンをプロビジョニングできる Azure リージョンを確認するには、こちらを参照してください。[**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

   > **注** : 展開が完了するのを待つ必要はありません。次の作業に進んでください。


#### 作業 2 : Azure Resource Manager テンプレートを使用して、Web サーバー (IIS) の役割がインストールされた Windows Server 2016 Datacenter を実行している Azure 仮想マシンを 2 番目の Azure リージョンの可用性セットに展開する

1. Azure ポータルから [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Template deployment**」と入力します。

1. 検索結果から [**テンプレートのデプロイ**] ブレードを開き、[作成] をクリックします。

1. [**カスタム デプロイ**] ブレードで、[**エディターで独自のテンプレートを作成する**] リンクをクリックします。このリンクが確認できない場合は [**テンプレートの編集**] をクリックします。

1. [**テンプレートの編集**] ブレードで、テンプレート ファイルを読み込みます。**Labfiles\\Module_08\\Load_Balancer_and_Traffic_Manager\\az-101-03_01_azuredeploy.json**

   > **注** : このテンプレートは、前の作業で使用したテンプレートです。2 つ目のリージョンに、ペアとなる Azure 仮想マシンを展開します。

1. このテンプレートを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードから、[**パラメーターの編集**] ブレードを開きます。

1. [**パラメーターの編集**] ブレードで、パラメーター ファイルを読み込みます。**Labfiles\\Module_08\\Load_Balancer_and_Traffic_Manager\\az-101-03_01_2_azuredeploy.parameters.json**.

1. パラメーターを保存し、[**カスタム デプロイ**] ブレードに戻ります。

1. [**カスタム デプロイ**] ブレードで、次の設定を行い、[購入] をクリックして展開を開始します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ : 「**az1010302-RG**」という名前のリソース グループを新規作成します。

   - 場所 : Azure 仮想マシンをプロビジョニングできるリージョンを指定しますが、前の作業で選択した Azure リージョンとは異なるリージョンを選択します。

   - Admin Username : **Student**

   - Admin Password : **Pa55w.rd1234**

   - Vm Name Prefix: **az1010302w-vm**

   - Nic Name Prefix: **az1010302w-nic**

   - Image Publisher : **MicrosoftWindowsServer**

   - Image Offer : **WindowsServer**

   - Image SKU: **2016-Datacenter**

   - Vm Size: **Standard_D2s_v3**

   - Virtual Network Name: **az1010302-vnet**

   - Address Prefix: **10.101.32.0/24**

   - Virtual Network Resource Group: **az1010302-RG**

   - Subnet0Name: **subnet0**

   - Subnet0Prefix: **10.101.32.0/26**

   - Availability Set Name: **az1010302w-avset**

   - Network Security Group Name: **az1010302w-vm-nsg**

   - Modules Url: **https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-iis-server-windows-vm/ContosoWebsite.ps1.zip**

   - Configuration Function: **ContosoWebsite.ps1\\ContosoWebsite**
   > **注** : 展開が完了するのを待つ必要はありません。次の演習に進んでください。

> **結果**: この演習では、Azure Resource Manager テンプレートを使用して、Web サーバー (IIS) の役割がインストールされた Windows Server 2016 Datacenter を実行している Azure 仮想マシンを 2 つの Azure リージョンの可用性セットへ展開しました。


### 演習 1 : Azure 負荷分散を実装する

この演習の主な作業は次のとおりです。

1. 最初のリージョンに Azure 負荷分散規則を実装する。

1. 2 番目のリージョンに Azure 負荷分散規則を実装する。

1. 最初のリージョンに Azure NAT 規則を実装する。

1. 2 番目のリージョンにAzure NAT 規則を実装する。

1. Azure 負荷分散規則とNAT 規則を確認する。


#### 作業 1 : 最初のリージョンに Azure 負荷分散規則を実装する

> **注** : この作業を開始する前に、演習 0 の作業 1 で開始したテンプレートの展開が完了していることを確認してください。

1. Azure ポータルから [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Load Balancer**」と入力します。

1. 検索結果から、[Load Balancer] ブレードを開き、[**作成**] をクリックします。

1. [**ロード バランサーの作成**] ブレードで、次のような設定の Azure Load Balancer を作成します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ: **az1010301-RG**

   - 名前 : **az1010301w-lb**

   - 地域 : 演習 0 の作業 1 で Azure 仮想マシンを展開したリージョン
   
   - 種類 : **パブリック**

   - SKU : **Basic**

   - パブリック IP アドレス : 「**az1010301w-lb-pip**」という名前のパブリック IP アドレスを新規作成します。

   - 割り当て : **動的**

1. Azure ポータルから、新規作成した Azure Load Balancer [**az1010301w-lb**] ブレードを開きます。

1. [**az1010301w-lb**] ブレードで、[**バックエンド プール**] ブレードを開きます。

1. [**az1010301w-lb - バックエンド プール**] ブレードで、[追加] をクリックし、次のような設定のバックエンド プールを追加します。

   - 名前 : **az1010301w-bepool**

   - IP バージョン : **IPv4**

   - 関連付け先 : **可用性セット**

   - 可用性セット : **az1010301w-avset**

   - ターゲット仮想マシン : **az1010301w-vm0**
     ([ターゲット ネットワーク IP 構成の追加] をクリックして追加します) 

   - ネットワーク IP 構成 : **az1010301w-nic0/ipconfig1 (10.101.31.5)**

   - ターゲット仮想マシン : **az1010301w-vm1**
     ([ターゲット ネットワーク IP 構成の追加] をクリックして追加します) 

   - ネットワーク IP 構成 : **az1010301w-nic1/ipconfig1 (10.101.31.4)**
   > **注** : Azure 仮想マシンの IP アドレスが逆になっている可能性もあります。

   > **注** : 処理が完了するまで待ちます。処理は、1 分弱で完了します。

1. [**az1010301w-lb - バックエンド プール**] ブレードから、[**az1010301w-lb - 正常性プローブ**] ブレードを表示します。

1. [**az1010301w-lb - 正常性プローブ**] ブレードで、[追加] をクリックし、次のような設定の正常性プローブを追加します。

   - 名前 : **az1010301w-healthprobe**

   - プロトコル : **TCP**

   - Port : **80**

   - 間隔 : **5** 秒

   - 異常しきい値 : **2** 連続エラー数
   > **注** : 処理が完了するまで待ちます。処理は、1 分弱で完了します。

1. [**az1010301w-lb - Health probes**] ブレードから、[**負荷分散規則**] ブレードに移動します。

1. [**az1010301w-lb - 負荷分散規則**] ブレードで、次のような設定の負荷分散規則を追加します。

   - 名前 : **az1010301w-lbrule01**

   - IP バージョン : **IPv4**

   - フロントエンド IP アドレス : **LoadBalancerFrontEnd**

   - プロトコル : **TCP**

   - Port : **80**

   - バックエンド ポート : **80**

   - バックエンド プール : **az1010301w-bepool (2 台の仮想マシン)**

   - 正常性プローブ : **az1010301w-healthprobe (TCP:80)**

   - セッション永続化 : **なし**

   - アイドル タイムアウト (分) : **4**

   - フローティング IP (Direct Server Return) : **無効**


#### 作業 2 : 2 番目のリージョンに Azure 負荷分散規則を実装する

> **注** : この作業を開始する前に、演習 0 の作業 2 で開始したテンプレートの展開が完了していることを確認してください。

1. Azure ポータルから [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Load Balancer**」と入力します。

1. 検索結果から、[Load Balancer] ブレードを開き、[**作成**] をクリックします。

1. [**ロード バランサーの作成**] ブレードで、次のような設定の Azure Load Balancer を作成します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ : **az1010302-RG**

   - 名前 : **az1010302w-lb**

   - 地域 : 演習 0 の作業 2 で Azure 仮想マシンを展開したリージョン

   - 種類 : **パブリック**

   - SKU : **Basic**

   - パブリック IP アドレス : 「**az1010302w-lb-pip**」という名前のパブリック IP アドレスを新規作成します。

   - 割り当て : **動的**

1. Azure ポータルから、新規作成した Azure Load Balancer [**az1010302w-lb**] を開きます。

1. [**az1010302w-lb**] ブレードで、[**az1010302w-lb - バックエンド プール**] ブレードを開きます。

1. [**az1010302w-lb - バックエンド プール**] ブレードで、[追加] をクリックし、次のような設定のバックエンド プールを追加します。

   - 名前 : **az1010302w-bepool**

   - IP バージョン : **IPv4**

   - 関連付け先 : **可用性セット**

   - 可用性セット : **az1010302w-avset**

   - ターゲット仮想マシン : **az1010302w-vm0**
     ([ターゲット ネットワーク IP 構成の追加] をクリックして追加します) 

   - ネットワーク IP 構成 : **az1010302w-nic0/ipconfig1 (10.101.32.5)**

   - ターゲット仮想マシン : **az1010302w-vm1**
     ([ターゲット ネットワーク IP 構成の追加] をクリックして追加します) 

   - ネットワーク IP 構成 : **az1010302w-nic1/ipconfig1 (10.101.32.4)**
   > **注** : Azure 仮想マシンの IP アドレスが逆になっている可能性もあります。

   > **注** : 処理が完了するまで待ちます。処理は、1 分弱で完了します。

1. [**az1010302w-lb - バックエンド プール**] ブレードから、[**az1010302w-lb - 正常性プローブ**] ブレードを表示します。

1. [**az1010302w-lb - 正常性プローブ**] ブレードで、[追加] をクリックし、次のような設定の正常性プローブを追加します。

   - 名前 : **az1010302w-healthprobe**

   - プロトコル : **TCP**

   - Port : **80**

   - 間隔 : **5** 秒

   - 異常しきい値 : **2** 連続エラー数
   > **注** : 処理が完了するまで待ちます。処理は、1 分弱で完了します。

1. [**az1010302w-lb - 正常性プローブ**] ブレードから、[**負荷分散規則**] ブレードに移動します。

1. [**az1010301w-lb - 負荷分散規則**] ブレードで、次のような設定の負荷分散規則を追加します。

   - 名前 : **az1010302w-lbrule01**

   - IP バージョン : **IPv4**

   - フロントエンド IP アドレス : **LoadBalancerFrontEnd**

   - プロトコル : **TCP**

   - Port : **80**

   - バックエンド ポート : **80**

   - バックエンド プール : **az1010302w-bepool (2 台の仮想マシン)**

   - 正常性プローブ : **az1010302w-healthprobe (TCP:80)**

   - セッション永続化 : **なし**

   - アイドル タイムアウト (分) : **4**

   - フローティング IP (Direct Server Return) : **無効**


#### 作業 3 : 最初のリージョンに Azure NAT 規則を実装する

1. Azure ポータルから、Azure Load Balancer [**az1010301w-lb**] ブレードを開きます。

1. [**az1010301w-lb**] ブレードで、[**インバウンド NAT 規則**] ブレードを開きます。

   > **注** : NAT 機能は正常性プローブに依存しません。

1. [**az1010301w-lb - インバウンド NAT 規則**] ブレードで、[追加] をクリックして、次のような設定の NAT 規則を作成します。

   - 名前 : **az1010301w-vm0-RDP**

   - フロントエンド IP アドレス : **LoadBalancerFrontEnd**

   - IP バージョン : **IPv4**

   - サービス : **Custom**

   - プロトコル : **TCP**

   - ポート : **33890**

   - ターゲット仮想マシン : **az1010301w-vm0**

   - ネットワーク IP 構成 : **ipconfig1 (10.101.31.4)** または **ipconfig1 (10.101.31.5)**

   - ポート マッピング : **カスタム**

   - フローティング IP (Direct Server Return) : **無効**

   - ターゲット ポート : **3389**
   > **注** : 処理が完了するまで待ちます。処理は、1 分弱で完了します。

1. [**az1010301w-lb - インバウンド NAT 規則**] ブレードで、[追加] をクリックし、次のような設定で 2 つ目のインバウンド NAT 規則を作成します。

   - 名前 : **az1010301w-vm1-RDP**

   - フロントエンド IP アドレス : **LoadBalancerFrontEnd**

   - IP バージョン : **IPv4**

   - サービス : **Custom**

   - プロトコル : **TCP**

   - ポート : **33891**

   - ターゲット仮想マシン : **az1010301w-vm1**

   - ネットワーク IP 構成 : **ipconfig1 (10.101.31.4)** または **ipconfig1 (10.101.31.5)**

   - ポート マッピング : **カスタム**

   - フローティング IP (Direct Server Return) : **無効**

   - ターゲット ポート : **3389**
   > **注** : 処理が完了するまで待ちます。処理は、1 分弱で完了します。


#### 作業 4 : 2 番目のリージョンにAzure NAT 規則を実装する

1. Azure ポータルから、Azure Load Balancer **az1010302w-lb** ブレードを開きます。

1. [**az1010302w-lb**] ブレードで、[**インバウンド NAT 規則**] ブレードを開きます。

1. [**az1010302w-lb - Inbound NAT rules**] ブレードで、[追加] をクリックして、次のような設定の NAT 規則を作成します。

   - 名前 : **az1010302w-vm0-RDP**

   - フロントエンド IP アドレス : **LoadBalancedFrontEnd**

   - IP バージョン : **IPv4**

   - サービス : **Custom**

   - プロトコル : **TCP**

   - ポート : **33890**

   - ターゲット仮想マシン : **az1010302w-vm0**

   - ネットワーク IP 構成 : **ipconfig1 (10.101.32.4)** または **ipconfig1 (10.101.32.5)**

   - ポート マッピング : **カスタム**

   - フローティング IP (Direct Server Return) : **無効**

   - ターゲット ポート : **3389**
   > **注** : 処理が完了するまで待ちます。処理は、1 分弱で完了します。

1. [**az1010302w-lb - インバウンド NAT 規則**] ブレードで、[追加] をクリックし、次のような設定で 2 つ目の NAT 規則を作成します。

   - 名前 : **az1010302w-vm1-RDP**

   - フロントエンド IP アドレス : **LoadBalancedFrontEnd**

   - IP バージョン : **IPv4**

   - サービス : **Custom**

   - プロトコル : **TCP**

   - ポート : **33891**

   - ターゲット仮想マシン : **az1010302w-vm1**

   - ネットワーク IP 構成 : **ipconfig1 (10.101.32.4)** または **ipconfig1 (10.101.32.5)**

   - ポート マッピング : **カスタム**

   - フローティング IP (Direct Server Return) : **無効**

   - ターゲット ポート : **3389**
   > **注** : 処理が完了するまで待ちます。処理は、1 分弱で完了します。


#### 作業 5 : Azure 負荷分散規則とNAT 規則を確認する

1. Azure ポータルから、Azure Load Balancer [**az1010301w-lb**] ブレードを開きます。

1. [**az1010301w-lb**] ブレードで、LoadBalancerFrontend に割り当てられたパブリック IP アドレスを特定します。

1. Microsoft Edge で、新しいタブを開き、先ほどの IP アドレスを入力して開きます。

1. タブには、デフォルトの Internet Information Services ホーム ページが表示されていることを確認します。

1. タブを閉じます。

1. Azure ポータルから、Azure Load Balancer [**az1010301w-lb**] ブレードを開きます。

1. [**az1010301w-lb**] ブレードで、LoadBalancerFrontend に割り当てられたパブリック IP アドレスを特定します。

1. 演習用の仮想マシンで、次のコマンドを実行します。&lt;az1010301w-lb_public_IP&gt; プレースホルダーには、先ほど特定した IP アドレスを入力します。

   ```
   mstsc /v:<az1010301w-lb_public_IP>:33890
   ```

   > **注** : このコマンドにより、前の作業で作成した **az1010301w-vm0-RDP** NAT 規則を使用した 仮想マシン **az1010301w-vm0** へのリモート デスクトップ セッションが開始されます。

1. プロンプトが出されたら、資格情報を入力してください。

   - Admin Username : **Student**

   - Admin Password : **Pa55w.rd1234**

1. サインインできたら、コマンド プロンプトから次のコマンドを実行します。

   ```
   hostname
   ```

1. 出力結果を確認し、仮想マシン **az1010301w-vm0** に接続していることを確認します。

   > **注** : 2 つ目のリージョンに関しても、同じ作業を繰り返して確認します。

> **結果** : この演習では、2 つのリージョンで Azure Load Balancer に負荷分散規則と NAT 規則を実装し、検証しました。


### 演習 2 : Azure Traffic Manager の負荷分散を実装する

この演習の主な作業は次のとおりです。

1. Azure Load Balancer のパブリック IP アドレスに DNS 名を割り当てる。

1. Azure Traffic Manager の負荷分散を実装する。

1. Azure Traffic Manager の負荷分散を確認する。


#### 作業 1 : Azure Load Balancer のパブリック IP アドレスに DNS 名を割り当てる

   > **注** : 各 Traffic Manager エンドポイントには、DNS 名を割り当てる必要があるため、この作業は必須です。

1. Azure ポータルで、1 つ目のリージョンの Azure Load Balancer に関連付けられた IP アドレス [**az1010301w-lb-pip**] ブレードを開きます。

1. [**az1010301w-lb-pip**] ブレードで、[**構成**] を開きます。

1. [**az1010301w-lb-pip - 構成**] ブレードで、パブリック IP アドレスの [**DNS 名ラベル**] として、一意な値を入力し、[保存] をクリックします。

   > **注** : [**DNS 名ラベル (オプション)**] テキスト ボックスに付く緑のチェックは、一意な値として使用可能であることを示しています。

1. 2 つ目のリージョンの Azure Load Balancer に関連付けられた IP アドレス [**az1010302w-lb-pip**] ブレードを開きます。

1. [**az1010302w-lb-pip**] ブレードで、[**構成**] を開きます。

1. [**az1010302w-lb-pip - 構成**] ブレードで、パブリック IP アドレスの [**DNS 名ラベル**] として、一意な値を入力し、[保存] をクリックします。

   > **注** : [**DNS 名ラベル (オプション)**] テキスト ボックスに付く緑のチェックは、一意な値として使用可能であることを示しています。


#### 作業 2 : Azure Traffic Manager の負荷分散を実装する

1. Azure ポータルから [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Traffic Manager profile**」と入力します。

1. 検索結果から、[**Traffic Manager プロファイル**] ブレードを開き、[作成] をクリックします。

1. [**Traffic Manager プロファイルの作成**] ブレードで、次のような設定の Traffic Manager プロファイルを作成します。

   - 名前 : trafficmanager.net DNS 名前空間でグローバルに一意な値

   - ルーティング方法 : **重み付け**

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ : 「**az1010303-RG**」という名前のリソース グループを新規作成します。

   - リソース グループの場所 : この章の前半の演習で指定した Azure リージョンのいずれか

1. Azure ポータルから、新規作成した Traffic Manager プロファイルを開きます。

1. Traffic Manager プロファイル ブレードで、[**構成**] ブレードで、設定を確認します。

   > **注** : Traffic Manager プロファイルの TTL は、既定で 60 秒となっています

1. Traffic Manager プロファイル ブレードで、[**エンドポイント**] ブレードを開きます。

1. [**エンドポイント**] ブレードで、次のような設定のエンドポイントを作成します。

   - 種類 : **Azure エンドポイント**

   - 名前 : **az1010301w-lb-pip**

   - ターゲット リソースの種類 : **パブリック IP アドレス**

   - ターゲット リソース : **az1010301w-lb-pip**

   - 重み : **100**

   - カスタム ヘッダーの設定 : 空白

   - 無効として追加 : 空白

1. [**エンドポイント**] ブレードで、次のような設定で 2 つ目のエンドポイントを作成します。

   - 種類 : **Azure エンドポイント**

   - 名前 : **az1010302w-lb-pip**

   - ターゲット リソースの種類 : **パブリック IP アドレス**

   - ターゲット リソース : **az1010302w-lb-pip**

   - 重み : **100**

   - カスタム ヘッダーの設定 : 空白

   - 無効として追加 : 空白

1. [**エンドポイント**] ブレードで、両エンドポイントの [**モニターの状態**] 列を検証します。両方の状態が "**オンライン**" になっていることを確認してから、次の作業に進みます。


#### 作業 3 : Azure Traffic Manager の負荷分散を確認する

1. [**エンドポイント**] ブレードから、Traffic Manager ブレードの [**概要**] セクションに移動します。

1. Traffic Manager プロファイルに割り当てられた DNS 名 を控えておきます。(**http://** のあとに続く文字列です。)

1. Azure ポータル の Cloud Shell ペインから、PowerShell セッションを開始します。

   > **注** : 現在の Azure サブスクリプションで Cloud Shell を初めて起動する場合、Cloud Shell ファイルを維持するための Azure ファイル共有の作成を要求されます。既定のまま設定すると、自動生成されたリソース グループにストレージ アカウントが作成されます。

1. Cloud Shell ペインで、次のコマンドを実行します。&lt;TM_DNS_name&lt; プレースホルダーには、前の手順で特定した Traffic Manager プロファイルに割り当てられた DNS 名を入力します。

   ```
   nslookup <Traffic Manager プロファイルに割り当てられた DNS 名>
   ```

1. 出力結果の [**Name**] 欄を確認します。ここには、先ほど作成した Traffic Manager プロファイル エンドポイントのいずれかの DNS 名が表示されています。

1. 最低 60 秒待って、先ほどと同じコマンドを実行します。

   ```
   nslookup <Traffic Manager プロファイルに割り当てられた DNS 名>
   ```
1. 出力結果の [**Name**] 欄を確認します。今度は、Traffic Manager プロファイル エンドポイントの内、先ほどとは別の DNS 名が表示されています。

> **結果** : この演習では、Azure Traffic Manager の負荷分散を実装し、検証しました。

## 演習 3 : 演習用リソースを削除する

#### 作業 1 : Cloud Shell を開く

1. Cloud Shell インターフェイスで [**Bash**] を選択します。

1. **Cloud Shell** のコマンド プロンプトで、次のコマンドを入力し [**Enter**] を押します。これにより、この演習で作成したすべてのリソース グループが表示されます。

   ```sh
   az group list --query "[?starts_with(name,'az1010')].name" --output tsv
   ```

1. 出力結果を確認し、リストにはこの演習で作成したリソース グループのみが含まれることを確認します。これらのリソース グループを、次の作業で削除します。

#### 作業 2 : リソース グループを削除する

1. **Cloud Shell** のコマンド プロンプトで、次のコマンドを入力し [**Enter**] を押します。これにより、この演習で作成したリソース グループを削除できます。

   ```sh
   az group list --query "[?starts_with(name,'az1010')].name" --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
   ```

1. ポータルの下部に表示される **Cloud Shell** プロンプトを閉じます。

> **結果** : この演習では、演習内で使用したリソースをすべて削除しました。
