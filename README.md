# unity-triplanar-terrain-cliff-shader
drop-in replacement shader to add triplanar cliff shading to the default Unity terrain system (no plugins, vanilla terrain system, built-in 3D pipeline)

(image)

## description
- this shader adds a single albedo + bump map as a general "cliff" texture, to tile along the X and Z planes
- as an optimization, I didn't add Y plane sampling, so technically this is more biplanar than triplanar
- as an optimization, I didn't add smoothness / metalness / occlusion maps
    - instead, I sample some approximate smoothness / occlusion from the cliff's albedo
    - I recommend against adding extra texture map slots, because you have to sample each texture map twice (once for X plane, once for Z plane) so it's a bit expensive

## compatibility
- for built-in 3D pipeline only (maybe you can modify it for HDRP / URP? but I'm not going to port it, sorry)
- made for Unity 2021.2+ but probably mostly works for Unity 2019.3+ / 2020.x? but haven't tested it, sorry

## usage / install
1. put the `/TriplanarTerrainShaders/` folder in your Unity `/Assets/` folder
2. create a new Material that uses the `/Nature/Terrain/StandardTriplanar` shader
3. in your Terrain object settings, assign the material from step 2
4. configure cliff albedo + normal map in the material (not as a terrain layer)

## implementation

This tries to replace as little as possible in the built-in Unity shaders. It's basically just 2 files:

- `TerrainSplatmapTriplanar.cginc` is just a copy of `TerrainSplatmapCommon.cginc` from Unity 2021.2.0f1 built-in shaders, except it adds `worldPos` and `worldNormal` to the `Input` shader struct. Unity's shader magic automatically populates IN.worldPos and IN.worldNormal variables if they're defined, so there's nothing else to do there.
- `Terrain-TriplanarStandard-FirstPass.shader` has various cliff texture properties, hooks into TerrainSplatmapTriplanar instead of the default TerrainSplatmapCommon, and then applies triplanar mapping to the terrain mesh in the `surf()` function. You should probably tweak some of the hardcoded values.

When this inevitably breaks in future Unity versions, you can easily patch this shader yourself by following those steps above, and copy and pasting the relevant sections. You'll need to know a little about shaders to do that. I recommend Catlike Coding's shader tutorials for a good intro to Unity shaders.

## license
public domain / unlicense / cc0

I'm happy if this helps you and I'm happy to share, but I'm not really going to maintain this repo or pay much attention to requests, sorry

textures (not included) are from ambientCG.com
