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

    

12. pow函数

    ```
    float result = pow(x, y);
    ```

    这段代码可以计算x的y次方。

    注意：pow函数不支持负数的n次方，也不支持一个数的0次方或者负次方。

    > 那么，为什么pow(x, y)函数如此奇怪呢？如果你仔细看这个函数的文档说明，你会发现这个函数会返回一个undefined如果x小于0或者x等于0或者y小于等于0。
    >
    > Source: https://copyfuture.com/blogs-details/20211102100119806G

    如果需要做负数的整数次方，可以用下面这段代码（有bug，等下会讲如何修复）：

    ```
    // Only support integral y values, but support negative x values.
    float intPow(float x, int y){
        float result = 1.0f;
        while(true){
            result *= x;
            y -= 1;
            if(y == 0){
                break;
            }
        }
        return result;
    }
    ```

    上面的代码经常崩溃，一开始以为是GPU算力不够，结果后来发现不是：

    原来，上面代码运算0次方时会崩溃，应该把if条件改成y <= 0，并且加入0次方时结果为1的预检验：

    ```
    // Only support integral y values, but support negative x values.
    float intPow(float x, int y){
        if(y == 0){
            return 1.0f;
        }
        
        float result = 1.0f;
        while(true){
            result *= x;
            y -= 1;
            if(y <= 0){
                break;
            }
        }
        return result;
    }
    ```

    这样之后，我们就可以用负一的n次方的运算规律来制作Blinker了：

    ```
    // Only support integral y values, but support negative x values.
    float intPow(float x, int y){
        if(y == 0){
            return 1.0f;
        }
        
        float result = 1.0f;
        while(true){
            result *= x;
            y -= 1;
            if(y <= 0){
                break;
            }
        }
        return result;
        
    }
    
    void mainImage( out vec4 fragColor, in vec2 fragCoord )
    {
        vec2 uv = fragCoord.xy / iResolution.xy;
        float color;
        for(int i = 0; i < 1; i++){
            color = float(intPow(-1.0f, int(fragCoord.x)));
        }
        fragColor = vec4(color, color, color, 1.0);
    }
    ```

    把MainImage的For循环次数从1修改成1000，会发现明显的性能下降，FPS从75调到12。

    于是我用下面的代码优化负一的n次方运算：

    ```
    // Only support integral y values, and only support x=-1.
    int neg1Pow(int y){
        return y % 2 == 1 ? -1 : 1;
    }
    
    // Only support integral y values, but support negative x values.
    float intPow(float x, int y){
        
        // Power of -1's speed optimization when x ~= -1.
        if(abs(x + 1.0f) < 0.0005f){
            return float(neg1Pow(y));
        }
        
        if(y == 0){
            return 1.0f;
        }
        
        float result = 1.0f;
        while(true){
            result *= x;
            y -= 1;
            if(y <= 0){
                break;
            }
        }
        return result;
        
    }
    
    void mainImage( out vec4 fragColor, in vec2 fragCoord )
    {
        vec2 uv = fragCoord.xy / iResolution.xy;
        float color;
        for(int i = 0; i < 1000; i++){
            color = float(intPow(-1.0f, int(fragCoord.x)));
        }
        fragColor = vec4(color, color, color, 1.0);
    }
    ```

    优化过后，同样的1000次循环，FPS仍然稳定在75。

    接着，我们继续优化，抽象出一个通用的，优化过的pow函数，比自带的pow函数范围更广：

    ```
    // Only support integral y values, and only support x=-1.
    int neg1Pow(int y){
        return y % 2 == 1 ? -1 : 1;
    }
    
    // Only support integral y values, but support negative x values.
    float intPow(float x, int y){
        
        // Power of -1's speed optimization when x ~= -1.
        if(abs(x + 1.0f) < 0.0005f){
            return float(neg1Pow(y));
        }
        
        if(y == 0){
            return 1.0f;
        }
        
        float result = 1.0f;
        while(true){
            result *= x;
            y -= 1;
            if(y <= 0){
                break;
            }
        }
        return result;
    }
    
    // Support more cases than pow():
    // Case 1: x > 0 and y > 0 (the case that pow() supports)
    // Case 2: x < 0 and y is an integer (the extra case)
    float universalPow(float x, float y){
    
        // Integral y and negative x case speed optimization.
        if( (abs(y - floor(y + 0.5f)) < 0.0005f) && x < 0.0f){
            return intPow(x, int(y));
        }
        else{
            return pow(x, y);
        }
    }
    
    void mainImage( out vec4 fragColor, in vec2 fragCoord )
    {
        vec2 uv = fragCoord.xy / iResolution.xy;
        float color;
        for(int i = 0; i < 1000; i++){
            color = float(intPow(-1.0f, int(fragCoord.x)));
        }
        fragColor = vec4(color, color, color, 1.0);
    }
    ```

    

13. 四舍五入

    ```
    floor(f+0.5)
    ```

    

14. 啊啊啊

