

# Unity-Raymarching-Framework
A framework to easy implement raymarching in unity. Include lots of hash,noise,fbm,SDF,rotate functions,most of them come from shadertoy,with this library,you can easy write raymarching shader in unity without rewrite the noise function again.    

also,this framework provide a easy way to merge your raymarching scene with unity scene.    

Include
Noise:
>1.PNoise :Perlin Noise  
2.VNoise :Value Noise  
3.SNoise :Simplex Noise   
4.WNoise :Worley Noise (Voronoi)  
4.TNoise :TriNoise  

Hash
>//  x out, x in...  
HashXX  
eg:
float2 Hash22(float2 p)   
means 2 in 2 out  

FBM  
>float FBM( float2 p )  
float FBM( float2 p,float iterNum)  

Rot:
>Rot2D  
Rot3D  

With this framework,you can easy merge raymarching scene with Unity ,and easy  to walk around in it ,without rewrite a "SetCamera" function to init ro,rd variable.

Here is a example:
<p align="center">
<img src="https://github.com/JiepengTan/JiepengTan.github.io/blob/master/assets/img/blog/Show/MergeRaymarchExample.gif?raw=true" width="660"></p>   

```c
Shader "FishManShaderTutorial/RaymarchMergeExample" {
    Properties{
        _MainTex("Base (RGB)", 2D) = "white" {}
    }
    SubShader{
        Pass {
            ZTest Always Cull Off ZWrite Off
            CGPROGRAM
#pragma vertex vert   
#pragma fragment frag  
#include "ShaderLibs/Framework3D.cginc"
            float4 ProcessRayMarch(float2 uv,float3 ro,float3 rd,inout float sceneDep,float4 sceneCol)  {
                float3 col = float3(0.,0.,0.);
                float3 n = float3(0.,1.0,0.);
                float t = sceneDep + 10;
                float occ = 0.;
                float3 sc =  float3(0.,0.5,0.);
                float3 sr = 0.5;
                float3 ce = sc - ro;
                float b = dot( rd, ce );
                float tt = sr*sr - (dot( ce, ce )- b*b );
                if( tt > 0.0 ){
                    t = b - sqrt(tt);
                    float3 p = ro+t*rd;
                    col = 0.5+0.5*cos(2.*PI*(float3(1.,1.,1.)*p.y*0.2+float3(0.,0.33,0.67)));
                }
                MergeRayMarchingIntoUnity(t,col, sceneDep,sceneCol);
                return float4(col,1.0);
            } 
            ENDCG
        }//end pass
    }//end SubShader
    FallBack Off
}
```



More examples:
 <p align="center">
<img src="https://github.com/JiepengTan/JiepengTan.github.io/blob/master/assets/img/blog/Show/Fog.gif?raw=true" width="512"></p> 

<p align="center">
<img src="https://github.com/JiepengTan/JiepengTan.github.io/blob/master/assets/img/blog/Show/HpUI.gif?raw=true" width="512"></p> 

<p align="center">
<img src="https://github.com/JiepengTan/JiepengTan.github.io/blob/master/assets/img/blog/Show/LakeInHighland.gif?raw=true" width="512"></p> 


- [More Examples][1]
- [My shadertoy link][2]
- [My blog][3]

  [1]: https://github.com/JiepengTan/FishManShaderTutorial
  [2]: https://www.shadertoy.com/user/FishMan
  [3]: https://jiepengtan.github.io/