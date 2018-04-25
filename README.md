

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

SDF:
>SdBox ...  
OpU OpS ...

File specification:
>.Math.cginc :some math functions and macros   
.Hash.cginc : hash function  
.Noise.cginc : Noise functions eg:PerlinNoise,ValueNoise,SimplexNoise,VoronoiNoise,TriNoise  
.FBM.cginc: Fbm functions  
.Feature.cginc: some special function : Caustic,Stars,Cloud,Fog ...    
.SDF.cginc :some distance function use for modeling  
.Framework3D.cginc  :a raymarching framework   
.Framework3D_DefaultScene.cginc : use for quickly create a Test scene   


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


A simple scene example:  
<p align="center">
<img src="https://github.com/JiepengTan/JiepengTan.github.io/blob/master/assets/img/blog/ShaderTutorial3D/BounceBall/head.gif?raw=true" width="512"></p> 


```c
// create by JiepengTan 2018-04-14 
// email: jiepengtan@gmail.com
Shader "FishManShaderTutorial/SDFBounceBall" {
    Properties{
        _MainTex("Base (RGB)", 2D) = "white" {}
    }
    SubShader{
        Pass {
            ZTest Always Cull Off ZWrite Off
            CGPROGRAM

#pragma vertex vert   
#pragma fragment frag  

#define DEFAULT_PROCESS_FRAG
#define DEFAULT_RENDER
#include "ShaderLibs/Framework3D_DefaultRender.cginc"
            
            fixed SdBounceBalls(fixed3 pos){
                fixed SIZE = 2.;
                fixed2 gridSize = fixed2(SIZE,SIZE);
                fixed rv = Hash12( floor((pos.xz) / gridSize));
                pos.xz = OpRep(pos.xz,gridSize);
                fixed bollSize = 0.1;
                fixed bounceH = .5;
                return SdSphere(pos- fixed3(0.,(bollSize+bounceH+sin(_Time.y*3.14 + rv*6.24)*bounceH),0.),bollSize);
            }
            // user define mat shading function 
            fixed3 MatCol(float matID,float3 pos,float3 nor)
            { 
                // material        
                fixed3 col = 0.45 + 0.35*sin( fixed3(0.05,0.08,0.10)*(matID-1.0) );
                if( matID<1.5 )
                {       
                    fixed f = CheckersGradBox( 5.0*pos.xz );
                    col = 0.3 + f*fixed3(0.1,0.1,0.1);
                }
                return col;
            }
            // user define scene construct function
            fixed2 Map( in fixed3 pos )
            {
                fixed2 res = fixed2( SdPlane(     pos), 1.0 )  ;
                res = OpU( res, fixed2( SdBounceBalls( pos),1.) );
                return res;
            }

            ENDCG
        }//end pass
    }//end SubShader
    FallBack Off
}

```

More examples:
<p align="center">
<img src="https://github.com/JiepengTan/JiepengTan.github.io/blob/master/assets/img/blog/ShaderTutorial3D/SDF/head.gif?raw=true" width="512"></p> 

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
  [3]: https://blog.csdn.net/tjw02241035621611