# ShaderLab 固定管线(**透明实现会有渲染排序问题**)

* [Unity Shader基本用法](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_note001.md)

## Shader 例子
    // 这是固定管线的Shader
    Shader "FixedShader" {
    Properties {
        _Color ("Main Color", Color) = (1,1,1,0)
        _SpecColor ("Spec Color", Color) = (1,1,1,1)
        _Emission ("Emmisive Color", Color) = (0,0,0,0)
        _Shininess ("Shininess", Range (0.01, 1)) = 0.7
        _MainTex ("Base (RGB)", 2D) = "white" {}
    }
    SubShader {
        Pass {
            // 设置顶点颜色
            Color[_Color]
            // 设置材质 (Lighting On)
            Material {
                // 漫反射效果，Lighting On才能正常使用，不然是黑的，因为没有光
                Diffuse [_Color]
                // 环境光效果，Lighting On才能正常使用
                Ambient [_Color]
                // 自发光效果，Lighting On才能正常使用
                Emission [_Emission]
                // 高光集中程度，数值越大越集中，越光滑
                Shininess [_Shininess]
                // 高光效果, Lighting On和SeparateSpecular On才能正常使用
                Specular [_SpecColor]
            }
            Lighting On                         // 默认Off
            SeparateSpecular On                 // 默认Off
            // 设置纹理
            SetTexture [_MainTex] {
                Combine texture * primary DOUBLE, texture * primary
            }
        }
    }

## 语法
1. 设置颜色
    - Color[_Color]
    - Color(1, 1, 1, 1)
2. 设置灯光属性 (==**旧版本**==)
    - Material { ... }
        - Diffuse color: The diffuse color component. This is an object’s base color.
        - Ambient color: The ambient color component. This is the color the object has when it’s hit by the ambient light set in the Lighting Window.
        - Specular color: The color of the object’s specular highlight.
        - Shininess number: The sharpness of the highlight, between 0 and 1. At 0 you get a huge highlight that looks a lot like diffuse lighting, at 1 you get a tiny speck.
        - Emission color: The color of the object when it is not hit by any light.
        
        The full color of lights hitting the object is:
        - Ambient * Lighting Window’s Ambient Intensity setting + (Light Color * Diffuse + Light Color * Specular) + Emission
    - Lighting On | Off
    - SeparateSpecular On | Off
    - ColorMaterial AmbientAndDiffuse | Emission
3. 设置纹理 (==**旧版本**==)
    - SetTexture [TextureName] {Texture Block}
    - Texture Block
        - Combine
            - Combine 语法
                - combine src1 * src2: Multiplies src1 and src2 together. The result will be darker than either input.
                - combine src1 + src2: Adds src1 and src2 together. The result will be lighter than either input.
                - combine src1 - src2: Subtracts src2 from src1.
                - combine src1 lerp (src2) src3: Interpolates between src3 and src1, using the alpha of src2. Note that the interpolation is opposite direction: src1 is used when alpha is one, and src3 is used when alpha is zero.
                - combine src1 * src2 + src3: Multiplies src1 with the alpha component of src2, then adds src3.
            - All the src properties can be either one of previous, constant, primary or texture.
                - Previous is the the result of the previous SetTexture.
                - Primary is the color from the lighting calculation or the vertex color if it is bound.
                - Texture is the color of the texture specified by TextureName in the SetTexture (see above).
                - Constant is the color specified in ConstantColor.
            - Modifiers:
                - The formulas specified above can optionally be followed by the keywords Double or Quad to make the resulting color 2x or 4x as bright.
                - All the src properties, except lerp argument, can optionally be preceded by one - to make the resulting color negated.
                - All the src properties can be followed by alpha to take only the alpha channel.
        - constantColor [_Color]
        - matrix [unity_LightmapMatrix]
4. 绑定通道 (==**旧版本**==)
    - BindChannels { Bind "source", target }
    - Source can be one of:
        - Vertex: vertex position
        - Normal: vertex normal
        - Tangent: vertex tangent
        - Texcoord: primary UV coordinate
        - Texcoord1: secondary UV coordinate
        - Color: per-vertex color
    - Target can be one of:
        - Vertex: vertex position
        - Normal: vertex normal
        - Tangent: vertex tangent
        - Texcoord0, Texcoord1, …: texture coordinates for corresponding texture stage
        - Texcoord: texture coordinates for all texture stages
        - Color: vertex color

## 深入了解
- Unity的固定管线Shader可以通过Inspectot中的Show generated code来查看对应的cg shader，这是很好的研究方法。
- 以下所诉的固定管线命令都不会在Vertex Fragment Shader中起作用（至少在最新版本的Unity中是这样的）
- 以下所述也许会有所偏差，有疑惑就去Show generated code看看对应得Veretx & Fragment Shader

## unity自带的使用固定管线实现的Shader
    Shader "Legacy Shaders/Transparent/VertexLit" {
        Properties {
            _Color ("Main Color", Color) = (1,1,1,1)
            _SpecColor ("Spec Color", Color) = (1,1,1,0)
            _Emission ("Emissive Color", Color) = (0,0,0,0)
            _Shininess ("Shininess", Range (0.1, 1)) = 0.7
            _MainTex ("Base (RGB) Trans (A)", 2D) = "white" {}
        }
        SubShader {
            Tags {"Queue"="Transparent" "IgnoreProjector"="True" "RenderType"="Transparent"}
            LOD 100
            ZWrite Off
            Blend SrcAlpha OneMinusSrcAlpha 
            ColorMask RGB
            // Non-lightmapped
            Pass {
                Tags { "LightMode" = "Vertex" }
                Material {
                    Diffuse [_Color]
                    Ambient [_Color]
                    Shininess [_Shininess]
                    Specular [_SpecColor]
                    Emission [_Emission]    
                }
                Lighting On
                SeparateSpecular On
                SetTexture [_MainTex] {
                    Combine texture * primary DOUBLE, texture * primary
                } 
            }
            // Lightmapped, encoded as dLDR
            Pass {
                Tags { "LightMode" = "VertexLM" }
                
                BindChannels {
                    Bind "Vertex", vertex
                    Bind "normal", normal
                    Bind "texcoord1", texcoord0 // lightmap uses 2nd uv
                    Bind "texcoord", texcoord1 // main uses 1st uv
                }
                SetTexture [unity_Lightmap] {
                    matrix [unity_LightmapMatrix]
                    constantColor [_Color]
                    combine texture * constant
                }
                SetTexture [_MainTex] {
                    combine texture * previous DOUBLE, texture * primary
                }
            }
            // Lightmapped, encoded as RGBM
            Pass {
                Tags { "LightMode" = "VertexLMRGBM" }
                BindChannels {
                    Bind "Vertex", vertex
                    Bind "normal", normal
                    Bind "texcoord1", texcoord0 // lightmap uses 2nd uv
                    Bind "texcoord1", texcoord1 // unused
                    Bind "texcoord", texcoord2 // main uses 1st uv
                }
                SetTexture [unity_Lightmap] {
                    matrix [unity_LightmapMatrix]
                    combine texture * texture alpha DOUBLE
                }
                SetTexture [unity_Lightmap] {
                    constantColor [_Color]
                    combine previous * constant
                }
                SetTexture [_MainTex] {
                    combine texture * previous QUAD, texture * primary
                }
            }
        }
    }

...
