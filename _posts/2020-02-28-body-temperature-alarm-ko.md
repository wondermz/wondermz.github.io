---
title: "AWS 클라우드, 라즈베리파이, 온도센서를 활용한 체온 알람 서비스"
date: 2020-02-28 12:26:54 +0900
header:
  image: /assets/images/2020-02-28/doorman-ko.jpg
  og_image: /assets/images/2020-02-28/doorman-ko.jpg
---

요즘 신종 코로나19(COVID-19) 로 인하여 국내외가 어지럽습니다. 한국에서도 많은 수의 확진자가 발생하고 있으며, 공항, 병원 등을 비롯한 많은 곳에 열감지 카메라들이 설치되고 있습니다. 하지만 많은 곳에서 카메라 옆에 사람이 상주하여 카메라를 계속 모니터링 하고있습니다.

저는 IT 개발자로서 알고있는 지식으로 더 편하고, 빠르고, 정확하게 판단하고 알려주는 서비스를 만들고 싶다는 생각을 하게 되었습니다.

![doorman](/assets/images/2020-02-28/doorman.jpg)

## Thermal camera

더 화소수가 많고 성능이 좋은 카메라는 비싸고, 특히 중국에서 배송이 되야 하므로, 즉시 구할수 있는 8x8 의 해상도를 가진 열화상 카메라를 구매했습니다.

AMG8833 센서를 부착한 [Adafruit AMG8833 IR Thermal Camera Breakout](http://www.devicemart.co.kr/goods/view?no=12382843) 입니다.

![amg8833](/assets/images/2020-02-28/amg8833.jpg)

[python + pygame 으로 만든 샘플 코드 입니다.](https://learn.adafruit.com/adafruit-amg8833-8x8-thermal-camera-sensor/raspberry-pi-thermal-camera)

## Raspberry pi

처음에는 [AWS Deeplens](https://aws.amazon.com/ko/deeplens/) 가 고려되었으나, 열감지 카메라가 GPIO 를 통해 정보를 받으므로 다른 대안을 찾아야 했습니다.
그래서 책상 서랍에 있던 라즈베리파이를 선택 했습니다.

다행이도 라즈베리파이 케이스가 레고호환이어서 라즈베리 카메라와 열감지 카메라를 레고 거치대에 설치할 수 있었습니다.

![raspberrypi](/assets/images/2020-02-28/raspberrypi.jpg)

라즈베리파이에 python 으로 된 프로그램을 설치하고, [Amazon S3](https://aws.amazon.com/ko/s3/) Bucket 에 사진을 업로드 할수 있는 권한도 부여 했습니다.

자세한 코드는 [여기](https://github.com/nalbam/rpi-doorman)를 참고 하세요.

## Slack App

슬랙에 알림을 받거나, 슬랙에서 사용자 이름을 지정 하기 위하여 [설정법](https://github.com/nalbam/deeplens-doorman/blob/master/README-slack.md) 에 따라 Slack App 을 만들어 줬습니다.

![slack-04](/assets/images/2020-02-28/slack-04.png)

## Lambda Backend

Amazon S3 Bucket 에 사진이 업로드 되면 `Trigger` 에 의하여 [AWS Lambda Function](https://aws.amazon.com/ko/lambda/) 이 호출되야 합니다.
그리고 Lambda function 에서는 [Amazon Rekognition](https://aws.amazon.com/ko/rekognition/) 으로 안면인식을 수행하여 사람별로 [Amazon DynamoDB](https://aws.amazon.com/ko/dynamodb/) 에 저장 합니다.

이번에는 [Serverless framework](https://serverless.com/) 을 이용하여 개발 및 배포를 했습니다.

자세한 코드는 [여기](https://github.com/nalbam/deeplens-doorman-backend)를 참고 하세요.

## Amplify Frontend

DynamoDB 에 저장된 이름과 사진을 웹을 통해 서비스 합니다.
이 앱은 [AWS Amplify](https://aws.amazon.com/ko/amplify/) 를 이용하여 개발 및 배포를 했습니다.

Forntend 는 `Javascript` 와 `React` 를 사용 했습니다.
그리고 `Rest API` 를 사용하여 Backend 에서 만든 DynamoDB 를 조회 하였고, 이 역시 `AWS Lambda Function` 으로 생성 하였습니다.

인식은 하였으나 이름을 모르는 사람은 `Unknown` 으로 저장 하였고, 이름을 저장 하는 폼을 위해 [Amazon Cognito](https://aws.amazon.com/ko/cognito/) 를 사용해서 인증을 처리 했습니다. Amplify 를 통해 손 쉽게 로그인 및 가입 페이지를 직접 코딩하지 않고도 적용할 수 있었습니다.

![doorman-web](/assets/images/2020-02-28/doorman-web.jpg)

자세한 코드는 [여기](https://github.com/nalbam/doorman)를 참고 하세요.

## Architecture

![doorman-arch](/assets/images/2020-02-28/doorman-arch.jpg)

감사합니다.

![bar](/assets/images/bar.png)

![song](/assets/images/2020-02-28/nalbam.jpg)

### 유정열 매니저는...

Java 개발자에서 DevOps Engineer / SRE 로 전향하였으며, MEGAZONE CLOUD 에서 Solutions Architect 로 일하고 있다. AWS DeepRacer Championship 2019 결승진출을 하였고, 요즘도 DeepRacer 리그에 도전 중 이다.
