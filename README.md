# 自宅ラボの物理構成図です。
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
| :-------- | -------------------: | :-------------: |
| CPU       | Intel Xepn E5-2470v2 | 2sckets 20C40T  |
| RAM       |             DDR3 ECC |      192GB      |
| Storage   |    HP SAS HDD 6TB x4 |      24TB       |
| NIC       |              X540-T2 | 2port 10GBASE-T |
| OS        |              Proxmox |       9.1       |
### Ryzen5700G機

| component |             Name |      Spec       |
| :-------- | ---------------: | :-------------: |
| CPU       | AMD Ryzen5 5700G |      8C16T      |
| RAM       |             DDR4 |      64GB       |
| Storage   |     M.2 SSD gen4 |       1TB       |
| NIC       |          X540-T2 | 2port 10GBASE-T |
| OS        |          Proxmox |       9.1       |
### メインPC

| component |               Name |      Spec       |
| :-------- | -----------------: | :-------------: |
| CPU       | AMD Ryzen9 9950X3D | 2sckets 20C40T  |
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
    
    subgraph Peripherals["sub net"]
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

