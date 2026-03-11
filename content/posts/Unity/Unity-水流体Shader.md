---
title: "Unity-水流体Shader"
date: 2026-02-08T22:48:15+08:00

tags:
  - Unity

categories:
  - Unity

cover:
  image: https://blog-1308267163.cos.ap-shanghai.myqcloud.com/imgs/202211192022487.jpg
---

# 水流体Shader

```
Shader "UI/WaterWave"
{
    Properties
    {
        [PerRendererData] _MainTex ("Sprite Texture", 2D) = "white" {}
        _Color ("Tint", Color) = (1,1,1,1)

        _StencilComp ("Stencil Comparison", Float) = 8
        _Stencil ("Stencil ID", Float) = 0
        _StencilOp ("Stencil Operation", Float) = 0
        _StencilWriteMask ("Stencil Write Mask", Float) = 255
        _StencilReadMask ("Stencil Read Mask", Float) = 255
        _ColorMask ("Color Mask", Float) = 15

        [Toggle(UNITY_UI_ALPHACLIP)] _UseUIAlphaClip ("Use Alpha Clip", Float) = 0

        // 水波参数
        _WaterHeight ("Water Height", Range(0, 1)) = 0.5
        _WaveSpeed ("Wave Speed", Float) = 1.0
        _WaveFrequency ("Wave Frequency", Float) = 5.0
        _WaveAmplitude ("Wave Amplitude", Range(0, 0.1)) = 0.02
        _WaveOffset ("Wave Offset", Float) = 0.0
        _WaveRange ("Wave Range", Range(0, 0.2)) = 0.05
    }

    SubShader
    {
        Tags
        {
            "Queue"="Transparent"
            "IgnoreProjector"="True"
            "RenderType"="Transparent"
            "PreviewType"="Plane"
            "CanUseSpriteAtlas"="True"
        }

        Stencil
        {
            Ref [_Stencil]
            Comp [_StencilComp]
            Pass [_StencilOp]
            ReadMask [_StencilReadMask]
            WriteMask [_StencilWriteMask]
        }

        Cull Off
        Lighting Off
        ZWrite Off
        ZTest [unity_GUIZTestMode]
        Blend SrcAlpha OneMinusSrcAlpha
        ColorMask [_ColorMask]

        Pass
        {
            Name "Default"
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag
            #pragma target 2.0

            #include "UnityCG.cginc"
            #include "UnityUI.cginc"

            #pragma multi_compile_local _ UNITY_UI_CLIP_RECT
            #pragma multi_compile_local _ UNITY_UI_ALPHACLIP

            struct appdata_t
            {
                float4 vertex   : POSITION;
                float4 color    : COLOR;
                float2 texcoord : TEXCOORD0;
            };

            struct v2f
            {
                float4 vertex   : SV_POSITION;
                fixed4 color    : COLOR;
                float2 texcoord : TEXCOORD0;
                float4 worldPosition : TEXCOORD1;
            };

            sampler2D _MainTex;
            fixed4 _Color;
            fixed4 _TextureSampleAdd;
            float4 _ClipRect;
            float4 _MainTex_ST;

            float _WaterHeight;
            float _WaveSpeed;
            float _WaveFrequency;
            float _WaveAmplitude;
            float _WaveOffset;
            float _WaveRange;

            v2f vert(appdata_t v)
            {
                v2f o;
                o.worldPosition = v.vertex;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.texcoord = TRANSFORM_TEX(v.texcoord, _MainTex);
                o.color = v.color * _Color;
                return o;
            }

            fixed4 frag(v2f i) : SV_Target
            {
                // ---------- 水波计算 ----------
                float distanceToSurface = abs(i.texcoord.y - _WaterHeight);

                float wave = 0;
                float2 waveUV = i.texcoord;

                if (distanceToSurface < _WaveRange)
                {
                    float t = _Time.y * _WaveSpeed + _WaveOffset;
                    float x = i.texcoord.x * _WaveFrequency;

                    float w1 = sin(x + t) * _WaveAmplitude;
                    float w2 = sin(x * 1.5 + t * 0.8) * _WaveAmplitude * 0.6;
                    wave = w1 + w2;

                    float influence = smoothstep(_WaveRange, 0, distanceToSurface);
                    waveUV.y += wave * influence;
                }

                half4 col = tex2D(_MainTex, waveUV);

                // ---------- 水面软裁剪（无锯齿核心） ----------
                float waterLevel = saturate(_WaterHeight + wave);

                // 自适应软边缘（跟分辨率有关）
                float edge = fwidth(i.texcoord.y) * 2.0;

                float alphaMask = smoothstep(
                    waterLevel + edge,
                    waterLevel - edge,
                    i.texcoord.y
                );

                col.a *= alphaMask;

                // ---------- 水面高光 ----------
                float surfaceDist = abs(i.texcoord.y - waterLevel);
                float highlight = smoothstep(0.03, 0.0, surfaceDist);
                col.rgb += highlight * 0.15;

                // ---------- UI 标准处理 ----------
                col += _TextureSampleAdd;
                col *= i.color;

                #ifdef UNITY_UI_CLIP_RECT
                col.a *= UnityGet2DClipping(i.worldPosition.xy, _ClipRect);
                #endif

                #ifdef UNITY_UI_ALPHACLIP
                clip(col.a - 0.001);
                #endif

                return col;
            }
            ENDCG
        }
    }

    Fallback "UI/Default"
}
```

