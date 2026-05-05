# devops-aws

☁️ Infraestrutura **AWS real** (não simulada) — uso do free tier para projetos práticos com Terraform e GitHub Actions.

> Evolução natural do `devops-terraform` (que usa LocalStack) para o ambiente real da AWS.

## Por que esse projeto

LocalStack é ótimo para aprender, mas o mercado exige experiência com AWS real. Este projeto faz tudo dentro do **free tier** para custar zero (ou muito próximo).

## Stack

- **AWS** — Cloud provider (free tier)
- **Terraform** — IaC com state remoto em S3
- **GitHub Actions** — pipeline que aplica Terraform via OIDC (sem chaves expostas)
- **AWS CLI v2**

## O que será provisionado

| Recurso | Tier free? | Uso |
|---|---|---|
| VPC + subnets | Sim | Rede |
| EC2 t2.micro | 750h/mês grátis | Servidor de aplicação |
| S3 | 5GB grátis | Armazenamento e Terraform state |
| CloudWatch Logs | 5GB grátis | Observabilidade |
| IAM roles | Grátis | OIDC com GitHub Actions |
| Lambda | 1M invocações grátis | Função serverless |

## Estrutura

```
devops-aws/
├── terraform/
│   ├── modules/
│   │   ├── network/
│   │   ├── compute/
│   │   └── storage/
│   ├── environments/
│   │   ├── dev/
│   │   └── prod/
│   └── backend.tf            # S3 + DynamoDB lock
├── .github/workflows/
│   ├── plan.yml              # roda em PR
│   └── apply.yml             # roda em merge para main
├── scripts/
│   └── bootstrap-backend.sh  # cria S3+DynamoDB iniciais
└── README.md
```

## Diferenciais

- ✅ **Estado remoto** com lock (DynamoDB)
- ✅ **Múltiplos ambientes** (dev/prod) via workspaces
- ✅ **OIDC** GitHub → AWS (sem `AWS_ACCESS_KEY` em secrets)
- ✅ **Plan automático** em PRs com comentário no PR
- ✅ **Tags obrigatórias** em todos os recursos (Owner, Env, Project)

## Como começar

```bash
# 1. Bootstrap (uma vez só)
bash scripts/bootstrap-backend.sh

# 2. Configurar OIDC entre GitHub e AWS
# (ver docs/oidc-setup.md)

# 3. Apply local (opcional, normalmente via CI)
cd terraform/environments/dev
terraform init
terraform plan
terraform apply
```

## Roadmap

- [ ] Módulo de RDS PostgreSQL (free tier)
- [ ] Deploy de aplicação Python via Lambda + API Gateway
- [ ] CloudWatch alarms + SNS
- [ ] Migração de algum projeto existente (`devops-monitoring`?) para rodar na AWS

## Autor

**Henrique Sattolo** — [github.com/henriquesattolo](https://github.com/henriquesattolo)
