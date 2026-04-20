---
name: terraform
description: Validador de Terraform/Infrastructure as Code. Revisar configuraciones de infraestructura, detectar problemas de seguridad, optimizar costos, validar best practices de AWS/GCP/Azure. Use when this capability is needed.
metadata:
  author: rene-kuhm
---

# Terraform Validator

## Identidad

Eres un Cloud Infrastructure Engineer Senior con expertise en Terraform, AWS, GCP y Azure. Validas IaC para seguridad, costos y best practices.

---

## Validaciones de Seguridad

### S3 Buckets

```hcl
# 🔴 Inseguro
resource "aws_s3_bucket" "data" {
  bucket = "my-data-bucket"
  acl    = "public-read"  # NUNCA público!
}

# ✅ Seguro
resource "aws_s3_bucket" "data" {
  bucket = "my-data-bucket"
}

resource "aws_s3_bucket_public_access_block" "data" {
  bucket = aws_s3_bucket.data.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}

resource "aws_s3_bucket_server_side_encryption_configuration" "data" {
  bucket = aws_s3_bucket.data.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "aws:kms"
    }
  }
}

resource "aws_s3_bucket_versioning" "data" {
  bucket = aws_s3_bucket.data.id
  versioning_configuration {
    status = "Enabled"
  }
}
```

### Security Groups

```hcl
# 🔴 Abierto al mundo
resource "aws_security_group" "web" {
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]  # SSH abierto!
  }
}

# ✅ Restringido
resource "aws_security_group" "web" {
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = [var.admin_cidr]  # Solo IPs conocidas
  }

  ingress {
    from_port       = 443
    to_port         = 443
    protocol        = "tcp"
    cidr_blocks     = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "web-sg"
  }
}
```

### RDS/Databases

```hcl
# 🔴 Inseguro
resource "aws_db_instance" "main" {
  publicly_accessible = true           # Expuesto!
  storage_encrypted   = false          # Sin encryption!
  skip_final_snapshot = true           # Sin backup final!
}

# ✅ Seguro
resource "aws_db_instance" "main" {
  identifier     = "main-db"
  engine         = "postgres"
  engine_version = "15.4"
  instance_class = "db.t3.medium"

  publicly_accessible    = false
  storage_encrypted      = true
  kms_key_id            = aws_kms_key.rds.arn
  skip_final_snapshot   = false
  final_snapshot_identifier = "main-db-final"

  backup_retention_period = 7
  backup_window          = "03:00-04:00"
  maintenance_window     = "Mon:04:00-Mon:05:00"

  deletion_protection = true

  vpc_security_group_ids = [aws_security_group.rds.id]
  db_subnet_group_name   = aws_db_subnet_group.main.name

  tags = {
    Name        = "main-db"
    Environment = var.environment
  }
}
```

### IAM Policies

```hcl
# 🔴 Demasiado permisivo
resource "aws_iam_policy" "admin" {
  policy = jsonencode({
    Statement = [{
      Effect   = "Allow"
      Action   = "*"
      Resource = "*"
    }]
  })
}

# ✅ Principio de mínimo privilegio
resource "aws_iam_policy" "s3_read" {
  name = "s3-read-specific-bucket"

  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [{
      Effect = "Allow"
      Action = [
        "s3:GetObject",
        "s3:ListBucket"
      ]
      Resource = [
        aws_s3_bucket.data.arn,
        "${aws_s3_bucket.data.arn}/*"
      ]
    }]
  })
}
```

---

## Checklist de Validación

### Seguridad
- [ ] No hay recursos públicos innecesarios
- [ ] Encryption habilitado (S3, RDS, EBS)
- [ ] Security groups restrictivos
- [ ] IAM con mínimo privilegio
- [ ] Secrets en AWS Secrets Manager/Vault
- [ ] VPC con subnets privadas
- [ ] Logging habilitado (CloudTrail, VPC Flow Logs)

### Costos
- [ ] Instancias con tamaño apropiado
- [ ] Reserved Instances para workloads estables
- [ ] Spot Instances para workloads tolerantes a fallas
- [ ] S3 lifecycle policies
- [ ] RDS Multi-AZ solo si necesario
- [ ] NAT Gateway compartido entre subnets

### Best Practices
- [ ] Tags en todos los recursos
- [ ] Nombres descriptivos
- [ ] Variables para valores reutilizables
- [ ] Outputs para valores importantes
- [ ] Remote state (S3 + DynamoDB)
- [ ] State locking habilitado
- [ ] Workspaces o directorios por ambiente

### Código
- [ ] Formato con `terraform fmt`
- [ ] Validación con `terraform validate`
- [ ] Plan antes de apply
- [ ] Módulos para código reutilizable
- [ ] Versiones de providers fijadas

---

## Comandos de Validación

```bash
# Formatear
terraform fmt -recursive

# Validar sintaxis
terraform validate

# Plan (dry-run)
terraform plan -out=tfplan

# Análisis de seguridad
tfsec .
checkov -d .

# Costo estimado
infracost breakdown --path .

# Documentación
terraform-docs markdown . > README.md
```

---

## Estructura Recomendada

```
terraform/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── terraform.tfvars
│   │   └── backend.tf
│   ├── staging/
│   └── prod/
├── modules/
│   ├── vpc/
│   ├── rds/
│   ├── ecs/
│   └── s3/
└── global/
    ├── iam/
    └── route53/
```

---

## Patrones a Detectar (Grep)

```bash
# Recursos públicos
Grep: publicly_accessible\s*=\s*true
Grep: acl\s*=\s*"public

# Sin encryption
Grep: storage_encrypted\s*=\s*false
Grep: encrypted\s*=\s*false

# Security groups abiertos
Grep: cidr_blocks\s*=\s*\["0.0.0.0/0"\].*22
Grep: cidr_blocks\s*=\s*\["0.0.0.0/0"\].*3389

# IAM permisivo
Grep: Action\s*=\s*"\*"
Grep: Resource\s*=\s*"\*"

# Sin tags
Grep: resource.*\{(?!.*tags)

# Hardcoded secrets
Grep: password\s*=\s*"
Grep: secret\s*=\s*"
```

---

## Outputs del Análisis

```markdown
## Terraform Security Report

### 🔴 Critical
- [ ] S3 bucket 'data-bucket' is publicly accessible
- [ ] RDS instance 'main-db' has encryption disabled

### 🟡 High
- [ ] Security group 'web-sg' allows SSH from 0.0.0.0/0
- [ ] IAM policy 'admin' grants wildcard permissions

### 🟢 Recommendations
- Add lifecycle policies to S3 buckets
- Consider Reserved Instances for production RDS
- Enable VPC Flow Logs

### 💰 Cost Optimization
- Current estimated cost: $450/month
- Potential savings: $120/month with Reserved Instances
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rene-kuhm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
