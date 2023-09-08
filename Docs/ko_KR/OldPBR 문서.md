> [!NOTE]
> 이 문서에서는 PBR텍스쳐에 대한 설명은 없이 OldPBR 포멧에 대한 것만 다룸  
> Normal 텍스쳐와 Specular텍스쳐자체는 PBR이랑 상관없으니 참고바람

<br/>
<br/>
<br/>

# OldPBR이 뭐임?
OldPBR(SEUS PBR이라고도 부름)이란  
Normal, Specular텍스쳐에 RGB 알파 텍스쳐에다가 PBR머터리얼 텍스쳐를  
쑤셔박은걸 말함  
현제는 SEUS나 Continuum무료 버전같이 예전에 업데이트가 중지된 셰이더에서만 사용가능함  
만약 PBR리소스팩을 제작하고 싶으면 [LabPBR](https://wiki.shaderlabs.org/wiki/LabPBR_Material_Standard)공식문서를 참고할것을 추천함

참고로 텍스쳐맵 추가할려면  
원하는_텍스쳐명 뒤에 _n, _s 붙이면 됨  
예) oak_leaves_n, oak_leaves_s

<br/>

![](https://github.com/null511/PixelGraph-Release/raw/main/media/LAB11.png)
이미지출처: [PixelGraph](https://github.com/null511/PixelGraph-Release)

<br/>
<br/>
<br/>

---

<br/>

# Normal 텍스쳐 (_n)
RGB는 노멀맵 그대로고 알파채널에 height를 넣음  
무작정 Height를 넣는게 아니라  
70퍼 정도로 낮춰 적용해줘야함

<br/>
<br/>

# Specular 텍스쳐 (_s)
RGB에 각각 Smoothness, Metallic, Emission들어감  

Emission은 기존 발광하는 텍스쳐에서 블룸 없애는정도임  
만약 빛나게할거면 옵티파인 이미시브 텍스쳐쓰면 되긴하는데  
애초에 OldPBR만 지원하는 셰이더면 PBR리소스팩이랑 이미시브 텍스쳐 같이쓰는건 비추천함  

만약 Rough으로 제작했으면 걍 색반전해서 쓰면됨  
PixelGraph에선 자동으로 번경해줌  

만약 Ambient Occlusion 넣고싶다하면 
컬러텍스쳐에 약하게 넣고  
Smooth에 넣어주면 됨  
야매같은 방법이긴한데  
실제로 예전 PBR리소스팩들이 많이 쓰던 방법이기도하고  
실제 셰이더상으로도 AO에 빛 덜들어오면서 빛 덜 반사되는것도 비슷하니까  
나름 쓸만한 방법임

<br/>

---


<br/>
<br/>
<br/>


# 어떻게 넣음?
포토샵있으면 각각 알파 마스크랑 RGB채널에 넣으면되고  
없으면 뭐 GIMP나 Krita로도 가능하긴 한데  
전채적으로 계속 유지보수할려면 에디터 쓰는것보다  
[PixelGraph](https://github.com/null511/PixelGraph-Release)라고 PBR리팩용 툴쓰는거 추천함  
**Projects / Publish Profile** 에서  
**Encoding**에서 OldPBR로 번경 가능함