---

copyright:
  years: 2018, 2019
lastupdated: "2019-05-17"

keywords: create, configure, permissions, ACL, virtual, server, instance, subnet, block, storage, volume, security, group, images, Windows, Linux, example, monitoring, VPN, load balancer, IKE, IPsec

subcollection: vpc-on-classic

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:important: .important}
{:download: .download}
{:DomainName: data-hd-keyref="DomainName"}

# 使用 {{site.data.keyword.cloud_notm}} 主控台建立 VPC
{: #creating-a-vpc-using-the-ibm-cloud-console}
[comment]: # (鏈結的說明主題)

本文件說明如何使用 {{site.data.keyword.cloud_notm}} 主控台來建立和配置 {{site.data.keyword.cloud}} Virtual Private Cloud。

若要建立及配置虛擬專用雲端 (VPC) 及其他連接的資源，請依以下次序執行各節中的步驟：

1. 建立 VPC 及子網路，以定義網路。建立子網路時，連接公用閘道，以容許子網路中的所有資源與公用網際網路進行通訊。
1. 配置存取控制清單 (ACL)，以限制子網路的入埠及出埠資料流量。依預設，容許所有資料流量。
1. 建立虛擬伺服器實例。
1. 建立區塊儲存空間磁區並將其連接到實例。
1. 配置安全群組，以定義容許用於實例的入埠及出埠資料流量。
1. 保留並關聯浮動 IP 位址，以能夠從網際網路連接實例。
1. 建立負載平衡器，以將要求分散到多個實例。
1. 建立虛擬專用網路 (VPN)，讓 VPC 可以安全地連接至另一個專用網路，例如您的內部部署網路或另一個 VPC。

## 開始之前
{: #before}
請確定您有足夠的許可權來建立及管理 VPC 中的資源。如需相關資訊，請參閱[管理 VPC 資源的使用者許可權](/docs/vpc-on-classic?topic=vpc-on-classic-managing-user-permissions-for-vpc-resources)。

產生 SSH 金鑰，以用來連接至虛擬伺服器實例。例如，透過執行 `ssh-keygen -t rsa -C "user_ID"` 指令，在 Linux 伺服器上產生 SSH 金鑰。該指令會產生兩個檔案。產生的公開金鑰位於 `<your key>.pub` 檔案中。

如果您計劃建立負載平衡器，並針對接聽器使用 HTTPS，則需要 SSL 憑證。您可以使用 [IBM 憑證管理程式 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://{DomainName}/catalog/services/certificate-manager){: new_window} 來管理憑證。您還必須建立授權，以容許負載平衡器實例存取包含 SSL 憑證的「憑證管理程式」實例。您可以透過[身分及存取授權 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://{DomainName}/iam/#/authorizations){: new_window} 來建立授權。對於來源，選取 **VPC 基礎架構**作為「來源服務」，選取 **Load Balancer for VPC** 作為「資源類型」，對於「來源資源實例」，選取**所有資源實例**。選取**憑證管理程式**作為「目標」服務，並針對服務存取角色指派**撰寫者**。將「目標」服務實例設為**所有實例**，或設為特定的「憑證管理程式」實例。如需相關資訊，請參閱[在 IBM Cloud VPC 中使用負載平衡器](/docs/vpc-on-classic-network?topic=vpc-on-classic-network---using-load-balancers-in-ibm-cloud-vpc)。

## 建立 VPC 及子網路

若要建立 VPC 及子網路，請執行下列動作：

1. 開啟 [{{site.data.keyword.cloud_notm}} 主控台 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](https://{DomainName}){: new_window}
1. 按一下**「功能表」圖示 ![「功能表」圖示](../../icons/icon_hamburger.svg) > VPC 基礎架構 > 網路 > VPC**，然後按一下**新建虛擬專用雲端**。
1. 輸入 VPC 的名稱，例如 `my-vpc`。
1. 為 VPC 及其所有連接的資源選取資源群組。資源群組可讓您組織帳戶資源，以用於存取控制及計費用途。如需相關資訊，請參閱[組織資源群組中資源的最佳作法](/docs/resources?topic=resources-bp_resourcegroups)。
1. _選用項目：_ 輸入標籤來協助您組織及尋找資源。您之後可以新增其他標籤。如需相關資訊，請參閱[使用標籤](/docs/resources?topic=resources-tag)。
1. 在此 VPC 中選取或建立新子網路的預設 ACL。在本指導教學中，建立新的預設 ACL。我們稍後將配置 ACL 的規則。
1. 選取預設安全群組是否容許在此 VPC 中對虛擬伺服器實例進行入埠 SSH 及 ping 資料流量。我們稍後將為預設安全群組配置更多規則。
1. 輸入 VPC 中新子網路的名稱，例如 `my-subnet`。
1. 選取子網路的位置。位置由地區及區域組成。

    您選取的地區用來作為 VPC 的地區。您在此 VPC 中建立的所有其他資源都會建立於選取的地區中。
    {: tip}

1. 以 CIDR 表示法輸入子網路的 IP 範圍，例如：`10.240.0.0/24`。在大部分情況下，您可以使用預設 IP 範圍。如果您要指定自訂 IP 範圍，則可以使用 IP 範圍計算機來選取不同的位址字首或變更位址數目。
1. 選取子網路的 ACL。選取**使用 VPC 預設**，以使用為此 VPC 建立的預設 ACL。
1. 將公用閘道連接至子網路，以容許所有連接的資源與公用網際網路通訊。  

    您也可以在建立子網路之後連接公用閘道。
    {: tip}

1. 按一下**建立虛擬專用雲端**。
1. 若要在此 VPC 中建立另一個子網路，請按一下**子網路**標籤，然後按一下**新建子網路**。當您定義子網路時，請務必在**虛擬專用雲端**欄位中選取 `my_vpc`。

## 配置 ACL

您可以配置 ACL 來限制子網路的入埠及出埠資料流量。依預設，容許所有資料流量。

每個子網路只能連接至一個 ACL。不過，每個 ACL 都可以連接至多個子網路。

若要配置 ACL，請執行下列動作：

1. 在導覽窗格中，按一下**網路 > 子網路**。
1. 按一下您已建立的子網路。
1. 在**子網路詳細資料**區域中，按一下 ACL 的名稱。
1. 按一下**新增規則**來配置入埠及出埠規則，以定義容許進入或離開子網路的資料流量。針對每個規則，指定下列資訊：  
   * 指定規則的優先順序。會先評估數字較低的規則，並且會置換數字較高的規則。例如，如果優先順序為 2 的規則容許 HTTP 資料流量，但優先順序為 5 的規則拒絕所有資料流量，則仍會容許 HTTP 資料流量。  
   * 選取是要容許還是拒絕指定的資料流量。  
   * 指定 CIDR 區塊，以指出要套用規則的 IP 範圍。
   * 選取要套用規則的通訊協定及埠。
1. 完成建立規則時，請按一下頁面頂端的**所有存取控制清單**瀏覽途徑。

### 範例 ACL

例如，您可以配置執行下列動作的入埠規則：

 1. 容許來自網際網路的 HTTP 資料流量
 1. 容許來自子網路 10.10.20.0/24 的所有入埠資料流量
 1. 拒絕所有其他入埠資料流量  

然後，配置執行下列動作的出埠規則：

1. 容許送往網際網路的 HTTP 資料流量
1. 容許送往子網路 10.10.20.0/24 的所有出埠資料流量
1. 拒絕所有其他出埠資料流量  

![顯示範例入埠及出埠規則](images/acl-rules.png)

## 建立虛擬伺服器實例

若要在新建立的子網路中建立虛擬伺服器實例，請執行下列動作：

1. 按一下導覽窗格中的**運算 > 虛擬伺服器實例**，然後按一下**新建實例**。
1. 輸入實例的名稱，例如 `my-instance`。
1. 選取您已建立的 VPC。
1. 在**位置**欄位中，選取要在其中建立實例的區域。
1. 選取映像檔（即作業系統及版本），例如 Ubuntu Linux 16.04。
1. 若要設定實例大小，請選取其中一個熱門設定檔，或按一下**所有設定檔**，以選擇最適合您工作負載的不同核心與 RAM 組合。
1. 選取現有 SSH 金鑰，或新增將用來存取虛擬伺服器實例的 SSH 金鑰。若要新增 SSH 金鑰，請按一下**新建金鑰**，並將金鑰命名。在您輸入先前產生的公開金鑰值之後，請按一下**新增 SSH 金鑰**。

金鑰只能在一開始建立 VSI 時新增。沒有任何工具可供之後再新增金鑰。
{:tip}

1. _選用項目：_ 輸入使用者資料，以在實例啟動時執行一般配置作業。例如，您可以為 Linux 映像檔指定 cloud-init 指引或 Shell Script。如需相關資訊，請參閱[使用者資料](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-user-data)。
1. 記下開機磁區。在現行版本中，將 100 GB 分配給開機磁區。已對磁區啟用*自動刪除*；如果刪除實例，則會自動予以刪除。
1. 在**連接的區塊儲存空間磁區**區域中，可以按一下**新建區塊儲存空間磁區**以將區塊儲存空間磁區連接到實例。在本指導教學中，我們將建立區塊儲存空間磁區，稍後將其連接到實例。
1. 在**網路介面**區域中，可以編輯網路介面，並變更其名稱。如果您在選取的區域及 VPC 中有多個子網路，則可以將不同的子網路連接至介面。如果您想要實例存在於多個子網路中，則可以建立更多介面。

   您也可以選取要連接至每個介面的安全群組。依預設，會連接 VPC 的預設安全群組。預設安全群組容許入埠 SSH 及 ping 資料流量、所有出埠資料流量，以及群組中實例之間的所有資料流量。已封鎖所有其他資料流量；您可以配置規則以容許更多資料流量。如果您稍後編輯預設安全群組的規則，則那些更新的規則會套用至該群組中的所有現行及未來實例。

1. 按一下**建立虛擬伺服器實例**。實例的狀態會啟動為*擱置*、變更為*已停止*，然後是*已開啟電源*。您可能需要重新整理頁面，才能看到狀態變更。

## 建立並連接區塊儲存空間磁區

可以建立區塊儲存空間磁區，並將其連接到虛擬伺服器實例。

若要建立並連接區塊儲存空間磁區，請執行下列動作：

1. 在導覽窗格中，按一下**儲存空間 > 區塊儲存空間磁區**。
1. 在「VPC 的區塊儲存空間磁區」頁面中，按一下**新建磁區**，然後指定下列資訊。
  * **名稱**：為區塊儲存空間磁區輸入名稱，例如 `data-volume-1`。  
  * **資源群組**：為區塊儲存空間磁區選取資源群組。資源群組可讓您組織帳戶資源，以用於存取控制及計費用途。如需相關資訊，請參閱[組織資源群組中資源的最佳作法](/docs/resources?topic=resources-bp_resourcegroups)。
  * **標籤**：_選用：_輸入標籤以協助您組織和尋找資源。您之後可以新增其他標籤。如需相關資訊，請參閱[使用標籤](/docs/resources?topic=resources-tag)。
  * **位置**：為區塊儲存空間磁區選取位置。位置由地區和區域組成，例如美國南部 1。
  * **大小**：指定 10 GB 到 2000 GB 之間的磁區大小。
  * **IOPS**：選取其中一個 IOPS 層級，或者按一下「自訂」以根據磁區大小輸入 IOPS 值。
  * **加密**：接受預設「提供者管理的加密」，或者選取「客戶管理的加密」，並使用您自己的加密金鑰。此步驟需要佈建 Key Protect 服務實例，並建立或匯入根金鑰。如需相關資訊，請參閱[建立使用客戶管理的加密的區塊儲存空間磁區](/docs/vpc-on-classic-block-storage?topic=vpc-on-classic-block-storage-block-storage-encryption#data-vol-encryption-ui)。
1. 按一下**建立磁區**。
1. 在區塊儲存空間磁區清單中，找到已建立的磁區。當狀態為 "available" 時，請按一下 "..."，然後選取**連接到實例**。
1. 選取要將磁區連接到的實例，然後按一下**連接**。

## 配置實例的安全群組

您可以配置安全群組，以定義容許用於實例的入埠及出埠資料流量。例如，在您根據公司的安全原則配置子網路的 ACL 規則之後，即可進一步限制特定實例的資料流量（視其工作負載而定）。

若要配置安全群組，請執行下列動作：

1. 在「虛擬伺服器實例」頁面上，按一下實例來檢視其詳細資料。
1. 在**網路介面**區段中，按一下安全群組。
1. 按一下**新增規則**來配置入埠及出埠規則，以定義容許進入及離開實例的資料流量類型。針對每個規則，指定下列資訊：  
   * 為允許的資料流量指定 CIDR 區塊或 IP 位址。或者，您可以在相同的 VPC 中指定安全群組，以容許進入或離開連接至所選取安全群組的所有實例的資料流量。   
   * 選取要套用規則的通訊協定及埠。    

   **提示：**  
  * 無論所有規則的新增次序為何，都會對它們進行評估。
  * 規則是有狀態的，表示會自動允許回應所容許資料流量的傳回資料流量。例如，容許埠 80 上入埠 TCP 資料流量的規則也容許埠 80 上回覆出埠 TCP 資料流量回到原始主機，而不需要其他規則。
1. _選用項目：_ 如果您要將此安全群組連接至其他實例，請按一下導覽窗格中的**連接的介面**，並選取其他介面。
1. 完成建立規則時，請按一下頁面頂端的**所有安全群組**瀏覽途徑。

### 範例安全群組  

例如，您可以配置執行下列動作的入埠規則：

 * 容許所有 SSH 資料流量（TCP 埠 22）
 * 容許所有 ping 資料流量（ICMP 類型 8）

然後，配置容許所有 TCP 資料流量的出埠規則。

![顯示範例入埠及出埠規則](images/sg-example-ui.png)

## 保留浮動 IP 位址

保留並關聯浮動 IP 位址，以能夠從網際網路連接實例。  

您的實例必須先執行，您才能關聯浮動 IP 位址。可能需要一些時間，實例才能開始執行。
{: tip}

若要保留及關聯浮動 IP 位址，請執行下列動作：

1. 在「虛擬伺服器實例」頁面上，按一下實例來檢視其詳細資料。
1. 在**網路介面**區段中，針對您要與浮動 IP 位址相關聯的介面，按一下**保留**。

如果您稍後想要將此浮動 IP 位址重新指派給相同區域中的另一個實例，請在**網路 > 浮動 IP** 頁面上尋找浮動 IP 位址，按一下其溢位功能表 (**...**)，然後按一下**取消關聯**。然後，按一下**關聯**，以選取要與浮動 IP 位址相關聯的實例及網路介面。
{: tip}

## 連接至實例
使用已建立的浮動 IP 位址，ping 您的實例，以確保它已開始執行：

```
ping <public-ip-address>
```
{:pre}


### 連接至 Linux 映像檔

因為您已使用公開 SSH 金鑰建立實例，所以您現在可以使用私密金鑰直接與其連接：


```
ssh -i <path-to-private-key-file> root@<public-ip-address>
```
{:pre}

如需如何連接至實例的相關資訊，請參閱[使用 Linux 連接至實例](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-connecting-to-your-linux-instance)。

### 連接至 Windows 映像檔
若要連接至 Windows 映像檔，請使用其解密後的密碼來登入。若要取得解密後的密碼，請從實例的詳細資料頁面複製已加密的密碼，然後執行下列指令：

```
# Decode the encrypted password
cat ~/examplepwd | base64 --decode > ~/examplepwd64
# Decrypt the decoded password using the RSA private key
openssl pkeyutl -in ~/examplepwd64 -decrypt -inkey private.pem -pkeyopt rsa_padding_mode:oaep -pkeyopt rsa_oaep_md:sha256
-pkeyopt rsa_mgf1_md:sha256
```
{:codeblock}


如需如何連接至實例的相關資訊，請參閱[連接至 Windows 實例](/docs/vpc-on-classic-vsi?topic=vpc-on-classic-vsi-connecting-to-your-windows-instance)。



## 監視實例

如需顯示實例啟動、停止或重新開機時間的活動日誌，請按一下導覽窗格中的**活動**。

## 建立負載平衡器
您可以建立負載平衡器，以將入埠資料流量分散到多個實例。

若要建立負載平衡器，請執行下列動作：
1. 在導覽窗格中，按一下**網路 > 負載平衡器**。
1. 在「負載平衡器」頁面上，按一下**新建負載平衡器**，並指定下列資訊。
    * **名稱**：輸入負載平衡器的名稱，例如 `my-load-balancer`。
    * **虛擬專用雲端**：選取 VPC。
    * **資源群組**：選取負載平衡器的資源群組。
    * **類型**：選取負載平衡器類型：公用或專用。
    * **地區**：指出將在其中建立負載平衡器的地區；亦即，為 VPC 選取的地區。
    * **子網路**：選取要在其中建立負載平衡器的子網路。若要最大化應用程式的可用性，請選取不同區域中的子網路。
1. 按一下**新建儲存區**，並指定下列資訊來建立後端儲存區。您可以建立一個以上的儲存區。
    * **名稱**：輸入儲存區的名稱，例如 `my-pool`。
    * **通訊協定**：為負載平衡器後面的後端實例選取通訊協定。儲存區的通訊協定必須符合其關聯接聽器的通訊協定。例如，如果已為接聽器選取 HTTPS 或 HTTP 通訊協定，則儲存區的通訊協定必須是 HTTP。同樣地，如果接聽器通訊協定是 TCP，則後端儲存區的通訊協定必須是 TCP。
    * **方法**：選取您要負載平衡器將資料流量分散到後端實例的方式：
      * **循環式**：依次將要求轉遞給每個實例。所有實例都會收到大約相等數目的用戶端連線。
      * **加權循環式**：依實例的指派加權比例，將要求轉遞給每個實例。例如，如果您有實例 A、B 及 C，而且其加權設為 60、60 及 30，則實例 A 及 B 會接收到相等數目的連線，而實例 C 會接收到一半的連線。
      * **最少連線數**：在現行時間，將要求轉遞給連線數目最少的實例。  
    * **階段作業綁定**：選取是否將使用者階段作業期間的所有要求都傳送至相同實例。  
    * **性能檢查**：配置負載平衡器檢查實例性能的方式。
      * **性能通訊協定**：負載平衡器用來向後端實例傳送性能檢查訊息的通訊協定。
      * **性能路徑**：只有在選取 HTTP 作為性能檢查通訊協定時，性能路徑才適用。性能路徑指定負載平衡器用來將 HTTP 性能檢查要求傳送至後端實例的 URL。依預設，性能檢查會傳送至根路徑 (`/`)。
      * **間隔**：兩次連續性能檢查嘗試之間的間隔（以秒為單位）。依預設，每 5 秒傳送一次性能檢查。
      * **逾時**：系統等待性能檢查要求回應的時間量上限。依預設，負載平衡器會等待兩秒，以取得回應。
      * **最大重試次數**：負載平衡器在宣告後端實例性能不佳之前進行的其他性能檢查嘗試次數上限。依預設，在兩次失敗的性能檢查之後，就不再將實例視為性能良好。

        雖然負載平衡器停止將連線傳送至性能不佳的實例，但負載平衡器會繼續監視這些實例的性能，並在藉由順利傳遞兩次連續性能檢查嘗試而再次發現它們性能良好時回復其使用。

      如果後端實例性能不佳，且您認為應用程式執行正常，請仔細檢查性能通訊協定及性能路徑值。同時檢查任何連接至實例的安全群組，確保規則容許負載平衡器與實例之間的資料流量。{: tip}

1. 按一下**建立**。
1. 在新儲存區項目的旁邊，按一下**實例**直欄中的**連接**，以將實例新增至儲存區。按一下**新增**，以將更多實例新增至儲存區。為每個實例指定下列資訊：
   * 選取一個以上要從中選取實例的子網路。
   * 選取實例。如果實例具有多個介面，請確定您選取正確的 IP 位址。
   * 指定用來將資料流量傳送至實例的埠。
   * 如果您的儲存區使用**加權循環式**方法，請指派每個實例的加權。  

      將 '0' 加權指派給實例表示不會將任何新的連線轉遞給該實例，但只要現行連線處於作用中，任何現有資料流量就會繼續流入。使用加權 '0' 可協助您溫和地卸下實例，並從服務循環中移除它。
      {: tip}

1. 按一下**新建接聽器**，並指定下列資訊來建立接聽器。您可以建立一個以上的接聽器。
   * **通訊協定**：用來接收送入要求的通訊協定。
   * **埠**：用來接收要求的接聽埠。埠範圍 56500 到 56520 保留供管理用途，因此無法使用。
   * **後端儲存區**：此接聽器將資料流量轉遞至其中的後端儲存區。
   * **連線數上限**（選用）：接聽器容許的並行連線數目上限。
   * **SSL 憑證**：如果 HTTPS 是針對此接聽器所選取的通訊協定，則您必須選取 SSL 憑證。請確定負載平衡器已獲授權存取 SSL 憑證。如需指示，請參閱[開始之前](#before)。
1. 按一下**建立**。
1. 完成建立儲存區及接聽器之後，請按一下**建立負載平衡器**。
1. 若要檢視現有負載平衡器的詳細資料，請在**負載平衡器**頁面上按一下負載平衡器名稱。

## 建立 VPN
您可以建立虛擬專用網路 (VPN)，讓 VPC 可以安全地連接至另一個專用網路，例如內部部署網路或另一個 VPC。

若要檢視程式碼範例，請參閱[搭配使用 VPN 與 VPC](/docs/vpc-on-classic-network?topic=vpc-on-classic-network---using-vpn-with-your-vpc)。
{: tip}

若要建立 VPN，請執行下列動作：
1. 在導覽窗格中，按一下**網路 > VPN**。
1. 在 VPN 頁面上，按一下**新建 VPN 閘道**，並指定下列資訊：
    * **名稱**：輸入虛擬專用雲端中 VPN 閘道的名稱，例如 `my-vpn-gateway`。
    * **虛擬專用雲端**：選取 VPC。
    * **資源群組**：選取 VPN 的資源群組。
    * **子網路**：選取要在其中建立 VPN 閘道的子網路。
1. 在**新建 VPN 連線**區段中，指定下列資訊，以定義此閘道與您 VPC 外部網路之間的連線。
    * **連線名稱**：輸入連線的名稱，例如 `my-connection`。
    * **對等節點閘道位址**：指定 VPC 外部網路的 VPN 閘道的 IP 位址。
    * **預先共用金鑰**：指定 VPC 外部網路的 VPN 閘道的鑑別金鑰。
    * **本端子網路**：在您要透過 VPN 通道連接的 VPC 中，指定一個以上的子網路。
    * **對等節點子網路**：在您要透過 VPN 通道連接的其他網路中，指定一個以上的子網路。
1. 若要配置雲端閘道傳送訊息來確認對等節點閘道為作用中的方式，請在**無回應對等節點偵測**區段中指定下列資訊。
    * **無回應對等節點偵測動作**：在對等節點閘道停止回應時所要採取的動作。例如，如果您要閘道立即重新協議連線，請選取**重新啟動**。
    * **間隔**：確認對等節點閘道為作用中的頻率。依預設，每 30 秒傳送一次訊息。
    * **逾時**：等待對等節點閘道回應的時間長度。依預設，如果在 150 秒內未收到回應，則不再將對等節點閘道視為作用中。
1. 指定連線的 IKE 及 IPsec 安全參數。
    * 如果您要讓雲端閘道嘗試自動建立連線，請選取**自動**。
    * 如果您需要施行特定的安全需求，或者其他網路的 VPN 閘道不支援自動協調嘗試的安全提案，請選取或建立自訂原則。

  **重要事項**：您針對連線所指定的 IKE 及 IPsec 安全參數，必須是 VPC 外部網路的閘道上所設定的相同參數。

## 恭喜！

您已順利建立並配置了 VPC 和子網路、ACL、虛擬伺服器實例、區塊儲存空間磁區、安全群組、浮動 IP 位址、負載平衡器以及 VPN。您可以透過新增更多實例、子網路及其他資源來繼續開發 VPC。
