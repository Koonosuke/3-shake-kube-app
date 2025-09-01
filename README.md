## プロジェクト概要

このリポジトリは Terraform + Kubernetes(EKS Fargate) を用いて、
簡単な Nginx アプリをクラウド上で動作させるデモ環境

インフラ層 → Terraform 管理 (VPC / EKS / IAM / Fargate)

アプリ層 → Kubernetes マニフェスト管理 (nginx Deployment / Service)

将来的に GitOps (ArgoCDなど) でアプリを継続的デプロイ可能にする構成

## 技術スタックな

### AWS

- VPC (Public / Private Subnet, NAT Gateway, IGW)

- IAM (EKSクラスタ用, FargatePod実行用 Role)

- EKS (Fargate専用クラスタ, version 1.30)

### Terraform

- Provider: aws >= 5.40.0

- Module: terraform-aws-modules/vpc, terraform-aws-modules/eks

### Kubernetes

- Namespace: default

- Pod 実行基盤: AWS Fargate

- サンプルアプリ: Nginx

### その他

将来的に GitOps, MCP Server 検証にも対応予定

## フォルダ構成
3-shake-demo-app/
├── terraform/                 # IaC (インフラ層)
│   ├── main.tf                # Provider & Module 呼び出し
│   ├── vpc.tf                 # VPC 定義
│   ├── iam.tf                 # IAM ロール定義
│   ├── eks.tf                 # EKS クラスタ + Fargate プロファイル
│   ├── variables.tf           # 変数
│   └── outputs.tf             # 出力 (cluster endpoint 等)
│
└── k8s-manifests/             # Kubernetes マニフェスト (アプリ層)
    └── nginx-deployment.yaml  # Deployment + Service (LoadBalancer)

### セットアップ手順
1. Terraform 初期化 & 適用
cd terraform
terraform init -upgrade
terraform plan
terraform apply

2. kubeconfig 更新
aws eks update-kubeconfig --region ap-northeast-1 --name demo-cluster --profile eks-admin

3. Nginx デプロイ
kubectl apply -f k8s-manifests/nginx-deployment.yaml
kubectl get svc


👉 EXTERNAL-IP に表示された LoadBalancer のURLをブラウザで開くと Welcome to nginx! が表示されます。

## 今後の展望

GitHub Actions + ArgoCD を組み合わせて GitOps 化

MCP サーバーを利用した Kubernetes リソース管理の自動化実験

## Terraform実行前

CMD

set AWS_PROFILE=eks-admin
set AWS_REGION=ap-northeast-1

