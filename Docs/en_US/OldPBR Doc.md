> [!NOTE]
> This document covers only the OldPBR format without explaining PBR textures. Normal textures and specular textures themselves are not related to PBR, so please keep that in mind.

> [!NOTE] 
> ChatGPT Translations

<br/>
<br/>
<br/>

# What is OldPBR?
OldPBR (also known as SEUS PBR) refers to applying PBR material textures to RGB alpha textures of Normal and Specular textures. Currently, it is only usable in shaders that have discontinued updates, such as SEUS or Continuum free versions. If you want to create a PBR resource pack, it is recommended to refer to the [LabPBR](https://wiki.shaderlabs.org/wiki/LabPBR_Material_Standard) official documentation.

Please note that to add texture maps, you should append _n and _s to the desired texture name. For example, oak_leaves_n, oak_leaves_s.

<br/>

![](https://github.com/null511/PixelGraph-Release/raw/main/media/LAB11.png)
Image source: [PixelGraph](https://github.com/null511/PixelGraph-Release)

<br/>
<br/>
<br/>

---

<br/>

# Normal Texture (_n)
The RGB channel remains the same as the normal map, and the alpha channel contains the height information. It's essential to lower the height to about 70% for proper application.

<br/>
<br/>

# Specular Texture (_s)
The RGB channels contain Smoothness, Metallic, and Emission, respectively.

Emission is the extent to which it removes the bloom effect from the original glowing texture. If you want it to emit light, you can use Optifine emissive textures, but if you're using a shader that only supports OldPBR, it's not recommended to use PBR resource packs alongside emissive textures.

If you created it as Rough, you can simply invert the colors. PixelGraph automatically handles this conversion.

If you want to add Ambient Occlusion, you can lightly incorporate it into the color texture and put it in the Smooth channel. It's a makeshift method, but many old PBR resource packs used this method, and it's somewhat effective since it reduces the reflection of light similarly to how AO works in shaders.

<br/>

---

<br/>
<br/>
<br/>

# How to Apply?
If you have Photoshop, you can place them in the alpha mask and RGB channels individually. If you don't, you can use software like GIMP or Krita. However, for long-term maintenance, it's recommended to use a tool specifically designed for PBR resource packs called [PixelGraph](https://github.com/null511/PixelGraph-Release). You can change the encoding to OldPBR in **Projects / Publish Profile**.


