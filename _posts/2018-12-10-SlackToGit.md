git의 hooking은 기본적으로 메일로 가게 되어 있어 실시간 적인 확인이 어렵습니다.
실시간으로 확인 할 수 있게 하기 위해 slack과 연결하여 사용하기로 했습니다.

app을 추가하는 방식이라 모두 다 app을 설치하면 되는거로 예상했지만 한명만 설치 및 설정을 하면 됩니다.

#### 1. slack에 연동되는 App을 설치합니다.

![app 설치](/images/SlackToGit/step01.png)

채널에서 설정을 누른 후 Add apps를 선택합니다.

#### 2. Slack의 App Directory에서 GitHub와 GitHub Notifications(Legacy)를 설치합니다.

![app Directory](/images/SlackToGit/step02.png)

- 선택 후 Install로 설치합니다.

#### 3. GitHub Notification (Legacy)에서 Add Configuration으로 설정을 추가합니다.

![](/images/SlackToGit/step03.png)

그러면 다음과 같은 설정화면이 나옵니다.

![](/images/SlackToGit/step04.png)

slack에서 Notification이 나올 channel을 설정한 후 Add GitHub Integration을 선택합니다.

![](/images/SlackToGit/step05.png)

그럼 중간 아래쯤에 Webhook URL이라는 부분이 나오는데 URL을 복사한 후 저장을 선택합니다.

#### 4. Git에서 Webhook을 원하는 Repository로 이동합니다.

- Setting을 선택하면 좌측에 Webhook라는 항목이 보입니다. 그것을 선택합니다.

![](/images/SlackToGit/step06.png)

- 위에서 복사한 URL을 Payload URL에 붙여주고 Contents type은 application/json으로 설정합니다.
- **Which events would you like to trigger this webhook?의 3가지 중 하나를 필히 선택합니다**
  - Just the push event. : 소스의 push만 hooking 합니다.
  - Send me everything : 모든 hooking을 알려줍니다
  - Let me select individual events. : 선택적 hooking을 합니다.

위와 같은 과정을 하면 hooking된 내용을 slack의 해당 channels에서 볼 수 있습니다.
