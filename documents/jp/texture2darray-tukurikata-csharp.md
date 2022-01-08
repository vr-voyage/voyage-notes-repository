# Texture2DArrayとは

Texture2DArrayはShaderで使えるテクスチャの配列です。
名前の通り、テクスチャの数枚を含める配列です。
その配列は、Shaderで（そしてMaterialとね）一つのテクスチャ・ユニットとして使うことが出来ます。

# 生成方法

Unity・2020以下なら、公式文章に書いている通り「C#のAPIを使うことが必要です」。

https://docs.unity3d.com/560/Documentation/Manual/SL-TextureArrays.html
> **Creating and manipulating texture arrays**  
> As there is no texture import pipeline for texture arrays, they must be created from within your scripts. Use the Texture2DArray class to create and manipulate them. Note that texture arrays can be serialized as assets, so it is possible to create and fill them with data from editor scripts.

自分が使っている、Texture2DArrayを生成するのエディター・スクリプト・コードを紹介します：

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

## 例えの分析

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
でも、他のを使って、生成されるテクスチャのフォーマットが違うことになったり、ミップマップがなくなることになったり、色んな問題に会いましたから、紹介しているだけをお勧めします。

`TextureCreationFlags`の`Crunch`は"Crunch Compression"の圧倒アルゴリズムを有効するフラグですが、Texture2DArrayのC#のAPIはその圧倒方法に対応されいませんから、無視されています。

> https://docs.unity3d.com/ScriptReference/Texture2DArray-ctor.html  
> **Texture2DArray Constructor**  
> Note that this class does not support Texture2DArray creation with a Crunch compression TextureFormat.




Shaderのコードで、`UNITY_SAMPLE_TEX2DARRAY(2DArraySampler, float3UV)`と言うマクロで、配列から一枚のテクスチャをサンプリングできます。
テクスチャの配列のメリットは、数枚のテクスチャを一つのテクスチャ・ユニットとして使えることです。
そこは「Atlas」とほぼ同じです。

テクスチャの配列にある一枚を使うために、Shaderで索引を決めることが必要です。
索引される一枚は普通のテクスチャの一枚と使えます。
そのため、「Atlas」に比べると、タイリングに向いています。

