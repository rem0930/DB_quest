# DB_quest

以下では、テーブルを構築するドキュメントを記載します。

#### まず、MySQLにログインし、APEAMO　という新しいデータベースを作成します。

```.spl
CREATE APEMO;
USE APEMAO; 
```

次に、ステップ1で設計したテーブルをデータベースに作成します.

#### １つ目は、チャンネル情報を格納するテーブルです(Channels)
```.spl
CREATE TABLE Channels (
  channel_id INT PRIMARY KEY AUTO_INCREMENT,
  channel_name VARCHAR(200) NOT NULL   
);
```
'channnel_id' チャンネルを一意に識別する列です。INTは整数を表し、チャンネルごとにユーニークなIDが自動的に増分されます。  
'PRIMARY KEY' : 'channel_id' がこのテーブルの主キーであることを示します。  
'channel_name' はチャンネルの名前を格納する列で最大2００文字。NULL値は許容されません。



#### 2つ目は、ジャンルを格納するテーブルです(genres)
```.sql
CREATE TABLE genres (
  genre_id INT PRIMARY KEY AUTO_INCREMENT,
  genre_name VARCHAR(50) NOT NULL
);
```

'genre_id' は主キーであり、自動増分する整数型です。  
'genre_name' はジャンルの名前を表す文字列型で、一位性が保たれ、NULL値は許容されない。



#### ３つ目は、番組情報を格納するテーブルです(programs)
```.sql
CREATE TABLE programs (
  program_id INT PRIMARY KEY,
  title VARCHAR(100) NOT NULL,
  program_detail TEXT,
  genre VARCHAR(50)
);
```
'program_id' は主キーです。  
'title'　は番組のタイトルを格納する列で、100字までで表せて、NULLは許容しません。  
'program_detail' は番組詳細を格納する列です。　'TEXT' は詳細な説明を格納するのに適したデータ型です。  


#### 4つ目は、番組枠テーブルです（program_slots）
```.sql
CREATE TABLE program_slots (
  slot_id INT PRIMARY KEY,
  channel_id INT,
  program_id INT,
  start_time INT,
  end_time TIME,
  FOREIGN KEY (channel_id) REFERENCES channels(channel_id),
  FOREING KEY (program_id) REFERENCES programs(program_id)
);
```


#### 5つ目は、シーズン情報を格納するテーブルです(seasons)
```.spl
CLEATE TABLE season (
  season_id INT PRIMARY KEY,
  program_id INT,
  season_number INT,
  FOREING KEY (program_id) REFERENCES programs(program_id)
);
```
'season_id' は主キーです。  
'program_id' は外部キーであり、 'programs' テーブルの　'program_id'と関連づけられる。  
'season_number' はシーズンの番号を表す整数型です。


#### 6つ目は、エピソード情報を格納するテーブルです(episodes)
```.sql
CREATE TABLE episodes (
  episode_id INT PRIMARY KEY,
  season_in INT,
  episode_number INT,
  title VARCHAR(100) NOT NULL,
  episode_detail TEXT,
  duration_minutes TIME,
  release_date DATE,
  FOREING KEY (season_id) REFERENCES seasons(season_id)
);
```
'episode_id' は主キーです。  
'season_id' は外部キーであり、 'seasons' テーブルの 'season_id' と関連づけられます。  
'episode_number' はエピソードの番号を表す整数型です。
'title' はエピソードのタイトルを表す文字列型で、NULL値は許容されません。
'duration_minutes' はエピソードの動画時間（分単位）を格納する列で,INT型で整数で表現。
'release_date' エピソードの公開日を格納する列です。DATE型で、日付を表現する。　　
'season_id' 列が 'season' テーブルの 'seasn_id' 列を参照する外部キー制約を持っています。  



#### 7つ目は、視聴数を格納するテーブルです。(viewership)
```.sql
CREATE TABLE viewership (
  viewership_id INT PRIMARY KEY,
  episode_id INT,
  slot_id INT,
  viewership_count INT,
  FOREIGN KEY (episode_id) REFERENCES episodes(episode_id),
  FOREIGN KEY (slot_id) REFERENCES program_slots(slot_id)
);
```








