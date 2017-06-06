# ShaderLab: Properties

* [Unity Shader基本用法](https://github.com/fankidark/blog/blob/master/themes/shader/shader_note001.md)

## Numbers and Sliders
- name ("display name", Range (min, max)) = number
- name ("display name", Float) = number
- name ("display name", Int) = number

## Colors and Vectors
- name ("display name", Color) = (number,number,number,number)
- name ("display name", Vector) = (number,number,number,number)

## Textures
- name ("display name", 2D) = "defaulttexture" {}
- name ("display name", Cube) = "defaulttexture" {}
- name ("display name", 3D) = "defaulttexture" {}

## Details
- For Range and Float properties it’s just a single number, for example “13.37”.
- For Color and Vector properties it’s four numbers in parentheses, for example “(1,0.5,0.2,1)”.
- For 2D Textures, the default value is either an empty string, or one of the built-in default Textures: “white” (RGBA: 1,1,1,1), “black” (RGBA: 0,0,0,0), “gray” (RGBA: 0.5,0.5,0.5,0.5), “bump” (RGBA: 0.5,0.5,1,0.5) or “red” (RGBA: 1,0,0,0).
- For non–2D Textures (Cube, 3D, 2DArray) the default value is an empty string. When a Material does not have a Cubemap/3D/Array Texture assigned, a gray one (RGBA: 0.5,0.5,0.5,0.5) is used.

## Property attributes and drawers
- [HideInInspector] - does not show the property value in the material inspector.
- [NoScaleOffset] - material inspector will not show texture tiling/offset fields for texture properties with this attribute.
- [Normal] - indicates that a texture property expects a normal-map.
- [HDR] - indicates that a texture property expects a high-dynamic range (HDR) texture.
- [Gamma] - indicates that a float/vector property is specified as sRGB value in the UI (just like colors are), and possibly needs conversion according to color space used. [See Properties in Shader Programs](https://docs.unity3d.com/Manual/SL-PropertiesInPrograms.html).
- [PerRendererData] - indicates that a texture property will be coming from per-renderer data in the form of a [MaterialPropertyBlock](https://docs.unity3d.com/ScriptReference/MaterialPropertyBlock.html). Material inspector changes the texture slot UI for these properties.

...
