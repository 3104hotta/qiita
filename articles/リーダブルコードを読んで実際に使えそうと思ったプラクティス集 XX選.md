# 引用
1. [リーダブルコード ―より良いコードを書くためのシンプルで実践的なテクニック (Theory in practice) ](https://www.amazon.co.jp/%E3%83%AA%E3%83%BC%E3%83%80%E3%83%96%E3%83%AB%E3%82%B3%E3%83%BC%E3%83%89-%E2%80%95%E3%82%88%E3%82%8A%E8%89%AF%E3%81%84%E3%82%B3%E3%83%BC%E3%83%89%E3%82%92%E6%9B%B8%E3%81%8F%E3%81%9F%E3%82%81%E3%81%AE%E3%82%B7%E3%83%B3%E3%83%97%E3%83%AB%E3%81%A7%E5%AE%9F%E8%B7%B5%E7%9A%84%E3%81%AA%E3%83%86%E3%82%AF%E3%83%8B%E3%83%83%E3%82%AF-Theory-practice-Boswell/dp/4873115655)




## プラクティス集
### ブール値の名前
ブール値の変数名は頭にis・has・can・shouldなどをつける。
> ```
> // Bad
> bool read_password = true;
> 
> // Good
> bool need_password = true;
> bool user_is_authenticated = true;
> ```
否定系ではなく、肯定系を使う。
> ```
> // Bad
> bool disable_ssl = false;
>
> // Good
> bool use_ssl = true;
> ```

### コメントするべきではないことすべきこと
以下のコメントは新しい情報を提供するわけでもなく、読み手がコードを理解しやすくするわけでもない。コードからすぐにわかることをコメントに書かない。
> ```
> // Bad
> // Accountクラスの定義
> class Account {
>   public:
>   // コンストラクタ
>   Account();
>        
>   // profitに新しい値を設定する
>   void SetProfit(double profit);
>       
>   // このAccountからprofitを返す
>   double GetProfit();
> };
> ```
以下のコメントは「新しい情報」を提供していないが、コメントがないよりはマシ。コードを理解するよりも、コメントを読んだ方が早く理解できる。
> ```
> // Good
> // 2番目の'*'以降を全て削除する
> name = '*'.join(line.split('*')[:2])
> ```

### コメントに実例を使う
文章で処理を説明すると複雑になってしまうものは実例を使う。
> ```
> // Bad
> // 'src'の先頭や末尾にある'chars'を除去する
> String Strip(String src, String chars) { ... }
> 
> // Good
> // 実例: Strip("abba/a/ba", "ab")は"/a/"を返す
> String Strip(String src, String chars) { ... } 
> ``` 

> ```
> // Bad
> // 'v'の「要素 < pivot」が「要素 >= pivot」の前に来るように配置し直す。
> // それから「v[i] < pivot」になる最大の'i'を返す(なければ-1を返す)。
> int Partition(vector<int>* v, int pivot);
> 
> // Good
> // 実例: Partition([8,5,9,8,2], 8)の結果は[5,2|8,9,8]となり、1を返す。
> int Partition(vector<int>* v, int pivot);
> ```

### コードの意図を書く
以下はコードの内容をそのままコメントしているだけ
> ```
> // Bad
> void DisplayProducts(list<Product> products) {
>   products.sort(CompareProductByPrice);
>
>   // listを逆人にイテレートする
>   for (list<Product>::reverse_iterator it = products.rbegin(); it != products.rend(); ++it)
>     DisplayPrice(it->price);
>   ...
> }
> ```
以下のようなコメントだとBetter
> ```
> // 値段の高い順に表示する
> for (list<Product>::reverse_iterator it = products.rbegin(); ... )
> ```

### 条件は否定形よりも肯定形を使う
if(!debug)ではなく、if(debug)を使う。
> ```
> // Bad
> if (a != b) {
>   // 第1のケース
> } else {
>   // 第2のケース
> }
> 
> // Good
> if (a == b) {
>   // 第2のケース
> } else {
>   // 第1のケース
> }
> ```
単純で関心や注意を引く場合は否定形の条件を使っても良い。
> ```
> if not file:
>   // エラーをログに記録する
> else:
>   // ...
> ```

### 関数から早く返す
> ```
> // Good
> public boolean Contains(String str, String substr) {
>  if (str == null || substr == null) return false;
>  if (substr.equals("")) return true;
> 
>   ...
> }
> ```

### ネストを浅くする
> ```
> // Bad
> if (user_result == SUCCESS) {
>   if (permission_result != SUCCESS) {
>     reply.WriteErrors("error reading permissions");
>     reply.Done();
>     return;
>   }
>     reply.WriteErrors("");
> } else {
>     reply.WriteErrors(user_result);
> }
> reply.Done();
> 
> // Good
> if (user_result != SUCCESS) {
>   reply.WriteErrors(user_result);
>   reply.Done();
>   return;
> }
> if (permission_result != SUCCESS) {
>   reply.WriteErrors(permission_result);
>   reply.Done();
>   return;
> }
> reply.WriteErrors("");
> reply.Done();
> ```

### 説明変数
説明変数で式の意味を説明する。
> ```
> // Bad
> if line.split(':').strip() == "root":
>   ...
>
> // Good
> username = line.split(':').strip()
> if username == "root":
>   ...
> ```
### 要約変数
> ```
> /* Bad */
> if (request.user.id == document.owner_id) {
>   // ユーザはこの文書を編集できる
> }
> ...
> if (request.user.id != document.owner_id) {
>   // 文書は読み取り専用
> }
>
> /* Good */
> final boolean user_owns_document = (request.user.id == document.owner_id);
>
> if (user_owns_document) {
>   // ユーザはこの文書を編集できる
> }
> ...
> if (!user_owns_document) {
>   // 文書は読み取り専用
> }
> ```
しかし、役に立たない一時変数は削除してよし
- 複雑なコードを簡潔にしているわけではない。
- ` datetime.datetime.now()`のままでも意味はすぐに把握できる
- 一度しか使っていない。重複コードの削除になっていない。
> ```
> // Bad
> now = datetime.datetime.now()
> root_message.last_view_time = now
> 
> // Good
> root_message.last_view_time = datetime.datetime.now()
> ```

### ド・モルガンの法則を使う
> ```
> // Bad
> if (!(file_exists && !is_protected)) Error("Sorry, could not read file.");
> 
> // Good
> if (!file_exists || is_protected)) Error("Sorry, could not read file.");
> ```

### 変数のスコープを縮める
