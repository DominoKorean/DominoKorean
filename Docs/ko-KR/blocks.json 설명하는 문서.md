> [!NOTE]
> 완성되지 않은 문서

> [!NOTE]
> 공식문서랑 비공식문서 어디에도 전부 정리되있는게 없어서  
> 직접 정리한 문서임

> [!NOTE]
> json구문 몰라도 쓸 수 있도록 설명한 문서  
> 늅늅이들을 위한 문서는 맞지만 일단 리소스팩을  
> 만드는 가장 기본적인 부분을 안다는 가정하에 설명함  
> 애등온용 아니고 only 리소스팩 개발자용

<br/>

# blocks.json이 뭐임?
blocks.json은 리소스팩으로 가능한  
블록의 모든 상태를 지정해 줄 수 있는 거라고 보면 됨

## 그전에 json이 뭐임?
대충 자바 스크립트랑 같이 많이 씌이는 데이터 표기, 저장 방식  
뭐 정확한건 그냥 [이 영상](https://youtu.be/1ID6pfTViXo?si=c9DSyJK5WcaaAt1d) 참고하고 (꼭 반드시 볼 필요는 없음)  
그냥 대충  
"자 이제부터 니 오른쪽 텍스쳐는 dirt.png를 쓰고 이거엔 3이라는 값을 써"  
해서 데이터를 형식에 맞게 저장해두면 그 데이터값을 불러와서  
리소스팩으로 불러와주는거임  
어렵다고 생각될 수도 있는데  
알맞은 폴더위치에 텍스쳐를 저장해두면 그 텍스쳐파일을 불러와서  
리소스팩으로 불러와주는거랑 비슷한 원리임  
(적용 되는게 비슷한 원리라는거지 코드상으로 불러오는 방법이 비슷하다는게 아님)

<br/>
<br/>
<br/>

# 기본적인 형식
일단 blocks.json은

> 📁리소스팩  
>> 🖼️ pack_icon.png  
>> 🗒️ manifest.json  
>> 🗒️ blocks.json

이런식으로 리팩 폴더안에 바로 넣으면 됨  

기본적인 베이스는 여기 베드락 리소스팩 공식 샘플파일 있으니까  
여기서 확인할 수 있고  
https://github.com/Mojang/bedrock-samples  
그래도 기본적인 형식 대충 보여주면
```jsonc
// 이렇게 /두개 옆에 있는건 주석이라고
// 대충 데이터 상에 아무런 영향도 안주는 메모 용도임
{
  "format_version": [
    1,
    1,
    0
  ],
  "leaves": { // 원하는 블록
    "isotropic": { // 원하는 블록에 지정해줄 데이터
      "up": true,
      "down": true
    },
      "concrete": { // 원하는 또다른 블록
    "sound": "stone",
    "textures": "concrete"
    }
  }
}

```
이런식으로 돼있음  
메모장이나 그런걸로 에디팅해도 되는데  
항목 다음에 다른 항목나올때 따옴표넣는거 등  
실수하는 경우가 많기 때문에 그냥 vscode같은 코드에디터쓰는걸 추천함  

<br/>
<br/>
<br/>
<br/>

# 커스텀 할 수 있는거

## render_method

> [!NOTE]
> **사전 설명**  
> AO는 Ambient Occlusion의 약자로
> 대충 블록간에 모서리진곳 사이에 그 그림자로 그라데이션 생기는거 말하는거임

|Material|설명|
|------|---|
|opaque|그냥 기존 우리가 아는 불투명 블록|
|alpha_test|완전히 투명한 텍스쳐에 사용. AO는 같이 비활성화 됨|
|blend|반투명이나 완전히 투명하게가 텍스쳐에서 사용할 수 있게할때 씀. AO는 같이 비활성화 됨|
|double_sided|뒷면 컬링을 완전히 비활성화 하는데 씀|

```jsonc
{
  "format_version": [
    1,
    1,
    0
  ],
  "leaves": {
	"render_method": "alpha_test"
  }
}
```

---

## ambient_occlusion
위에서 설명했던것처럼
블록간에 모서리진곳 사이에 그 그림자로 그라데이션 생기는거 있을지 말지 설정하는거임

```jsonc
{
  "format_version": [
    1,
    1,
    0
  ],
  "leaves": {
	"ambient_occlusion": true
  }
}
```

끌려면 true대신 false쓰면 됨
render_method에서 AO없애는거랑 같이 쓰면
ambient_occlusion이 우선 순위임

---

## textures
텍스쳐 뭐쓸지 지정해주는거임  
이걸로 여러 블록이 같은 텍스쳐쓰게해서  
용량 최적화 하는것도 가능  

```jsonc
{
  "format_version": [
    1,
    1,
    0
  ],
  "leaves": {
    "textures": "concrete" // 이렇게 하면 나뭇잎에 콘크리트 텍스쳐 쓰게 하는거임
  }
}
```
블록에 따라 여러 텍스쳐쓰는 경우에는
```jsonc
{
  "format_version": [
    1,
    1,
    0
  ],
  "chain_command_block": {
    "textures": {
      "up": "command_block_chain_conditional_side",
      "down": "command_block_chain_conditional_side",
      "north": "command_block_chain_front",
      "south": "command_block_chain_back",
      "west": "command_block_chain_side",
      "east": "command_block_chain_side"
    },
    "sound": "metal"
  }
}
```
이런식으로도 사용
각각 위, 아래, 동, 서, 남, 북 이런식으로 적용되고  
굳이 각각 이름대로의 방향이 아니더라도 다른 방식으로 사용되는 경우도 있음  
```jsonc
{
  "format_version": [
    1,
    1,
    0
  ],
   "azalea" : {
      "sound" : "azalea",
      "textures" : {
         "down" : "potted_azalea_bush_top",
         "east" : "azalea_plant",
         "north" : "azalea_side",
         "side" : "azalea_side",
         "south" : "potted_azalea_bush_side",
         "up" : "azalea_top",
         "west" : "potted_azalea_bush_plant"
      }
  }
}
```
진달래 묘목(철쭉)인데 보면  
모델에 씌이지 않는 서, 남, 아래부분을  
화분에 꽂았을때 텍스쳐를 넣어둠  
이런거는 샘플팩에서 하나하나 확인하는 수 밖에 없음

---

## sound
이 블록을 부수거나 위에서 걷거나 할때  
나오는 소리를 지정해주는거임  
소리 리스트는 여기 참고하면 됨  
https://wiki.bedrock.dev/blocks/block-sounds.html
```jsonc
{
  "format_version": [
    1,
    1,
    0
  ],
  "leaves": {
      "sound" : "grass"
  }
}
```

---

## isotropic
텍스쳐 랜덤으로 회전시킬때 쓰는거임  
```jsonc
{
  "format_version": [
    1,
    1,
    0
  ],
   "leaves" : {
      "brightness_gamma" : 0.80,
      "carried_textures" : "leaves_carried",
      "isotropic" : {
         "down" : true,
         "up" : true
           }
   }
}
```

---

## face_dimming

블록 면 각도에따라 그림자지는거 없앨 수 있음

```jsonc
{
  "format_version": [
    1,
    1,
    0
  ],
   "leaves" : {
	"face_dimming": false
   }
}
```
킬려면 true쓰면 됨

---

## blockshape

블록 무슨 모델  쓸건지 지정해주는거임

```jsonc
{
  "format_version": [
    1,
    1,
    0
  ],
   "leaves" : {
	"blockshape": "frame" // 이러면 나뭇잎이 아이템액자 모델 쓰게됨
   }
}
```
해당 모델을 쓴다고해서  
해당 블록의 textures를 쓰지는 않음  
특정 모델만 쓸 수 있고  
모델 리스트는 여기서 확인할 수 있음  
https://wiki.bedrock.dev/blocks/block-shapes.html

---

