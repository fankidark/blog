# [ShaderLab 可编程着色器](https://docs.unity3d.com/Manual/SL-ShaderPrograms.html)

* [Unity Shader基本用法](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_note001.md)

##### ![](https://github.com/fankidark/blog/blob/master/assets/shaderlab/shader_process_01.png)

## Shader例子(生成的不受光的Shader去掉Fog的实现)

    Shader "VertexFragment"
    {
        // 属性定义
        Properties
        {
            _MainTex ("Texture", 2D) = "white" {}
        }
        SubShader
        {
            // 设置标签
            Tags { "RenderType"="Opaque" }
            // 设置LOD等级
            LOD 100
            Pass
            {
                // cg 程序
                CGPROGRAM
                // 定义顶点着色器函数名为vert
                #pragma vertex vert
                // 定义片元着色器函数名为frag
                #pragma fragment frag
                // 导入unity自带的cg头文件
                #include "UnityCG.cginc"
                // 定义应用程序输入
                struct appdata
                {
                    float4 vertex : POSITION;
                    float2 uv : TEXCOORD0;
                };
                // 定义vert输出frag
                struct v2f
                {
                    float2 uv : TEXCOORD0;
                    float4 vertex : SV_POSITION;
                };
                sampler2D _MainTex;
                float4 _MainTex_ST;
                v2f vert (appdata v)
                {
                    v2f o;
                    // UNITY_INITIALIZE_OUTPUT在"HLSLSupport.cginc","UnityCG.cginc"引用了"HLSLSupport.cginc"
                    UNITY_INITIALIZE_OUTPUT(v2f, o);
                    o.vertex = mul(UNITY_MATRIX_MVP, v.vertex);
                    // TRANSFORM_TEX在"UnityCG.cginc"
                    o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                    return o;
                }
                fixed4 frag (v2f i) : SV_Target
                {
                    // tex2D"HLSLSupport.cginc","UnityCG.cginc"引用了"HLSLSupport.cginc"
                    fixed4 col = tex2D(_MainTex, i.uv);
                    return col;
                }
                ENDCG
            }
        }
    }
    
## 自定义着色器关键字
- HLSL snippets
    - At the start of the snippet compilation directives can be given as #pragma statements. Directives indicating which shader functions to compile:
        - **#pragma vertex** name - compile function name as the vertex shader.
        - **#pragma fragment** name - compile function name as the fragment shader.
        - **#pragma geometry** name - compile function name as DX10 geometry shader. Having this option automatically turns on #pragma target 4.0, described below.
        - **#pragma hull** name - compile function name as DX11 hull shader. Having this option automatically turns on #pragma target 5.0, described below.
        - **#pragma domain** name - compile function name as DX11 domain shader. Having this option automatically turns on #pragma target 5.0, described below.
    - Other compilation directives:
        - **#pragma target name** - which shader target to compile to. See Shader Compilation Targets page for details.
        - **#pragma only_renderers** space separated names - compile shader only for given renderers. By default shaders are compiled for all renderers. See Renderers below for details.
        - **#pragma exclude_renderers** space separated names - do not compile shader for given renderers. By default shaders are compiled for all renderers. See Renderers below for details.
        - **#pragma multi_compile** … - for working with multiple shader variants.
        - **#pragma enable_d3d11_debug_symbols** - generate debug information for shaders compiled for DirectX 11, this will allow you to debug shaders via Visual Studio 2012 (or higher) Graphics debugger.
        - **#pragma hardware_tier_variants** renderer name - generate multiple shader hardware variants of each compiled shader, for each hardware tier that could run the selected renderer. See Renderers below for details.

## 注意事项
- 实现部分在CGPROGRAM和ENDCG之间或者CGINCLUDE和ENDCG之间。
- 定义顶点着色器和片元着色器要成对定义缺一不可。
- 尽可能不用使用if和for等关键字。
- 变量类型要注意，颜色、单位向量、尽量少用float。

...
