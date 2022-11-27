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

    注意三角函数的参数一定要是float型：

    > 别被这些红框框给吓到了，两个报错都是同一个原因，参数类型不对。shadertoy上，只要你定义的是float型，那参数一定要是float型，不然就直接no matching overloaded function found。报错的时候没意识到这个问题，我还以为明明就是个普通的内置函数，怎么就突然报没有重载的方法了，为次我还去看了其他人写的代码，都还没发现问题所在，最后万念俱灰也不知为何将后面的return返回值的1.0-t改成了1-t，红通通的报错内容才打醒了我，原来是参数类型不对！！！！smoothstep方法一定要浮点型！！不会帮你自己转换！！！就算你是0也好，也得写成0.0！！！
    > ————————————————
    > 版权声明：本文为CSDN博主「ssssssilver」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
    > 原文链接：https://blog.csdn.net/ssssssilver/article/details/81129441

    

11. 像素坐标的小数点陷阱

    ```
    color = cos(fragCoord.x * PI);
    ```

    这样，你以为color会变成1, -1, 1, -1, 1, -1, ...这样，其实不会。实际上，Color是0, 0, 0, 0, 0, 0, ...。

    为什么呢？因为fragColor.x是0.5, 1.5, 2.5, 3.5, ...这样子，而不是0, 1, 2, 3, ...这样子。像素的中心在零点五这个位置，而不是0这个位置！

    正确的写法：

    ```
    color = cos(floor(fragCoord.x) * PI);
    ```

    

12. 啊啊啊