A shader generator for Panda3D
Specifically, a Shader Meta-Language for implementing, customizing and extending shader generators.
Panda3Dのシェーダジェネレータを実装、カスタマイズ、拡張するためのPythonベースのShader Meta-Language。カスタマイズ可能なCGコードジェネレータノードの非周期グラフを設定して、独自の100％カスタマイズ可能なシェーダジェネレータを作成できます。編集
新しいトピックを追加
 72件のコミット
 3つの枝
 0のリリース
 1人の貢献者
 Python 100.0％
Python
クローンまたはダウンロード新しいファイルの作成ファイルのアップロードファイルの検索ブランチ：v3新しいプル要求
 プルリクエストの比較このブランチは、Craig-Macomber：v3でさえも同じです。
最新のコミットea44806 @ 2015年7月3日@ Craig-Macomber Craig-Macomberがpanda3d 1.9.0の警告を修正
ShadersOut基本機能が動作します。 7年前のサンプルエフェクト
グラフは周囲照明を追加し、フォグを無効にします。また、いくつかの情報を追加... 4年前
ライブラリーに周囲照明を追加し、フォグを無効にするなどです。また、いくつかの情報を追加... 4年前
.gitignore 5年前にデバッググラフの視覚化を追加する
License.txt bsd licenseのライセンス6年以上前
README.rst update readme 5年前
__init__.pyライブラリは6年前に1つではなく、パスの繰り返しを取れるようにする
manager.py 5年前のコメントを改善する
nodes.pyはアンビエントライティングを追加し、フォグを無効にします。また、いくつかの情報を追加... 4年前
param.pyは、5年前のデバッググラフの視覚化を追加します
5年前にrenderState.pyフラグを追加
shaderBuilder.py fix warning for panda3d 1.9.0 2年前
test.pyを追加して使用するmanager module 6年前
test2.py 5年前にデバッググラフの視覚化を追加
 README.rst
Panda3Dのためのシェーダジェネレータ、シェーダジェネレータを実装、カスタマイズ、拡張するためのシェーダメタ言語。

ターゲットユーザー

これは混乱の一般的な領域です。明確にするために：このシステムは、高度なシェーダープログラマーを対象としています。あなたが多くの異なるが関連するシェーダファイルを維持することに苦労している場合、これはあなたのためのツールかもしれません。単一のシェーダコードセットから生成された各組み合わせまたは複数の属性に対して特別なシェーダを使用する場合は、これがあなたのためのツールです。

例：一部のモデル（またはモデルの一部）にテクスチャがあります。いくつかはモデルカラーを持っています。いくつかは材料の色をしています。あるものは法線マップを持っています。このツールを使用して、これらのすべての可能な組み合わせを正しく処理するシェーダージェネレータを定義できます。

モデルに配置されたタグ、使用可能な頂点データの列、テクスチャ、およびグローバルフラグに基づいてシェーダを調整することさえできます。タグはほとんどの特別なニーズをカバーするのに十分一般的だとは思うが、さらにカスタムコントロールを追加することもできる。

例：私は、遅延シェーディングと漫画インキングを有効にするなど、いくつかの調整可能な設定があります。フラグ機能を使用して、私のジェネレータは、シェーダの順序を変更し、それに応じて動作するように設計することができます。遅延シェーディングの場合、照明演算コードは、遅延照明パスシェーダとフォワードシェーディングのためのモデルシェーダとで共有することができます。漫画のインキングの場合、後処理のインキングコードはフラグがオフの場合は単に省略されます。

これは非常に強力で、非常に柔軟ですが、シェーダコードの作成は保存されません！それは、単に多くの関連するシェーダを維持し選択する必要を回避する方法を提供します。

ハイレベルな概念の概要

これはussageガイドではなく動作する方法です。いくつかの使用例は、ここのシェーダーチュートリアルにあります：https://github.com/Craig-Macomber/Shader-Tut

スクリプトはGenerator Graphを生成します。
RenderStatesはGenerator Graphへの入力として使用され、提供されたRenderStateに対するActive Graphを生成します。
アクティブグラフはシェーダにコンパイルされます。
スクリプト - >ジェネレータグラフ

各スクリプトファイルは、グラフ構造（ジェネレータグラフ）を記述（生成）します。これは、ノードを構築し、エッジ（および場合によってはいくつかの定数）を渡すことによって行われます。

nodeInstance = NodeType（input1、input2、 "SomeConstant"）
グラフのノードの種類はnodes.pyとライブラリ経由で定義されています（nodes.pyのCodeNodeとしてロードされます）

これらのノードタイプは0以上の出力を持ち、nodeInstance.nameとしてアクセス可能です。ノード自体を渡すだけでアクセスできるデフォルト出力を持つことができます（またそうでないかもしれません）：

nodeInstance2 = SomeNode（nodeInstance.someOutput、nodeInstance）
したがって、スクリプトは、有向非循環グラフを記述する。グラフを接続する必要はありません。

RenderStates

参照：renderState.py

ジェネレータ・グラフからシェーダを生成するとき、RenderStateが入力として提供されます（そしてその唯一の入力）。

シェーダジェネレータは、panda3d.core.RenderStateと非常によく似たRenderStateクラスを使用します。

パンダのRenderStateクラスが使用されない理由は2倍です：

それは不必要なデータをたくさん含んでいます（キャッシングを傷つける）
必要なデータがいくつかありません（タグ、geomVertexFormatなど）
ShaderBuilderのRenderStatesに格納されている最小限のデータを正確に取得するために、Generator Graphを使用してRenderStateFactoryをセットアップし、特定のGenerator Graph

シェーダの生成

シェーダビルダはGenerator Graphを使用し、RenderStateを渡してShaderを生成します。 shaderBuilder.ShaderBuilder.getShaderはこれを行います。それは含まれています









Target Users
============
This is a common area of confusion, so to be clear:
This system is intended for advanced shader programmers.
If you find yourself struggling with maintaining many different but related shader files,
this may be a tool for you. If you want to have a special shader for each combination or several atributes
generated from a single set of shader code, this is the tool for you.

Example: Some of my models (or even parts of models) have textures.
Some have model colors. Some have material colors. Some have normal maps. I can use this tool
to define a shader generator to handle all possible combinations of these correctly.

It can even handle adjusting shaders based on tags placed on models, available vertex data columns, textures, and global flags.
More custom controls can also be added, though I think tags are general enough to cover most special needs.

Example: I have several adjustable settings, such as enabling deferred shading and cartoon inking. 
Using the flags feature, my generator can be designed to reorder and change the shaders to act accordingly.
In the deferred shading case, the lighting computation code can be shared between the deferred lighting pass shader,
and the model shader for forward shading. For the cartoon inking,
the inking code in the post process is simply omitted of its flag is off.

This is very powerful, and very flexible, but it does not save writing the shader code!
It simply provides a way to avoid having to maintain and select between many related shaders.

High level conceptual overview
==============================
This is how it works, not a ussage guide.
Some example usage is in the shader tutorial here: https://github.com/Craig-Macomber/Shader-Tut

- A Script produces a Generator Graph.

- RenderStates are used as input to the Generator Graph to produce the Active Graph for to the
  provided RenderState.

- The Active Graphs are compiled into shaders.


Scripts --> Generator Graphs
++++++++++++++++++++++++++++
Each script file describes (generates) a graph structure (The generator graph)
This is done by constructing nodes and passing in incomming edges (and possibly some constants).

    nodeInstance=NodeType(input1,input2,"SomeConstant")
    
The types of nodes in the graph are defined in nodes.py, and via libraries (which are loaded as CodeNodes from nodes.py)

These node types have 0 or more outputs, accessable as nodeInstance.name
They may (and might not) have a default output which can be accessed by just passing the node itself:

    nodeInstance2=SomeNode(nodeInstance.someOutput,nodeInstance)

Thus, the scripts describe directed acyclic graphs. The graphs do not need to be connected.

RenderStates
++++++++++++
See also renderState.py

When generating a shader from the Generator Graph, a RenderState is provided as input (and its the only input).

The shader generator uses a very similar RenderState class to panda3d.core.RenderState.

The reason the panda RenderState class is not used is tweo fold:

- It contains lots of unneeded data (hurts caching)

- It is missing some needed data (tags, geomVertexFormat etc)

To get the exact minimum needed data stored in the RenderStates for the ShaderBuilder,
the Generator Graph is used to setup a RenderStateFactory the produces ideal minimal
RenderStates for a specific Generator Graph

Generating the Shader
+++++++++++++++++++++
A shader builder uses it's Generator Graph, and a passed in RenderState and produces an Shader. 
shaderBuilder.ShaderBuilder.getShader does this. It includes caching, since the process is a
deterministic process that depends only on the Generator Graph (constant for the builder),
and the input RenderState.


Generating the Active Graph
---------------------------
The first step is to generate the Active Graph. See shaderBuilder.makeStages.

Compiling the Active Graph into Stages
--------------------------------------
See shaderBuilder.makeStage.


 



Misc Notes
==========

Outputting visual graphs requires pydot and graphviz to be installed.

Goals:

- Allow coders to provide all possible shader effects (no restrictions on shader code, stages used, inputs, outputs etc) (Done)

- Allow easy realtime configuration and tuning of effects (accessible to artists and coders) (Not started, not longer emphasized)

- Generate custom shaders based on render state and other settings on a per NodePath basis from a single configuration (Done via use of meta-language design)

- Allow easy use of multiple separate configurations applied to different NodePaths (ex: one for deferred shaded lights, one for models, one for particles. Done.)

- Produce an extensive collection of useful NodeTypes and example Graphs, sample applications etc. (Most of the remaining work is here)

It is important that adding, sharing and using libraries of effects is easy. To facilitate this, they are packed into libraries which can simply be placed in a libraries folder (even recursively)
There is however currently no name spacing. For now, manually prefix things if you with to avoid any potential conflicts.

The focus on allowing full control of the shaders is important. A shader generator that can't use custom shader inputs, render to multiple render targets, or use multiple stages (vshader, fshader, gshader etc) is not complete. This design inherently supports all of these, and more.

Many more details in shaderBuilder.py, see comments near top.

Example Meta-Language scripts are in the graph directory. These scripts create a graph structure that is used to generate the final shader graphs for different render states which are compiled into shaders.

The set of nodes that can be used in these graphs are the regestered classes from nodes.py, and those loaded from the libraries (see the library folder).

Its possibe to add custom node types implemented in python, simply provide them when instancting the shaderBuilder.Library

This system currently does not modify render states, add filters or any other changes to the scene graph.
It just generates shaders. It assumes the scene graph will be setup separately.
It would be possible to add in some scene graph modifying active nodes that would be collected while generating the shader, and then applied along with the shader (by passing the node to the apply method of each).
This can be made to work with the current cache system.
