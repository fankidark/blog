Unity Shader 使用笔记一
==========================================
* Shader流程
    ##### ![](https://github.com/fankidark/blog/blob/master/assets/shaderlab/shader_process.png)
    ##### ![](https://github.com/fankidark/blog/blob/master/assets/shaderlab/shader_process_01.png)
* Shader语法
    - [Property说明](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_lab_property.md)
    - [Tag说明](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_lab_tag.md)
    - [Culling & Depth Testing说明](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_lab_culling.md)
    - [Blend混合说明](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_lab_blend.md)
    ```
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
        ```
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
            ```
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
            ```
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
    * [3] [表面着色器 - Unity封装的着色器](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_lab_lan_surface.md)
        ```
        CGPROGRAM
            #pragma surface surf Lambert
            void surf (Input IN, inout SurfaceOutput o) 
            {
                o.Albedo = (1, 1, 1);
            }
        ENDCG
        ```

## 需要了解的点
- Render Queue
- ZWrite
- ZTest
- Blend

## 暂不细说的功能部分
- [ShaderLab: Legacy Fog](https://docs.unity3d.com/Manual/SL-Fog.html)
- [ShaderLab: Legacy Alpha Testing](https://docs.unity3d.com/Manual/SL-AlphaTest.html)
- [ShaderLab: UsePass](https://docs.unity3d.com/Manual/SL-UsePass.html)
- [ShaderLab: GrabPass](https://docs.unity3d.com/Manual/SL-GrabPass.html)
- [ShaderLab: Stencil](https://docs.unity3d.com/Manual/SL-Stencil.html)

...

## 一些补充
- [经常使用的一些函数](http://www.cppblog.com/lai3d/archive/2008/10/23/64889.html)
- [Material Capture](http://blog.csdn.net/poem_qianmo/article/details/55803629)
- [Shader Keyword](https://docs.unity3d.com/Manual/SL-MultipleProgramVariants.html)
- 变量类型([官方建议](https://docs.unity3d.com/Manual/SL-ShaderPerformance.html))
    - float：高精度浮点值，通常是32位，也是三者中最慢的一个。
    - half：中精度浮点值。通常是16位，范围是-60000至+60000，它适合存储UV坐标，颜色值等，比float类型快很多。
    - fixed：低精度浮点值。通常是11位，范围是-2.0至+2.0，精度为1/256。这是三者中最小的一个，可以用于光照计算、颜色等。

## 补充资料和书籍
- [Unity官网手册](https://docs.unity3d.com/Manual/SL-Reference.html)
- Cg教程 (http://pan.baidu.com/s/1mhRjwiW) 75uf
- Unity Shaders and Effects Cookbook (http://pan.baidu.com/s/1o8SbxOI) 0mio
- Unity Shader入门精要 (http://pan.baidu.com/s/1pLynmnL) zthy

...
