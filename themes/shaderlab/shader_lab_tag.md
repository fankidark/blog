# ShaderLab: Pass Tags

[Unity Shader基本用法](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_note001.md)

## Syntax
- Tags { "TagName1" = "Value1" "TagName2" = "Value2" }
    ```
    e.g. Tags { "Queue" = "Transparent" }
    ```

## 1. [ShaderLab: SubShader Tags](https://docs.unity3d.com/Manual/SL-SubShaderTags.html)
- ==Rendering Order - Queue tag==
    - Background - this render queue is rendered before any others. You’d typically use this for things that really need to be in the background.
    - Geometry (default) - this is used for most objects. Opaque geometry uses this queue.
    - AlphaTest - alpha tested geometry uses this queue. It’s a separate queue from Geometry one since it’s more efficient to render alpha-tested objects after all solid ones are drawn.
    - Transparent - this render queue is rendered after Geometry and AlphaTest, in back-to-front order. Anything alpha-blended (i.e. shaders that don’t write to depth buffer) should go here (glass, particle effects).
    - Overlay - this render queue is meant for overlay effects. Anything rendered last should go here (e.g. lens flares).
- ==RenderType tag==
    - RenderType tag categorizes shaders into several predefined groups, e.g. is is an opaque shader, or an alpha-tested shader etc. This is used by [Shader Replacement](https://docs.unity3d.com/Manual/SL-ShaderReplacement.html) and in some cases used to produce [camera’s depth texture](https://docs.unity3d.com/Manual/SL-CameraDepthTexture.html).
- ==DisableBatching tag==
    - Some shaders (mostly ones that do object-space vertex deformations) do not work when [Draw Call Batching](https://docs.unity3d.com/Manual/DrawCallBatching.html) is used – that’s because batching transforms all geometry into world space, so “object space” is lost.

    - DisableBatching tag can be used to incidate that. There are three possible values: “True” (always disables batching for this shader), “False” (does not disable batching; this is default) and “LODFading” (disable batching when LOD fading is active; mostly used on trees).
- ==ForceNoShadowCasting tag==
    - If ForceNoShadowCasting tag is given and has a value of “True”, then an object that is rendered using this subshader will never cast shadows. This is mostly useful when you are using shader replacement on transparent objects and you do not wont to inherit a shadow pass from another subshader.
- ==IgnoreProjector tag==
    - If IgnoreProjector tag is given and has a value of “True”, then an object that uses this shader will not be affected by [Projectors](https://docs.unity3d.com/Manual/class-Projector.html). This is mostly useful on semitransparent objects, because there is no good way for Projectors to affect them.
- ==CanUseSpriteAtlas tag==
    - Set CanUseSpriteAtlas tag to “False” if the shader is meant for sprites, and will not work when they are packed into atlases (see [Sprite Packer](https://docs.unity3d.com/Manual/SpritePacker.html)).
- ==PreviewType tag==
    - PreviewType indicates how the material inspector preview should display the material. By default materials are displayed as spheres, but PreviewType can also be set to “Plane” (will display as 2D) or “Skybox” (will display as skybox).

## 2. [ShaderLab: Pass Tags](https://docs.unity3d.com/Manual/SL-PassTags.html)
- ==LightMode tag==
    - Always: Always rendered; no lighting is applied.
    - ForwardBase: Used in [Forward rendering](https://docs.unity3d.com/Manual/RenderTech-ForwardRendering.html), ambient, main directional light, vertex/SH lights and lightmaps are applied.
    - ForwardAdd: Used in [Forward rendering](https://docs.unity3d.com/Manual/RenderTech-ForwardRendering.html); additive per-pixel lights are applied, one pass per light.
    - Deferred: Used in [Deferred Shading](https://docs.unity3d.com/Manual/RenderTech-DeferredShading.html); renders g-buffer.
    - ShadowCaster: Renders object depth into the shadowmap or a depth texture.
    - MotionVectors: Used to calculate per-object motion vectors.
    - PrepassBase: Used in [legacy Deferred Lighting](https://docs.unity3d.com/Manual/RenderTech-DeferredLighting.html), renders normals and specular exponent.
    - PrepassFinal: Used in [legacy Deferred Lighting](https://docs.unity3d.com/Manual/RenderTech-DeferredLighting.html), renders final color by combining textures, lighting and emission.
    - Vertex: Used in [legacy Vertex Lit rendering](https://docs.unity3d.com/Manual/RenderTech-VertexLit.html) when object is not lightmapped; all vertex lights are applied.
    - VertexLMRGBM: Used in [legacy Vertex Lit rendering](https://docs.unity3d.com/Manual/RenderTech-VertexLit.html) when object is lightmapped; on platforms where lightmap is RGBM encoded (PC & console).
    - VertexLM: Used in [legacy Vertex Lit rendering](https://docs.unity3d.com/Manual/RenderTech-VertexLit.html) when object is lightmapped; on platforms where lightmap is double-LDR encoded (mobile platforms).
- ==PassFlags tag==
    - A pass can indicate flags that change how [rendering pipeline](https://docs.unity3d.com/Manual/SL-RenderPipeline.html) passes data to it. This is done by using **PassFlags** tag, with a value that is space-separated flag names. Currently the flags supported are:

    - OnlyDirectional: When used in ForwardBase pass type, this flag makes it so that only the main directional light and ambient/lightprobe data is passed into the shader. This means that data of non-important lights is not passed into vertex-light or spherical harmonics shader variables. See [Forward rendering](https://docs.unity3d.com/Manual/RenderTech-ForwardRendering.html) for details.
- RequireOptions tag
    - A pass can indicate that it should only be rendered when some external conditions are met. This is done by using **RequireOptions** tag, whose value is a string of space separated options. Currently the options supported by Unity are:

    - SoftVegetation: Render this pass only if Soft Vegetation is on in [Quality Settings](https://docs.unity3d.com/Manual/class-QualitySettings.html).
