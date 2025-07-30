# 🚢 Karmada + vCluster で実現する仮想的なマルチクラスタ運用環境

## はじめに

近年、複数の Kubernetes クラスタを運用するためのベストプラクティスが話題となり、それに伴う形で MCO (Multi-cluster Orchestrator) をはじめとしたマルチクラスタオーケストレーションツールが登場している。  
代表的なツールとして以下のものがある。  

    - [Karmada](https://karmada.io/)
    - [kro](https://kro.run/)
    - [GKE Multi-cluster Orchestrator](https://cloud.google.com/blog/products/containers-kubernetes/multi-cluster-orchestrator-for-cross-region-kubernetes-workloads?hl=en)

## vCluster

### vCluster とは？

![vcluster-component](https://www.vcluster.com/docs/assets/images/vcluster-architecture-ed041d884918b68c3aa11a6d3d65224c.png)

### vCluster CLI をインストールする

- Homebrew を使って vCluster をインストールする

    ```shell
    brew install loft-sh/tap/vcluster-experimental
    ```

### vCluster CLI 基本操作

- 仮想クラスタの作成 (--connect=false を追加するとクラスタ作成後に kube context を変更しない)
  
    ```shell
    vcluster create {{ cluster-name }} --namespace {{ namespace }} [--connect=false]
    ```

- 既存仮想クラスターの設定更新
  
    ```shell
    vcluster create --upgrade {{ cluster-name }} -n {{ namespace }} -f vcluster.yaml
    ```

    - k3s 以外の distro を指定する

        ```shell
        vcluster create --upgrade {{ cluster-name }} -n {{ namespace }} --distro k0s
        ```

- 仮想クラスタへの接続

    ```shell
    vcluster connect {{ cluster-name }} --namespace {{ namespace }}
    ```

- 接続中の仮想クラスターへの接続を切断してホストクラスターに戻る
  
    ```shell
    vcluster disconnect
    ```

- 仮想クラスター一覧
  
    ```shell
    vcluster list
    ```

- 仮想クラスター削除
    
    ```shell
    vcluster delete {{ cluster-name }} --namespace {{ namespace }}
    ```

## 検証 vCluster

### kind クラスタを作成する

- kind コマンドを実行してクラスタを構築する

    ```shell
    kind create cluster --config config/kind-config.yaml
    ```

- kubectl コマンドで作成したクラスタを確認する  
  今回はワーカノードを2つ作成する

    ```shell
    $ kubectl get nodes
    NAME                 STATUS   ROLES           AGE   VERSION
    kind-control-plane   Ready    control-plane   70s   v1.33.1
    kind-worker          Ready    <none>          60s   v1.33.1
    kind-worker2         Ready    <none>          60s   v1.33.1
    ```

### vCluster をデプロイする

- kubectl コマンドで現在のクラスタのコンテキストを取得する

    ```shell
    $ kubectl config current-context
    kind-kind
    ```

- 仮想クラスタ用の namespace を作成する

    ```shell
    kubectl create namespace test-cluster
    ```

- vCluster を kind で作成した Kubernetes クラスタにデプロイする

    ```shell
    vcluster create test-vcluster --namespace test-cluster --values vcluster.yaml
    ```

## Karmada

### Karmada とは？

![karmada-component](https://karmada.io/assets/images/components-9bbbf90a2242f49a418e53615f1b18be.png)

## 検証 Karmada

### kind クラスタを作成する
