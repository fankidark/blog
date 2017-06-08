# 裁剪说明

[Unity Shader基本用法](https://github.com/fankidark/blog/blob/master/themes/shaderlab/shader_note001.md)

##### ![](https://github.com/fankidark/blog/blob/master/assets/shaderlab/shader_process_01.png)

## Cull
- Cull Back | Front | Off
- Controls which sides of polygons should be culled (not drawn)
- Back Don’t render polygons facing away from the viewer (default).
- Front Don’t render polygons facing towards the viewer. Used for turning objects inside-out.
- Off Disables culling - all faces are drawn. Used for special effects.
- 简单说明：剔除哪个面哪个面就不显示

## ZWrite
- ZWrite On | Off
- Controls whether pixels from this object are written to the depth buffer (default is On). If you’re drawng solid objects, leave this on. If you’re drawing semitransparent effects, switch to ZWrite Off. For more details read below.
- 简单说明：是否把深度写入深度缓存

## ZTest
- ZTest Less | Greater | LEqual | GEqual | Equal | NotEqual | Always
- How should depth testing be performed. Default is LEqual (draw objects in from or at the distance as existing objects; hide objects behind them).
- 简单说明：深度测试类型

## Offset
- Offset Factor, Units
- Allows you specify a depth offset with two parameters. factor and units. Factor scales the maximum Z slope, with respect to X or Y of the polygon, and units scale the minimum resolvable depth buffer value. This allows you to force one polygon to be drawn on top of another although they are actually in the same position. For example Offset 0, –1 pulls the polygon closer to the camera ignoring the polygon’s slope, whereas Offset –1, –1 will pull the polygon even closer when looking at a grazing angle.
- 简单说明：深度偏移(解决z-fighting的方法)
