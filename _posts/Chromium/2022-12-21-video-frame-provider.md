---
title: "[Chromium 배우기] VideoFrameProvider 분석"
categories:
  - Chromium
tags:
  - Chromium
---

VideoFrameProvider 분석
==============

## VideoFrameProvider class architect
* VideoFrameProvider와 VideoFrameProvider::Client는 provider와 client 사이에 어떤 비디오 프레임이 교환되고 있는지 정의함
* MultiThread 호출 가능한 클래스

### VideoFrameProvider::Client
|함수명|설명|
|---|---|
|Start/StopRendering|Provider에 UpdateCurrentFrame 호출을 시작 혹은 중단을 알리는 noti|
|DidReceiveFrame|GetCurrentFrame 으로부터 새 데이터를 반환받았을 때 호출됨<br>Active video layer가 있다면 Redraw 를 요청|


### VideoFrameProvider
|함수명|설명|
|---|---|
|UpdateCurrentFrame|주기적으로 client에 의해 호출<br>업데이트할 새 프레임이 있을 때 true 반환|
|GetCurrentFrame|pdateCurrentFrame 호출 시 업데이트된 현재 프레임 반환<br>반환하는 프레임이 반드시 그려진다는 보장 없음|
|PutCurrentFrame|DidReceiveFrame 혹은 UpdateCurrentFrame이 true 일 때 현재 프레임을 그리고자 한다면 호출|


### VideoFrameController
* Interface

### VideoFrameProviderClientImpl
* VideoFrameProvider::Client 를 상속해 구현한 클래스

#### AcquireLockAndCurrentFrame()
* GetCurrentFrame 을 호출

|함수명|설명|
|---|---|
|OnBeginFrame|VideoFrameController 함수의 오버라이딩<br>BeginFrameArgs 로부터 전달받은 frame_time 과 interval 정보를 기반으로 VideoFrameProvider에 UpdateCurrentFrame 를 호출<br>UpdateCurrentFrame이 false를 반환할 때 함수 종료<br>DidReceiveFrame 을 호출|
|DidDrawFrame|VideoFrameController 함수의 오버라이딩<br>클래스 멤버 need put current frame 이 true 일 때 VideoFrameProvider 에 PutCurrentFrame을 호출|


### VideoFrameCompositor
* 미디어와 cc 레이어 사이에서 비디오 렌더링을 위해 동작한다.
* media::VideoRenderer와 cc::VideoFrameProvider::Client 가 이 클래스에서 만난다.
* cc::VideoFrameProvider 를 상속
* media::VideoRendererSink 를 상속


### VideoRendererSink::RenderCallback
* 렌더링되는 비디오 프레임을 위한 interface를 제공하는 클래스

|함수명|설명|
|---|---|
|Render|인자로 입력되는 deadline_min과 dealine_max interval에 그려져야 하는 비디오 프레임 제공|
|OnFrameDropped|Render() 에 의해 반환되었지만 그려지지 않은 프레임이 있을 때 호출됨|


### VideoFrameSubmitter
미디어 스택과 브라우저 렌더러 사이의 통신을 위한 single thread 클래스
cc::VideoFrameProvider::Client를 상속
VideoFrameSubmitter(client) <-> VideoFrameCompositor(provider) 모양이 됨

|함수명|설명|
|---|---|
|OnBeginFrame|CompositorFrame 을 생성하기 위한 notification|



## VideoFrameProvider class
![class_diagram](/assets/img/chromium/img/video_frame_provider_class.png)

## VideoFrameProvider sequences
![sequence_diagram](/assets/img/chromium/img/video_frame_provider_sequence.png)


## Simple diagram
![overall_diagram](/assets/img/chromium/img/video_frame_provider_overall.png)
