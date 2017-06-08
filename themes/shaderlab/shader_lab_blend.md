# ShaderLab: Blending - 混合模式

* [Unity Shader基本用法](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_note001.md)

- 在Unity中颜色的混合表达式是这样的：float4 result = SrcFactor * fragment_output + DstFactor * pixel_color;  其中fragment_output 是fragment shader计算出的最终值，pixel_color是当前帧缓存的颜色。result 就是最终混合的颜色值。
    * ##### ![](https://github.com/fankidark/blog/blob/master/assets/shaderlab/shader_blend_01.png)
    * Additive Blending：Blend One One
    * 对应的表达式：float4 result = float4(1.0) * fragment_output + float4(1.0) * pixel_color;  
    * Additive Blending这种混合模式常常在写粒子Shader的时候用到。

## Syntax
- ==Blend Off: Turn off blending (this is the default)==
- Blend SrcFactor DstFactor: Configure and enable blending. The generated color is multiplied by the SrcFactor. The color already on screen is multiplied by DstFactor and the two are added together.
- ==Blend SrcFactor DstFactor==, SrcFactorA DstFactorA: Same as above, but use different factors for blending the alpha channel.
- BlendOp Op: Instead of adding blended colors together, carry out a different operation on them.
- BlendOp OpColor, OpAlpha: Same as above, but use different blend operation for color (RGB) and alpha (A) channels.
- Additionally, you can set upper-rendertarget blending modes. When using multiple render target (MRT) rendering, the regular syntax above sets up the same blending modes for all render targets. The following syntax can set up different blending modes for individual render targets, where N is the render target index (0..7). This feature works on most modern APIs/GPUs (DX11/12, GLCore, Metal, PS4):
    - Blend N SrcFactor DstFactor
    - Blend N SrcFactor DstFactor, SrcFactorA DstFactorA
    - BlendOp N Op
    - BlendOp N OpColor, OpAlpha

## Blend operations
| | |
| :---------- | :------- |
| Add | Add source and destination together. |
| Sub | Subtract destination from source. |
| RevSub | Subtract source from destination. |
| Min | Use the smaller of source and destination. |
| Max | Use the larger of source and destination. |
| LogicalClear | Logical operation: Clear (0) DX11.1 only. |
| LogicalSet | Logical operation: Set (1) DX11.1 only. |
| LogicalCopy | Logical operation: Copy (s) DX11.1 only. |
| LogicalCopyInverted | Logical operation: Copy inverted (!s) DX11.1 only. |
| LogicalNoop | Logical operation: Noop (d) DX11.1 only. |
| LogicalInvert | Logical operation: Invert (!d) DX11.1 only. |
| LogicalAnd | Logical operation: And (s & d) DX11.1 only. |
| LogicalNand | Logical operation: Nand !(s & d) DX11.1 only. |
| LogicalOr | Logical operation: Or (s | d) DX11.1 only. |
| LogicalNor | Logical operation: Nor !(s | d) DX11.1 only. |
| LogicalXor | Logical operation: Xor (s ^ d) DX11.1 only. |
| LogicalEquiv | Logical operation: Equivalence !(s ^ d) DX11.1 only. |
| LogicalAndReverse | Logical operation: Reverse And (s & !d) DX11.1 only. |
| LogicalAndInverted | Logical operation: Inverted And (!s & d) DX11.1 only. |
| LogicalOrReverse | Logical operation: Reverse Or (s | !d) DX11.1 only. |
| LogicalOrInverted | Logical operation: Inverted Or (!s | d) DX11.1 only. |

## Blend factors
| | |
| :---------- | :------- |
| One | The value of one - use this to let either the source or the destination color come through fully. |
| Zero | The value zero - use this to remove either the source or the destination values. |
| SrcColor | The value of this stage is multiplied by the source color value. |
| SrcAlpha | The value of this stage is multiplied by the source alpha value. |
| DstColor | The value of this stage is multiplied by frame buffer source color value. |
| DstAlpha | The value of this stage is multiplied by frame buffer source alpha value. |
| OneMinusSrcColor | The value of this stage is multiplied by (1 - source color). |
| OneMinusSrcAlpha | The value of this stage is multiplied by (1 - source alpha). |
| OneMinusDstColor | The value of this stage is multiplied by (1 - destination color). |
| OneMinusDstAlpha | The value of this stage is multiplied by (1 - destination alpha). |
