---
title: URP 接入 Geometric Specular Anti-aliasing
date: 2025-12-1 00:00:00 +0800
categories: 
tags:
---

HDRP/Lit中是带了这项功能
![[Pasted image 20251201171744.png]]




```
//shader..
[Toggle_Switch] _ENABLE_GEOMETRIC_SPECULAR_AA("高光抗锯齿", float) = 0  
[Switch(_ENABLE_GEOMETRIC_SPECULAR_AA)] _SpecularAAScreenSpaceVariance("SpecularAAScreenSpaceVariance", Range(0.0, 1.0)) = 0.1  
[Switch(_ENABLE_GEOMETRIC_SPECULAR_AA)] _SpecularAAThreshold("SpecularAAThreshold", Range(0.0, 1.0)) = 0.2


..修改smoothness
surfaceData.smoothness = GeometricNormalFiltering(surfaceData.smoothness, inputData.normalWS, _SpecularAAScreenSpaceVariance, _SpecularAAThreshold);

```



![[Pasted image 20251201172132.png]]


还可以这样做
```
half3 GlassGlobalIllumination(BRDFData brdfData, BRDFData brdfDataClearCoat, float clearCoatMask,
half3 bakedGI, half occlusion, float3 positionWS,
half3 normalWS, half3 viewDirectionWS)
{

    half3 reflectVector = reflect(-viewDirectionWS, normalWS);
    half NoV = saturate(dot(normalWS, viewDirectionWS));

    // TODO 改成Pow5

    half fresnelTerm = Pow4(1.0 - NoV) * (1.0 - NoV);
    // 抗锯齿奇淫技巧
    #define _SpecularAAThreshold 1
    fresnelTerm = GeometricNormalFiltering(fresnelTerm, normalWS, _SpecularAAScreenSpaceVariance, _SpecularAAThreshold);
    fresnelTerm = saturate(fresnelTerm);
    half3 indirectDiffuse = bakedGI;
    // 间接镜面反射自定义
    half3 indirectSpecular = GlassGlossyEnvironmentReflection(reflectVector, positionWS, brdfData.perceptualRoughness, 1.0h);
    half3 color = EnvironmentBRDF(brdfData, indirectDiffuse, indirectSpecular, fresnelTerm);
    return color * occlusion;
}
```