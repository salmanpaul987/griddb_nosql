# GridDB CE 4.0

## 柔軟性と利便性の強化

GridDB CE V4.0は、IoTにおける時系列データを高速に処理する"分散データベース"として、その柔軟性と利便性を強化しました。

1. 大規模データ管理強化
    - 従来のスケールアウト型の分散データベースは1ノード当たり数TBですが、1ノード当たり50TBまでの幅を持たせて、より少ないノード台数で柔軟に効率よく対応するための強化・改善を行いました。

2. 検索結果取得時の分割処理への対応
    - バッファのサイズなどに収まるよう、内部で自動的に分割し処理することで、大量の検索結果が取得可能になりました。
3. コンテナ名などの名前に使用できる文字の拡張
    - 他のデータベースとの連携を容易にするためにコンテナ名などに使用できる文字を拡張しました。
4. データベースファイルのサイズ縮小機能
    - データベースファイルのサイズ(実ディスク割当量)を縮小することが可能になりました。 

---

### 1. 大規模データ管理強化

従来のスケールアウト型の分散データベースは1ノード当たり数TBですが、1ノード当たり50TBまでの幅を持たせて、頻繁にアクセスされるホットデータはメモリに配置するなどにより、性能劣化を防ぎながらより少ないノード台数で柔軟に効率よく対応するための強化・改善を行いました。

具体的には次のような改善をしました。
- データブロック未使用領域解放
- データベースファイルのメタ情報のサイズ削減
- コンテナ削除など重い機能ををバックグラウンド処理化し応答性を改善
- バッファコントロール改善による登録・更新性能の向上


### 2. 検索結果取得時の分割処理への対応

検索結果サイズが大きい場合に、「部分実行モード」を使うことで、クエリの結果送受信に用いるバッファのサイズなどがなるべく一定の範囲に収まるよう、必要に応じて実行対象のデータ範囲を分割して処理することが可能になります。

Javaクライアントを使う場合、QueryクラスのsetFetchOption()メソッドを使って、部分実行モード(PARTIAL_EXECUTION)を設定します。

例： query.setFetchOption(FetchOption.PARTIAL_EXECUTION, true);

部分実行モードは、現バージョンでは次の条件すべてを満たすクエリに使用できます。また、LIMITオプションと併用することができます。条件を満たさない場合でも、各種フェッチオプションの設定時点ではエラーを検知しない場合があります。
- TQL文からなるクエリであること
- TQL文において、選択式が「*」のみからなり、ORDER BY節を含まないこと
- 対応するContainerが個々の部分的なクエリ実行時点において常に自動コミットモードに設定されていること


### 3. コンテナ名などの名前に使用できる文字の拡張

クラスタやコンテナなどの名前に使用できる文字を拡張しました。  
従来の文字に加え、記号(ハイフン '-'、ドット '.'、スラッシュ '/'、イコール '=')が使用可能になります。  

使える文字が増えたことにより、他のNoSQLデータベースと連携する際にオブジェクトの名前を変更することなくそのまま利用できるケースが増え、連携がより容易になります。


### 4. データベースファイルのサイズ縮小機能(データブロック未使用領域解放機能)

データベースファイル（チェックポイントファイル）の使用されていないブロック領域に対して、Linuxのファイルブロック割り当て解除処理を行い、データベースファイルのサイズ(実ディスク割当量)を縮小することができる機能です。 

以下のようなケースにおいて、ディスク使用量を削減したい場合にご利用ください。 
- データを大量に削除した場合 
- 今後データ更新の予定が無く、DBを長期保存するような場合 
- データ更新時にディスクフルになり、回避する暫定手段としてDBサイズ縮小が必要な場合 

GridDBノード起動時に、gs_startnodeコマンドでデータブロック未使用領域解放オプション(--releaseUnusedFileBlocks)を指定します。 

GridDBノード起動時に、データベースファイル（チェックポイントファイル）の未使用領域を解放します。解放された領域は、新たなデータ更新が発生しない限りディスク領域は割り当てられません。 

サポート環境は、データブロック圧縮機能と同じです。 

---

Copyright (C) 2018 TOSHIBA Digital Solutions Corporation