## EC2 생성

간단한 서버 API 이므로 `t2.micro` 로 생성 합니다

![img.png](images/ec2-apiserver/image%20(9).png)
![img.png](images/ec2-apiserver/image%20(10).png)
![img.png](images/ec2-apiserver/image%20(11).png)
![img.png](images/ec2-apiserver/image%20(12).png)
![img.png](images/ec2-apiserver/image%20(13).png)

`인터넷에서 HTTP 트래픽 허용`은 80 포트의 접근을 허용하는 옵션 입니다.   
해당 옵션이 없다면 보안그룹에서 80 port를 추가해 주도록 합니다.
![img.png](images/ec2-apiserver/image%20(16).png)

## 간단한 API 서버 셋팅하기

**1) Node 설치**

```shell
# root 권한으로 전환 합니다
$ sudo su
# root 권한으로 설치를 진행 합니다
$ apt-get update && /
apt-get install -y ca-certificates curl gnupg && /
mkdir -p /etc/apt/keyrings && /
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg && /
NODE_MAJOR=20 && /
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list && /
apt-get update && /
apt-get install nodejs -y

# 잘 설치됐는 지 확인하기
$ node -v
```

**2) Express 서버 셋팅**

Express 서버 초기화를 위해 아래 명령어를 실행 합니다.

```shell
$ mkdir my-server
$ cd my-server
$ npm init # 의존성 관리 파일 생성
$ npm i express # express 라이브러리 설치
```

`npm init` 명령어 실행 후 나오는 질문들에 대해서는 엔터키를 눌러 진행 합니다.  
![img.png](images/ec2-apiserver/image (14).png)

`app.js` 파일을 생성하고 간단한 API 로직을 작성 합니다.

```js
const express = require('express');
const app = express();
const port = 80;

app.get('/boards', (req, res) => {
    res.send([
        {id: 1, title: '첫 번째 게시글', content: '이것은 첫 번째 게시글의 내용입니다.'},
        {id: 2, title: '두 번째 게시글', content: '이것은 두 번째 게시글의 내용입니다.'},
        {id: 3, title: '세 번째 게시글', content: '이것은 세 번째 게시글의 내용입니다.'}
    ]);
})

app.listen(port, () => {
    console.log(`Example app listening on port ${port}`)
})
```

**3) pm2 설치**

Node 기반에서는 많은 경우 Project Manager 인 `pm2` 를 활용 합니다.   
실행 중인 node가 예기치 않게 종료되거나 다운되었을 때 자동으로 재시작 해주는 기능을 재공하며,          
pm2-logrotate 모듈을 사용하며 로그 로테이션 등의 기능을 사용 할 수 있습니다.

다음 명령어를 실행해 `pm2` 를 설치 합니다.

```shell
$ npm i -g pm2
```

**4) API 서버 실행**

앞서 생성한 API 서버(app.js) 파일을 실행 합니다.

```shell
$ pm2 start app.js
```

**5) 잘 동작하는 지 확인**

![img.png](images/ec2-apiserver/image%20(15).png)