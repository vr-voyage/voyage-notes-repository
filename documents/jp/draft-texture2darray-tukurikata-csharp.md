# Texture2DArrayとは

Texture2DArrayはShaderで使えるテクスチャの配列です。
名前の通り、テクスチャの数枚を含める配列です。
その配列は、Shaderで（そしてMaterialとね）一つのテクスチャ・ユニットとして使うことが出来ます。

# 生成方法

Unity・2020以下なら、公式文章に書いている通り、テクスチャ配列を生成するために、
C#のAPIを使うことが必要です」。

https://docs.unity3d.com/560/Documentation/Manual/SL-TextureArrays.html
> **Creating and manipulating texture arrays**  
> As there is no texture import pipeline for texture arrays, they must be created from within your scripts. Use the Texture2DArray class to create and manipulate them. Note that texture arrays can be serialized as assets, so it is possible to create and fill them with data from editor scripts.

使っているTexture2DArrayを生成するスクリプトを紹介します：

```csharp
#if UNITY_EDITOR

using UnityEngine;
using UnityEditor;

using UnityEngine.Experimental.Rendering;

namespace Myy
{
    public class TextureArrayGenerator : EditorWindow
    {
        SerializedObject serialO;
        SerializedProperty texturesSerialized;
        SerializedProperty textureNameSerialized;
        
        public string textureName;
        public Texture2D[] textures;

        private void OnEnable()
        {
            serialO = new SerializedObject(this);
            texturesSerialized = serialO.FindProperty("textures");
            textureNameSerialized = serialO.FindProperty("textureName");
        }

        [MenuItem("Voyage / Texture Array Generator")]
        public static void ShowWindow()
        {
            GetWindow(typeof(TextureArrayGenerator), false, "Texture Array Generator");
        }

        private void OnGUI()
        {
            bool everythingOK = true;
            serialO.Update();

            EditorGUILayout.PropertyField(textureNameSerialized);
            
            EditorGUILayout.PropertyField(texturesSerialized, true);
            serialO.ApplyModifiedProperties();

            if (!everythingOK) return;

            if (GUILayout.Button("Texture2DArrayを生成する"))
            {
                Texture2D modelTexture = textures[0];
                int texturesCount = textures.Length;

                TextureCreationFlags flags = 
                    (modelTexture.mipmapCount > 1 ? 
                        TextureCreationFlags.MipChain :
                        TextureCreationFlags.None);

                Texture2DArray array = new Texture2DArray(
                    modelTexture.width, modelTexture.height, texturesCount,
                    modelTexture.graphicsFormat,
                    flags);

                int nMipmaps = Mathf.Min(array.mipmapCount, textures[0].mipmapCount);
                for (int t = 0; t < texturesCount; t++)
                {
                    Texture2D currentTexture = textures[t];
                    for (int mipMapLevel = 0; mipMapLevel < nMipmaps; mipMapLevel++)
                    {
                        Graphics.CopyTexture(textures[t], 0, mipMapLevel, array, t, mipMapLevel);
                    }
                }

                AssetDatabase.CreateAsset(array, $"Assets/{textureName}.asset");
            }
        }
    }
}
#endif
```

## スクリプトの分析

本体のコードは：
```csharp
Texture2D modelTexture = textures[0];
int texturesCount = textures.Length;

TextureCreationFlags flags = 
    (modelTexture.mipmapCount > 1 ? 
        TextureCreationFlags.MipChain :
        TextureCreationFlags.None);

Texture2DArray array = new Texture2DArray(
    modelTexture.width, modelTexture.height, texturesCount,
    modelTexture.graphicsFormat,
    flags);

int nMipmaps = Mathf.Min(array.mipmapCount, textures[0].mipmapCount);
for (int t = 0; t < texturesCount; t++)
{
    Texture2D currentTexture = textures[t];
    for (int mipMapLevel = 0; mipMapLevel < nMipmaps; mipMapLevel++)
    {
        Graphics.CopyTexture(textures[t], 0, mipMapLevel, array, t, mipMapLevel);
    }
}

AssetDatabase.CreateAsset(array, $"Assets/{textureName}.asset");
```

そして、その配列を生成しているコードはこれです：  
```csharp
TextureCreationFlags flags = 
    (modelTexture.mipmapCount > 1 ? 
        TextureCreationFlags.MipChain :
        TextureCreationFlags.None);

Texture2DArray array = new Texture2DArray(
    modelTexture.width, modelTexture.height, texturesCount,
    modelTexture.graphicsFormat,
    flags);
```

`Texture2DArray(int width, int height, int count, ..., ...)`は`Texture2DArray`
の複数のシグネチャの一つです。  
他のシグネチャを使いましたが、テクスチャのフォーマットが違うことになったり、
ミップマップがなくなったり、色んな問題に遭いましたから、紹介しているだけのをお勧めします。

`TextureCreationFlags`で：
  * `MipChain`は有効します。しないと、ミップマップをコピーできなくなります。  
    ミップマップがないと、テクスチャを遠くから観ると、がチカチカになります。
  * `Crunch`は無視します。  
    そのフラグは「Crunch Compression」の圧倒アルゴリズムを有効しますが、
     Texture2DArrayを生成するAPIで使えません。

> https://docs.unity3d.com/ScriptReference/Texture2DArray-ctor.html  
> **Texture2DArray Constructor**  
> Note that this class does not support Texture2DArray creation
> with a Crunch compression TextureFormat.

## シェイダーで使用方法

### 簡単な置換

Texture2Dの代わりにTexture2DArrayを使いたいなら、
つぎの文字（？）を置換して：

| 所　　　　　　　　　 | テクスチャ　      | テクスチャ配列
| Properties　|　`2D`　       |　`2DArray`
| Subshader　　|　`sampler2D`　| `UNITY_DECLARE_TEX2DARRAY`
| Subshader　　|　`tex2D`     | `UNITY_SAMPLE_TEX2DARRAY`

後は、次のPragmaを追加して
`#pragma require 2darray`
`#pragma target 3.5`

### 添字次第

UNITY_SAMPLE_TEX2DARRAYの最後の引数は`float3(u,v,添字)`です。

添字を決める方法はいくらでも想像できますが、普段で使われている方法は：

* プロパティー
* 色のチャネル
* 3DUV

#### プロパティー

一番の簡単な方法、そしてInstanced Renderingに相応しい方法です。

同じマテリアルを使っている物に、MaterialPropertyBlockで、
その添字のプロパティーを決めることが出来ます。  
すると、Unityはその物をメッシュで分配して、同じメッシュを使っているオブジェクトを
一気に描きます（Batching）。

Shaderで、次のプロパティーを追加して：

`_Soeji("添え字", Range(0, 最大の枚数)) = 0`
`最大の枚数`を数字で変わって。

後は、Subshaderで同じ名前の`float`を設定して、

```shader
    Subshader {
    
        /* ... */
        
        float _Soeji;
        
        float4 _MainTex_ST;
        
        /* ... */
        
        void surf(...) {
            
            float3 arrayCoords = float3(_MainTex_ST.u, _MainTex_ST.v, _Soeji);
            
            /* ... */
            
            UNITY_SAMPLE_TEX2DARRAY(配列サンプラー名、arrayCoords); 
        }
        
    }
```

`frag`、または`surf`でその添え字を使って、`float3`


UNITY_SAMPLE_TEX2DARRAY


Shaderのコードで、`UNITY_SAMPLE_TEX2DARRAY(2DArraySampler, float3UV)`と言うマクロで、配列から一枚のテクスチャをサンプリングできます。
テクスチャの配列のメリットは、数枚のテクスチャを一つのテクスチャ・ユニットとして使えることです。
そこは「Atlas」とほぼ同じです。

テクスチャの配列にある一枚を使うために、Shaderで索引を決めることが必要です。
索引される一枚は普通のテクスチャの一枚と使えます。
そのため、「Atlas」に比べると、タイリングに向いています。

