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

まずは：
* Unityのシーンを用意して、
* RenderDocを実行することは必要です。

後は：
1. **Unity**で「Scene」タブに右クリックして、「Load Renderdoc」を選んで、
2. **RenderDoc**で「File」メニューから、「Attach to Running Instance」を選んで、
3. そうすると、「Remote Host Manager」と言う画面が開いて、そこで：
  3.1 「Unity」を選んで
  3.2 「Connect to App」を選ぶと、その画面が閉めて、メイン画面で新しい「Localhost」のタブが追加現れます。
4. **Unity**で、「Scene」タブ、また「Game」タブのツールバーの右側で追加されている、円と円弧のアイコンのボタンをクリックすると、**Unity**は一つのフレームを**RenderDoc**に送信します。

![UnityのシーンタブからLoad RenderDocを選んで](/images/Unity-RenderDoc-0.png)  
![RenderDocでFile→Attach to Running Instance](/images/Unity-RenderDoc-1.png)  
![Remote Host Managerの画面でUnityを選んで、Connect To Appを押して](/images/Unity-RenderDoc-2.png)  
![Unityから、Sceneツールバーの右側のボタンをおして、フレームを送信して](/images/Unity-RenderDoc-3.png)  
![フレームをダブルクリックして、分析を始まって](/images/Unity-RenderDoc-4.png)

それで、RenderDocの「Localhost」タブで、送信されたフレームの画像が追加せれたはずです。  
画像をダブルクリックしたら、そのフレームの分析が始まります。　　
ここから、左にある「Event Browser」のパネルに、フレームで実行されていた「DirectX」（またOpenGL）の関数の呼び出しが順番で現れます。  
加えて「Resource Inspector」のタブで、使用されていた物（オブジェクト、テクスチャ、シェイダー、・・・）を検査できることになります。

![Resource Inspectorのタブから、使用されていた物を検査出来ます](/images/RenderDoc-Resource-Inspector.png)

# フィルター機能

「Event Browser」のフィルター機能はかなり便利です。

例えば、あるシェイダーを使っているドローコールに集中したいなら、そのシェイダーの名前を入力すると（最初の文字だけでいい）、一覧が減少されて、関連の呼び出しだけを表示することになります。  
シェイダーなら、大体その関数の名前は「VSSetShader」、また「PSSetShader」です。
関数の名前が見えないなら、「Name」の一列から、一番上の一行の右クリックメニューから、「Expand All（すべて展開）」を選ぶと、見えることになるはずです。

後は、その次のドローコールを分析したいなら、まずフィルターされている前のアイテムにクリックして、フィルターを削除して、  
すべての関数呼び出しが見えることになると、スクロールバーが先に選んだ関数に移動しますから、  
選べている呼び出しから、次の「Draw*」コールをクリックすると、「Pipeline State」のタブで分析できることになります。

![シェイダーの名前を入力すると一覧が関連にする呼び出しに減少されます](/images/RenderDoc-Kensaku-1.png)
![名前が見えないなら、一番上の一行で右クリックメして、「Expand All（すべて展開）」を選んで](/images/RenderDoc-Kensaku-2.png)
![関数の呼び出しが見えることになるはず](/images/RenderDoc-Kensaku-3.png)
![次のドローコールを分析したいなら、フィルターされているアイテムを選んで](/images/RenderDoc-Kensaku-4.png)
![フィルターを消して](/images/RenderDoc-Kensaku-5.png)
![次のDrawコールを選ぶと、Pipeline Stateのタブで分析でその分析を出来ます](/images/RenderDoc-Kensaku-6.png)

# テクスチャを確認して

ドローコールを分析していると、「Pipeline State」タブの「Pixel Shader」フェーズから、使っているテクスチャの一覧を確認できます。
そして、各テクスチャ・アイテムの右にある「→」矢印に左クリックすると、その検査画面に移動されます。  
その画面から、下をステータスバーで、テクスチャの情報（名前、寸法、フォーマット、・・・）を確認できます。  
テクスチャのピクセルに右クリックすると、同じ所で、その色の情報も確認できます。

後は、その画面は閉めるまで「Texture Viewer」のタブに残ります。

![Pixel Shaderフェーズから、使っているテクスチャの一覧を確認できます](/images/RenderDoc-View-Texture.png)
![各テクスチャ・アイテムの右にある「→」矢印にクリックすると、選んだテクスチャの検査画面が現れます](/images/Unity-RenderDoc-7.png)

# タブを再開する

上にある「Window」メニューから、タブの名前を選ぶと、閉ざしたタブを再開できます。

