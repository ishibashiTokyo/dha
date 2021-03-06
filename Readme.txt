------------------------------------------------------------------------
■■■「DHA」(Diff and History and Approval)■■■ ver0.5
------------------------------------------------------------------------
「DHA」は履歴と承認と差分表示の機能を追加するモジュール&プラグインです。
jeyson氏作のDAAAH(http://jeyson.eshizuoka.jp/e606304.html)のVer0.5.3を
ベースとしています。

次のような機能があります。

・更新履歴の管理
・現在のデータと過去データの差分表示(Diff機能)
・過去データへ復帰(ロールバック機能)
・一般公開のための簡易承認ワークフロー

■インストール
------------------------------------------------------------------------
1.モジュールを新規作成。モジュール名は「DHA」としてください。
2. module_dha.phpの中身をコピー&ペーストして保存してください。
3.プラグインを新規作成、プラグイン名称は何でもかまいません。
4. plugin_dha.phpの中身をコピー&ペーストして保存してください。
5. plugins/dha/フォルダのconfig.inc.phpを使用環境に合わせて設定してください。
　※設定方法は後述
6. plugins/dha/フォルダを/assets/plugins/に配置してください。
7. admin権限でログインし、トップページの編集画面を開き、画面下部の承認の
 ドロップダウンを操作してワークフローのすべてで「承認する」を選択して保存
 してください。トップページの一般公開の承認を行うことができました。


※dittoとWayfinderを使用している場合、承認済みコンテンツの内容をみるため
　使用するテーブルを以下のように変更してください。

Wayfinder(ver2.0)の場合
---------------------------
/assets/snippets/wayfinder/wayfinder.inc.php

316行目
$tblsc = $modx->getFullTableName("site_content");
　↓
$tblsc = $modx->getFullTableName("approvaled_site_content");

439行目
$tb1 = $modx->getFullTableName("site_tmplvar_contentvalues");
　↓
$tb1 = $modx->getFullTableName("approvaled_site_tmplvar_contentvalues");


Ditto(ver2.1.0)の場合
---------------------------
/assets/snippets/ditto/classes/ditto.class.inc.php

902行目
$tblsc= $modx->getFullTableName("site_content");
　↓
$tblsc = $modx->getFullTableName("approvaled_site_content");

982行目
$tblsc= $modx->getFullTableName("site_content");
　↓
$tblsc = $modx->getFullTableName("approvaled_site_content");

762行目
$tb1 = $modx->getFullTableName("site_tmplvar_contentvalues");
　↓
$tb1 = $modx->getFullTableName("approvaled_site_tmplvar_contentvalues");



■設定ファイル「config.inc.php」の記述方法
------------------------------------------------------------------------
このファイルでは承認機能(ワークフロー)部分の設定を記述します。

1.単純設定
---------------------------
結局、差分と履歴管理しか使う必要のない場合や、結局、1つのアカウントしか
使わない場合は下記のように設定します。

$approval_level = 1;
$level_and_role [ 1 ] = "1";
$level_and_mes [ 1 ] = "承認";

$a_approval_string [ 0 ] = "承認しない";
$a_approval_string [ 1 ] = "承認する";


2.ワークフロー設定1
---------------------------
各部門に上長がいて、ページを編集するユーザーと公開承認を行う上長ユーザーが
いる場合は下記のようになります。
まず、「ユーザー」-「ロール管理」で2つのロールを作ります。
1つのロールは「ページ編集ユーザー」で「ページの公開」の公開を外してください。
もう1つのロールは「上長ユーザー」で「ページの公開」の公開をつけてください。
「上長ユーザー」のロールIDが3の場合、次のような記述になります。

$approval_level = 1;
$level_and_role [ 1 ] = "1/3"; // 3は上長ユーザーのロールID(1はadmin)
$level_and_mes [ 1 ] = "承認";

$a_approval_string [ 0 ] = "承認しない";
$a_approval_string [ 1 ] = "承認する";

各部門で編集できるページはMODx標準の「ユーザー」-「管理画面のアクセス許可」で
設定してください。
各部門の編集ユーザーと上長は、同じページをアクセスできるように設定してください。
これにより各部門ごとに編集できるページを設定することができます。
各部門ごとに編集できるページを設定することで、各部門の上長が公開許可を出すことが
可能なページを細かく設定できます。


3.ワークフロー設定2(実は推奨設定)
---------------------------
各部門に上長がいて、ページを編集するユーザーと公開承認を行う上長ユーザーが
いる場合、かつ、Web管理の総括部門として総務部等がある場合では、下記のよう
になります。
まず、「ユーザー」-「ロール管理」で3つのロールを作ります。
1つのロールは「ページ編集ユーザー」で「ページの公開」の公開を外してください。
もう1つのロールは「上長ユーザー」で「ページの公開」の公開をつけてください。
最後のロールは「総務ユーザー」で「ページの公開」の公開をつけてください。
「上長ユーザー」のロールIDが3、「総務ユーザー」のロールIDが4の場合、
次のような記述になります。

$approval_level = 1;
$level_and_role [ 1 ] = "1/3"; // 3は上長ユーザーのロールID(1はadmin)
$level_and_role [ 2 ] = "1/4"; // 4は総務ユーザーのロールID(1はadmin)
$level_and_mes [ 1 ] = "部門承認";
$level_and_mes [ 2 ] = "Web総括承認";

$a_approval_string [ 0 ] = "承認しない";
$a_approval_string [ 1 ] = "承認する";



■使い方
------------------------------------------------------------------------
ページの編集画面の下部に承認操作と差分確認を行うことができるインタフェース
が表示されます。

上長が承認したいときは、この編集画面から「承認する」を選択して、保存してください。
すべての人が「承認する」を選択したとき、承認データとして更新履歴とともに記録され、
一般公開されます。

その後、ページの内容を改変した場合は再び全員が「承認する」を選択しない限り、
承認時点の内容で表示されます。

ページの編集画面の下部「編集中ドキュメントの更新履歴/差分表示」ボタンを押すと
編集中のページと以前承認を受けたページとの差分を表示できます。
(PEARのText/Diffを利用しています。)

タブを操作して、様々な機能を利用できます。
中でも「編集内容を戻す」は、編集内容を昔の状態へ戻すことが可能です。
ようはロールバック機能なので、これだけでも利用価値はあるのかなと思っていたりします。



■ライセンスについて
------------------------------------------------------------------------
当然ながらMODxのGPLに則り、「DHA」モジュール&プラグインはGPLで公開します。
よって、利用に関して特別な費用は発生しません。
自由に使ってください。
ただし、利用に関して生じる不具合につきましては、当方で責任を持つことは
できかねます。



