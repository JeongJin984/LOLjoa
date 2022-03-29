# **AWS Architecture**
- - -
![스크린샷 2022-03-26 오후 10 02 59](https://user-images.githubusercontent.com/43293666/160240678-d622f101-8857-4cc6-8eaf-107f7d8fb0fa.png)

### Admin
  1. 테라폼으로 퍼블릭 클라우드 아키텍처 구성도를 보고 구축
  2. 앤서블을 사용하여 내부 서버 구축
  3. Bastion Host를 통해 관리
        - NatGateway를 이용하여 Private subnet에 진입
        - Git의 웹훅을 이용하여 외부 이슈 트래커 또는 CI 빌드 트리거 → Jenkins
        - 오브젝트 관리를 위해 s3 구축
  4. (이미지 임시)ELK 또는 EFK를 통해 로그 (EKS 구축후 자세히)
  
 ![스크린샷 2022-03-27 오전 12 24 40](https://user-images.githubusercontent.com/43293666/160246167-da5190d8-5cf2-4a27-9db4-691c62ce7bbe.png)
 
  5. (이미지 임시)prometheus를 통해 모니터링 (EKS 구축후 자세히)
  6. (미정) LAMBDA를 사용하여 클라우드 이용시간 제어
 
### Client
  1. Route53 도메인을 통해 인터넷 게이트웨이 → ELB → Ingress로 진입하는 구조
        - 트래픽에 따라 ELB에서 오토스케일링 그룹을 통해 connect
  2. 연결 설정된 인그레이스를 통해 각 쿠버네티스의 서비스로 도달
  3. 클라우드 워치를 통해 connection/CPU 사용량 감지하여 트래픽 제어
        - 클라우드 워치에서 ELB 감시
        - 일정 수준의 connection/CPU 사용량 도달시 오토스케일링을 사용하여 스케일링
        - (기본 2개에서최대 3개 구성함)
- - -
###  Resource
- VPC = 1 
- Elastic IP add = 3 (bastionhost, ELB, NatGateway)
- Security groups = 5 (임시)
- IAM roles = ±11 (첫구성시 11 이후 필요한 역할에 따라 늘어날 수 있음)
- IAM Role (임시)
    - AdministratorAccess
    - AmazonRDSFullAccess
    - AmazonEC2FullAccess
    - CloudWatchFullAccess
    - AmazonRoute53FullAccess
    - AmazonVPCFullAccess
    - AmazonEKSClusterPolicy
    - AmazonEKSVPCResourceController
    - AmazonEKSWorkerNodePolicy
    - AmazonS3FullAccess
    - 각 ReadOnlyAccess ()
- Auto Scaling groups =  3 
- ELB load balancers =  1 (ingress 연동을 위해 ALB로 구성)
- t2.small Instance = 4 (최소사양 worker 구성)
- t2.medium Instance = 3 (권장사양 bastion host, master 구성)
- RDS Instance = 2 (사양은 데이터양에 따라 구성)
- 네트워크 & 정책 = Calico
- 스토리지 & 정책 = EBS스토리지 클래스
- 컴퓨트 & 정책 = 오토스케일링 
