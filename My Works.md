# Cosine nPi Stripes2

 https://www.shadertoy.com/view/mdlSDB 

```
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord.xy / iResolution.xy;
    float PI = 3.1415926;
    float color = cos(floor(fragCoord.x * 0.01f + iTime) * PI);
    fragColor = vec4(color, color, color, 1.0);
}
```

# Cosine nPi Swinger

 https://www.shadertoy.com/view/ddlSWB 

```
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord.xy / iResolution.xy;
    float PI = 3.1415926;
    float color = cos(floor(fragCoord.x) * PI);
    fragColor = vec4(color, color, color, 1.0);
}
```

# Power of Negative One Stripes

 https://www.shadertoy.com/view/mssXWB 

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
    float color = float(intPow(-1.0f, int(fragCoord.x * 0.01f + iTime)));
    fragColor = vec4(color, color, color, 1.0);
}
```

# Power of Negative One Swinger

 https://www.shadertoy.com/view/mslSWB 

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
    float color = float(intPow(-1.0f, int(fragCoord.x)));
    fragColor = vec4(color, color, color, 1.0);
}
```

# SirJackie's First Shader

 https://www.shadertoy.com/view/mslGDl 

```
void mainImage( out vec4 fragColor, in vec2 fragCoord )
{
    vec2 uv = fragCoord.xy / iResolution.xy;
    uv -= 0.5;
    uv.x *= iResolution.x / iResolution.y;
    float d = length(uv);
    float r = 0.5 * abs(sin(iTime)) * 0.5;
    float c = smoothstep(r, r - 0.005, d);
    
    fragColor = vec4(vec3(c), 1.0);
}
```

