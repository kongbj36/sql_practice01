# sql_practice01

01_DB種類
1)Key-balue Database(ex)redis
2)Rational Database:Oracle, Mysql, Postgress등
->정규화가 거의 필수: 데이터가 중복될때 테이블을 별도로
입출력속도보다 정확도(입출금등도 관계형데이터로)
3)Graph Database:비행기노선, 전염맵, sns친구관계
4)Document Database:정규화없이사용(ex:몽고DB)
->데이터분산에 좋음: 게시판, 실시간채팅, 온라인게임, SNS
->정확도가 좀 떨어짐
5)Column-familiy Database: 2)하고 4)을 섞은느낌(카산드라)
6)Search engine: elastic, Amazon CloudSearch, Google Cloud Search
->index보관에 특화됨

02_DBMSとは
-Database Management System
-SQLはDBMSでDBを操作するプログラミング言語
-DBMSはDBを操作するサーバーも含めている

3_Dbeaver
DBMSをGUIで操作できるプログラム

4_GUIツール
DBをDBMS操作するとき、CLIとGUIで操作ができるけど
大規模のDBではGUIで操作する。
データベースやテーブルを作るときも基本GUIで操作するのが一般的。

5_列のデータタイプ
列を生成するとdata typeを指定する必要がある。
「文字」、「定数」、「少数」、「時間」、「True,False」などがある。

6_**********基本的なSQL文法**********
基本select
1)select * from テーブル名
1-1)select * from データベース名.テーブル名
1-2)select カーラム名 from テーブル名
1-2-1)select カーラム名1, カーラム名2, カーラム名3 from テーブル名

order by
2)select * from テーブル名 order by カーラム名 DESC;
*ASCはdefault価
2-1)select * from テーブル名 order by 3 DESC;
→該当するテーブルの3番目のカーラムのDESC順番で
2-2)select * from テーブル名 order by カーラム1, カーラム2 DESC

where文
3)select * from product where category = '家具'
3-1)select * from product where price > 50000
3-2)select * from product where price between 5000 and 8000
3-2-1)select * from product where category = '家具' and price = 5000;
3-2-3)select * from product where (category = '家具' or category = '服') and price = 3000;

***in→orをいくつか使わなくて、in ()で簡単にできる
4)select * from product where category = '靴' or category = '家電' or　category = '食品'
→select * from product in ('靴', '家電', '食品')
*同じカーラム名だけ使える
*or何個より、in ()のほうが処理速度が速い！
*in ()の中でまたselectを使用できる。それをサーブクエリと呼ぶ。

like文：カーラム名で一部入っている文字列などを全部含む
5)select * from product where 商品名 like 'スポーツ'
としたら、スポーツだけ検索するのでlikeを使う意味がない。
likeを使う時は%%を一緒に使う必要がある！（ワイルドカード）
select * from product where 商品名 like '%スポーツ%'
としたら、商品名の中で山スポーツ、レジャースポーツ、子供スポーツなどスポーツの単語が入っているデータを全部表示する。
5-1)select * from product where 商品名 like 'スポーツ%'
こんな感じだったら、スポーツ少年団みたいにスポーツで始まる団子を調べる

max() min()
6)select max(price) from product
productテーブルのpriceカーラムで一番高い値だけ出力する
6-1)select min(price) from product
productテーブルのpriceカーラムで一番低い値だけ出力する
6-2)max()で出る値のカーラム名をpriceから「最大price」に変更したいときは
select max(price) as 最大price from product
で、出力したカーラム名を変更することができる

avg()
7)select avg(price) from product
productテーブルのpriceカーラムの平均値を出力する
7-1)vipの顧客の平均金額を調べるクエリ
select avg(price) from product where 顧客レベル = 'vip'

sum()
8)select sum(price) from product
productテーブルのpriceカーラムのすべての行の足し算を出力する

count()
9)select count(price) from product
productテーブルのpriceカーラムのすべての行の数を出力する
*これはselect count(*) from productとかこれはselect count(他のカーラム名) from productをしても、同じ結果になる

distinct：該当するカーラムの値で重複する値は除いて出力
10)select distinct 会員レベル from お客様情報
→お客様情報で会員レベルの種類を確認できる
*uniqなカーラムはdistinctを付けてもそもそも重複する値がないから、distinctを使う必要がない

数字データの+-*/
11)select price * 0.7 from product
70%セールした値段
12)select price * 0.7 as 70%セール値段, 商品名 + sale70% from product
12-1)
select 사용금액 / 결제횟수 from card

concat：二つのカーラムを繋げて出力する
13)select concat(商品名, セールパーセント) from product
→バナナ0.3
13-1)少し見るとき理解しやすくて調整
select concat(商品名, 'は', セールパーセント * 100, '%セール中です') as 'セール表示' from product
カーラム名は「セール表示」になり、データは「バナナは30%セール中です」のようになる
*****Oracleの場合は少しconcat文法が違う
select 商品名 || 'は' || セールパーセント * 100 ||  '%セール中です') from product

replace() →テーブルの参照なしで使う
14)select replace('東京に住んでいる東京の人', '東京', '福岡')
→福岡に住んでいる福岡の人
14-1)
select replace(カーラム名, ' ', '') from テーブル名
→カーラムの値の中で空白を除去できる（trim関数を使ってもいい）

substr('文字列', 5, 3)→文字列の５番目の文字から3桁の文字を出力
15)select substr('abcdef', 4, 2) 
de
*select substr('abcdef', 4, 531)のように最後の文字を超えたら普通に最後の文字まで出力 
def

16)文字列の部分を変更
insert('test@gmail.com', 1, 4, 'aflddsafdjklafd')
aflddsafdjklafd@gmail.com
*****Oracleはinsert機能がないので、substrと+を利用する

17)left(), right()
電話番号のカーラムで
08012345678形式のデータの後ろの4桁を出力
select right(電話番号, 4) as '後ろ番号' from テーブル名
*substr()でも同じことができる
select substr(電話番号, 9, 4) as '後ろ番号' from テーブル名

18)GREATEST / LEAST
SELECT GREATEST(5, 3, 2, 1, 4);
→5
SELECT LEAST(5, 3, 2, 1, 4);
→1

19)FLOOR/CEIL
SELECT FLOOR(10.1); →10
SELECT FLOOR(10.9); →10
SELECT CEIL(10.1); →11
SELECT CEIL(10.9); →11

20)ROUND/TRUNCATE
ROUND：반올림
TRUNCATE：내림

SELECT ROUND(10.777, 2); →10.78
SELECT ROUND(10.773, 2); →10.77
소수점 셋째자리에서 반올림에서 둘째자리까지 표현

SELECT TRUNCATE(10.777, 2);  →10.77
소수점 셋째자리를 버리고 둘째자리까지 표현

21)거듭제곱
SELECT POWER(4, 2) →16

22)절대값
SELECT ABS(-100.34)   →100.34

**********23)サーブクエリ**********
使用方法
➀select avg(price) from product
→productテーブルにあるpriceカーレムの平均値を出力：例えば3329円

その平均値より高いpriceだけ表示したい
➁select * from product where price > 3329

➀、➁のクエリを一回でするのがサーブクエリを利用してできる
select * from product where price > (select avg(price) from product)

他の例
select price / (select avg(price) from product) from product
→これもサーブクエリの例になれる

**********24)group by**********
SELECT department, COUNT(*) AS employee_count
FROM employees
GROUP BY department;

SELECT customer_id, SUM(amount) AS total_spent
FROM orders
GROUP BY customer_id
HAVING SUM(amount) >= 1000;
→HAVINGの代わりにWHEREを使うとエラーになる
HAVINGはGROUP BYをフィルターするときだけ使う

**********25)if()**********
if(条件式, 条件式が合ってるときはこれ, 条件式が間違ってるときはこれ)
select 顧客名, 使用金額, if(使用金額>100000, 'vip', 'normal') from card

**********26)case when end**********
➀ifは条件式が２つの比較の場合、caseは複数の比較の場合

SELECT 顧客名, 使用金額,
CASE
  WHEN 使用金額 >= 200000 THEN 'vip'
  WHEN 使用金額  >= 100000 AND 사용금액 < 200000 THEN 'blue level'
  WHEN 使用金額  >= 0 AND 사용금액 < 100000 THEN 'normal'
  ELSE 'blacklist' 
END
FROM card; 

➁집계함수SUM에서의 사용방법도 있음
SELECT SUM(
  CASE
    WHEN 顧客レベル = 'vip' THEN 3 
    WHEN 顧客レベル = '로열' THEN 2 
    ELSE 1
  END
) 
FROM card

