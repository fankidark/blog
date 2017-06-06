# ShaderLab 可编程着色器

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

## 注意事项
- 实现部分在CGPROGRAM和ENDCG之间或者CGINCLUDE和ENDCG之间。
- 定义顶点着色器和片元着色器要成对定义缺一不可。
- 尽可能不用使用if和for等关键字。
- 变量类型要注意，颜色、单位向量、尽量少用float。

...
