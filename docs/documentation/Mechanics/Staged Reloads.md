---
title: Staged Reloads
description: "explaining how Staged Reloads in HDTF work"
hero: "Mechanics: Staged Reloads"
og_title: "Mechanics: Staged Reloads"
page_path: misc/
---
{% import 'main_image.html' as image %}

# Staged Reloads
{{- image.main_image( is_youtube=true, content="T3gaPpjdyCM", footer_text="Video showcasing Staged reload's   The footage is outdated the slide doesn't pop back when pulling the gun out anymore") -}}

HDTF has support for Staged Reloads.

Staged reloads work off of animation events

## Animation Events
the animation events are called

 - **`AE_WPN_STAGE_MAGIN`** When the mag is taken out
 - **`AE_WPN_STAGE_EMPTY_MAGIN`** When the gun has 0 ammo in its clip and the mag is taken out
 - **`AE_WPN_STAGE_BOLTRELEASE`** When the gun has a mag inside of it but the bolt hasnt been released
 - **`AE_WPN_FULLCLIP1`** Fills the clip of the gun to the max clip capacity
 - **`AE_WPN_STAGE_ONEBULLET`** Sets clip to only have 1 bullet and gives back the rest of the ammo that was inside of the clip back into the ammo reserve
 
these are just basic `Booleans`

they are turned on and off based on a animation frame

## Activity Names

the following activity's used for staged reloads are

 - **`ACT_VM_STAGE_MAGIN`**
 - **`ACT_VM_STAGE_EMPTY_MAGIN`**
 - **`ACT_VM_STAGE_BOLTRELEASE`**
 
these are the activity's that are called when the corresponding
animation event is set to `1`

## Emaxple Sequences
here are examples of how you might setup a sequence for staged reloads inside your qc file
### *`ACT_VM_RELOAD`*

```
$sequence "reload" {
	"v_1911_anims\reload.smd"
	activity "ACT_VM_RELOAD" -1
	
	//////Reload stage
	{ event AE_WPN_STAGE_MAGIN 34 "1" }
	
	{ event AE_WPN_STAGE_ONEBULLET 35 }
	
	{ event AE_WPN_STAGE_MAGIN 70 "0" }
	
	{ event AE_WPN_FULLCLIP1 71 }
	
	fadein 0.2
	fadeout 0.2
	snap
	fps 60
}
```
### *`ACT_VM_RELOAD_EMPTY`*

```
$sequence "reload_empty" {
	"v_1911_anims\reload_empty.smd"
	activity "ACT_VM_RELOAD_EMPTY" -1
	
	//////Reload stage
	{ event AE_WPN_STAGE_EMPTY_MAGIN 17 "1" }
	
	{ event AE_WPN_STAGE_EMPTY_MAGIN 58 "0" }
	
	{ event AE_WPN_STAGE_BOLTRELEASE 59 "1" }
	
	{ event AE_WPN_STAGE_BOLTRELEASE 104 "0" }
	
	{ event AE_WPN_FULLCLIP1 105 }
	
	fadein 0.2
	fadeout 0.2
	snap
	fps 60
}
```

### *`ACT_VM_STAGE_MAGIN`*

```
$sequence "reload_magin_stage" {
	"v_1911_anims\reload_magin_stage.smd"
	activity "ACT_VM_STAGE_MAGIN" -1
	
	{ event AE_WPN_STAGE_MAGIN 41 "0" }

	{ event AE_WPN_FULLCLIP1 41 }

	fadein 0.2
	fadeout 0.2
	snap
	fps 60
}
```

### *`ACT_VM_STAGE_EMPTY_MAGIN`*

```
$sequence "reload_empty_magin_stage" {
	"v_1911_anims\reload_empty_magin_stage.smd"
	activity "ACT_VM_STAGE_EMPTY_MAGIN" -1
	
	{ event AE_WPN_STAGE_EMPTY_MAGIN 40 "0" }
	
	{ event AE_WPN_STAGE_BOLTRELEASE 41 "1" }

	{ event AE_WPN_STAGE_BOLTRELEASE 86 "0" }
	
	{ event AE_WPN_STAGE_MAGIN 40 "0" } //DO THIS ON MAGIN EMPTY TO FIX BOLT RELEASE
	
	{ event AE_WPN_FULLCLIP1 86 }

	fadein 0.2
	fadeout 0.2
	snap
	fps 60
}
```

!!! Important
	
	we need to set `AE_WPN_STAGE_MAGIN` to `0`
	here so we can fix the bolt release caused by the activity being called incorrectly

### *`ACT_VM_STAGE_BOLTRELEASE`*

```
$sequence "reload_empty_bolt_stage" {
	"v_1911_anims\reload_empty_bolt_stage.smd"
	activity "ACT_VM_STAGE_BOLTRELEASE" -1
	{ event AE_WPN_STAGE_BOLTRELEASE 16 "0" }
	
	{ event AE_WPN_STAGE_MAGIN 1 "0" }
	
	{ event AE_WPN_FULLCLIP1 17 }
	
	fadein 0.2
	fadeout 0.2
	snap
	fps 60
}
```

## Pose Parameter

We use pose parameters to make sure the gun's bolt stays locked back even when switching weapons

the pose parameter is called `pose_depleted`

### `Example Pose Parameter`

```
$poseparameter "pose_depleted" 0 1 loop 0

$animation "a_normal" "v_1911_anims\idle.smd" {
  frames 0 0
}

$animation "a_empty" "v_1911_anims\slide_poses.smd" {
  frames 1 1
  subtract "a_normal" 0
}

$animation "a_full" "v_1911_anims\slide_poses.smd" {
  frames 0 0
  subtract "a_normal" 0
}

$sequence "empty_power" {
  "a_full"
  "a_empty"
  autoplay
  blend "pose_depleted" 0 1
  blendwidth 2
  delta
  fadein 0.2
  fadeout 0.2
  hidden
}
``` 

## Weapon And Magazine BodyGroups

for the bodygroups on the magazine to work properly

we need to setup propper bodygroups

for the weapons itself
and for the magzine

### `Example BodyGroups`

the following is a properly setup bodygroup for the weapon viewmodel

```
$bodygroup Weapon
{
	studio "mesh.smd"
}

$bodygroup magazine
{
	studio "mag.smd"
	blank
}
```

!!! Note
	
	the Mesh of the gun needs to be made withouth a magazine on it
	and the mag needs to be a mesh withouth a weapon on it 
	
	you can use your normal mesh to do this


## Info

for more help with `Staged Reloads` join the [HDTF Discord server](https://discord.gg/hdtf) to ask for Help on the subject
