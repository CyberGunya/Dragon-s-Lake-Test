WARNING!!!
========
 I understood, that Framework files were forbidden to change. However, header "SDL_image.h" included if file "launch.cpp" in my system is not in SDL library. This header is in separate library SDL_image. Without this change Framework refused to compile at all, so I had to do it. But, this is the only change that I made to files "launch.cpp" and "Framework.h". Hope for your understanding)
=========

This game is made with all mandatory requirements and has all four optional tasks.
========
1) Turret is moving randomly left/right and shooting two bullets, if player is in turret sight area.
2) Blue blocks are moving up and down.
3) Abilities are implemented. Red abilities are negative, green are positive.
4) Abilities are spawned from destroyed blocks, with 20% probability for each block. After player caught the ability, texture of the platform changes. If current speed is higher than default - platform says "Fast", if lower - "Slow". If abilities are matched and they neutralise each other so the current speed is the same as default - platform has 3 blue dots on it.
=======

