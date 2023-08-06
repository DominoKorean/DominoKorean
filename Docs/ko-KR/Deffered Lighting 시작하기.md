# Deferred Lighting으로 시작하기

**이 문서에서**
> [Deferred Lighting 파이프라인 사용하기](#deferred-lighting-파이프라인-사용하기)  
> [PBR 리소스팩](#pbr-리소스팩)  
> [설정에서 Deferred Technical Preview 활성화하기](#설정에서-deferred-기술-미리보기-활성화하기)  
> [Deferred Lighting JSONs](#deferred-lighting-jsons)  
> [대기 (Atmospherics) JSON 스키마](#대기-atmospherics-json-스키마)  
> [키 프레임 JSON 구문](#키-프레임-json-구문)  
> [JSON Files 예시](#json-files-예시)  

마인크래프트는 Deferred Lighting 및 Ray Tracing에 사용할 수 있는 물리 기반 렌더링(PBR) 파이프라인을 지원합니다. 이는 표면 세부 사항과 장면 조명 요소를 정확하게 모델링하여 주간 시간대에 일관성 있는 조명 모델을 구현하는 것을 의미합니다. PBR과 새로운 Deferred Lighting 파이프라인을 활용함으로써 전체 장면의 분위기와 조명 조건에 대해 새로운 창의적인 제어력을 얻게 됩니다.

예를 들어, 태양과 달의 강도와 색상을 제어하여 장면에 기여하는 방식을 조정할 수 있으며(직접 조명, 블룸 활성화, 노출 감도, 그림자 각도 등), 새로운 세계의 대기를 설명하는 데 사용되는 새로운 속성은 새로운 수준의 하늘 제어를 가능하게 합니다! 이 모든 사용자 정의 포인트는 새로운 "키 프레임" 구문과 결합되어 게임 내 낮-밤 주기 동안 조명의 모든 세부 사항을 변경할 수 있게 해줍니다.



<div class="rounded" style='padding:0.1em; background-color:#E9D8FD; color:#69337A; border-radius: 10px;'>
<span>
<p style='margin-top:1em; margin-left:1em;'>
<b> 주의</b></p>
<p style='margin-left:1em;'>
Deferred Lighting 기능은 실험적입니다. 모든 실험과 마찬가지로, Minecraft 버전에서는 중요한 사전 경고 없이 추가, 제거 및 기능 변경이 발생할 수 있습니다. 변경 사항에 대한 자세한 내용은 Minecraft 변경 로그를 확인하세요.<br><br>
실험적 기능에 대해 더 자세히 알아보려면 [Minecraft: Bedrock Edition의 실험적 기능 문서](https://learn.microsoft.com/en-us/minecraft/creator/documents/experimentalfeaturestoggle)를 방문하세요.
</p>
<p style='margin-bottom:1em; margin-right:1em; text-align:right; font-family:Georgia'>
</p></span>
</div>


<br/>

새로 추가된 조명 기능은 선택 사항으로, 만약 리소스팩에서 어떤 매개 변수도 제공되지 않는 경우 적절한 기본값이 사용됩니다. 이러한 속성은 또한 RTX 리소스팩에서 찾을 수 있는 기존 텍스처 세트 기능을 보완하고 확장하기 위해 설계되었습니다 (텍스처 세트는 이제 RTX 전용 리소스팩으로 제한되지 않습니다).

<div class="rounded" style='padding:0.1em; background-color:#E9D8FD; color:#69337A; border-radius: 10px;'>
<span>
<p style='margin-top:1em; margin-left:1em;'>
<b> 주의</b></p>
<p style='margin-left:1em;'>
새로운 기능은 RTX 파이프라인과 호환되지 않습니다.
</p>
<p style='margin-bottom:1em; margin-right:1em; text-align:right; font-family:Georgia'>
</p></span>
</div>

<br/>

# Deferred Lighting 파이프라인 사용하기

Deferred Lighting 파이프라인을 사용하려면 PBR(PBR을 지원하는) 기능이 활성화된 리소스팩을 제공하고 "크리에이터를 위한 렌더 드래곤 기능" 실험이 활성화된 세계에 참여해야 합니다. RTX를 위해 만들어진 리소스팩은 새로운 파이프라인과 함께 작동할 수 있지만, 파이프라인의 모든 기능을 최대한 활용하려면 다음 섹션에서 설명하는 대로 몇 가지 JSON 파일을 추가하는 것이 좋습니다.

<br/>

# PBR 리소스팩

Deferred Lighting 파이프라인은 RTX에서 사용하는 PBR 텍스처 세트에 대해 동일한 규칙을 사용합니다. 만약 물리 기반 렌더링이나 텍스처 세트 구축에 익숙하지 않다면, 문서 웹사이트에서 [물리 기반 렌더링 소개](https://learn.microsoft.com/en-us/minecraft/creator/documents/rtxpbrintro)와 [텍스처 세트 소개](https://learn.microsoft.com/en-us/minecraft/creator/reference/content/texturesetsreference/texturesetsconcepts/texturesetsintroduction)에서 개요를 확인할 수 있습니다.

높이 맵 값에 대해서는, 새로운 파이프라인은 높이 맵과 노말 입력을 모두 지원합니다. 그러나 Deferred Lighting 모드에서는 블록뿐만 아니라 엔티티와 몹에 대해서도 PBR 텍스처를 설정할 수 있게 되었습니다. 이들은 정확히 동일한 텍스처 세트 규칙을 따르며, 리소스팩 내의 textures/entity 폴더에 비슷하게 추가할 수 있습니다.

리소스팩이 Deferred Lighting을 지원하는지 게임 클라이언트에게 전달하는 핵심적인 요소는 팩의 manifest.json 파일의 capabilities 배열에 특정 문자열이 있는지 여부입니다. RTX에 필요한 에셋들이 Deferred Lighting에 필요한 에셋들의 하위 집합이기 때문에 "raytraced" 능력이 있는 팩은 Deferred Lighting도 활성화할 수 있습니다.

그러나, Deferred/PBR Lighting 팩은 "raytraced" 팩과 달리 RTX만을 지원하는 장치에 제한되지 않으므로 새로운 능력 문자열을 사용하는 것이 추천됩니다.

새로운 능력 문자열은 "pbr" 입니다:

```jsonc
{ 
  "format_version": 1, 
  "header": { 
    ... 
  }, 
  "modules": [ 
    ... 
  ], 
  "capabilities": [ 
    "pbr" 
  ] 
}
```

<br/>

# 설정에서 Deferred 기술 미리보기 활성화하기

호환 가능한 미리보기 또는 베타 버전을 사용 중이라면, "크리에이터를 위한 렌더 드래곤 기능"이라는 새로운 실험 토글을 찾을 수 있습니다. 이 실험을 켜고, 사용하는 PBR 활성화된 리소스팩이 해당 세계에서 활성화되도록 하세요. 미리보기에서 세계를 공유하기 위해 Realm을 호스팅하는 경우, 플레이어들이 리소스 팩을 다운로드하도록 요구하는 것을 잊지 마세요.

![](https://learn.microsoft.com/en-us/minecraft/creator/documents/media/deferredlighting/experiments_renderdragon_on.png)

세계에 들어가면 "비디오" 설정 메뉴에서 새로운 옵션을 볼 수 있습니다. "그래픽 모드" 아래에서 UI 요소가 드롭다운 선택으로 변경되며, 이제 "Deferred Technical Preview" 옵션을 볼 수 있습니다.

이 옵션을 켜면 새로운 Deferred Lighting 파이프라인으로 세계가 렌더링되게 됩니다!

![](https://learn.microsoft.com/en-us/minecraft/creator/documents/media/deferredlighting/settings_video_graphicsmode_deferredtechnicalpreview.png)

<br/>

## 하드웨어 요구사항

Deferred Lighting 파이프라인을 실행하려면 적절한 하드웨어와 미리보기가 가능한 플랫폼이 필요합니다. 레이 트레이싱을 지원하는 비디오 카드는 필요하지 않으며, Windows에서 대부분의 현대적인 GPU는 Deferred 파이프라인을 지원할 것으로 예상됩니다. 더 많은 장치가 지원될 예정입니다.

Android의 경우, 현재 GLES 3 장치이며 Android OS 9 이상을 실행하는 기기만 파이프라인을 실행할 수 있습니다.

<br/>

# Deferred Lighting JSONs

텍스처 세트는 개별 블록이나 몹의 표면 속성을 정의하는 데 적합하지만, 더 전역적인 조명 속성을 위해서는 JSON 파일이 렌더러를 데이터로 구동하는 간단한 방법을 제공합니다. 여기서 설명하는 JSON 파일은 주어진 리소스팩의 루트 디렉토리 내에 "lighting" 디렉토리 안에 위치해야 합니다.

![](https://learn.microsoft.com/en-us/minecraft/creator/documents/media/deferredlighting/rp_file_structure.png)

<br/>

## Global Lighting JSON 스키마

파일 위치: lighting/global.json

```jsonc
{
  version "format_version",
  object "directional_lights"
    {
      object "sun"
      {
          float "illuminance" : optkeyframe, // 태양의 밝기; 럭스 (lx) 단위로 측정
          color "color" : optkeyframe // 태양이 직접적인 표면 조명에 기여하는 RGB 색상; RGB 배열 또는 HEX 문자열을 지원
      },
      object "moon"
      {
          float "illuminance" : optkeyframe,  // 달의 밝기; 럭스 (lx) 단위로 측정
          color "color" : optkeyframe // 달이 직접적인 표면 조명에 기여하는 RGB 색상; RGB 배열 또는 HEX 문자열을 지원
      }
        float "orbital_offset_degrees" : optkeyframe // 태양과 달의 표준 궤도 축으로부터의 회전적 오프셋; 각도 단위로 측정
    }
    object "pbr"
    {
      object "blocks"
      {
        color "global_metalness_emissive_roughness" // 블록에 대한 기본 MER(Metalness, Emissive, Roughness) 값을 설정하지 않은 경우에 사용되는 기본값; RGB 배열 또는 HEX 문자열을 지원
      },
      object "actors"
      {
        color "global_metalness_emissive_roughness" // 액터 또는 몹에 대한 기본 MER(Metalness, Emissive, Roughness) 값을 설정하지 않은 경우에 사용되는 기본값; RGB 배열 또는 HEX 문자열을 지원
      }
    }
 }
```
<br/>

# Directional Lights

"directional_lights" 객체는 태양과 달의 속성을 구성할 수 있는 곳입니다. 이러한 속성은 각각의 빛이 표면에 어떤 세기로 기여하는지, 어떤 색상을 표면에 조명하는지, 그리고 그들이 그림자를 드리우는 각도 등에 영향을 미칩니다. 이것은 또한 대기 산란 계산을 통해 하늘의 색상에 큰 영향을 미칠 것입니다 (자세한 내용은 lighting/atmospherics.json 파일을 참조하세요).

태양과 달은 하늘에서 항상 서로 반대편에 위치해 있다고 가정됩니다. 두 천체가 동시에 하늘에 보이는 경우, 둘 다 장면에 반대 방향으로 빛을 기여할 것입니다.

"실제 세계" 값에 해당하는 조명 값들은 이 파이프라인에서 적절하게 사용될 수 있습니다. 예를 들어, 맑은 날의 정오에는 태양이 10만 럭스 이상을 측정하며, 달은 < 1 럭스만을 측정합니다. 톤 매핑과 자동 노출 기능을 통해 광도의 극적인 차이를 적절하게 재조정할 것입니다.

색상 값은 0-255 범위의 3 또는 4개의 숫자 값의 배열이거나 6 또는 8 자리의 16진수 문자열로 설명될 수 있습니다. 이러한 값들은 각각 개별적으로 키 프레임 지정이 가능합니다 (자세한 내용은 "Key Frame JSON Syntax" 섹션을 참조하세요).

<br/>

# PBR Uniforms

"pbr" 객체는 큰 텍스처 세트 기능을 보완하기 위해 사용되며, 특정 블록이나 액터에 대한 텍스처 세트 세부 정보를 제공하지 않을 때 기본 값 또는 대체 값으로 작동합니다. 예를 들어, 돼지와 크리퍼에 대한 텍스처 세트를 제공하지만 다른 액터에 대해서는 제공하지 않으면, 게임에서 소가 렌더링될 때 global.json에서 정의된 "global_metalness_emissive_roughness" 값이 소의 전체 표면에 균일하게 적용될 것입니다. 이를 통해 모든 게임 오브젝트에 대한 텍스처를 처음부터 만들지 않고도 빠르게 일반적인 예술 방향을 제공하고, 필요에 따라 블록/액터에 더 많은 세부 정보를 점진적으로 추가할 수 있습니다.

값은 0-1 범위의 3개의 숫자 값으로 설명하거나, 3자리 16진수 문자열로 설명할 수 있습니다.

<br/>

# 대기 (Atmospherics) JSON 스키마

파일 위치: lighting/atmospherics.json

```jsonc
{
  object "horizon_blend_stops" // 대기가 나눠지는 방법
  {
    float "min" : optkeyframe, // 최소 지평선 높이
    float "start" : optkeyframe, // 지평선과 수직선이 교차하는 높이에서 천정 영역이 우세해지는 지점
    float "mie_start" : optkeyframe, // 지평선과 수직선이 교차하는 높이에서 미 산란이 시작되는 지점
    float "max" : optkeyframe // 최대 지평선 높이
  }
  float "rayleigh_strength" : optkeyframe, // 대기의 레일리 산란의 세기
  float "sun_mie_strength" : optkeyframe, // 태양의 Mie 산란의 세기
  float "moon_mie_strength" : optkeyframe, // 달의 Mie 산란의 세기
  float "sun_glare_shape" : optkeyframe, // 태양의 Mie 산란 로브의 모양
  color "sky_zenith_color" : optkeyframe, // 대기 천정 영역의 RGB 색상; RGB 배열 또는 HEX 문자열을 지원
  color "sky_horizon_color" : optkeyframe // 대기 지평선 영역의 RGB 색상; RGB 배열 또는 HEX 문자열을 지원
}
```
위의 값들은 각각 개별적으로 키 프레임 지정이 가능합니다 (자세한 내용은 "Key Frame JSON Syntax" 섹션을 참조하세요).

"lighting/global.json"에서 정의된 태양과 달 방향성 조명에 대한 색상 또한 대기의 계산에 사용되며, 따라서 최종적인 하늘의 색상에 상당한 영향을 미칩니다. 특히 Rayleigh 산란과 Mie 산란의 경우 이러한 영향이 더욱 두드러집니다.

<br/>

# 지평선 블렌드 지점 (Horizon Blend Stops)

"horizon_blend_stops" 객체에는 대기를 다양한 영역으로 나누는 데 사용되는 4개의 값이 포함되어 있습니다. 이 값들은 모두 0-1 범위 내에 있습니다. 다음 다이어그램은 이러한 값들을 이해하는 데 도움이 될 수 있습니다.

![](https://learn.microsoft.com/en-us/minecraft/creator/documents/media/deferredlighting/horizon_blend_stops.jpg)

(1) "min"과 "max"에 의해 결정되는 주요 지평선은 하늘을 두 가지 일반적인 영역으로 나눕니다: 지평선 아래의 모든 것과 지평선 위의 모든 것입니다. 이 분할 라인은 다음과 같이 계산됩니다:

**지평선 = min - max**

지평선 값이 0이면 지평선이 플레이어/관찰자의 수준과 동일하게 설정됩니다. 양수인 지평선은 평면을 더 높게 설정하여 천정을 더 많이 가릴 것이고, 음수인 지평선은 라인을 더 낮게 설정하여 천정을 더 많이 드러낼 것입니다.

(2) "start"는 "sky_horizon_color"와 "sky_zenith_color" 사이의 전환이 지평선 라인 (1)과 상대적으로 어느 고도 범위에서 발생하는지를 제어합니다. 더 큰 값은 블렌드를 더 넓게 퍼뜨리고, 더 작은 값은 더 조밀하게 퍼뜨릴 것입니다. 이 값은 다음 범위에 유지되어야 합니다:

**0 < start ≤ 1**

(3) "mie_start"는 "sun_glare_shape"와 함께 미 산란이 지평선 라인 (1)과 상대적으로 어느 고도 범위에서 발생하는지를 제어합니다. 더 큰 값은 Mie 산란 로브를 더 두드러지게 만들고, 더 작은 값은 구분되지 않을 정도로 더 사라지게 만들 것입니다. 이 값은 다음 범위에 유지되어야 합니다:

**0 < mie start ≤ 1**

<br/>

## Rayleigh 강도

"rayleigh_strength" 매개변수는 상층 대기에서 발견되는 Rayleigh 산란 효과의 강도를 조절합니다. 이 현상은 낮에는 파란색을, 일몰 시간에는 보라색/빨간색을 만들어냅니다. 더 큰 값은 효과를 강하게 만들며, 이로 인해 "sky_zenith_color"의 우세성이 증가합니다. 더 작은 값은 효과를 약하게 만들며, 값이 0인 경우 Rayleigh 산란을 완전히 제거하여 순수한 검정색과 혼합됩니다. 이 매개변수는 이론적으로 무한대로 갈 수 있지만, 이 값에 적절한 범위는 다음과 같습니다: 0≤rayleigh strength≤10

<br/>

## 태양과 달의 Mie 강도

"sun_mie_strength"와 "moon_mie_strength" 매개변수는 태양 또는 달에 대한 Mie 산란 효과의 강도를 조절할 수 있게 합니다. 이 현상은 대기 중의 물증기, 꽃가루 또는 연기와 같은 큰 입자들로 인해 대기의 색상이 달라지게 만듭니다. 더 큰 값은 효과를 강화하며, 값이 0인 경우 Mie 산란을 완전히 제거합니다. 이 값들은 다음 범위 내에 유지되어야 합니다: 0≤strength≤1

<br/>

## Sun Glare Shape

"sun_glare_shape" 매개변수는 Mie 산란 로브의 모양을 조절하는 데 사용됩니다. 더 작은 값은 로브를 더 넓게 퍼뜨리고, 더 큰 값은 로브를 점점 더 조밀하게 만듭니다. 이 매개변수는 이론적으로 무한대로 갈 수 있지만, 이 값에 적절한 범위는 다음과 같습니다:

**0 < sun glare shape ≤ 50**

<br/>

## Sky Zenith 그리고 Horizon Colors

"sky_zenith_color" 매개변수는 대기의 천정 영역의 색상을 정의하며, "sky_horizon_color" 매개변수는 지평선 라인 아래 영역의 색상을 정의합니다. 이 색상 값들은 0-255 범위의 3 또는 4개의 숫자 값의 배열이거나 6 또는 8 자리의 16진수 문자열로 설명될 수 있습니다.

<br/>

# 키 프레임 JSON 구문

가끔은 세계를 더 동적으로 만들기 위해 특정 조명 매개변수를 시간에 따라 변경하고 싶을 수 있습니다. 이를 위해 "키 프레임" 구문을 활용할 수 있으며, 이 구문은 "optkeyframe"으로 표시된 조명 JSON 스키마의 값들에 대체하여 사용될 수 있습니다. 작동 방식은 다음과 같습니다. 다음은 lighting/global.json 파일의 예제입니다.

```jsonc
{
    "format_version": [1, 0, 0], 
    "directional_lights": {
      "sun": {
        "illuminance": 100000,
        "color": [ 255.0, 255.0, 255.0, 255.0 ]
      },
      "moon": { 
        "illuminance": 0.27,
        "color": [ 255.0, 255.0, 255.0, 255.0 ]
      }, 
    "orbital_offset_degrees": 0.0
    }, 
    "pbr": { 
      "blocks": { 
        "global_metalness_emissive_roughness": [0.0, 0.0, 1.0]  
      },
    }, 
    "actors": { 
        "global_metalness_emissive_roughness": [0.0, 0.0, 1.0]  
    } 
}
```

해당 예제에서 태양의 illuminance 매개변수가 100,000 lux로 정오에는 잘 보이지만, 새벽과 저녁에는 너무 밝습니다. 이를 해결하기 위해, 우리는 태양의 illuminance 매개변수에 키 프레임을 사용할 수 있습니다.

키 프레임은 간단히 수치 쌍의 모음입니다. 이러한 쌍은 "키-값" 쌍이라고 하며, "키"는 (0-1) 사이의 숫자로 게임의 특정 시간을 나타내며, "값"은 키 프레임이 적용되는 매개변수를 나타냅니다.

태양 illuminance의 예에서 값은 "float" 타입이 됩니다. 지원되는 조명 매개변수에 대해 단일 값 대신 키 프레임을 제공하면 엔진은 게임 내 시간에 따라 이러한 키 프레임 사이를 선형으로 보간합니다.

이러한 모든 내용을 고려하여 lighting/global.json을 약간 수정하여 태양의 illuminance를 시간에 따라 변경할 수 있습니다.

```jsonc
{
    "format_version": [1, 0, 0], 
    "directional_lights": {
        "sun": {
            "illuminance": {
              "0.0": 1.0,
              "0.25": 400.0, // 일출
              "0.35": 39000.0,
              "0.5": 100000.0,  // 정오
              "0.65": 39000.0,
              "0.75": 400.0, // 일몰
              "1.0": 1.0
            },
            "color": [ 255.0, 255.0, 255.0, 255.0 ]
        },
        "moon": { 
            "illuminance": 0.27,
            "color": [ 255.0, 255.0, 255.0, 255.0 ]
        }, 
        "orbital_offset_degrees": 0.0
    }, 
    "pbr": { 
        "blocks": { 
            "global_metalness_emissive_roughness": [0.0, 0.0, 1.0]  
        }, 
        "actors": { 
            "global_metalness_emissive_roughness": [0.0, 0.0, 1.0]  
        } 
    } 
}
```

태양의 색상 또는 달의 illuminance 또는 대기의 기타 매개변수들과 같은 것들도 비슷하게 조정할 수 있습니다! 자신의 세계에 맞는 적절한 값을 찾기 위해 실험해보세요. 키 프레임은 현재 float와 색상 (RGB 및 16진수)을 지원하며, 지원되는 보간 방법은 선형만 있습니다. 키 프레임을 정의하는 데 사용할 수 있는 세부적인 정도는 거의 제한이 없습니다.

다음 다이어그램은 하루 동안의 키 프레임 시간을 참조하는 데 사용될 수 있습니다:

![](https://learn.microsoft.com/en-us/minecraft/creator/documents/media/deferredlighting/key_frame_times_of_day.png)

<br/>

# JSON Files 예시

다음은 오버월드를 기반으로 한 맵에 대한 합리적인 시작 값을 제공하는 예제 JSON 파일입니다.

<br/>

## lighting/global.json
```jsonc
{
  "format_version": [1, 0, 0], 
  "directional_lights": {
    "sun": {
      "illuminance": {
        "0.0": 1.0,
        "0.25": 400.0,
        "0.35": 20000.0,
        "0.5": 109880.0,
        "0.65": 20000.0,
        "0.75": 400.0,
        "1.0": 1.0
      },
      "color": [ 255.0, 255.0, 255.0, 255.0 ]
    },
    "moon": { 
        "illuminance": 0.27,
        "color": "#ffffffff"
    }, 
    "orbital_offset_degrees": 3.0
  }, 
  "pbr": { 
      "blocks": { 
        "global_metalness_emissive_roughness": [0.0, 0.0, 1.0]  
      }, 
      "actors": { 
        "global_metalness_emissive_roughness": "#0000ff"
      } 
  } 
}
```
<br/>

## lighting/atmospherics.json
```jsonc
{  
  "horizon_blend_stops":{
    "min": { 
      "0.0": 0.0, 
      "0.1962499916553497": 0.008, 
      "0.25": 0.0, 
      "0.34": 0.11, 
      "0.6": 0.027, 
      "0.6750000119209290": 0.01332983374595642, 
      "0.7487499713897705": 0.0, 
      "0.8174999952316284": 0.0 
    }, 
    "start": { 
      "0.0": 0.25, 
      "0.1962499916553497": 0.4179066121578217, 
      "0.25": 0.213, 
      "0.34": 0.401, 
      "0.6": 0.23, 
      "0.6750000119209290": 0.2143078744411469, 
      "0.7487499713897705": 0.1990064233541489, 
      "0.8174999952316284": 0.6179999709129333 
    }, 
    "mie_start": { 
      "0.0": 0.5, 
      "0.1962499916553497": 1.172000050544739, 
      "0.25": 0.5, 
      "0.34": 0.5009999871253967, 
      "0.6": 0.167, 
      "0.6750000119209290": 0.15, 
      "0.7487499713897705": 0.55, 
      "0.8174999952316284": 0.79 
    }, 
    "max": { 
      "0.0": 0.25, 
      "0.1962499916553497": 0.137, 
      "0.25": 0.0, 
      "0.34": 0.467, 
      "0.6": 0.2,
      "0.6750000119209290": 0.1919008344411850, 
      "0.7487499713897705": 0.214, 
      "0.8174999952316284": 0.0 
    } 
  }, 
  "rayleigh_strength": { 
    "0.0": 1.026124954, 
    "0.25": 0.1624998152, 
    "0.3": 0.1624998152, 
    "0.3037499785423279": 0.1624998152, 
    "0.3249999880790710": 0.1624998152, 
    "0.6": 0.5, 
    "0.7074999809265137": 0.1982535422, 
    "0.75": 0.125
  }, 
  "sun_mie_strength": { 
    "0.0": 1.0, 
    "0.25": 3.0, 
    "0.3": 3.0, 
    "0.3037499785423279": 2.878501892089844, 
    "0.3249999880790710": 1.0, 
    "0.6": 1.0, 
    "0.7074999809265137": 1.0, 
    "0.75": 1.0 
  }, 
  "moon_mie_strength": { 
    "0.0": 0.0, 
    "0.25": 0.0, 
    "0.3": 0.0, 
    "0.3037499785423279": 0.114, 
    "0.3249999880790710": 1.0, 
    "0.6": 1.0, 
    "0.7074999809265137": 0.0, 
    "0.75": 0.0 
  }, 
  "sun_glare_shape": { 
    "0.0": 15.89900016784668, 
    "0.25": 15.89900016784668, 
    "0.3": 15.89900016784668, 
    "0.3037499785423279": 15.84438705444336, 
    "0.3249999880790710": 15.0, 
    "0.6": 4.0, 
    "0.7074999809265137": 4.0, 
    "0.75": 4.0 
  }, 
  "sky_zenith_color": { 
    "0.0": [0, 125, 164], 
    "0.25": [11, 55, 97], 
    "0.5": [7, 10, 36], 
    "0.75": [50, 88, 170] 
  }, 
  "sky_horizon_color": { 
    "0.0": [255, 255, 254],
    "0.171875": [196, 133, 102], 
    "0.25": [255, 85, 85], 
    "0.305": [173, 60, 67], 
    "0.35": [168, 76, 140], 
    "0.4174999892711639": [130, 99, 169], 
    "0.47": [66, 98, 238], 
    "0.6030000448226929": [66, 98, 238], 
    "0.6474999785423279": [168, 99, 162], 
    "0.75": [238, 144, 124], 
    "0.84": [242, 168, 152] 
  } 
}
```
---
원본 글  
https://learn.microsoft.com/en-us/minecraft/creator/documents/deferredlighting#opting-into-the-deferred-lighting-pipeline