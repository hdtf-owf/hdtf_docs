# PBR

HDTF has support for two PBR shader.

One is called "**PBR**" the other one is called "**PBR_Override**"
The **PBR** shader is what you would expect, a PBR shader.
The **PBR_Override** Shader is a Shader designed for use with default materials
This means it's a simple override for Shader names to allow for special effects.
However it does not allow some other effects and is merely designed for already existing source materials.

!!! note

    The "PBR" Shader supports certain effects.
    The usual PBR effects such as:

    - Emissive mapping
    - Parallax mapping / height mappimg / displacement mapping / ... / whatever other name you can come up with
    - Bump mapping
    - Alphatesting, a basic transparency effect
    - MRAO maps, not technically an effect, but it should be mentioned you can't specify       individual texture maps.
    - Envmapping, enabled by default, always.

    And a special effect called:

    - Parallax Corrected Cubemaps, a way to correct cubemaps so they properly allign with a predefined rectangular boundary.


The "PBR_Override" Shader supports following effects.
The usual PBR effects such as:
-Emissive mapping, in source terms this is RGB selfillum maps
-Parallax mapping / height mappimg / displacement mapping / ... / whatever other name you can come up with
-Bumpmapping
-Alphatesting, a basic transparency effect
-Envmapping, enabled by default, always.

The special effect called
-Parallax Corrected Cubemaps, a way to correct cubemaps so they properly allign with a predefined rectangular boundary.

And the following

- Envmapmask by Basealpha
- Envmapmask by Normalalpha
- No Envmapmasking
- Flipping the Envmapmask
- Modifying Envmap Tint
- Modifying Envmap Contrast
- Modifying Envmap Saturation

Here is how it works, for both shaders

!!! Important
    If you want to use the PBR Shader on a model, define :
    `:::c++ $model "1"`
    Otherwise you will receive errors, on models. Don't define if you don't use it on a model.
    Keep in mind, Models use per origin lighting and ambient cubes.
    Models using the pbr shaders do NOT support model lightmapping, vertex lighting.

Define the Basetexture using :
$Basetexture	"path\texturename"
When not defined, uses a default white texture instead.

Define a Bumpmap using :
$Bumpmap	"path\texturename"
For proper PBR this must be assigned.

Enable Alphatesting using :
$Alphatest	"bool"
Default is 0. Uses the Alphachannel of the Basetexture for the Opacity mask.

Define Alphatest Reference using
( The reference to which the mask should be considered ) :
$Alphatestreference "float"
usually from 0-1. Default is 0

Define a RGB Emissive Texture using
$emissiontexture	"path\texturename"
again, its rgb.

Enable Parallax Occlusion Mapping / Height Mapping / Displacement Mapping.......... using :
$Parallax "bool"
This uses the Alphachannel of the Bumpmap for the Heightmap / Displacement Map / ...
Default is 0

Define the "depth" of the parallax map using :
$Parallaxdepth "float"
Default is "0.0030"

Define the "center depth" of the parallax map using :
$Parallaxcenter "float"
Default is "0.5"

Some OG parameters will work such as $nocull "bool"
Use at your own risk.


Parameters ONLY for the "PBR" Shader :

Define the MRAO Map :
$Mraotexture	"path\texturename"
If you don't know what an MRAO texture is, look up examples online...


Parameters ONLY for the "PBR_Override" Shader :

Define the alpha channel of the basetexture for Envmapmasking using:
$Basealphamask "bool"
Internally this sets the Alphachannel of the basetexture as the metallic mask.
The inverse will be used for roughness. AO is defaulted to "1.0"

Define the alpha channel of the basetexture for Envmapmasking using:
$Normalalphamask "bool"
Internally this sets the Alphachannel of the bumpmap as the metallic mask.
The inverse will be used for roughness. AO is defaulted to "1.0"

Flip the mask internally using:
$Flipenvmapmask "bool"
This is intended to be used for materials that previously used $basemapalphaenvmapmask
As it is notorious for being magically being flipped in Valve's spaghetti lazer shadercode on seemingly random occassions.

If you don't specify either $Basealphamask or $Normalalphamask it will set metallic as "1.0" ( White )
Also $Basealphamask & $Normalalphamask at the same time is not supported. its one or nothing.

Tint the envmap using:
$Envmaptint "[floatscale floatscale floatscale]"
in "[R G B]", if you don't know what RGB is use your favorite search engine
Example $envmaptint "[0.9 0.0 0.0]"
This will make the envmap reflection only reflect as red.

Contrast the Envmap using:
$Envmapcontrast "floatscale" ( from 0.0 up to 9.9 don't try more digits, for now. )
0.0 is normal and 1.0 is color*color
Define this even if you don't use it.

Saturate the envmap using:
$Envmapsaturation "floatscale" ( from 0.0 up to 9.9 don't try more digits, for now. )
1.0 is normal, 0 is greyscale.
Define this even if you don't use it.



Parameters used for Parallax Corrected Cubemaps on BOTH Shaders :

Disclaimer : these are set automatically when compiling the map
However that is not the case for models, if you require models to have it for some reason I might be oblivious to
You can manually define them. I expect however that you actually know what you are doing, therefore no explanation.

Turn on PCC
$Envmapparallax	"bool"

Set parallax obbs
$Envmapparallaxobb1	"[float float float float]"
$Envmapparallaxobb2	"[float float float float]"
$Envmapparallaxobb3	"[float float float float]"

Where the envmap is in the world
$Envmaporigin	"[float float float]"



# Q&A : 

Question :
My Material appears to be pure black, why?

Answers :

1. Make sure you have built cubemaps, they are required for PBR to work correctly.

2. If your material is very metallic, make sure the AO Channel is not totally Black
If your material uses PBR_Override, this cannot be the case, proceed with the other points.

3. Make sure the material does not have high metallic and high roughness values.
This could cause great visual problems on the model, including a full black model
If your material uses PBR_Override, this cannot be the case, proceed with the other points.

4. Make sure the Bumpmap and MRAO Texture are NOT imported as sRGB
Also make sure that the Normalmap is NOT saved in any texture format with only 8 bits.
Normal maps ( generated with ANY modern texturing software ) are 16 bits, and you should ALWAYS pay attention for this.
Any other texture maps are read as sRGB, saving them differently won't change this fact.

5. Potential cause us very dark Basetextures on very metallic objects.
( it just multiplies it all together in the shadercode, so if everything is black... yeah )

6. Its not actually receiving light. Try pointing a projected_texture at it.
If even then you can't see *anything* then something is very wrong.


Question :
How do I use Parallax Corrected Cubemaps?

Answer :
Make a env_cubemap in the RECTANGULAR Room, that you want to have PCC in, as possible.
Make a RECTANGULAR Brush encapsulating the entire Room.
Make it an entity of class "Parallax_obb" and give it a name.
Define brush faces to use Parallax Corrected Cubemaps in the properties of the env_cubemap.
( Keep in mind Parallax Corrected Cubemaps are ONLY supported for Brushmaterials using the PBR or PBR_Override Shader )
Define the Parallax_Obb in the "Cubemap Bounds" field
Compile the map and build cubemaps, after reloading all materials / restarting the game,
brushfaces assigned to the cubemap should be parallax corrected.
