## AWS(S3 + CloudFront) + React.js 배포방법

### AWS CLI 설치

- https://aws.amazon.com/ko/cli/ 사이트에 접속하여 OS에 맞는 CLI 설치파일을 다운로드 받아 설치합니다.

### IAM 사용자 추가

- AWS IAM Console에 접속하여 사용자를 추가메뉴로 이동합니다.<br />아래 이미지와 같이 액세스 유형을 <span style="color: #EC7420; font-weight: bold">액세스 키 - 프로그래밍 방식 액세스</span> 를 선택하고 <span style="color: #EC7420; font-weight: bold">다음: 권한</span> 버튼을 클릭합니다. <br /> :: image1 ::
- 권한 설정에서 아래 이미지와 같이 <span style="color: #EC7420; font-weight: bold">기존 정책 직접 연결</span>을 선택하고 <span style="color: #EC7420; font-weight: bold">S3</span>를 검색하여 <span style="color: #EC7420; font-weight: bold">AmazonS3FullAccess</span>를 체크하고 <span style="color: #EC7420; font-weight: bold">다음: 태그</span> 버튼을 클릭합니다.<br /> :: image2 ::
- 태그 추가는 필수는 아니기에 추가 없이 <span style="color: #EC7420; font-weight: bold">다음: 검토</span> 버튼을 클릭합니다.<br /> :: image3 ::
- <span style="color: #EC7420; font-weight: bold">사용자 만들기</span> 버튼을 클릭하여 IAM 사용자를 생성 합니다.<br /> :: image4 ::
- <span style="color: #EC7420; font-weight: bold">.csv 다운로드</span> 버튼을 클릭하여 파일을 잘 보관해 둡니다.<span style="display: block; color: #ff0000;">비밀 액세스 키를 분실한 경우 엑세스 키를 새롭게 생성해야 합니다.</span> :: image5 ::

### AWS CLI 유저 추가

- AWS CLI를 통해 유저를 추가합니다. <span style="display: block; background-color: #121212; padding: 4px 8px; color: #fff;"><span style="color: #96F2D7; margin-right: 8px; font-family: Roboto;">></span>aws configure --profile <span style="color: #EC7420; font-weight: bold">프로필 명</span></span> 아래 이미지와 같이 생성한 IAM 사용자 액세스 정보를 <span style="color: #EC7420; font-weight: bold">AWS Access Key ID</span>와 <span style="color: #EC7420; font-weight: bold">AWS Secret Access Key</span>에 입력해주고 Default region name 을 입력한뒤 <span style="color: #EC7420; font-weight: bold">Default output format</span>값을 <span style="color: #EC7420; font-weight: bold">json</span>으로 입력해줍니다. <br /> :: image 6 ::

### AWS S3 설정

- AWS S3 Console에 접속하여 S3버킷을 생성합니다.
- 버킷 메뉴에 <span style="color: #EC7420; font-weight: bold">버킷 만들기</span>버튼을 클릭합니다.<br /> :: image 7 ::
- <span style="color: #EC7420; font-weight: bold">버킷 이름</span>과 <span style="color: #EC7420; font-weight: bold">AWS 리전</span> 값을 선택 후 아래 이미지와 같이 <span style="color: #EC7420; font-weight: bold">객체 소유권, 이 버킷의 퍼블릭 액세스 차단설정, 버킷 버전 관리, 태그, 기본 암호화, 고급설정</span>을 설정하고 <span style="color: #EC7420; font-weight: bold">버킷 만들기</span> 버튼을 클릭합니다.<br /> :: image 8 ::
- 버킷 생성이 완료되면 아래 이미지와 같이 노출됩니다.<br /> :: image 9 ::

### AWS S3 익명 사용자에게 읽기전용 권한 부여

- AWS S3 Console에 접속하여 생성한 S3버킷의 <span style="color: #EC7420; font-weight: bold">권한 탭</span>으로 이동합니다.<br /> :: image 10 ::
- <span style="color: #EC7420; font-weight: bold">퍼블릭 액세스 차단(버킷 설정)</span> 영역의 <span style="color: #EC7420; font-weight: bold">편집</span> 버튼을 클릭 후 아래 이미지와 같이 세팅해줍니다.<br /> :: image 11 ::
- 버킷 정책 영역의 <span style="color: #EC7420; font-weight: bold">편집</span> 버튼을 클릭하여 <span style="color: #EC7420; font-weight: bold">버킷 정책 편집</span> 페이지로 이동합니다.
- 정책을 아래와 같이 입력 후 <span style="color: #EC7420; font-weight: bold">변경사항 저장</span> 버튼을 클릭합니다.
  <pre style="background-color: #121212; color: #fff; border-radius: 5px; padding: 4px 8px;">
  {
    <span style="color: #3f8ed2;">"Version"</span>: "2012-10-17",
    <span style="color: #3f8ed2;">"Statement"</span>: [
          {
            <span style="color: #3f8ed2;">"Sid"</span>:"AddPerm",
            <span style="color: #3f8ed2;">"Effect"</span>:"Allow",
            <span style="color: #3f8ed2;">"Principal"</span>: "*",
            <span style="color: #3f8ed2;">"Action"</span>:["s3:GetObject"],
            <span style="color: #3f8ed2;">"Resource"</span>:["arn:aws:s3:::<span style="color: #EC7420; font-weight: bold">S3 버킷 이름</span>/*"]
          }
    ]
  }
  </pre>
  :: image 12 ::
- S3버킷의 <span style="color: #EC7420; font-weight: bold">속성 탭</span>으로 이동하여 <span style="color: #EC7420; font-weight: bold">정적 웹 사이트 호스팅</span> 영역의 <span style="color: #EC7420; font-weight: bold">편집</span> 버튼을 클릭합니다. <br /> :: image 13 ::
- 아래 이미지와 같이 <span style="color: #EC7420; font-weight: bold">정적 웹사이트 호스팅</span> 값을 <span style="color: #EC7420; font-weight: bold">활성화</span>를 해주고 각 설정 값들을 설정 후 변경사항 저장 버튼을 클릭합니다<br /> :: image 14 ::

### React.js를 AWS S3에 배포

- React.js Project Root에서 AWS CLI를 활용하여 S3에 배포를 진행합니다. <span style="display: block; background-color: #121212; padding: 4px 8px; color: #fff;"><span style="color: #96F2D7; margin-right: 8px; font-family: Roboto;">></span>aws s3 sync ./build s3://<span style="color: #EC7420; font-weight: bold">S3버킷 이름</span> --profile=<span style="color: #EC7420; font-weight: bold">IAM 유저명</span></span>
- 편의를 위해 <span style="color: #EC7420; font-weight: bold">package.json</span>의 <span style="color: #EC7420; font-weight: bold">Scripts섹션</span>에 아래와 같이 스크립트를 추가해주면 편하게 배포가 가능합니다.
  <pre style="background-color: #121212; color: #fff; border-radius: 5px; padding: 4px 8px;">
  <span style="color: #3f8ed2;">"scripts"</span>: {
    <span style="color: #3f8ed2;">"start"</span>: "react-scripts start",
    <span style="color: #3f8ed2;">"build"</span>: "react-scripts build",
    <span style="color: #3f8ed2;">"test"</span>: "react-scripts test",
    <span style="color: #3f8ed2;">"eject"</span>: "react-scripts eject",
    <span style="color: #3f8ed2;">"deploy"</span>: "aws s3 sync ./build s3://severless-react-test --profile=react-test"
  },
  </pre>

### CloudFront 설정

- CloudFront를 사용하여 S3의 파일들을 CDN에 태워줌으로서 <span style="color: #EC7420; font-weight: bold">커스텀 도메인, HTTPS, CDN을 통한 빠른 페이지 응답속도</span> 등의 이점을 얻을 수 있습니다.
- AWS CloudFront Console에 접속하여 <span style="color: #EC7420; font-weight: bold">배포</span> 메뉴의 <span style="color: #EC7420; font-weight: bold">배포 생성</span> 버튼을 클릭합니다.<br /> :: image 15 ::
- 원본 도메인을 S3에서 생성한 도메인으로 선택하고 각각의 옵션을 설정 해주고 배포 생성 버튼을 클릭합니다.<span style="display: block; color: #4FA6F0;">커스텀 도메인을 사용할 경우 아래 설정영역 대체도메인 이름(CNAME)에 해당 도메인을 작성해줍니다.</span> :: image 16 ::

### Cache Purge 방법

- 소스 업데이트가 발생했을 때 CDN에 퍼져있는 파일들을 새로고침 해주는 작업입니다.
- IAM에 생성한 유저에 <span style="color: #EC7420; font-weight: bold">CloudFrontFullAccess</span> 권한을 추가해 줍니다.<br /> :: image 17 ::
- AWS CLI를 활용하여 명령어를 날려주도록 합니다.<span style="display: block; background-color: #121212; padding: 4px 8px; color: #fff;"><span style="color: #96F2D7; margin-right: 8px; font-family: Roboto;">></span>aws cloudfront create-invalidation --profile=<span style="color: #EC7420; font-weight: bold">IAM 유저명</span> --distribution-id <span style="color: #EC7420; font-weight: bold">CloudFront Id값</span> --paths / /index.html /error.html /manifest.json /favicon.ico</span>
- 편의를 위해 <span style="color: #EC7420; font-weight: bold">package.json</span>의 <span style="color: #EC7420; font-weight: bold">Scripts섹션</span>에 아래와 같이 스크립트를 추가해주면 편하게 배포가 가능합니다.
  <pre style="background-color: #121212; color: #fff; border-radius: 5px; padding: 4px 8px;">
  <span style="color: #3f8ed2;">"scripts"</span>: {
    <span style="color: #3f8ed2;">"start"</span>: "react-scripts start",
    <span style="color: #3f8ed2;">"build"</span>: "react-scripts build",
    <span style="color: #3f8ed2;">"test"</span>: "react-scripts test",
    <span style="color: #3f8ed2;">"eject"</span>: "react-scripts eject",
    <span style="color: #3f8ed2;">"deploy"</span>: "aws s3 sync ./build s3://react-test --profile=react-test",
    <span style="color: #3f8ed2;">"invalidate"</span>: "aws cloudfront create-invalidation --profile=react-test --distribution-id E2YGM0CU4YNZO4 --paths / /index.html /error.html /service-worker.js /manifest.json /favicon.ico"
  },
  </pre>
  :: image 18 ::
