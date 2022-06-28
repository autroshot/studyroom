# DNS

DNS(Domain Name System) Server는 도메인 이름을 IP 주소로 변환해준다.

host - 네트워크에 연결된 컴퓨터들

![hosts](https://user-images.githubusercontent.com/95019875/176091765-e26b2e65-332c-4346-9f80-e1b9a86d3612.jpeg)

자신의 컴퓨터에 있는 hosts 파일을 수정하면 직접 도메인 이름을 부여할 수 있다.

![DNS](https://user-images.githubusercontent.com/95019875/176091788-7321f1c3-7e6f-4b14-9c85-21a5fdfdc641.jpeg)

브라우저에 도메인 이름을 입력하면 먼저 자신의 컴퓨터의 hosts 파일에서 해당 도메인 이름을 찾는다. 그곳에서 못 찾으면 DHCP를 통해 알고 있던 DNS Server에서 해당 도메인 이름을 찾는다.

![도메인 이름의 구조](https://user-images.githubusercontent.com/95019875/176091802-ce19433c-bee1-4678-8d3e-ca782f1c6f3c.jpeg)

단계마다 DNS Server가 있으며 각 서버는 하위 DNS Server의 주소를 알고 있다.

![도메인 이름 등록 과정과 원리](https://user-images.githubusercontent.com/95019875/176091817-41b4792b-b326-4194-b5a0-8789fbc5be58.jpeg)

위 그림은 도메인 이름 등록 과정과 원리를 설명한다.

nslookup을 이용하면 도메인 이름에 대한 정보를 조회할 수 있다.

DNS Record - 도메인 이름에 대한 정보가 담겨있다. Record 타입으로는 A, CNAME 등이 있다.
