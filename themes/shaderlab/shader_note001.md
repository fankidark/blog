Unity Shader 使用笔记一
==========================================
* Shader流程
    ##### ![](https://github.com/fankidark/blog/blob/master/assets/shaderlab/shader_process.png)

* Shader语法
    - [Property说明](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_lab_property.md)
    - [Tag说明](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_lab_tag.md)
    ``` shader
    Shader "Shader Index Name"
    {
        // 定义属性
        Property
        {
        }
        SubShader
        {
            // 设置一些定义
            Tag {}
            Pass
            {
                // Pass的独立设置一些定义
                Tag{}
            }
            Pass { ... }
        }
        // 返回其他Shader的处理
        Fallback "Fallback Shader"
        // 编辑器相关的显示
        CustomEditor "StandardShaderGUI"
    }
    ```

* Shader分类
    * [1] [固定管线](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_lab_lan_fixed_pipeline.md)
        ``` shader
        Pass {
            // Set up basic white vertex lighting
            Material {
                Diffuse (1,1,1,1)
                Ambient (1,1,1,1)
            }
            Lighting On
            // Use texture alpha to blend up to white (= full illumination)
            SetTexture [_MainTex] {
                constantColor (1,1,1,1)
                //注意这里，由于是第一个SetTexture，previous的值是光照计算的结果值
                combine constant lerp(texture) previous 
            }
            // Multiply in texture
            SetTexture [_MainTex] {
                combine previous * texture
            }
        }
        ```
    * [2] [可编程着色器 - 常用的着色器](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_lab_lan_vf.md)
        * 顶点着色器
            ``` shader
            Pass{
                CGPROGRAM
                    #pragma vertex vert
                    v2f vert(appdata v)
                    {
                        v2f o;
                        UNITY_INITIALIZE_OUTPUT(v2f, o);
                        return o;
                    }
                ENDCG
            }
            ```
        * 面片着色器
            ``` shader
            Pass{
                CGPROGRAM
                    #pragma fragment frag
                    fixed4 frag (v2f i) : SV_Target
                    {
                        return (1, 1, 1, 1);
                    }
                ENDCG
            }
            ```
    * [3] 表面着色器 - Unity封装的着色器
        ``` shader
        CGPROGRAM
            #pragma surface surf Lambert
            void surf (Input IN, inout SurfaceOutput o) 
            {
                o.Albedo = (1, 1, 1);
            }
        ENDCG
        ```

## 暂不细说的功能部分
- [ShaderLab: Legacy Fog](https://docs.unity3d.com/Manual/SL-Fog.html)
- [ShaderLab: Legacy Alpha Testing](https://docs.unity3d.com/Manual/SL-AlphaTest.html)
- [ShaderLab: UsePass](https://docs.unity3d.com/Manual/SL-UsePass.html)
- [ShaderLab: GrabPass](https://docs.unity3d.com/Manual/SL-GrabPass.html)
- [ShaderLab: Stencil](https://docs.unity3d.com/Manual/SL-Stencil.html)

...
