Amazon Web Service\(AWS\)에서의 PostgreSQL

VPC 설정은 AWS에서 제공하는 기본 VPC를 사용해도 되지만, 여기서는 다음과 같이 VPC와 네트워크 환경을 미리 준비했습니다.

VPC : 10.11.0.0/16

Private Subnet : 10.11.0.0/24, 10.11.1.0/24

Public Subnet : 10.11.2.0/24, 10.11.3.0/24

Security Group : default, ssh



1. EC2에 설치

AWS 콘솔에 접속하여 로그인 한 뒤, “EC2” 메뉴로 접속합니다.

aws-001.png![](/assets/aws-001.png)

EC2 dashboard 화면의 중간에 위치한 “Launch Instance”를 클릭합니다.

aws-002.png![](/assets/aws-002.png)

인스턴스 생성을 위해서는 총 7단계를 마쳐야 하는데 그 중 첫 단계입니다. 본인이 사용하기 편한 운영체제 이미지를 선택하면 되는데, 여기서는 Amazon Linux 를 선택합니다.

aws-003.png![](/assets/aws-003.png)

2단계는 인스턴스의 타입을 선택합니다. 필요한 vCPU, Memory 등을 고려하여 필요한 타입을 선택하면 되는데, 비용 절약을 위해 가장 낮은 타입을 선택합니다. 당연하겠지만, 고사양을 선택하면 비용이 장난 아니게 나옵니다. 일단 테스트용이므로 t2.micro를 선택하고 Next.

aws-004.png





3단계는 인스턴스가 운영될 환경을 설정합니다.

Network : 추가로 구성한 VPC 가 있을 경우 사용할 VPC를 선택합니다.

Subnet : 외부에서 인스턴스에 접속하려면 인터넷에 연결되어야 하므로 Public subnet을 선택 합니다.

Auto-assign Public IP : subnet 설정에 따라 Disable 될 수도 있으니 Enable 해주도록 합니다.

나머지는 기본으로 놔두고 Next.

aws-005.png





4단계에서는 스토리지를 설정합니다. PostgreSQL을 설치하고 데이터를 저장하기 위해 50GB를 추가해줍니다.

aws-006.png





5단계에서는 Tag를 추가해줍니다. 인스턴스의 Name을 추가해주면 인스턴스를 구분하기 쉬워집니다.

aws-007.png





6단계에서는 보안 그룹을 설정합니다. 기본으로 만들어져 있는 default와 인터넷으로부터 인스턴스에 ssh 접속을 하기위해 추가한 Security Group 을 추가해줍니다.

Security Group : default

이거는 기본으로 만들어져 있습니다.

Security Group : rp-sjyun-ssh

```
Type : SSH \(22\)

Protocol : TCP \(6\)

Port Range : 22

Source 0.0.0.0/0
```

aws-008.png

마지막 7단계입니다. 지금까지의 설정 내용을 확인하고 Launch를 클릭.

aws-009.png

key pair 파일을 선택하라는 창이 나오면 사용할 key pair 를 선택해줍니다. 준비된 key pair 파일이 없다면 여기에서 새로 생성해도 됩니다. key pair 생성 시 다운 받은 pem 파일은 매우 중요합니다. AWS에서는 인스턴스 생성시에 선택한 key pair의 pem 파일로만 인스턴스에 접속이 가능하므로 잃어버리지 않게 잘 관리하셔야만 합니다. 또한 외부에 유출되면 심각한 보안 사고가 발생할 수도 있습니다.

aws-010.png

여기까지 잘 진행되었다면 아래와 같은 화면을 만날 수 있습니다. 인스턴스 ID 를 클릭하면 현재 생성되고 있는 인스턴스의 상태를 보실 수 있습니다.

aws-011.png

Status Checks의 항목이 “Initializing”에서 “2/2 checks passed” 로 바뀌면 인스턴스 생성이 완료되어 서비스 준비가 완료된 상태입니다. 인스턴스를 클릭하면 화면 아래에 여러 정보가 나옵니다.

aws-012.png

이제 Putty를 이용하여 생성한 인스턴스에 접속하기 위해 “Public DNS \(IPv4\)” 를 확인합니다. “IPv4 Public IP”를 이용해서 접속할 수도 있습니다.

aws-013.png

이제부터 설명하는 Putty 접속을 위한 내용은 PuTTY를 사용하여 Windows에서 Linux 인스턴스에 연결 에서 더 자세한 내용을 확인할 수 있습니다. \([http://docs.aws.amazon.com/ko\_kr/AWSEC2/latest/UserGuide/putty.html\](http://docs.aws.amazon.com/ko_kr/AWSEC2/latest/UserGuide/putty.html%29\)

Putty 는 AWS의 PEM Key를 그대로 사용할 수 없기에 puttygen 프로그램을 이용하여 Putty에서 사용할 수 있는 ppk 파일로 변환해줘야 합니다. puttygen을 실행하고 RSA를 선택합니다.

aws-014.png

puttygen 을 실행하여 “Load” 를 클릭하여 PEM key 파일을 오픈합니다.

aws-015.png

팝업창의 내용을 보면 “Save private key” 하라고 나옵니다. 확인을 클릭합니다.

aws-016.png

“Save private key” 를 클릭하여 ppk 파일로 저장합니다.

Putty를 열어서 Connection &gt; SSH &gt; Auth에 AWS 접속을 위해 위에서 저장한 ppk 파일을 등록해줍니다.

aws-017.png

Session &gt;  \[Host Name\] 상자에 user\_name@public\_dns\_name을 입력합니다.

aws-018.png

이제 Open 을 클릭하면 처음 접속할 경우 보안 경고가 나옵니다.

aws-019.png

“예\(Y\)”를 클릭하면 인스턴스로 접속이 됩니다.

aws-020.png

이제부터는 일반적인 리눅스에서 설치하는 방법과 동일하게진행하면 됩니다. 

PostgreSQL 설치를 위해 추가한 50GB의 디스크를 마운트 합니다. 


''''
디스크를 포맷해줍니다. 
mkfs.ext4 /dev/sdb

적당한 디렉토리를 생성하고 마운트 해줍니다. 
sudo mkdir /db
sudo mount /dev/sdb /db

PostgreSQL 설치를 위해 디렉토리를 생성합니다. 
sudo mkdir /db/pgsql
cd /db/pgsql
''''

이제 이곳에 최신 버전을 다운로드 받고 설치하면 됩니다. 

1. AWS MarketPlace를 이용한 설치

[https://aws.amazon.com/marketplace](https://aws.amazon.com/marketplace)

1. AWS RDS의 PostgreSQL

2. AWS Aurora의 PostgreSQL

3. AWS CloudWatch를 이용한 모니터링

\#Google Cloud Platform\(GCP\)에서의 PostgreSQL

1. Compute Engine에 설치

2. Cloud Launcher를 이용한 설치

3. Stackdriver를 이용한 모니터링

\#Microsoft Azure

