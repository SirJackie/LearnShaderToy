# Learn ShaderToy

1. 获取归一化uv坐标

   ```
   vec2 uv = fragCoord.xy / iResolution.xy;
   ```

   

2. 返回像素颜色

   ```
   fragColor = vec4(1.0, 1.0, 1.0, 1.0);
   ```

   

3. 最简单变色程序

   ```
   void mainImage( out vec4 fragColor, in vec2 fragCoord )
   {
       // Normalized pixel coordinates (from 0 to 1)
       vec2 uv = fragCoord.xy / iResolution.xy;
   
       // Time varying pixel color
       vec3 col = 0.5 + 0.5 * cos(iTime + uv.xyx + vec3(0, 2, 4));
   
       // Output to screen
       fragColor = vec4(col, 1.0);
   }
   ```

   

4. vec2每个分量都加减一个float

   ```
   vec2var -= 0.5;
   ```

   

5. 获取vec2长度

   ```
   float len = length(vec2var);
   ```

   

6. 标量扩充到三分量向量

   ```
   float c = 1.0;
   fragColor = vec4(vec3(c), 1.0);
   ```

   

7. if-else语句

   这会造成uncontinueaty from plato to plato，不推荐使用。

   ```
   if(d < 0.6) c = 1.0; else c = 0.0;
   ```

   ```
   if(d < 0.6){
   	c = 1.0;
   }
   else{
   	c = 0.0;
   }
   ```

   

8. Aspect Ratio修正

   ```
   uv.x *= iResolution.x / iResolution.y;
   ```

   

9. Smooth Step函数进行平滑过渡

   ```
   float d = length(uv);
   float r = 0.3;
   float c = smoothstep(r, r - 0.01, d);
   ```

   

10. 绝对值和三角函数

    ```
    float r = 0.5 * abs(sin(iTime)) * 0.5;
    ```

    

11. 啊啊啊