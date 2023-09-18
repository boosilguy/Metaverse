# Chatbot

다양한 **챗봇**, **TTS**, **STT** 서비스 (e.g. CLOVA, KAKAO)를 사용할 수 있는 상황을 고려하여, 설계되었습니다. 물론, 네이버랩스와의 협업으로 네이버 클라우드 팀으로부터 무료 크레딧을 받아 사용하게 되었습니다. 따라서, CLOVA 서비스만 구현되어 사용되고 있습니다.

## 구성

서비스 정책 상, Navigation에 음성인식이 포함되어있는 구조가 되어야 합니다 (e.g. 우리 사업의 네비게이션 서비스는 사회적 약자도 지원합니다). 그에 따라, ChatbotModule은 네비게이션에 쉽게 엮을 수 있도록 지원되고 있습니다 (물론, 길찾기 음성 안내만 한정적으로 지원하며, Navigation의 **Builder**로 쉽게 구현할 수 있습니다).

## STT

서비스는 STT를 통해, 음성 인식을 통한 명령 수행 기능을 구현할 수 있습니다. Module에 직접 이벤트 핸들러를 추가하여, 음성 인식 State에 대한 이벤트를 전달 받을 수 있습니다.

### ISpeechEventHandler

```csharp

/// <summary>
/// SpeechModule에 대한 이벤트 핸들러 인터페이스입니다.
/// </summary>
public interface ISpeechEventHandler
{
    void OnReady();                         // Recognition 준비
    void OnStayForPermission();             // Permission 승낙 대기
    void OnDeniedPermission();              // Permission 거부
    void OnGrantedPermission();             // Permission 승인
    void OnRecordStart();                   // 음성 녹음 시작
    void OnRecordEnd();                     // 음성 녹음 종료
    void OnHypothesize();                   // Hypothesis 시작
    void OnEnd(string result);              // 종료
    void OnStateChanged(SpeechState state);
    void OnError(Exception e);
}

```

STT 실행은 아래 형태로 수행됩니다.

```csharp

/// <summary>
/// STT를 실행합니다
/// </summary>
/// <returns>Task</returns>
public async UniTask RunSTT()
{
    try
    {
        if (!sttInitialization)
            SPRFErrorListener.Check(new SPRFFailuretError("STT가 초기화되지 않았습니다."));

        State = SpeechState.Permission;     // Check Permission
        speechEventHandlers?.ForEach(h => h.OnStayForPermission());
        microphonePermission = CheckMicPermission();

        if (microphonePermission)          // No permission => shutdown
        {
            State = SpeechState.Recording;      // Start Record
            speechEventHandlers?.ForEach(h => h.OnRecordStart());
            await RecordVoice();

            State = SpeechState.EndRecording;   // End Record
            speechEventHandlers?.ForEach(h => h.OnRecordEnd());
            EndRecordVoice();

            State = SpeechState.Hypothesis;     // Start Hypothesis
            speechEventHandlers?.ForEach(h => h.OnHypothesize());
            string result = await Hypothesize();

            State = SpeechState.End;            // End
            speechEventHandlers?.ForEach(h => h.OnEnd(result));
        }
        
        State = SpeechState.Ready;          // Reready
        speechEventHandlers?.ForEach(h => h.OnReady());
    }
    catch (Exception e)
    {
        SPRFDebugger.Error(e);
        speechEventHandlers?.ForEach(h => h.OnError(e));

        State = SpeechState.Ready;
        speechEventHandlers?.ForEach(h => h.OnReady());
    }
}

```

## TTS

TTS는 Navigation의 음성 길 안내를 기본적으로 지원합니다. 이는, Navigation의 **Builder**로 쉽게 구현이 가능하며, 이와 별개로 독립적인 TTS 기능도 지원합니다.

```csharp

/// <summary>
/// TTS를 실행합니다
/// </summary>
/// <param name="query">쿼리</param>
/// <param name="cancellationToken">토큰</param>
/// <returns></returns>
public async UniTask RunTTS(string query, CancellationToken cancellationToken = default(CancellationToken))
{
    try
    {
        if (!ttsInitialization)
            SPRFErrorListener.Check(new SPRFFailuretError("TTS가 초기화되지 않았습니다."));

        ttsSpeaker.clip = await ConfigureAudio(query);
        
        if (autoPlay)
        {
            ttsSpeaker.Play();
            try
            {
                await UniTask.WaitUntil(() => ttsSpeaker.isPlaying == false, cancellationToken: cancellationToken);
                SPRFDebugger.Info("[SpeechModule] 음성 출력 종료");
            }
            catch (OperationCanceledException tce)
            {
                ttsSpeaker.Stop();
                SPRFDebugger.Info("[SpeechModule] 음성 출력 강제 종료");
            }
        }
    }
    catch (Exception e)
    {
        SPRFDebugger.Error(e);
    }
}

```

### Navigation + TTS

```csharp

private void Awake()
{
    Screen.sleepTimeout = SleepTimeout.NeverSleep;

    //네비게이션 설정
    Configuration conf = Configuration.Builder.Create()
        .AddNavigationEventHandler(this)
        .AddNavigationEventHandler(NavigationGuideRenderer.Instance)
        .AddNavigationEventHandler(NavigationExtendedMinimap.Instance)
        .AddNavigationEventHandler(MilestoneContainer.Create(tts.Language.Kor, navigationSpeaker))
        .UseMinimap(DefaultMinimapViewer.Create(defaultMinimapViewerConfig))
        .Build();

    Navigation = new Navigation(conf);

}

```

`MilestoneContainer.Create()`에 언어 속성과, AudioSource를 파라미터로 넘겨, 네비게이션 시 음성 안내를 제공받을 수 있습니다.