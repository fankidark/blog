# [ShaderLab 表面着色器 Surface](https://docs.unity3d.com/Manual/SL-SurfaceShaders.html)

* [Unity Shader基本用法](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_note001.md)

## Shader例子(生成的Shader)

    Shader "Surface" {
        Properties {
            _Color ("Color", Color) = (1,1,1,1)
            _MainTex ("Albedo (RGB)", 2D) = "white" {}
            _Glossiness ("Smoothness", Range(0,1)) = 0.5
            _Metallic ("Metallic", Range(0,1)) = 0.0
        }
        SubShader {
            Tags { "RenderType"="Opaque" }
            LOD 200
            CGPROGRAM
            // Physically based Standard lighting model, and enable shadows on all light types
            #pragma surface surf Standard fullforwardshadows
            // Use shader model 3.0 target, to get nicer looking lighting
            #pragma target 3.0
            sampler2D _MainTex;
            struct Input {
                float2 uv_MainTex;
            };
            half _Glossiness;
            half _Metallic;
            fixed4 _Color;
            void surf (Input IN, inout SurfaceOutputStandard o) {
                // Albedo comes from a texture tinted by color
                fixed4 c = tex2D (_MainTex, IN.uv_MainTex) * _Color;
                o.Albedo = c.rgb;
                // Metallic and smoothness come from slider variables
                o.Metallic = _Metallic;
                o.Smoothness = _Glossiness;
                o.Alpha = c.a;
            }
            ENDCG
        }
        FallBack "Diffuse"
    }

## 表面着色器语法
- ==#pragma surface surfaceFunction lightModel [optionalparams]==
    - Required parameters
        - surfaceFunction - which Cg function has surface shader code. The function should have the form of void surf (Input IN, inout SurfaceOutput o), where Input is a structure you have defined. Input should contain any texture coordinates and extra automatic variables needed by surface function.
        - lightModel - lighting model to use. Built-in ones are physically based Standard and StandardSpecular, as well as simple non-physically based Lambert (diffuse) and BlinnPhong (specular). See [Custom Lighting Models](https://docs.unity3d.com/Manual/SL-SurfaceShaderLighting.html) page for how to write your own.
            - Standard lighting model uses SurfaceOutputStandard output struct, and matches the Standard (metallic workflow) shader in Unity.
            - StandardSpecular lighting model uses SurfaceOutputStandardSpecular output struct, and matches the Standard (specular setup) shader in Unity.
            - Lambert and BlinnPhong lighting models are not physically based (coming from Unity 4.x), but the shaders using them can be faster to render on low-end hardware.
    - Optional parameters
        - Transparency and alpha testing
            - alpha or alpha:auto - Will pick fade-transparency (same as alpha:fade) for simple lighting functions, and premultiplied transparency (same as alpha:premul) for physically based lighting functions.
            - alpha:blend - Enable alpha blending.
            - alpha:fade - Enable traditional fade-transparency.
            - alpha:premul - Enable premultiplied alpha transparency.
            - alphatest:VariableName - Enable alpha cutout transparency. Cutoff value is in a float variable with VariableName. You’ll likely also want to use addshadow directive to generate proper shadow caster pass.
            - keepalpha - By default opaque surface shaders write 1.0 (white) into alpha channel, no matter what’s output in the Alpha of output struct or what’s returned by the lighting function. Using this option allows keeping lighting function’s alpha value even for opaque surface shaders.
            - decal:add - Additive decal shader (e.g. terrain AddPass). This is meant for objects that lie atop of other surfaces, and use additive blending. See [Surface Shader Examples](https://docs.unity3d.com/Manual/SL-SurfaceShaderExamples.html)
            - decal:blend - Semitransparent decal shader. This is meant for objects that lie atop of other surfaces, and use alpha blending. See [Surface Shader Examples](https://docs.unity3d.com/Manual/SL-SurfaceShaderExamples.html)
        - Custom modifier functions
            - vertex:VertexFunction - Custom vertex modification function. This function is invoked at start of generated vertex shader, and can modify or compute per-vertex data. See [Surface Shader Examples](https://docs.unity3d.com/Manual/SL-SurfaceShaderExamples.html).
            - finalcolor:ColorFunction - Custom final color modification function. See [Surface Shader Examples](https://docs.unity3d.com/Manual/SL-SurfaceShaderExamples.html).
            - finalgbuffer:ColorFunction - Custom deferred path for altering gbuffer content.
            - finalprepass:ColorFunction - Custom prepass base path.
        - Shadows and Tessellation
            - addshadow - Generate a shadow caster pass. Commonly used with custom vertex modification, so that shadow casting also gets any procedural vertex animation. Often shaders don’t need any special shadows handling, as they can just use shadow caster pass from their fallback.
            - fullforwardshadows - Support all light shadow types in [Forward](https://docs.unity3d.com/Manual/RenderTech-ForwardRendering.html) rendering path. By default shaders only support shadows from one directional light in forward rendering (to save on internal shader variant count). If you need point or spot light shadows in forward rendering, use this directive.
            - tessellate:TessFunction - use DX11 GPU tessellation; the function computes tessellation factors. See [Surface Shader Tessellation](https://docs.unity3d.com/Manual/SL-SurfaceShaderTessellation.html) for details.
        - Code generation options
            - exclude_path:deferred, exclude_path:forward, exclude_path:prepass - Do not generate passes for given rendering path ([Deferred Shading](https://docs.unity3d.com/Manual/RenderTech-DeferredShading.html), [Forward](https://docs.unity3d.com/Manual/RenderTech-ForwardRendering.html) and [Legacy Deferred](https://docs.unity3d.com/Manual/RenderTech-DeferredLighting.html) respectively).
            - noshadow - Disables all shadow receiving support in this shader.
            - noambient - Do not apply any ambient lighting or light probes.
            - novertexlights - Do not apply any light probes or per-vertex lights in Forward rendering.
            - nolightmap - Disables all lightmapping support in this shader.
            - nodynlightmap - Disables runtime dynamic global illumination support in this shader.
            - nodirlightmap - Disables directional lightmaps support in this shader.
            - nofog - Disables all built-in Fog support.
            - nometa - Does not generate a “meta” pass (that’s used by lightmapping & dynamic global illumination to extract surface information).
            - noforwardadd - Disables Forward rendering additive pass. This makes the shader support one full directional light, with all other lights computed per-vertex/SH. Makes shaders smaller as well.
            - nolppv - Disables Light Probe Proxy Volume support in this shader.
        - Miscellaneous options
            - softvegetation - Makes the surface shader only be rendered when Soft Vegetation is on.
            - interpolateview - Compute view direction in the vertex shader and interpolate it; instead of computing it in the pixel shader. This can make the pixel shader faster, but uses up one more texture interpolator.
            - halfasview - Pass half-direction vector into the lighting function instead of view-direction. Half-direction will be computed and normalized per vertex. This is faster, but not entirely correct.
            - approxview - Removed in Unity 5.0. Use interpolateview instead.
            - dualforward - Use [dual lightmaps](https://docs.unity3d.com/Manual/GIIntro.html) in [forward](https://docs.unity3d.com/Manual/RenderTech-ForwardRendering.html) rendering path.
    - Surface Shader input structure
        - float3 viewDir - contains view direction, for computing Parallax effects, rim lighting etc.
        - float4 with COLOR semantic - contains interpolated per-vertex color.
        - float4 screenPos - contains screen space position for reflection or screenspace effects. Note that this is not suitable for GrabPass; you need to compute custom UV yourself using ComputeGrabScreenPos function.
        - float3 worldPos - contains world space position.
        - float3 worldRefl - contains world reflection vector if surface shader does not write to o.Normal. See Reflect-Diffuse shader for example.
        - float3 worldNormal - contains world normal vector if surface shader does not write to o.Normal.
        - float3 worldRefl; INTERNAL_DATA - contains world reflection vector if surface shader writes to o.Normal. To get the reflection vector based on per-pixel normal map, use WorldReflectionVector (IN, o.Normal). See Reflect-Bumped shader for example.
        - float3 worldNormal; INTERNAL_DATA - contains world normal vector if surface shader writes to o.Normal. To get the normal vector based on per-pixel normal map, use WorldNormalVector (IN, o.Normal).

## 注意事项
- 实现部分在CGPROGRAM和ENDCG之间或者CGINCLUDE和ENDCG之间。
- #pragma surface surf 光照函数

...
