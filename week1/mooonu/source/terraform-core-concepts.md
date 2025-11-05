
## 구성 요소

### code
```Terraform
# 1. Terraform 설정
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

# 2. Provider
provider "aws" {
  region = "ap-northeast-2"
}

# 3. Resource
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "main-vpc"
  }
}

# 4. Source
data "aws_ami" "latest_ubuntu" {
  most_recent = true
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }
}

# 5. Variable
variable "instance_type" {
  description = "EC2 인스턴스 타입"
  type        = string
  default     = "t2.micro"
}

# 6. Output
output "vpc_id" {
  description = "생성된 VPC의 ID"
  value       = aws_vpc.main.id
}
```

### lesson
- HCL (HashiCorp Configuration Language): `.tf` 확장자를 가진 파일에 사용되는 Terraform의 언어
- provider: AWS, Azure 등의 API와 통신하는 플러그인
- resource: EC2, VPC 등 Terraform이 관리할 대상
- source: 기존 인프라나 외부 정보를 읽어오는 데 사용
- variable: 코드의 재사용성을 위해 사용
- output: 생성된 리소스의 정보를 외부에서 확인하도록 노출
- state: 코드로 정의된 설정이 실제 인프라와 어떻게 매핑되는지 현재 상태를 JSON 형식의 파일로 저장

---

## 동작 원리

### code
```bash
# 1. 초기화
# provider 다운로드 및 초기화
$ terraform init

# 2. 실행 계획
# 실행 계획 확인 (변경 사항 확인)
# tf, state 읽기 및 인프라 상태 조회
$ terraform plan

# 3. 적용
# 인프라 생성/변경 실행
# plan 재실행, provider api call, state 업데이트
$ terraform apply

# 4. 삭제 
# 인프라 삭제
$ terraform destroy
```

### lesson
- 코드 작성 → State 비교 → 실행 계획 생성 → API 호출 → 인프라 변경 → State 업데이트
- plan은 읽기 전용이며, 변경 내용을 계속 확인할 수 있음
	- state가 현재 상태를 나타냄
- init은 의존성이 바뀌면 재실행, 패키지 install과 유사함
- apply 단계에서 plan을 재실행하는 이유는 항상 최신 상태를 유지하기 위함