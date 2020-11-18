---
layout: post
title:  "S3 정적호스팅 github actions 사용하여 자동 배포"
summary: github actions
author: coconut
date: 2020-11-18 11:30 AM
categories: [cloud]
tags: [github, s3, actions]
---
## 정적호스팅

[정적 호스팅 하는 방법](https://coconutstd.github.io/posts/s3-hosting/) 에서 Vue 앱을 웹팩으로 빌드하고 dist 폴더 아래의 파일을 aws s3 버킷에 업로드하여 호스팅했습니다. 오늘은 개발자의 컴퓨터에서 깃헙으로 푸쉬하면 자동으로 빌드를 수행하고 s3 버킷에 파일을 업로드 하는 작업을 자동화하겠습니다. 이를 구현하기 위해서, aws codepipeline, aws codebuild, travis-ci 등 다양한 도구가 있으나, 저는 관리 포인트를 최소화하기 위해 github actions 도구를 사용했습니다.

## github actions 란?

> # Automate your workflow from idea to production

github actions는 위와 같은 슬로건과 함께 소프트웨어 워크플로우를 자동화합니다. 워크플로우란 빌드, 테스트, 배포 와 같은 과정을 의미합니다. 간단히 깃헙 프로젝트에 .github/workflows/[파일명].yml 을 만들면 프로젝트에 대해 개발자가 정의한 과정을 수행합니다.



## github actions 사용하기

github repository와 s3 호스팅은 미리 준비되었다고 가정하고 간략히 설명하겠습니다.



1. IAM을 통해 GitHub actions를 위한 계정 생성

   ![image-20201118120031326](/Users/junui/Desktop/1.png)

    프로그래밍 방식 액세스를 선택합니다.

   ![image-20201118120134919](/Users/junui/Desktop/2.png)

   기존 정책 직접 연결에서 AmazonS3FullAccess로 권한을 설정해줍니다. 이는 github actions 측에서 aws s3의 업로드를 가능하게 해줍니다.

   ![image-20201118120517408](/Users/junui/Desktop/3.png)

   액세스 키와 비밀 액세스 키를 저장해줍니다.

2. 액세스 키, 시크릿 키 github에 등록

   ![image-20201118120623873](/Users/junui/Desktop/4.png)Settings > Secrets > New repository secret 에 위에서 저장한 키를 등록해주세요.

3. github actions 동작을 위한 yml 파일 생성

   ![image-20201118121301060](/Users/junui/Desktop/5.png)

   .github/workflows/[파일명].yml 경로로 생성하고 이 안에 스크립트를 정의합니다. 

   ```yml
   name: Main CI
   
   on:
     push:
       branches:
         - main
   
   jobs:
     build:
       runs-on: ubuntu-18.04
       steps:
         - name: git clone
           uses: actions/checkout@v2
   
         - name: npm install
           run: npm install
   
         - name: npm build
           run: npm run build
   
         - name: deploy
           env:
             AWS_ACCESS_KEY_ID: '${{ secrets.AWS_ACCESS_KEY_ID }}'
             AWS_SECRET_ACCESS_KEY: '${{ secrets.AWS_SECRET_ACCESS_KEY }}'
           run:
             aws s3 cp --recursive --region ap-northeast-2 dist s3://home.manual
   
   ```

   on: push: branches: - main <- main 브런치의 푸쉬 이벤트를 감지합니다.

   jobs: 

​	

## 참고

[github actions vue 배포](https://bin-e.tistory.com/44)



