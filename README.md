# DB_quest
以下は、好きな時間に好きな場所で話題の動画を無料で楽しめる「インターネットTVサービス」の新規に作成における、データベース設計のドキュメントです。

# Step1 : テーブル設計

### 1. channels テーブル
| カラム名  　　　　　　　　　　| データ型 　　　　　　　　　　　　　　| NULL  | キー     | 初期値 | AUTOINCLEMENT |
| :----------  | :------------ | :---- | :------ | :---- | :---- |
| channel_id   |　　bigint(20)		　　|       | PRIMARY |       | YES
channel_name	　　|　　varchar(100)  |       |         |

### 2. programs テーブル
| カラム名  　　　　　　　　　　| データ型 　　　　　　　　　　　　　　| NULL  | キー     | 初期値 | AUTOINCLEMENT |
| :----------  | :------------ | :---- | :------ | :---- | :---- |
| program_id	 | bigint(20)		 |       | PRIMARY |       |	YES  |
| title	       | varchar(255)  |    	 |         |       | 			 |
| program_detail |	text       | YES	|		
| genre	       | varchar(50)	 | YES	|	

### 3. program_slots テーブル

| カラム名  　　　　　　　　　　| データ型 　　　　　　　　　　　　　　| NULL  | キー     | 初期値 | AUTOINCLEMENT |
| :----------  | :------------ | :---- | :------ | :---- | :---- |
| slot_id	     | bigint(20)	 |       | PRIMARY |       | YES |
| channel_id	 | bigint(20)		|		
| program_id	 | bigint(20)		|		
| start_time	 | time				|
| end_time	   | time				|

### 4. genresテーブル

| カラム名  　　　　　　　　　　| データ型 　　　　　　　　　　　　　　| NULL  | キー     | 初期値 | AUTOINCLEMENT |
| :----------  | :------------ | :---- | :------ | :---- | :---- |
| genre_id	   | bigint(20)		|        | PRIMARY |	    | YES |
| genre_name   |	varchar(50)	|

### 5. seasons テーブル

| カラム名  　　　　　　　　　　| データ型 　　　　　　　　　　　　　　| NULL  | キー     | 初期値 | AUTOINCLEMENT |
| :----------  | :------------ | :---- | :------ | :---- | :---- |
| season_id	   | bigint(20)		 |       | PRIMARY | | YES |
| program_id	 |bigint(20) |				
| season_number | int			 |

### 6. episodes テーブル
| カラム名  　　　　　　　　　　| データ型 　　　　　　　　　　　　　　| NULL  | キー     | 初期値 | AUTOINCLEMENT |
| :----------  | :------------ | :---- | :------ | :---- | :---- |
| episode_id	 | bigint(20)    |     	 | PRIMARY | | YES |
| season_id	   | bigint(20)		 |		
| episode_number | 	int	       | YES |			
| title	       | varchar(255) |
| episode_detail |	text	 | YES			|
| video_duration | time		|	
| release_date | date			|

### 7. viewership テーブル
| カラム名  　　　　　　　　　　| データ型 　　　　　　　　　　　　　　| NULL  | キー     | 初期値 | AUTOINCLEMENT |
| :----------  | :------------ | :---- | :------ | :---- | :---- |
| viewership_id	| bigint(20) 	|        |PRIMARY		|      | YES |
| episode_id	| bigint(20) |				
| slot_id	| bigint(20)		|		
| viewership_count |	int |				


外部キー制約：
* program_slots.channel_id → channels.channel_id
* program_slots.program_id → programs.program_id
* seasons.program_id → programs.program_id
* episodes.season_id → seasons.season_id
* viewership.episode_id → episodes.episode_id
* viewership.slot_id → program_slots.slot_id

ユニークキー制約：
* genres.genre_name に対してユニークキー制約を設定  
<br><br>
  
これにより、正規化が達成され、アプリケーションの要件に合ったデータベースが構築されます。<br><br>  



# Step2 : データベース構築
まず、MySQLにログインし、APEAMA　という新しいデータベースを作成します。

``` .spl
CREATE APEMA;
USE APEMA; 
```

次に、ステップ1で設計したテーブルをデータベースに作成します.
以下は、先ほどのデータベース設計を元に、各テーブルを順に作成し、サンプルデータを挿入するSQLコードです。

## 1.チャンネル情報を格納するテーブル(channels)
``` .txt
CREATE TABLE channels (
    channel_id BIGINT(20) PRIMARY KEY AUTO_INCREMENT,
    channel_name VARCHAR(100) NOT NULL
);
```
'channnel_id' チャンネルを一意に識別する列です。INTは整数を表し、チャンネルごとにユーニークなIDが自動的に増分されます。  
'PRIMARY KEY' : 'channel_id' がこのテーブルの主キーであることを示します。  
'channel_name' はチャンネルの名前を格納する列で最大100文字。NULL値は許容されません。  
<br>
サンプルデータの追加
``` .txt
-- サンプルチャンネルデータの追加

INSERT INTO channels (channel_name) VALUES
('ドラマ1'),
('ドラマ2'),
('アニメ1'),
('アニメ2'),
('スポーツ'),
('ペット');
```

## 2.番組を格納するテーブル(programs)
``` .sql
CREATE TABLE programs (
    program_id BIGINT(20) PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    program_detail TEXT,
    genre VARCHAR(50)
);
```
サンプルデータの追加
``` .txt
-- サンプル番組データの追加
INSERT INTO programs (title, program_detail, genre) VALUES
('鬼滅の刃', '人気のアニメ', 'アニメ'),
('ドラマA', '感動のドラマ', 'ドラマ'),
('スポーツニュース', 'スポーツの最新情報', 'ニュース');
```


## 3.番組枠テーブル（program_slots）
``` .txt
CREATE TABLE program_slots (
  slot_id BIGINT(20) PRIMARY KEY AUTO_INCREMENT,
  channel_id BIGINT(20),
  program_id BIGINT(20),
  start_time TIME,
  end_time TIME,
  FOREIGN KEY (channel_id) REFERENCES channels(channel_id),
  FOREING KEY (program_id) REFERENCES programs(program_id)
);
```

 サンプルデータの追加
``` .txt
-- サンプル番組枠データの追加
INSERT INTO program_slots (channel_id, program_id, start_time, end_time) VALUES
(1, 1, '20:00:00', '21:30:00'),
(2, 2, '19:30:00', '21:00:00'),
(3, 3, '15:00:00', '18:00:00');
```

## 4.ジャンルを格納するテーブル（genres）
``` .sql
CREATE TABLE genres (
    genre_id BIGINT(20) PRIMARY KEY AUTO_INCREMENT,
    genre_name VARCHAR(50) NOT NULL UNIQUE
);
```
サンプルデータの追加
``` .sql
-- サンプルジャンルデータの追加
INSERT INTO genres (genre_name) VALUES
('アニメ'),
('ドラマ'),
('ニュース');
```

## 5.シーズン情報を格納するテーブル(seasons)
``` .spl
CLEATE TABLE season (
  season_id BIGINT(20) PRIMARY KEY AUTO_INCREMENT,
  program_id BIGINT(20),
  season_number INT,
  FOREIGN KEY (program_id) REFERENCES programs(program_id)
);
```
'season_id' は主キーです。  
'program_id' は外部キーであり、 'programs' テーブルの　'program_id'と関連づけられる。  
'season_number' はシーズンの番号を表す整数型です。  

サンプルデータの追加
``` .txt
-- サンプルシーズンデータの追加
INSERT INTO seasons (program_id, season_number) VALUES
(1, 1),
(2, 1);
```

## 6.エピソード情報を格納するテーブルepisodes)
``` .sql
CREATE TABLE episodes (
  episode_id BIGINT(20) PRIMARY KEY AUTO_INCREMENT,
  season_id BIGINT(20),
  episode_number INT,
  title VARCHAR(255) NOT NULL,
  episode_detail TEXT,
  video_duration TIME,
  release_date DATE,
  FOREIGN KEY (season_id) REFERENCES seasons(season_id)
);
```
'episode_id' は主キーです。  
'season_id' は外部キーであり、 'seasons' テーブルの 'season_id' と関連づけられます。  
'episode_number' はエピソードの番号を表す整数型です。
'title' はエピソードのタイトルを表す文字列型で、NULL値は許容されません。
'video_duration' はエピソードの動画時間（分単位）を格納する列で,INT型で整数で表現。
'release_date' エピソードの公開日を格納する列です。DATE型で、日付を表現する。　　
'season_id' 列が 'season' テーブルの 'seasn_id' 列を参照する外部キー制約を持っています。  
 
サンプルデータの追加
``` .txt
-- サンプルエピソードデータの追加
INSERT INTO episodes (season_id, episode_number, title, episode_detail, video_duration, release_date) VALUES
(1, 1, '第1話', '感動の始まり', '00:24:00', '2023-01-01'),
(1, 2, '第2話', '泣けるシーン', '00:24:00', '2023-01-08'),
(2, 1, '第1話', '新キャラ登場', '00:45:00', '2023-01-02');
```  


## 7.視聴数を格納するテーブル(viewership)
```.sql
CREATE TABLE viewership (
  viewership_id BIGINT(20) PRIMARY KEY AUTO_INCREMENT,
  episode_id BIGINT(20),
  slot_id BIGINT(20),
  viewership_count INT,
  FOREIGN KEY (episode_id) REFERENCES episodes(episode_id),
  FOREIGN KEY (slot_id) REFERENCES program_slots(slot_id)
);
```

サンプルデータの追加
``` .txt
-- サンプル視聴数データの追加
INSERT INTO viewership (episode_id, slot_id, viewership_count) VALUES
(1, 1, 500000),
(1, 2, 300000),
(2, 1, 450000),
(3, 3, 600000);
```
<br><br>
# Step3 : クエリを書く！　　
  
#### 1.よく見られているエピソードを知りたいです。エピソード視聴数トップ3のエピソードタイトルと視聴数を取得してください.
```.txt
SELECT
    e.title AS episode_title,
    v.viewership_count
FROM
    episodes e
JOIN
    viewership v ON e.episode_id = v.episode_id
ORDER BY
    v.viewership_count DESC
LIMIT
    3;
+---------------+------------------+
| episode_title | viewership_count |
+---------------+------------------+
| 第1話         |           600000 |
| 第1話         |           500000 |
| 第2話         |           450000 |
+---------------+------------------+
3 rows in set (0.00 sec)
```
このクエリは、episodes テーブルと viewership テーブルを結合し、視聴数に基づいて降順でソートしています。そして、LIMIT 3 を使用してトップ3の結果のみ取得しています。　　
<br><br>
#### 2.よく見られているエピソードの番組情報やシーズン情報も合わせて知りたいです。エピソード視聴数トップ3の番組タイトル、シーズン数、エピソード数、エピソードタイトル、視聴数を取得してください

```.txt
SELECT
    ->     p.title AS program_title,
    ->     s.season_number,
    ->     e.episode_number,
    ->     e.title AS episode_title,
    ->     v.viewership_count
    -> FROM
    ->     episodes e
    -> JOIN
    ->     seasons s ON e.season_id = s.season_id
    -> JOIN
    ->     programs p ON s.program_id = p.program_id
    -> JOIN
    ->     viewership v ON e.episode_id = v.episode_id
    -> ORDER BY
    ->     v.viewership_count DESC
    -> LIMIT
    ->     3;
+---------------+---------------+----------------+---------------+------------------+
| program_title | season_number | episode_number | episode_title | viewership_count |
+---------------+---------------+----------------+---------------+------------------+
| ドラマA       |             1 |              1 | 第1話         |           600000 |
| 鬼滅の刃      |             1 |              1 | 第1話         |           500000 |
| 鬼滅の刃      |             1 |              2 | 第2話         |           450000 |
+---------------+---------------+----------------+---------------+------------------+
3 rows in set (0.01 sec)
```
このクエリでは、episodes テーブルと viewership テーブルを結合し、さらにそのエピソードが属する番組やシーズンの情報を取得しています。同様に、視聴数に基づいて降順でソートし、LIMIT 3 を使用してトップ3の結果のみ取得しています。
<br><br>
#### 3.本日の番組表を表示するために、本日、どのチャンネルの、何時から、何の番組が放送されるのかを知りたいです。本日放送される全ての番組に対して、チャンネル名、放送開始時刻(日付+時間)、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を取得してください。なお、番組の開始時刻が本日のものを本日方法される番組とみなすものとします
```.txt
SELECT
    ->     c.channel_name,
    ->     ps.start_time AS broadcast_start_time,
    ->     ps.end_time AS broadcast_end_time,
    ->     p.title AS program_title,
    ->     s.season_number,
    ->     e.episode_number,
    ->     e.title AS episode_title,
    ->     e.episode_detail
    -> FROM
    ->     program_slots ps
    -> JOIN
    ->     channels c ON ps.channel_id = c.channel_id
    -> JOIN
    ->     programs p ON ps.program_id = p.program_id
    -> LEFT JOIN
    ->     seasons s ON p.program_id = s.program_id
    -> LEFT JOIN
    ->     episodes e ON s.season_id = e.season_id
    -> WHERE
    ->     DATE(ps.start_time) = CURDATE()
    -> ORDER BY
    ->     ps.start_time;
+--------------+----------------------+--------------------+--------------------------+---------------+----------------+---------------+--------------------+
| channel_name | broadcast_start_time | broadcast_end_time | program_title            | season_number | episode_number | episode_title | episode_detail     |
+--------------+----------------------+--------------------+--------------------------+---------------+----------------+---------------+--------------------+
| アニメ1      | 15:00:00             | 18:00:00           | スポーツニュース         |          NULL |           NULL | NULL          | NULL               |
| ドラマ2      | 19:30:00             | 21:00:00           | ドラマA                  |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ1      | 20:00:00             | 21:30:00           | 鬼滅の刃                 |             1 |              2 | 第2話         | 泣けるシーン       |
| ドラマ1      | 20:00:00             | 21:30:00           | 鬼滅の刃                 |             1 |              1 | 第1話         | 感動の始まり       |
+--------------+----------------------+--------------------+--------------------------+---------------+----------------+---------------+--------------------+
4 rows in set (0.00 sec)
```
このクエリでは、program_slots テーブルとそれに紐づく他のテーブルを結合して、本日放送される番組の情報を取得しています。DATE(ps.start_time) = CURDATE() の条件によって、番組の開始時刻が本日のものとなる番組を取得しています。最後に、ORDER BY ps.start_time によって番組の開始時刻順にソートしています。


#### 4.ドラマというチャンネルがあったとして、ドラマのチャンネルの番組表を表示するために、本日から一週間分、何日の何時から何の番組が放送されるのかを知りたいです。ドラマのチャンネルに対して、放送開始時刻、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を本日から一週間分取得してください
<br>
※ ドラマというチャンネル名が、実際のデータベースに存在しているか確認してください。もしデータが存在しない場合、データベースに該当の条件でデータが登録されていない可能性があります。データを挿入してください。

```　.txt
SELECT
    c.channel_name,
    ps.start_time AS broadcast_start_time,
    ps.end_time AS broadcast_end_time,
    s.season_number,
    e.episode_number,
    e.title AS episode_title,
    e.episode_detail
FROM
    program_slots ps
JOIN
    channels c ON ps.channel_id = c.channel_id
JOIN
    programs p ON ps.program_id = p.program_id
LEFT JOIN
    seasons s ON p.program_id = s.program_id
LEFT JOIN
    episodes e ON s.season_id = e.season_id
WHERE
    c.channel_name = 'ドラマ'
    AND DATE(ps.start_time) = CURDATE()
ORDER BY
    ps.start_time;
+--------------+----------------------+--------------------+---------------+----------------+---------------+--------------------+
| channel_name | broadcast_start_time | broadcast_end_time | season_number | episode_number | episode_title | episode_detail     |
+--------------+----------------------+--------------------+---------------+----------------+---------------+--------------------+
| ドラマ2      | 19:30:00             | 21:00:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ2      | 19:30:00             | 21:00:00           |             1 |           NULL | NULL          | NULL               |
| ドラマ2      | 19:30:00             | 21:00:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ1      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ1      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ1      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ1      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ1      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ1      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ2      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ2      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ1      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ1      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
| ドラマ1      | 22:00:00             | 23:30:00           |             1 |              1 | 第1話         | 新キャラ登場       |
```
このクエリは、program_slots テーブルを中心にして、チャンネル、番組、シーズン、エピソードの情報を結合して取得しています。チャンネルが 'ドラマ' であり、かつ本日放送される番組の情報を取得し、結果を番組の開始時刻順にソートしています。

#### 5.(advanced) 直近一週間で最も見られた番組が知りたいです。直近一週間に放送された番組の中で、エピソード視聴数合計トップ2の番組に対して、番組タイトル、視聴数を取得してください
