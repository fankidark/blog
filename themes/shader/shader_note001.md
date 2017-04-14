Unity Shader 使用笔记一
==========================================
* [shader流程](#shader流程)
    ##### ![](/assets/shader/shader_process.png)
* [shader语法](#shader语法)
```
Shader "Shader Index Name"
{
    Property
    {
    }
    SubShader
    {
        Tag {}
        Pass
        {
            Tag{}
        }
        Pass {}
    }
    Fallback "Fallback Shader"
}
```
* [shader分类](#shader分类)
    * [顶点着色器](#顶点着色器) - 常用的着色器
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
    * [面片着色器](#面片着色器) - 常用的着色器
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
    * [表面着色器](#表面着色器) - Unity封装的着色器
        ```
        CGPROGRAM
            #pragma surface surf Lambert
            void surf (Input IN, inout SurfaceOutput o) 
            {
                o.Albedo = (1, 1, 1);
            }
        ENDCG
        ```
