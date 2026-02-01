# 自宅ラボの物理構成図です。
### なぜこの構成が必要だったか
* 肥大化していくSteamゲームのDL速度の確保
* LAN内10G化とコストを両立させる
* 実験環境でもあるのでとりあえず動けばいい

---

## 物理マシン
| マシン管理名           |        用途 |
| :--------------- | --------: |
| Fujitsu TX2540M1 | ストレージサーバー |
| 自作PC Ryzen5700G機 |   Appサーバー |
| 自作PC メインPC       |      普段使い |

--- 

## マシンスペック
### TX2540M1

| component |                 Name |      Spec       |
| :-------: | :------------------: | :-------------: |
| CPU       | Intel Xeon E5-2470v2 | 2sockets 20C40T  |
| RAM       |             DDR3 ECC |      192GB      |
| Storage   |    HP SAS HDD 6TB x4 |      24TB       |
| NIC       |              X540-T2 | 2port 10GBASE-T |
| OS        |              Proxmox |       9.1       |
### Ryzen5700G機

| component |             Name |      Spec       |
| :-------: | :--------------: | :-------------: |
| CPU       | AMD Ryzen5 5700G |      8C16T      |
| RAM       |             DDR4 |      64GB       |
| Storage   |     M.2 SSD gen4 |       1TB       |
| NIC       |          X540-T2 | 2port 10GBASE-T |
| OS        |          Proxmox |       9.1       |
### メインPC

| component |               Name |      Spec       |
| :-------: | :----------------: | :-------------: |
| CPU       | AMD Ryzen9 9950X3D | 16C32T          |
| RAM       |               DDR5 |      64GB       |
| Storage   |       M.2 SSD gen5 |       1TB       |
| NIC       |  PCIe slot x4 lane | 1port 10GBASE-T |
| OS        |          Windows11 |      Home       |

---
# 構成図
```mermaid
flowchart TB
    subgraph WAN["🌐 WAN"]
        D["ONU<br/>10Gbps"]
    end
    
    subgraph Network["Network"]
        E["Router"]
        A["TX2540M1<br>10G Bridge"]
        B["Ryzen5700G<br>NetworkBridge"]
        C["Main PC"]
    end
    
    subgraph Peripherals["1Gbps Devices"]
        F["Printer"]
        G["TV Server"]
        H["Google Home<br/>Streamer"]
    end
    
    D -->|10Gbps| E
    E -->|10Gbps| A
    A -->|10Gbps| B
    B -->|10Gbps| C
    
    B -->|1Gbps| F
    B -->|1Gbps| G
    B -->|1Gbps| H
    
    classDef dark_blue fill:#1a3a52,stroke:#0d5a8f,color:#fff
    classDef dark_gray fill:#3a3a3a,stroke:#5a5a5a,color:#fff
    
    class D,E,A,B,C dark_blue
    class F,G,H dark_gray
```
# 設計思想

* 安価に10G環境を構築したい。
	* 2FでONU受け、ルーターは1ポートのみ10Gの制約
		* 1FのメインPCまで2回L2スイッチを買う必要がある
	* 10Gスイッチは個人で買うにはまだ高価
		* X540-T2でブリッジさせることによりL2スイッチなしのデイジーチェーン
		* 物理故障など障害時にはRJ45コネクタでバイパス運用
		*  単一障害点が多いがコストを優先し受容
		* 障害前提のためコネクタによる物理バイパスを準備
		* コネクタバイパスの運用でカバーするのでリスク受容
		* 差し替えるだけなので下流への影響は数分
* PoEのスイッチは高価+設置場所に難儀。
	* 5700G機に4ポートのNICを追加することで省略
	* 実測値など指標はないがある程度電気コスト減の効果
---
# コスト試算

**メインPCでfast.comにて実測4Gbpsを観測**
	→最初の目標、WANへの高速アクセスの達成

## コスト結果
* X540-T2の調達金額は2500円x2＝約5000円程
* 信頼できる10Gスイッチは6万円クラス、上記制約のため10Gスイッチを導入した場合12万円の支出
### 可用性リスクと引き換えにはなるが約96％のコスト削減に成功！
最悪を想定していつでもルーター⇔メインPC直結でのフェイルセーフモードの確保

---
# 今後のロードマップ
### 優先度降順
- [ ] AP追加で1FでのWi-Fi環境の改善
- [ ] 5700G機のメモリ増設
- [ ] メインPCのメモリ増設
- [ ] リスク回避の段階的10Gスイッチの導入


