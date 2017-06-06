Unity Shader 使用笔记一
==========================================
* Shader流程
    ##### ![](https://github.com/fankidark/blog/blob/master/assets/shader/shader_process.png)

* Shader语法
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

* Shader分类
    * [1] 基本管线
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
    * [2] 顶点面片着色器 - 常用的着色器
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
        * 面片着色器 - 常用的着色器
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
    * [3] 表面着色器 - Unity封装的着色器
        ```
        CGPROGRAM
            #pragma surface surf Lambert
            void surf (Input IN, inout SurfaceOutput o) 
            {
                o.Albedo = (1, 1, 1);
            }
        ENDCG
        ```
