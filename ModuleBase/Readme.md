# ModuleBase

모든 모듈의 근간인 클래스로, **ISPRFCommonBase**를 구현한 **ScriptableObject**인 **SPRFScriptableObject**를 상속받아 구현된 클래스입니다.

모듈은 State Machine으로 관리되며, 아래와 같은 상태를 갖습니다.

- Created
- Inactive
- Active
- Terminated

그리고 아래의 상태 명령을 갖습니다.

- Initialize (Created -> Inactive)
- None (Inactive -> Active; Active -> Active)
- Terminate (Inactive -> Terminated; Active -> Terminated)

각각의 라이프 사이클에 포함된 Action들은 Unity 라이프 사이클에 맞물려 처리됩니다.

- Inactive 상태 진입 직후, `sprfInitialize` 호출
- Active 상태 진입 직후, `sprfUpdate` 호출 (이후, None 명령으로 Active -> Active 실행으로 Update Event 호출)
- Terminated 상태 진입 직후, `sprfTerminate` 호출

상태 전이 처리는 아래의 함수를 통해 진행됩니다.

```csharp

internal void Process(EModuleCommand command = EModuleCommand.None)
{
    StateTransition transition = new StateTransition(CurrentState, command);
    EModuleState nextState;

    // 등록된 상태들 중, 입력된 상태 전이가 가능한지 확인
    if (!transitions.TryGetValue(transition, out nextState))
        throw new Exception("Invalid transition: " + CurrentState + " -> " + command);

    if (lifeCycleActions.ContainsKey(nextState))
    {
        lifeCycleActions[nextState].Invoke();
    }

    CurrentState = nextState;
}

```

## SPRFManager

**Module** 관리를 주관하는 Manager로, Scene내 유일한 형태로 존재합니다. **Editor** 소스에서, *Init* 버튼을 통해, Reflection으로 Module 인스턴스를 수집합니다.

**MonoBehaviour**를 상속받은 일반 컴포넌트이기에, 내부에 라이프 사이클 함수가 구현되어 있습니다.
따라서, 수집된 모듈들의 상태 전이 함수를 실행하여, 그들의 Action들을 실행합니다.

```csharp

private void Update()
{
    foreach (var module in modules)
        module?.Process();
}

private void OnDestroy()
{
    foreach (var module in modules)
        module?.Process(EModuleCommand.Terminate);
    
    instance = null;
}

```