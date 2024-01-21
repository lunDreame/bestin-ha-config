# bestin-ha-config
Home Assistant Config files for Bestin IOT

## 사전 요구사항
- IPARK 스마트홈 사용자

- curl 명령어가 수행가능한 환경 (home assistant가 구동중인 서버)

- 본인 거주 아파트의 원격 제어 Host IP 주소 : http://www.i-parklife.com/ 접속 후 아파트 이름을 클릭하면 원격제어 페이지로 접속된다. 거기서 나오는 IP 주소 획득

- 원격제어 페이지에서 알아내야 하는 정보
  1. 계정 정보 : 회원 가입하면 생성되는 id와 password 필요
  2. 쿠키 정보 : (크롬 브라우저 기준) F12키 누르고 개발자도구 진입 -> 응용프로그램 클릭 -> 쿠키 -> user_name의 값, PHPSESSID의 값
 
## 설정 가이드
1. 'environment_variables.json'의 파일 내용 수정 : Host IP 주소, 계정 id, 계정 password 및 쿠키 정보에서 알아낸 user_name의 값 입력
2. `automations.yaml` 참고하여 주기적으로 cookie_phpsessid command를 호출하여 인증토큰을 갱신하도록 자동화 구성
3. 본인 거주 아파트 환경에 맞게 방(이름) 갯수, 조명(이름) 갯수, 전원(이름) 소켓 갯수, 난방(이름) 갯수에 따라 sensors.yaml, lights.yaml, switches.yaml 파일 내용 수정 필요
   
   3-1. 
   
        [조명]
        req_unit_num : 조명번호를 의미
        req_ctrl_action : 조명제어명령을 의미, off 또는 on로 입력
        req_dev_num : 방번호를 의미
        
        *조명&거실 조명 상태조회 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_light&req_action=status&req_dev_num=1
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_livinglight&req_action=status
        *조명 조작 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_light&req_action=control&req_unit_num=1&req_ctrl_action=off&req_dev_num=1 
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_livinglight&req_action=control&req_unit_num=1&req_ctrl_action=off
        
        [콘센트]
        req_unit_num : 콘센트 소켓 번호를 의미
        req_ctrl_action : 콘센트제어명령을 의미, off 또는 on로 입력
        req_dev_num : 방번호를 의미
        
        *콘센트 상태조회 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_electric&req_action=status&req_dev_num=1
        *콘센트 조작 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_electric&req_action=control&req_unit_num=1&req_ctrl_action=on&req_dev_num=1
         
        [난방]
        req_unit_num : 각 난방기 번호를 의미
        req_ctrl_action : 난방제어명령을 의미, (off 또는 on)/(원하는 온도 설정)으로 구성됨
        
        *난방 상태조회 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice_heat.php?req_name=remote_access_temper&req_action=status&req_unit_num=room1
        *난방 조작 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice_heat.php?req_name=remote_access_temper&req_action=control&req_unit_num=room1&req_ctrl_action=on/23.5
        
        [전열교환기]
        req_unit_num : ventil
        req_ctrl_action : 전열교환기제어명령을 의미, off 또는 on(low,mid,high)
        
        *전열교환기 상태조회 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_ventil&req_action=status
        *전열교환기 조작 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_ventil&req_action=control&req_unit_num=ventil&req_ctrl_action=on
         
        [가스벨브]
        req_unit_num : gas1
        req_ctrl_action : 가스벨브제어명령을 의미, close(닫힘만 지원)
        
        *가스벨브 상태조회 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_gas&req_action=status
        *가스벨브 조작 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_gas&req_action=control&req_unit_num=gas1&req_ctrl_action=close
          
        [도어락]
        *도어락 상태조회 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_doorlock&req_action=status
         
        [외출설정]
        req_unit_num : unoccupied
        req_ctrl_action : 외출설정제어명령을 의미, unoccupied
        
        *외출설정 상태조회 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_mode&req_action=status
        *외출설정 조작 예시
         (GET) http://<Host IP 주소>/webapp/data/getHomeDevice.php?req_name=remote_access_mode&req_action=control&req_ctrl_action=unoccupied
         
## 지원 기능
1. 조명
2. 난방
3. 콘센트(대기전력 차단 지원)
4. 전열교환기
5. 가스벨브(단방향)
6. 외출설정
7. 도어락(상태조회 지원)
8. 에너지 조회(수정중..)
9. 차량 정보조회(추가중...)

## 작성시 참조한 github
https://github.com/py-kim/bestin_config_HA
