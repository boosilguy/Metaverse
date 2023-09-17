# Minimap

기본적으로 Navigation의 형태에 맞물려, 제공되는 기본 미니맵입니다. **NavigationBehaviour**를 상속받은 **MinimapViewerBehaviour**의 Static Method인 **Create**로 인스턴스를 생성할 수 있습니다. 생성된 인스턴스를 **Navigation Builder**에 엮어내면, 쉽게 미니맵을 구현할 수 있습니다.

SDK에서는 MinimapViewerBehaviour를 상속받은 **DefaultMinimapViewer**를 지원합니다. Editor에서 쉽게 미니맵 정보를 제어하도록 ScriptableObject 형태의 **DefaultMinimapViewerConfig**를 제공하며, 여기에는 아래와 같은 속성들이 있습니다.

```csharp

[CreateAssetMenu(fileName = "DefaultMinimapViewerConfig", menuName = ".../DefaultMinimapViewerConfig")]
/// <summary>
/// DefaultMinimapViewer를 구성하는 Configuration입니다
/// </summary>
public class DefaultMinimapViewerConfig : ScriptableObject
{
    [SerializeField] private RenderTexture minimapTexture;
    [SerializeField] private float initMinimapCameraSize = 2f;
    [SerializeField] private float cameraHeight = 5f;
    [SerializeField] private bool perspective = false;
    [SerializeField] float gradient = 60f;
    [SerializeField] private Material[] pathMaterials;
    [SerializeField] private MapRendererConfig minimapRendererConfig;
    [SerializeField] private GameObject currentPointIconPrefab = null;
    [SerializeField] private GameObject destinationIconPrefab = null;
    [SerializeField] private GameObject waypointIconPrefab = null;
    [SerializeField] private bool stickFloor = false;
    [SerializeField] private bool renderOnlyCurrent = true;
    [SerializeField] private bool renderMap = true;
    [SerializeField] private bool nodeLinkVisable = true;

    /// <summary>
    ///미니맵 카메라의 RenderTexture를 반환합니다
    /// </summary>
    public RenderTexture MinimapTexture => minimapTexture;

    /// <summary>
    /// 미니맵 카메라 사이즈를 반환합니다
    /// </summary>
    public float InitMinimapCameraSize => initMinimapCameraSize;

    /// <summary>
    /// 미니맵 카메라 높이를 반환합니다
    /// </summary>
    public float CameraHeight => cameraHeight;

    /// <summary>
    /// 기본 미니맵 카메라의 Perspective 설정 여부를 반환합니다
    /// </summary>
    public bool Perspective => perspective;

    /// <summary>
    /// brief 미니맵 카메라의 기울기 정도를 반환합니다
    /// </summary>
    public float Gradient => gradient;

    /// <summary>
    /// 미니맵 Path의 Material들을 반환합니다
    /// </summary>
    public Material[] PathMaterials => pathMaterials;

    /// <summary>
    /// MinimapRendererConfig를 반환합니다
    /// </summary>
    public MapRendererConfig MinimapRendererConfig => minimapRendererConfig;

    /// <summary>
    /// 현재 위치를 나타내는 GameObject를 반환합니다
    /// </summary>
    public GameObject CurrentPointIconPrefab => currentPointIconPrefab;

    /// <summary>
    /// 목적지 위치를 나타내는 GameObject를 반환합니다
    /// </summary>
    public GameObject DestinationIconPrefab => destinationIconPrefab;

    /// <summary>
    /// 경유지 위치를 나타내는 GameObject를 반환합니다
    /// </summary>
    public GameObject WaypointIconPrefab => waypointIconPrefab;

    /// <summary>
    /// 현재 위치를 나타내는 GameObject의 Floor 밀착 여부를 반환합니다
    /// </summary>
    public bool StickFloor => stickFloor;

    /// <summary>
    /// 현재 Spatial만 렌더링하는 속성의 여부를 반환합니다
    /// </summary>
    public bool RenderOnlyCurrent => renderOnlyCurrent;

    /// <summary>
    /// 미니맵 렌더링 여부를 반환합니다.
    /// </summary>
    public bool RenderMap => renderMap;

    /// <summary>
    /// Node, Link를 시각적으로 표시합니다
    /// </summary>
    public bool NodeLinkVisable => nodeLinkVisable;
}

```

## Visualization (DefaultMinimapViewer)

**StartView** 함수를 호출하여, Minimap을 실행할 수 있습니다.
