---
title: FMOD Studio
description: "explaining how FMOD Studio in HDTF WORKS"
hero: "SOUND: FMOD Studio"
og_title: "SOUND: FMOD Studio"
page_path: misc/
og_image: "images/sound/logic_fmod_music.png"
---

# FMOD Studio
<figure markdown>
  ![FMOD STUDIO APP](../images/fmod/FMOD_Studio_zQmmlfwVZP.png){ width="1920" }
  <figcaption>FMOD STUDIO APP showcase</figcaption>
</figure>



FMOD is a proprietary sound effects engine and authoring tool for video games and applications developed by Firelight Technologies, that play and mix sounds of diverse formats on many operating systems.

!!! note

    we implemented FMOD Studio inside of Hunt Down the freeman to allow us the following 

    - ***the use of other file formats like .ogg***
    - ***the abbilty to allow for dynamic music***
    - ***the use of more up to date tools***
    - ***and many more things***

# logic_fmod_music
<figure markdown>
 ![](../images/fmod/logic_fmod_music.png){width="200" loading=lazy }
 <figcaption>FMOD Enitity icon used by hammer</figcaption>
</figure>
we curently have a new entity inside of Hunt Down the Freeman

its called `logic_fmod_music`

we use this entity to allow us to edit paramteres with hammer I/O with paramteres that are defined inside of FMOD Studio

the entity has the following parameters

- BankFile - this referes to the bank file that you want to use located inside of `sound\fmod`

!!! note
    
    THIS PAGE IS A HEAVY WIP MORE STUFF WILL BE ADDED TO IT IN THE FUTURE