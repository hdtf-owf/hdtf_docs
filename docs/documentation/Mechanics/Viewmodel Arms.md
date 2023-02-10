---
title: Viewmodel Arms
description: "Explaining how viewmodel arms work"
hero: "Mechanics: Viewmodel Arms"
og_title: "Mechanics: Viewmodel Arms"
page_path: misc/
og_image: "https://steamuserimages-a.akamaihd.net/ugc/1894345102686279579/25B9FD45693359D46AE610CE153F0B4C13971172/?imw=1920&imh=1080&ima=fit&impolicy=Letterbox&imcolor=%23000000"
og_image_type: "video.other"
---
# Viewmodel Arms
{% import 'main_image.html' as image %}
{{ image.main_image(content='https://steamuserimages-a.akamaihd.net/ugc/1894345102686279579/25B9FD45693359D46AE610CE153F0B4C13971172/?imw=1920&imh=1080&ima=fit&impolicy=Letterbox&imcolor=%23000000', footer_text='gif showing how c_arms can look               \nimage author: [MonsterBoo_](https://steamcommunity.com/sharedfiles/filedetails/?id=2796533997){ target="_blank"}') }}

!!! note
    Aspects of this feature may be subject to change

HDTF supports seperate arms from the viewmodels akin to L4D, Garry's mod and CSGO with some slight differences and requirements.

## Using arms with weapons

As the transition from the old HL2 style v_models will take time, by default weapons do not have any arm models attached. To attach arms the associated weapon script requires a `default_arms` entry.

To specify different arms for different acts of the game there is: `snow_arms` for Act 2, `jacket_arms` for Act 3 and `camp_arms` for the Tutorial. If any of the act specific entries are absent or blank the model specified with `default_arms` will be used instead.

```
// Example using scripts/weapon_knife.txt
WeaponData
{
	"printname"	"#HDTF_KNIFE"
	"viewmodel"				"models/weapons/v_knife.mdl"
	"playermodel"			"models/weapons/w_knife.mdl"
    ...
    "default_arms"  "models/weapons/arms/v_arms_hecu.mdl"            // act 1
    "snow_arms"     "models/weapons/arms/v_arms_winter_survivor.mdl" // act 2
    "jacket_arms"   "models/weapons/arms/v_arms_coat.mdl"            // act 3
    "camp_arms"     "models/weapons/arms/v_arms_camp.mdl"            // tutorial
    ...
}
```

## Specifying arms per map

If a map needs to use a specific arm type it must be specified in the map itself. To do this open the map in Hammer, then on the Menu bar go to `Map` → `Map Properties`, and change the option `Arms Skin` to the desired type.

```
"Arms Skin"
[    
    0 : "Default" - Marine outfit seen in Prologue & Act 1.
    1 : "Jacket" - black jacket with leather gloves seen in Act 3.
    2 : "Snow coat" - white coat seen in Act 2.
    3 : "Bare" - bare hands as seen in Tutorial level.
]
```

## Console Commands

- `cl_disable_arms` - Setting this to 1 will disable drawing the arm model.
- `hdtf_set_arms_type` - Setting this to a value from 0 to 3 will change the current arm type being used. Uses the same ordering found in the map properties setting.