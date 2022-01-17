# RenderDocとは

RenderDocはGPU・APIのフレームデバッガーです。  
かなり有能なプログラムで、Unityエディターから使うことが出来ます。

公式ウェブサイト  
https://renderdoc.org/  
Githubリポジトリ  
https://github.com/baldurk/renderdoc  

ウェブサイトから、またGithubから入手することが出来ます。  
インストール方法は普段のプログラムのと変わりません。

# RenderDocでUnity・Sceneのフレームを分析する方法

Unityの公式文書は最初のステップを説明します：
https://docs.unity3d.com/Manual/RenderDocIntegration.html

1. **RenderDoc**を実行して、
2. **Unity**のシーンを用意して、
3. **Unity**で「Scene」タブに右クリックして、「Load Renderdoc」を選んで、
4. **RenderDoc**でメニューから「File」→「Attach to Running Instance」を選んで、
5. 開いた「Remote Host Manager」の画面で：
  5.1. 「Unity」を選んで
  5.2. 「Connect to App」を選ぶと、その画面が閉めて、メイン画面で新しい「Localhost」のタブが追加現れます。
6. **Unity**で、「Scene」タブ、また「Game」タブのツールバーの右側で追加されている、円と円弧のアイコンのボタンをクリックすると、**Unity**は一つのフレームを**RenderDoc**に送信します。

![UnityのシーンタブからLoad RenderDocを選んで](/images/Unity-RenderDoc-0.png)  
![RenderDocでFile→Attach to Running Instance](/images/Unity-RenderDoc-1.png)  
![Remote Host Managerの画面でUnityを選んで、Connect To Appを押して](/images/Unity-RenderDoc-2.png)  
![Unityから、Sceneツールバーの右側のボタンをおして、フレームを送信して](/images/Unity-RenderDoc-3.png)  
![フレームをダブルクリックして、分析を始まって](/images/Unity-RenderDoc-4.png)

それで、**RenderDoc**の「Localhost」タブで、送信されたフレームの画像が追加せれたはずです。  
画像をダブルクリックしたら、そのフレームの分析が始まります。　　
ここから、左にある「Event Browser」のパネルから、フレームで実行されていた「DirectX」（またOpenGL）の関数の呼び出し詳細が順番で現れます。  
加えて「Resource Inspector」のタブで、使用されていた物（オブジェクト、テクスチャ、シェイダー、・・・）を確認することができます。

![Resource Inspectorのタブから、使用されていた物を検査出来ます](/images/RenderDoc-Resource-Inspector.png)

# RenderDocのフィルター機能

「Event Browser」のフィルター機能はかなり便利です。

大体の使用されているリソースの名前、また関数の名前をフィルター出来ます。

例えば、シェイダーの名前を入力するして（最初の文字だけでいい）、下の一覧が減少されて、関連の呼び出しだけを表示することになります。  
関数の名前が見えないなら、折りたたんでいる分類で隠れているかもしれません。  
一番上の一行の「Name」の一列の所で右クリックして、「Expand All（すべて展開）」を選ぶと、フィルターされている呼び出しの全部を見えることになるはずです。

ですから、あるシェイダーを使っているドローコールを分析したいなら：
* 名前を入力して、
* 関連の「VSSetShader」、また「PSSetShader」の呼び出しを選んで、
* フィルターを削除して、

そうすると、すべての関数呼び出しが見えることになって、そしてスクロールバーが先に選んだ関数に移動しますから、

* 次の「Draw*」コールをクリックすると、

そのシェイダーを使うドローコールを「Pipeline State」のタブで分析できることになります。

![シェイダーの名前を入力すると一覧が関連にする呼び出しに減少されます](/images/RenderDoc-Kensaku-1.png)
![名前が見えないなら、一番上の一行で右クリックメして、「Expand All（すべて展開）」を選んで](/images/RenderDoc-Kensaku-2.png)
![関数の呼び出しが見えることになるはず](/images/RenderDoc-Kensaku-3.png)
![次のドローコールを分析したいなら、フィルターされているアイテムを選んで](/images/RenderDoc-Kensaku-4.png)
![フィルターを消して](/images/RenderDoc-Kensaku-5.png)
![次のDrawコールを選ぶと、Pipeline Stateのタブで分析でその分析を出来ます](/images/RenderDoc-Kensaku-6.png)

# 使用されているテクスチャを検査する方法

ドローコールを分析していると、「Pipeline State」タブの「Pixel Shader」フェーズから、使用されているテクスチャの一覧が見えます。
そして、各テクスチャ・アイテムの右にある「→」矢印を左クリックすると、そのテクスチャの検査画面に移動されます。  
検査画面の下にあるステータスバーで、テクスチャの詳細（名前、寸法、フォーマット、・・・）が書いてあります。  
そして、マウスポインタの下にあるピクセル、また右クリックで選ばれているピクセルの情報も確認できます。

その画面は閉めるまで「Texture Viewer」のタブに残りますから、複数のドローコールで使用されているテクスチャを比べることが出来ます。

![Pixel Shaderフェーズから、使っているテクスチャの一覧を確認できます](/images/RenderDoc-View-Texture.png)
![各テクスチャ・アイテムの右にある「→」矢印にクリックすると、選んだテクスチャの検査画面が現れます](/images/Unity-RenderDoc-7.png)

# タブを再開する

上にある「Window」メニューから、タブの名前を選ぶと、閉ざしたタブを再開できます。

