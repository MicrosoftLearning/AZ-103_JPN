---
lab:
    title: 'Implement Directory Synchronization'
    module: 'Module 09 - Azure Active Directory'
---

# 演習 : ディレクトリ同期の実装

この演習のすべての作業には、Azure ポータル (PowerShell クラウド シェル セッションを含む) を使用します。ただし、リモート デスクトップからの Azure 仮想マシンでの操作を含む、演習 3 の作業 1 から作業 3 は除きます。

> **注** : クラウド シェルを使用しない場合は、Azure PowerShell 1.2.0 (またはそれ以降の) モジュールをインストールした演習用仮想マシンが必要となります。 [https://docs.microsoft.com/en-us/powershell/azure/install-az-ps](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps)

演習用ファイル : なし

### シナリオ

Adatum Corporation は、オンプレミスの Active Directory を Azure Active Directory (Azure AD) と統合したいと考えています。 


### 目的

この演習を修了すると、次のことができるようになります。

- Active Directory ドメイン コントローラーをホストする Azure 仮想マシンを展開する。

- Azure Active Directory テナントを作成して構成する。

- Active Directory フォレストを Azure Active Directory テナントと同期する。


### 演習 1 : Active Directory ドメイン コントローラーをホストする Azure 仮想マシンを展開する

この演習の主な作業は次のとおりです。

1. Azure 仮想マシンの展開に使用できる DNS 名を指定する。

1. Azure Resource Manager (ARM) テンプレートを使用して、Active Directory ドメイン コントローラーをホストする Azure 仮想マシンを展開する。


#### 作業 1 : Azure 仮想マシンの展開に使用できる DNS 名を指定する

1. 演習用の仮想マシンで、Microsoft Edge を起動します。[**http://portal.azure.com**](http://portal.azure.com) から Azure ポータル にアクセスします。演習用の Azure サブスクリプションのうち、所有者役割と Azure AD テナントのグローバル管理者の役割を持つ Microsoft アカウントでサインインします。

1. Azure ポータル の Cloud Shell ペインから、PowerShell セッションを開始します。

   > **注** : 現在の Azure サブスクリプションで Cloud Shell を初めて起動する場合、Cloud Shell ファイルを維持するための Azure ファイル共有の作成を要求されます。既定のまま設定すると、自動生成されたリソース グループにストレージ アカウントが作成されます。

1. Cloud Shell ペインで、次のコマンドを実行します。&lt;custom-label&gt; プレースホルダーには、一意の文字列を入力し、&lt;location&gt; プレースホルダーには Active Directory ドメイン コントローラーをホストする Azure 仮想マシンを展開する Azure リージョンを入力する。

   > **注** : Azure 仮想マシンをプロビジョニングできる Azure リージョンを確認するには、こちらを参照してください。[**https://azure.microsoft.com/en-us/regions/offers/**](https://azure.microsoft.com/en-us/regions/offers/)

   ```pwsh
   Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
   ```

1. コマンドが検証され "**True**" が返されます。"**True**" が返されるまで、&lt;custom-label&gt; の値を変えながら、同じコマンドを実行してください。

1. 成功した &lt;custom-label&gt; の値を控えておいてください。次の作業で使用します。


#### 作業 2 : Azure Resource Manager (ARM) テンプレートを使用して、Active Directory ドメイン コントローラーをホストする Azure 仮想マシンを展開する

1. 演習用の仮想マシンで、別の Microsoft Edge インスタンスを起動し、GitHub の Azure QuickStart Templates ページを参照します。[**https://github.com/Azure/azure-quickstart-templates**](https://github.com/Azure/azure-quickstart-templates)

1. Azure Quickstart Templates ページで [**active-directory-new-domain**] をクリックします。

1. [**Create a new Windows VM and create a new AD Forest, Domain and DC**] ページの[**Deploy to Azure**] を右クリックして、[**新しいタブで開く**] をクリックします。

1. [**Create an Azure VM with a new AD Forest**] ブレードで、次のような設定のテンプレートを展開します。

   - サブスクリプション : この演習で使用しているサブスクリプション名

   - リソース グループ :「**az1000501-RG**」という名前のリソース グループを新規作成します。

   - 場所 : 前の作業で選択した Azure リージョン

   - Admin Username : **Student**

   - Admin Password : **Pa55w.rd1234**

   - Domain Name : **adatum.com**

   - Dns Prefix : 前の作業で指定した &lt;custom-label&gt;

   - VM Size : **Standard_D2s_v3**

   - _artifacts Location : 既定値のまま

   - _artifacts Location Sas Token : 空白

   - Location : 既定値のまま
   > **注** : 展開が完了するのを待つ必要はありません。次の演習に進んでください。作成した仮想マシンは、演習 3 で使います。

> **結果** : この演習では、ARM テンプレートを使用して Active Directory ドメイン コントローラーをホストする Azure 仮想マシンを展開しました。


### 演習 2 : Azure Active Directory テナントを作成して構成する

この演習の主な作業は次のとおりです。

1. Azure Active Directory (AD) テナントを作成する。

1. 新しい Azure AD テナントにカスタム DNS 名を追加する。

1. グローバル管理者の役割を持つ Azure AD ユーザーを作成する。


#### 作業 1 : Azure Active Directory (AD) テナントを作成する

1. Azure ポータルから [**リソースの作成**] ブレードを開きます。

1. [**リソースの作成**] ブレードの、Azure Marketplace の検索ボックスに「**Azure Active Directory**」と入力します。

1. 検索結果から、[Azure Active Directory] ブレードを開き [**作成**] をクリックします。

1. [**ディレクトリの作成**] ブレードで、次のような設定の Azure AD テナントを作成します。

- 組織名 : **AdatumSync**

- 初期ドメイン名 : 文字と数字の組み合わせで構成される一意の名前

- 国/リージョン : **米国**

   > **注** : [**初期ドメイン名**] テキスト ボックスに付く緑のチェックは、一意な値として使用可能であることを示しています。

#### 作業 2 : 新しい Azure AD テナントにカスタム DNS 名を追加する

1. 画面右上の "**ディレクトリ + サブスクリプション**" フィルターをクリックし、新規作成した Azure AD テナントをクリックします。

   > **注** : [**ディレクトリ + サブスクリプション**] フィルターは、Azure ポータルのツール バーの通知アイコンの左にあります。

   > **注** : [**ディレクトリ + サブスクリプション**] フィルターのリストに "**AdatumSync**" エントリがない場合は、ページを更新します。

1. "AdatumSync" をクリックしてフィルターを設定すると、**AdatumSync - 概要** ブレードが開きます。

1. [**AdatumSync - 概要**] ブレードから、[**カスタム ドメイン名**] ブレードに移動します。

1. [**AdatumSync - カスタム ドメイン名**] ブレードで、この Azure AD テナントに既定で関連付けられているプライマリの DNS ドメイン名を確認します。ドメイン名が、前の作業で設定した値であることを確認します。

1. [**AdatumSync - カスタム ドメイン名**] ブレードで、[**adatum.com**] というカスタム ドメイン名を追加します。

1. [**adatum.com**] ブレードで、Azure AD ドメイン名の確認に必要な情報を確認します。

   > **注** : ただし、**adatum.com** DNS ドメイン名を所有していないため、このドメイン名の確認処理は失敗します。これにより、**adatum.com** という Active Directory ドメインと Azure AD テナントを同期することを防ぐことができます。ここでは、先ほど確認した Azure AD テナント (**onmicrosoft.com**サフィックス) のプライマリ DNS 名を使用します。結果として、Active Directory ドメインの DNS ドメイン名と Azure AD テナントの DNS 名は異なることを覚えておいてください。つまり、Adatum ユーザーは、Active Directory ドメインにサインインする時と、Azure AD テナントにサインインする際には別の名前を使用する必要があります。


#### 作業 3 : グローバル管理者役割を持つ Azure AD ユーザーを作成する

1. Azure ポータルの [すべてのサービス] から「ユーザー」と検索し、Azure AD テナント**AdatumSync**の [**ユーザー - すべてのユーザー**] ブレードを開きます。

1. [**ユーザー - すべてのユーザー**] ブレードで、[新しいユーザー] をクリックし、次のような設定で新しいユーザーを作成します。

   - ユーザー名 : **syncadmin@***<DNS-domain-name>* where *<DNS-domain-name>*前の作業で確認したプライマリ DNS 名このユーザー名を控えておいてください。この演習の後半で使用します。

   - 名前 : **syncadmin**

   - パスワード : [**パスワードを表示**] のボックスにチェックを入て [**初期パスワード**] に表示される文字列を控えておきます。この作業の後半で使用します。
   > **注**: Azure AD Connect を実装するためには、グローバル管理者役割を持つ Azure AD ユーザー管理者ロールが必須です。

   - グループ : **0 個のグループが選択されました**

   - 役割 : **グローバル管理者**

1. Microsoft Edge で InPrivate ウィンドウを開きます。

1. 新しいウィンドウで、**syncadmin** ユーザー アカウントとして、Azure ポータルにサインインします。プロンプトが表示された場合は、パスワードを新しく設定してください。

   > **注**: **syncadmin** Azure AD テナントの DNS ユーザー名を含む、アカウントの正式なユーザー名を入力する必要があります。

1. **syncadmin** からサインアウトして、InPrivate ウィンドウを閉じます。

> **結果**: この演習では、Azure AD テナントを作成し、新しい Azure AD テナントにカスタム DNS 名を追加し、グローバル管理者の役割を持つ Azure AD ユーザーを作成しました。


### 演習 3 : Active Directory フォレストを Azure Active Directory テナントと同期する

この演習の主な作業は次のとおりです。

1. ディレクトリ同期の準備として Active Directory を設定する。

1. Azure AD Connect をインストールする。

1. ディレクトリ同期を確認する。


#### 作業 1 : ディレクトリ同期の準備として Active Directory を設定する

> **注** : この作業を開始する前に、演習 1 で開始したテンプレートの展開が完了していることを確認してください。

1. Azure ポータルで、[**ディレクトリ + サブスクリプション**] フィルターを、演習 1 で使用した Azure サブスクリプションに関連付けられている Azure AD テナントに戻します。

   > **注** : [**ディレクトリ + サブスクリプション**] フィルターは、Azure ポータルのツール バーの通知アイコンの左にあります。

1. Azure ポータルから、[Virtual Machines] ブレードを開き、[**adVM**] をクリックして、Active Directory ドメイン コントローラーをホストする Azure 仮想マシンのプロパティを表示します。ブレードを開きます。

1. [**adVM**] ブレードの [**概要**] ペインで、[接続] をクリックして [**adVM**] に接続するための RDP ファイルを生成します。

1. プロンプトが表示されたら、次の資格情報を指定して認証します。

   - ユーザー名 : **Student**

   - パスワード : **Pa55w.rd1234**

1. [**adVM**] への RDP セッション内で、[**Server Manager**] から [**Tools**] をクリックし、[**Active Directory Administrative Center**] を開きます。

1. [**Active Directory Administrative Center**] で、左ペインの [**adatum (local)**] を右クリックし、[**New**] - [**Organizational Unit**] をクリックして、「**ToSync**」という名前のルート レベルの組織単位を作成します。

1. [**Active Directory Administrative Center**] で、左ペインの [**adatum (local)**] をクリックし、[**ToSync**] を右クリックし、[**New**] - [**User**] をクリックして、次のような設定のユーザーを作成します。

   - Full name: **aduser1**

   - User UPN logon: **aduser1@adatum.com**

   - User SamAccountName logon: **adatum\aduser1**

   - password : **Pa55w.rd1234**

   - Other password options: **Password never expires**


#### 作業 2 : Azure AD Connect をインストールする

1. [**adVM**] への RDP セッション内で、[**Server Manager**] から左ペインの [**Local Server**] をクリックし、[**IE Enhanced Security Configuration**] の [**On**] をクリックして、[**IE Enhanced Security Configuration**] の設定を [**Off**] にします。

1. [**adVM**] への RDP セッション内で IE を起動し、次のURL から**Azure AD Connect** をダウンロードします。[**https://www.microsoft.com/en-us/download/details.aspx?id=47594**](https://www.microsoft.com/en-us/download/details.aspx?id=47594)

1. [**Microsoft Azure Active Directory Connect**] ウィザードを起動し、licensing terms に同意します。[**Express Settings**] ページで、[**Customize**] を選択します。

1. [**Install required components**] ページで、いずれのオプションもクリックせずに [Install] をクリックします。

1. [**User sign-in**] ページで、[**Password Hash Synchronization**] のみが有効であることを確認します。

1. [Connect to Azure AD] ページで、演習 2 で作成した **syncadmin** アカウントの資格情報で認証します。

1. [Connect your directories] ページで、[**adatum.com**] forest をディレクトリとして追加するため [Add Directory] をクリックします。オプションから [**Create new AD account**] を選択して、次の資格情報で認証します。

   - User name: **ADATUM\Student**

   - パスワード : **Pa55w.rd1234**

1. [**Azure AD sign-in configuration**] ページで、"**Users will not be able to sign-in to Azure AD with on-premises credentials if the UPN suffix does not match a verified domain**" という警告が出ていることを確認し、[**Continue without matching all UPN suffixes to verified domains**] のチェックボックスをクリックして有効にします。

   > **注** : 前述のとおり、カスタムした Azure AD DNS ドメイン **adatum.com** を検証できないためこの警告が表示されます。

1. [**Domain and OU filtering**] ページで、[**sync selected domains and OUs**] をクリックし、[**ToSync**] OU のみを選択します。

1. [**Uniquely identifying your users**] ページは、既定の設定のまま進みます。

1. [**Filter users and devices**] ページも、既定の設定のまま進みます。

1. [**Optional features**] ページも、既定の設定のまま進みます。

1. [**Ready to configure**] ページで、[**Start the synchronization process when configuration completes.**] チェックボックスが選択されていることを確認し、[Install] をクリックします。

   > **注** : インストールには約 2 分かかります。

1. 完了したら、Microsoft Azure Active Directory Connect ウィンドウを閉じます。


#### 作業 3 : ディレクトリ同期を確認する

1. [**adVM**] への RDP セッション内で、Internet Explorer を起動し、 [**http://portal.azure.com**](http://portal.azure.com) から Azure ポータルを開きます。演習 2 で作成した **syncadmin** アカウントでサインインします。

1. Azure Portal から [**Azure Active Directory**] ブレードを開きます。

1. [**AdatumSync - Overview**] ブレードで、AdatumSync Azure AD テナントの [**Users**] ブレードを開きます。

1. [**Users - All users**] ブレードで、リストの中に "**aduser1**" アカウントが表示され、**SOURCE** 列に "**Windows Server AD**" が表示されていることを確認します。

1. [**Users - All users**] ブレードから、[**aduser1**] をクリックし、[**aduser1 - Profile**] ブレードを開きます。[**Department**] 属性は設定されていません。

1. [**adVM**] への RDP セッション内で、**Active Directory Administrative Center** に切り替え、"**aduser1**" ユーザー アカウントのプロパティを表示するウィンドウを開きます。このユーザーの **Department** 属性として "**Sales**" を設定します。

1. [**adVM**] への RDP セッション内で、管理者として **Windows PowerShell** を開きます。

1. Windows PowerShell プロンプトで、次のコマンドを実行して、Azure AD Connect の delta synchronization を開始します。

   ```pwsh
   Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

   Start-ADSyncSyncCycle -PolicyType Delta
   ```

1. [**adVM**] への RDP セッション内で、IE ウィンドウに切り替え、Azure ポータルを表示します。

1. Azure ポータルで、[**Users - All users**] ブレードを表示して、ページを更新します。

1. [**Users - All users**] ブレードから、[**aduser1 - Profile**] ブレードを開きます。**Department** 属性が、"**Sales**" になっていることを確認します。

   > **注** : **Department** 属性に何も表示されていない場合は、数分待ってページを更新してください。
   
   > **注** : **Department** 属性に何も表示されていない場合は、左ペインの [**Directory role**] をクリックしてから [**Profile**] をクリックしてください。

> **結果** : この演習では、ディレクトリ同期の準備として Active Directory を構成し、Azure AD Connectをインストールし、ディレクトリ同期を検証しました。

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

#### 作業 3 : Azure AD テナントを削除する

1. 演習用の仮想マシンで、管理者として Windows PowerShell を開きます。

1. Windows PowerShell コンソールで、次のコマンドを実行して、MsOnline PowerShell モジュールをインストールします。(NuGet プロバイダーをインストールするプロンプトが表示された場合は、「**y (yes)**」と入力します。)

   ```pwsh
   Install-Module MsOnline -Force
   ```

1. Windows PowerShell コンソールで、次のコマンドを実行して、AdatumSync Azure AD テナントに接続します。(資格情報が必要な場合は、SyncAdmin の資格情報で認証します。)

   ```pwsh
   Connect-MsolService
   ```

1. Windows PowerShell コンソールで、次のコマンドを実行して、Azure AD Connect 同期を無効にします。

   ```pwsh
   Set-MsolDirSyncEnabled -EnableDirSync $false -Force
   ```

1. Windows PowerShell コンソールで、次のコマンドを実行して、操作が成功したことを確認します。

   ```pwsh
   (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled 
   ```

1. 演習用仮想マシンで、Azure ポータルからサインアウトして、Microsoft Edge ウィンドウも閉じます。

1. Microsoft Edge を開き、SyncAdmin の資格情報で Azure ポータルにサインインします。

1. Azure ポータルから [Azure Active Directory] を開き、AdatumSync Azure AD の [**ユーザー - すべてのユーザー**] ブレードを開きます。SyncAdmin アカウントを除くすべてのユーザーを削除します。

> **注** : この処理が完了するまで、数時間かかる可能性があります。

1. AdatumSync の [**プロパティ**] ブレードを開きます。

1. [**Properties**] ブレードで、"**Azure リソースのアクセス管理**" セクションの[**はい**] をクリックして、[**保存**] します。

1. Azure ポータルからサインアウトして、再度 SyncAdmin の資格情報でサインインします。

1. [**AdatumSync - 概要**] ブレード上部の [**ディレクトリの削除**] をクリックし、Azure AD テナントを削除します。

1. [**ディレクトリ 'AdatumSync' を削除しますか?**] ブレードで [**削除**] をクリックします。

> **注** : この作業に関する詳細は、次のページを参照してください。 https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto

> **結果** : この演習では、演習内で使用したリソースをすべて削除しました。
