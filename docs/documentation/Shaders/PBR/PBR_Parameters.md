# PBR Parameters

## Shader Names

- PBR for Brushes
- PBR_MODEL for models
- Use $BaseTexture2, $MRAOTexture2, $BumpMap2.... for Blended Materials ( Displacements )

**IMPORTANT**

Having your material inside `materials/models/` folder will automatically force `$model` flag and thus PBR_MODEL

## Features for Models

### Wetness Porosity

Wetness | Porosity are special surface properties that are more or less intuitive for Artists to define a mask for.
Additionally because this is a separate function it can be used to dynamically ramp up the wetness of materials.
This can be very useful if a map requires accumulating material wetness, for example when initially there is no rain. Or vice versa.-If rain is going away

Wetness is Masked by using the RED Channel of the  $PropertiesTexture
Porosity is Masked by using the GREEN Channel of the $PropertiesTexture
The $PropertiesTexture has two channels remaining that are used for VectorLayer Textures!

Here are available parameters and what they do.

    // Defines a mask for the Effect. This has to be set in order to use Wetness/Porosity.
    // However, just defining a black texture allows you to simply bias Wetness, but not Porosity.
    "$PropertiesTexture" "path/name"
    
    // Enables Wetness/Porosity behaviour. This is a separate bool because of VectorLayers
    "$WetnessPorosity" "1"
    
    // Levels Wetness. -1 will entirely remove wetness. ( Proxies may be used to modify this )
    "$WetnessBias" "-1"

 Rock without Wetness ( or rather, it has -1 bias )
 ![Cliff model from Urban Chaos with no Wetness](https://i.imgur.com/ByGN06R.jpeg)
 Note that this is full wetness and its not really masked. Up-Facing Normals will be forced to 1, which will give the appearance of full submersion of the material.

 ![Cliff model from Urban Chaos with full Wetness](https://i.imgur.com/XWOf2HB.jpeg)

### Detail Textures

 See [Detail Textures on the Valve Developer Community ( VDC )](https://developer.valvesoftware.com/wiki/$detail)

 Note that some of the Blendmodes might have unexpected results. ( BlendMode 11 )
 Also $DetailBlendMode 10 is currently not implemented on Brushes and cannot be implemented on Models.

!!! note
    Incompatible with `$PropertiesTexture` ( Wetness/Porosity ) due to Sampler Limitation.

   
### Sub-Surface Scattering

Sub-Surface Scattering should be used sparingly as it does everything a usual shader does **and SSS**.
The effect itself will not be explained, you can use online reference for that instead.
In our Shader there are various parameters to control the effect, here they are.

```
// Refer to PBR Textures, R is the Thickness, GBA is RGB Color.
"$SSSTexture" "path/name"
 
// Float. Controls the spread of the SSS. If you can't spread enough, adjust Thickness.
// Default is 1.0, but 5 might be more reasonable.
"$SSSPower" "5"

// Adjust strength before being applied to anything.
// This is useful for when you want $SSSEmission AND regular Emission.
// However, it eventually just acts like $SSSEmission itself.
"$SSSScale" "1.0"

// Only applied to regular SSS and not $SSSEmission.
// Multiplier for the intensity of the SSS Effect. Default of 1.0
"$SSSIntensity" "1.0"

// Tint for the SSS Color. If the texture does not have an alpha channel,
// set blue to 0, this might happen automatically on the shader in a future update.
"$SSSTint" "[R G B]"

// SSS Emission is a special effect that will output the Emissiontexture multiplied by SSS separetely.
// This was intended to add glow to more solid objects like the crystals.
// This will force glow to only appear on dark/shadowed areas.
// But whether or not we find a use remains to be seen. Default Value is 0.0, its a float.
"$SSSEmission" "0.0"
```

Without SSS
!["Dyson" Model from Urban Chaos without SSS ](https://i.imgur.com/nzli9bK.jpeg)

With slight SSS
!["Dyson" Model from Urban Chaos with SSS ](https://i.imgur.com/ZlMleFJ.jpeg)

"Overgrown" dark side using `$SSSEmission`.
!["Dyson" Model from Urban Chaos but he has an Crystal Infestation or something](https://i.imgur.com/4uWGlzU.jpeg)

### Bent Normals

Explanation you must find online.
Bent Normals will occlude only occlude specular reflection areas and is a separate texture from the actual $BumpMap!
This effect can be activated using

`"$BentNormal" "path/name"`

The Bent Normal must be in Tangent Space!
And it might only have real visible effects on models with actual self occlusion.

Without Bent Normals
![Cloth Model without Bent Normals](https://i.imgur.com/9QaR6h5.jpeg)
With Bent Normals
![Cloth Model with Bent Normals](https://i.imgur.com/S7WBW9L.jpeg)

### Sheen

To be done. Currently kind of broken, its there but eeeeeeehhhh don't use it kk?

`$Sheen` and `$SheenIntensity`

Incompatible with `$EmissionTexture`

### Vector dependent Layering Textures ( VectorLayers )

VectorLayers are textures that are being applied to a model/brush/displacement depending on a user defined Vector ( Direction ).
This was intended as a way to give a more discrete look to surfaces on the Alaska map,
by adding thin layers of ice or snow materials to upwards facing surfaces ( "[0 0 1]" ).
It may be used for others effects. Some ideas :

1. Dust Accumulation
2. Sand Accumulation
3. Snow Accumulation
4. Blood Accumulation
5. Spider Webs
6. Fire Burn Mark Spreading Texture
7. Liquid Flow Restriction ( Liquids should only be on surfaces that point upwards. )

Vector Layers do not have their own Normal Maps and will thus not affect the actual thickness of its own layer.
Additionally they do not have their own metallic properties, this was done for several reasons.

1. All of the above shouldn't be metallic.
2. These are supposed to be THIN layers, they should not affect the underlying material surface normals

Using a VectorLayer requires two textures. You can find more information about them in the documentation about Textures and their requirements.

```
// The actual Texture to be applied to that Vector. Alpha is Ambient Occlusion.
// Specifying this, will enable the effect.
"$VectorLayerTexture"  "path/name"

// Required for VectorLayers. Blue Channel masks the Layer and Alpha is Roughness
"$PropertiesTexture"  "path/name"

// The Vector to apply the texture on. "[0 0 1]" faces upwards. -1 downwards.
// Sadly you cannot apply it to two vectors ( "[0 0 1]" && "[0 0 -1]" ) at the same time.
"$LayerVector"     "[X Y Z]"  

// Smoothsteps the blending factor. The tolerance between facenormal and $LayerVector
// Default Value is 0.3
"$LayerVectorSimilarity" "0.3"
```

### Emission Textures

$EmissionTexture's are added through addition to the results of the Shader.
This means that it will display the $EmissionTexture regardless of light conditions or other occlusions.
Note that for a bloom effect to appear, you must have *some* information the $BaseTexture.
Over-brightening ( and thus bloom ) will only occur when the Results are above white.

Note :
Incompatible with $PropertiesTexture.
These surfaces will glow because they are Incandescent, the surfaces are heated to >525°C and we disabled this combo because if water touched this surface it would literally just explode.
( Seriously  though, we disabled it to get faster compiles, we deemed this combo to be very unlikely to occur )
Also Incompatible with Sheen.

Here are available parameters and what they do.

    // Path/Name of the Texture to be used for Emission.
    "$EmissionTexture" "path/name"
    
    // Tints the Emission. Default Values are "[1 1 1]"
    "$EmissionTint"  "[R G B]"
    
    // See Sub-Surface Scattering for more information.
    "$SSSEmission"  "1"

![Xen Bulb without Emission](https://i.imgur.com/5Hi6GTB.jpeg)
![Xen Bulb with Emission](https://i.imgur.com/fTxAqlx.jpeg)

### Model Lightmapping & Lightmap UV's

Non-Bumped Model Lightmaps are supported on our Shader.
Dynamic light entities ( such as projected textures or named lights ) still cause a specular highlight through the bumpmap.Otherwise, static lights will have their lighting baked. This is ideal if you want nice Shadows for a Model.

You can use this feature by enabling lightmaps for a prop_static in Hammer.
Models that want to use Lightmaps must have $NormalTexture set to the NormalMap, and $bumpmap to literally any gibberish you can think of, it just needs to be defined ( as something other than " " ) in the VMT!

Note that all previous limitations applied except for the $BumpMap restriction.
If you want to use this feature, you are required to have some serious understanding on how models work.
Simply enabling it for your random models will not give you meaningful results.
Model Lightmaps require a Unique set of UV's, if the UV's are overlapping with eachother, mirrored or go out of the 0-1 range in either X or Y ( U or V ) this feature will not be usable.
Like,**REALLY**, if you don't know what you are doing, don't bother!!!

As for Lightmap UV's, this requires several custom tools to be used that are currently still in development.
You will have to ask for a rundown on how that works, whats required etc and it assumes you have a very extended knowledge-setabout what lightmap uv's are, how multiple UV's works, how to compile code ( probably ) and what the limitations of model lightmaps in Source are. Also, Blender Lightmap UV pack SUCKS just so you know, it will not give you any decent results AT ALL for this.

!!! note
    Incompatible with `$PropertiesTexture` ( Wetness/Porosity ) due to Sampler Limitation.


An old development screenshot showing off Lightmap UVs on a Pyramid Model ![An image of Lightmap UVs in Action](https://i.imgur.com/G38LnVU.jpeg)

### Micro Shadows

Microshadows are a technique to enhance Light Occlusion in Ambient Occluded areas through the use of the Light-Direction, Normal Map and Ambient Occlusion Texture.
The code for this is always run on models, however by default 0% of it will be applied.
The parameter that controls to which degree Microshadows are applied is:

```
// 0.0 is 0%, and 1.0 is 100%
"$MicroShadows" "0.0"
```

0% MicroShadows...
![An image of some props with no Microshadows](https://i.imgur.com/VVUNS9h.jpeg)

100% MicroShadows.
![An image of some props with Microshadows](https://i.imgur.com/7jrTWs0.jpeg)
Note that the corners near the keypads are noticeably darker. The amount of occlusion and direction depends on that of the light which is shining upon the model.

## Features for Brushes

### Parallax Corrected Cubemaps

Parallax Corrected Cubemaps are Cubemaps that are being corrected for Parallax...
OK, but what does that mean?
It means that the Cubemaps that is applied to brushes will be stretched to a predefined boundary area.
This will essentially align the reflections with the scenery objects and the boundary of a room.
This is unideal if the middle of the room is obstructed by objects and it cannot really be used for non-rectangular rooms. Use non-rectangular rooms and PCC at your own potential waste of time!
As for how to use them,
[See this Tutorial on Source Tricks Website](https://mrkleiner.github.io/source_tricks/?lt=7780847a)

### For these, see "Features for Models"

### Wetness Porosity

### Detail Textures

### Vector dependent Layering Textures ( VectorLayers )

### Emission Textures

## Features for Displacements

### Wetness Porosity

Displacements additionally receive following Parameters for Blending multiple Textures.

```
// Second texture for both Wetness/Porosity AND VectorLayers
"$PropertiesTexture2" "path/name"
"$WetnessBias2" "0"
```

### Vector dependent Layering Textures ( VectorLayers )

Displacements additionally receive following Parameters for Blending multiple Textures.

```
// Second texture for both Wetness/Porosity AND VectorLayers
"$PropertiesTexture2" "path/name"
"$VectorLayerTexture2" "path/name"
"$LayerVector2" "[X Y Z]"
"$LayerVectorSimilarity2" "0.3"
```

### Emission Textures ( Also see "Features for Models" )

Displacements additionally receive following Parameters for Blending multiple Textures.
ShiroDkxtro2 Note : Second Emission Texture might be broken right now due to a bug with s15.

```
// Please note that this texture can not be tinted.
"$EmissionTexture2" "path/name"
```

### Seamless Mapping & $Seamless_Secondary

For information about Seamless Mapping, [See this VDC Article](https://developer.valvesoftware.com/wiki/$seamless_scale)
Now for the interesting part. $Seamless_Secondary
ShiroDkxtro2 : As of today ( 17.04.2023 DMY ), this feature has not been fully implemented and seamless is kinda broken too.
I will fix it as soon as I can. But this DOES work on LUX_ Shaders!

Seamless Secondary allows to only have the second material to be seamless. This is very useful if you want to retain texture alignment whilst being able to account for stretchy textures on strong displacement inclines.

### Parallax Occlusion Mapping

No explanation given. Its Heightmapping.
NOTE: This is VERY expensive at the moment. Don't use it on very large surfaces or dare even attempt to use it on most materials in your scene, it will **DESTROY** your FPS.

```
// Turns the effect on. Uses Alpha Channel of the Normal Map for the Height Map
"$ParallaxMapping" "1"

// You have to play around with the value, it currently doesn't make much sense...
"$ParallaxHeight" "0"

// Same for the above.
"$ParallaxMaxOffSet" "0"
```

### Parallax Corrected Cubemaps ( See "Features for Brushes" )

### Detail Textures ( See "Features for Models" )

## Other Parameters

I will only list the parameters because it would be far too tedious to list what they do.
Its mostly self-explanatory anyways or somehow already documented on the VDC.
Just check if what you are looking for exists.

Parameters :
```
-----------------------------------------------------------------------------
//////////////////////////////////////////////////
// Actually implemented and working parameters //
//////////////////////////////////////////////////
// All Frame Vars :
$BumpFrame
$BumpFrame2
$Frame
$Frame2
$MRAOFrame
$BlendMaskFrame

// This will transform Base, MRAO, properties and vectorlayer.
$BaseTextureTransform
$BaseTextureTransform2
// Only transforms Bumpmaps
$BumpTransform
$BumpTransform2
// Only for BlendModulate.
$BlendMaskTransform

// Texture I forgot to mention.
$BlendModulateTexture

// Tints your $BaseTexture
$BaseTextureTint
$BaseTextureTint2

// EnvMapping Parameters
$EnvMap "custom envmap"
$EnvMapFrame "int"
$EnvMapTint "[R G B]"
$EnvMapContrast "float"
$EnvMapSaturation "float"
$EnvMapLightScale "default 0, 1 will multiply reflections by lighting"

// Biases the MRAO's -1 will remove M, R or AO and +1 will force it to full white!
$MRAOBias
$MRAOBias2

// Default value of 0.025f ( Average between 0.01 and 0.04 )
// No explanation because you should know what this is if you want to change it.
$DiElectricCoEfficient

// You can manually set a lightmap if you so desire.
$LightMap

// Only touch this if you truly know what you are doing.
$LightMapUVs "0 | 1"

//////////////////////////////////////////////////
// Parameters that exist right now but unused  //
//////////////////////////////////////////////////
$SelfIllum_EnvMapMask_Alpha
$color
$color2
$BlendTintByBaseAlpha
$BlendTintColorOverBase
$BlendTintColorOverBase2
$FresnelReflection
$EnvMapFresnel
$EnvMapFresnelMinMaxExp
// This was supposed to be used on PBR_OVERRIDE
$EnvMapMask
$EnvMapMaskFrame
$EnvMapMaskTransform

//////////////////////////////////////////////////////////////////////////////
// Features that are undetermined to be implemented or not yet implemented //
//////////////////////////////////////////////////////////////////////////////
$Hullshell
$DitheredAlpha
$MRAOFrame2
$ParallaxIntensity
// We have some Parallax Interval code, but it just looks worse when compared to POM.
$ParallaxInterval
// These are semi implemented
$SpecularTexture
$SpecularTexture2
// Wrinkle Mapping
$Compress
$Stretch
$BumpCompress
$BumpStretch

//////////////////////////////////////////////////
// Internal Parameters that you shouldn't touch //
//////////////////////////////////////////////////
// MaterialName is stored on this.
// Kind of a HACKHACK I had to do because theres no access later-on for materialnames
$MaterialName "String"
// This is used to turn on WVT internally.
// Define $basetexture2 or any secondary texture and this will be set to 1.
$InternalBoolWVT "0 | 1"

// PCC Parameters that are set automatically when compiling the map.
$EnvMapParallax
$EnvMapParallaxOBB1
$EnvMapParallaxOBB2
$EnvMapParallaxOBB3
$EnvMapOrigin
```