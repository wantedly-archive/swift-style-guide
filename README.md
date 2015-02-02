# Swiftコーディング規約@Wantedly 

## はじめに

Swiftのコードは多様な記述の仕方ができるので柔軟でかつ表現力もありますが、チームで開発を行うとどうしても記述の仕方が統一できず可読性も上がりません。弊社（[Wantedly](https://www.wantedly.com/)）でSwiftの[アプリ](https://itunes.apple.com/jp/app/wired-vicenadono-hao-qi-xinwo/id913849903?mt=8)を開発した経験をもとにアプリ開発におけるコーディングスタイルガイドを作成しました。このコーディング規約がベストプラクティスだというわけではなく、Swiftもまだまだ手探りなところもあるので、参考情報としてご参照ください。また、規約の範疇ではないですがエラーになりやすい記述も合わせてフォローしています。

## バージョン

v0.2

改版履歴は文末を参照ください。

## コーディング規約の必要性について

Swiftはプログラマがリスクを取ることによってより簡素に端的に記述ができたり、型推論が強力なので型の明記を省略して記述ができます。チームでSwift開発を行う場合は、詳細に記述するのか、省略して記述するのか、またはその間のどれぐらいの塩梅にするのかコンセンサスがないと統一されない多様な表現のコードが溢れてしまいます。

このSwiftコーディング規約は詳細と省略の中間になります。以下のポリシーで作成しています。

- 他のプログラマがコードレビューする際に必要な情報は省力しない
- プロパティのnilオプショナルはプログラマ判断で動作時にnilにならない判断ができる場合アンラップで定義する

このポリシーは開発チームによって変わってくると思うので、この規約の観点をベースにポリシーによって変更して運用いただければと思います。

## 作成中について

この規約は作成中です。何分ボリュームがあるので。作成中ですがフィードバックをいただけるかと思い公開しました。
今後改変を重ねてブラッシュアップをしていきます。

## 前提実行環境

- Xcode6.0.1 (6A317)
- Swift1.0

## 型について

型について自明や類推できるという表現を使用しているところは、Swift言語の型推論としてではなく、プログラマがコードリーディング時に判断できるかという観点で使用しています。

# Swiftコーディング規約

## 変数, 定数定義

### 変数, 定数共通

関連性のある定義の場合は１行で記述してもよい。

```Swift
let row: Int, column: Int
```

右辺が値で初期化する場合は左辺に型を記述しない。

```Swift
// ◯
let text = "Hoge Fuga"

// ☓
let text: String = "Hoge Fuga"

// ◯
let flg = false

// ☓
let flg: Bool = false

// ◯
let frame = CGRectZero

// ☓
let frame: CGRect = CGRectZero
```

ただし型が曖昧な場合は明記する。

```
// ◯ Int / UInt / NSInteger / NSUInteger の区別がつかないので明記する
let i: Int = 0

// ◯ Float / CGFloat / Double の区別がつかないので明記する
let p: Float = 3.14
```

右辺のクラスをNewすることで初期化する場合は型が自明なので左辺に型を記述しない。

```Swift
// ◯
let image = UIImage(named: "Hoge")

// ☓
let image: UIImage = UIImage(named: "Hoge")

// ◯
let size = CGSize(10.0, 10.0)

// ☓
let size: CGSize = CGRectZero
```

クラスメソッドの戻り値の型がそのクラスの型で初期化する場合は型が類推できるので左辺に型を記述しない。

```Swift
let device = UIDevice.currentDevice()

let app = UIApplication.sharedApplication()
```

キャストして代入する場合は変数定義の型明記を省力する

```Swift
// ◯
let text = data["text"] as String


// ☓
let text: String = data["text"] as String
```

### 変数定義

Emptyな配列、連想配列の初期化を伴う定義は記述方法がいくつかあるので統一する。Appleのドキュメントに準拠。

@see [Collection Types](https://developer.apple.com/librarY/prerelease/mac/documentation/Swift/Conceptual/Swift_Programming_Language/CollectionTypes.html)


```Swift
// 配列
// ◯
var elements = [Int]()

// ☓
var elements: [Int] = []

// ☓
var elements: [Int] = [Int]()

// ☓
var elements = Array<Int>()


// 連想配列
// ◯
var namesOfIntegers = [Int: String]()

// ☓
var namesOfIntegers: [Int: String] = [Int: String]()

// ☓
var namesOfIntegers = Dictionary<Int, String>()

```

グローバルな変数は宣言しない。

```Swift
// ☓
var globalValue: String = "init value"

class Hoge {

}
```

### 定数定義

変数を定義する場合は、定数として定義できないか検討し、どうしても変数である必要がある場合以外は定数で定義する。
（ローカル変数でも変数に再代入を行わない、ArrayやDictinaryがImmutable etc.)

```Swift
let notChangeString = "Hoge"
let notChangeArray = ["Hoge", "Fuga"]
let notChangeDictinary = ["Hoge": "Fuga"]
```

グローバルスコープ、クラススコープでの定数はprefixに小文字のkを記述する。

```Swift
// Swift
let kAnimationDuration = 0.3
```

```Objective-C
// Objective-C
#define kAnimationDuration 0.3
```

右辺が式の定義も可能。

```Swift
let kGoogleAnalyticsTrackingID = NSBundle.mainBundle().objectForInfoDictionaryKey("GoogleAnalyticsTrackingID") as String
```

構造体を用いたグローバル定数定義。

TODO:

## 文字列

SwiftのStringとObjCのNSStringは異なるクラスである。文字列はSwiftのString型を利用する。

```Swift
// ◯
var text: String?

// ☓
var text: NSString?
```

NSStringとして利用したい場合はNSStringにコンバートする。

```Swift
// ◯
("Swift text" as NSString).lowercaseString

// ☓
"Swift text".bridgeToObjectiveC().lowercaseString
```

文字列のカウントはcountElements関数を利用する。countElementsは各文字のバイト数をもとに正確にカウントする。
NSStringのlengthはUTF-16を2バイトコードとみなしてカウントしている。

```Swift
// ◯
countElements("Swift text")

// ☓
("Swift text" as NSString).length
```

文字列のゼロレングスチェックはisEmptyプロパティを利用する。

```Swift
// ◯
if "Hoge".isEmpty { }

// ☓
if countElements("Swift text") == 0 { }

// ☓
if ("Swift text" as NSString).length == 0 { }
```

文字列の比較は等価演算子で行う。

```Swift
let flg: Bool = "hoge" == "fuga"
```

文字列のフォーマットはSwiftの記法を利用する。ただし

```Swift
// ◯
"\(hoge) is \(fuga)"

// ☓
NSString(format: "%@ is %@", hoge, fuga)

// ◯
let someCGFloat: CGFloat = 0.25
String(format: "%.01f", Float(someCGFloat))

// ☓ Floatにコンバージョンしないと、0.0となり正しくない
let someCGFloat: CGFloat = 0.25
NSString(format: "%.01f", Float(someCGFloat))
```

## 数値

整数値型はSwiftの数値型を利用する。
iOS SDKのAPIでObjCではNSInteger, NSUInteger型のところはSwiftではInt, UIntに置き換えられている。

```Swift
// 例
// UITableViewDatasource
func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int
```

少数値型はSwift少数値型を利用する。ただしObjCのCGFloat型はSwiftのFloat型で利用しない。CPUが64ビットか32ビットかでCGFloat型がFloat型か、Double型か挙動がかわるため。

```Swift
// 例
// UITableViewDelegate
func tableView(tableView: UITableView, heightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat
```

Float型をCGFloat型と計算する場合や戻り値でCGFloat型として返したい場合はコンバージョンする。

```Swift
let f: Float = 1.0
let c: CGFloat = 1.0
let r = CGFloat(f) + c
```

Swiftの数値型、Bool型はnilの状態をとれるので活用する。
ObjCではスカラー型はnilをとれないので、nil値をとる必要がある場合はNSNumberを利用していた。

```Swift
var flg: Bool?
var index: Int?
```

## nil

Swiftのnilは'nil'であることを示しており、ObjCのnilが存在しないオブジェクトのポインタである点が異なる。

変数はオプショナルにすることでどんな型でもnilを代入できる。

```Swift
var hoge: AnyObject? = nil
```

連想配列の値もnilを代入することができる。

```Swift
var hoge: String?
let hash: [String: String?] = ["hoge": hoge, "fuga": "not nil"]
println(hash)
// -> [hoge: nil, fuga: Optional("not nil")]
```

## 関数

戻り値がVoid型の場合、戻り値の型の記述を省略する。

```Swift
// ◯
func chnageWorld(text: String) {  }

// ☓
func chnageWorld(text: String) -> () { }

// ☓
func chnageWorld(text: String) -> Void { }
```

第一引数は可読性を考慮して、必要であれば外部引数名を利用する。

```Swift
// ◯
func dateFromString(dateString: String) -> NSDate
dateFromString("2014-03-14") // 引数がStringを取ることが関数から類推できる

// ☓
func dateFromString(#dateString: String) -> NSDate
dateFromString(dateString: "2014-03-14") // 冗長

// ◯
func convertPointAt(#column: Int, #row: Int) -> CGPoint
convertPointAt(column: 42, row: 13) 

// ☓
func convertPointAt(column: Int, #row: Int) -> CGPoint
convertPointAt(42, row: 13) // 第一引数がわかりにくい
```

第一引数の引数名を省略可能でかつデフォルト値を設定する場合は_を記述する。

```Swift
func log(_ message: String = "default message") 

log("this is message") // -> this is message
log()                  // -> default message
```

デフォルト引数を活用する。

```Swift
// ◯
func stringFromDate(date NSDate = NSDate()) -> String
let text = stringFromDate()

// ☓
let date = NSDate()
func stringFromDate(date NSDate) -> String
let text = stringFromDate(date)
```

## クロージャ

クロージャの引数の記述は略記しない。

```Swift
// ◯
let funcClosure = { (text: String) in
  println(text)
}

// ☓
let funcClosure = { text in
  println(text)
}
```

クロージャの１行目の記述が長い場合は引数の前で改行する。

```Swift
let funcClosure = { 
  (text: String) in
  println(text)
}
```

クロージャ型の変数を定義し初期値（クロージャ）を代入する場合。

```Swift
// クロージャで初期化する
let funcClosure = { (text: String) in
  println(text)
}
funcClosure("Hoge")
```

クロージャ型の変数を定義し、あとでクロージャを代入する場合。

```Swift
// あとでクロージャを代入する
var funcClosure: ((text: String) -> ())?
funcClosure = { (text: String) in
  println(text)
}
funcClosure!(text: "Hoge")
```

可能であれば、後置記法(trailing closure)を利用して記述する。

クロージャの引数の型が類推できる場合は簡易引数名を利用する。

クロージャ内コードが１行でかつ戻り値を要求する場合はreturnの記述を省略する。

```Swift
// ◯
["Chris", "Alex", "Ewa", "Barry", "Daniella"].map {
  ($0 as NSString).lowercaseString
}

// ☓
["Chris", "Alex", "Ewa", "Barry", "Daniella"].map({
  ($0 as NSString).lowercaseString
})

// ☓
["Chris", "Alex", "Ewa", "Barry", "Daniella"].map { (value: String) -> String in
  (value as NSString).lowercaseString
}

// ☓
["Chris", "Alex", "Ewa", "Barry", "Daniella"].map {
  return ($0 as NSString).lowercaseString
}
```

クロージャ内コードが１行でかつ戻り値を要求しない場合は最後にreturnのみ記述する。
戻り値がVoidにも関わらず、戻り値を返却することでエラーになるのを防ぐため。

```Swift
let funcClosure = { (text: String) -> String in
  println(text)
  return
}
```

## 制御構文

### if文

if に続く評価式は括弧（）で括らない。

```Swift
// ◯
if a == b { }

// ☓
if (a == b) { }
```

### for文, for-in文

ループ処理はfor-in文で記述する。

```Swift
// ◯
for index in 0..<3 {
    println("index is \(index)")
}
    
// ☓
for var index = 0; index < 3; ++index {
    println("index is \(index)")
}


// ◯
for value in array {
  println("index is \(index)")
  println("value is \(value)")  
}

// ☓
for var index = 0; index < array.count; index++ {
  let value = array[index]
  println("value is \(value)")
}
```

for文のキャストは配列の要素に対して個別に行わず、配列をキャストした上でループする。

```Swift
// ◯
for view in self.view.subviews as [UIView] {

}

// ☓
for temp in self.view.subviews {
  let view = temp as UIView
}
```

### Switch文

ケースの処理で何も実施しない場合は、`break`を記述する。

```Swift
switch section {
  case .ProfileSection:
    println("HOGE")
  case .SettingSection:
    break // do nothing
  default:
    break // do nothing
}
```

## 記述

### ブラケット

メソッドブラケットと制御構文のブラケット（if/else/switch/while etc.）は式と同じ行でオープンブラケットを記述する。

```Swift
// ◯
func changeWorld() {
    //Do something
}

// ☓
func changeWorld()
{
    //Do something
}

// ◯
if user.isHappy {
    //Do something
} else {
    //Do something else
}

// ☓
if user.isHappy
{
    //Do something
}
else {
    //Do something else
}
```

### セミコロン

式の終端にセミコロンは記述しない。

```Swift
// ◯
println("hoge")

// ☓
println("hoge");
```

### ファイル終末

ファイル終末は改行で終わるようにする。

## 列挙型 (enum)

enumとcaseはパスカルケースで記述する。

caseがInt型で0からのシーケンスの場合は、enumの型とcaseの値は記述しない。

```Swift
// ◯
enum CustomResult {
    case Success, Error

}

// ☓
enum CustomResult: Int {
    case Success = 0, Error
}
```

各caseが値を取る場合はcaseごとに改行して記述する。

```Swift
// ◯
enum CustomResult: String {
    case Success = "success"
    case Error = "error" 
}
```

型が自明な変数に代入するenumの値はenum名を省略する。

```Swift
// ◯
button.setTitle("title", forState: .Normal)
var state: UIControlState = .Normal

// ☓
button.setTitle("title", forState: UIControlState.Normal)
var state: UIControlState = UIControlState.Normal
```

グローバルな列挙型を定義するときは、クラススコープ、構造体スコープで記述できないか検討する。

```Swift
// ◯
class NetworkManager {
  enum CustomResult: String {
      case Success = "success",
      case Error = "error" 
  }
  var customResult: CustomResult? 
}

// ☓
enum CustomResult: String {
    case Success = "success",
    case Error = "error" 
}

class NetworkManager {
  var customResult: CustomResult? 
}
```

## クラス(Class)、構造体(Struct)

### init

NSObjectを継承している場合は、initにoverrideを記述し、super.init()をコールする。

```Swift
class ArticleData: NSObject {    
    override init() {
        super.init()
    }
}
```

NSObjectを継承していない場合は、以下のとおり。

```Swift
class ArticleData {    
    init() {
        
    }
}
```
 
### convenience init

TODO: 

### プロパティ

initで初期化するプロパティはオプショナルやアンラップを用いない。
let、varどちらで定義するかはプロパティの変更があるかどうかで判断する。
ただし、利用時にプロパティがnilを代入する可能性がある場合はオプショナルで定義する。

```Swift
// ◯
class ArticleData {
  let createdAt: NSDate
  
  init() {
    self.createdAt = NSDate()
  }
}

// ☓
class ArticleData {
  let createdAt: NSDate!
  
  init() {
    self.createdAt = NSDate()
  }
}
```    

プロパティのgetのみを記述する場合はget{ }を省略する。

```Swift
// ◯
class ArticleData {
  var createdAt: NSDate { return NSDate() }
}

// ☓
class ArticleData {
  var createdAt: NSDate { 
    get {
      return NSDate() 
    }
  }
}
```    

TODO: didSet


### self

TODO:

### description

NSObjectを継承するクラスの場合はdescriptionプロパティをオーバーライドする。

```Swift
clas DataModel: NSObject {
  override var description: String { return "something debug message" }
}
```

NSObjectを継承しないクラスの場合は、Printableプロトコルに準拠する。

```Swift
clas DataModel {
}

extension DataModel: Printable {
  var description: String { return "something debug message" }
}
```

### アクセスコントロール

TODO:

### デリゲート

TODO:

## プロトコル (Protocol)

TODO:

## 拡張 (Extension)

TODO:

## ObjC

### id型

TODO:

### デリゲート

TODO:

### define

TODO:

### マクロ

TODO:

## Interface Builder アウトレット, アクション

アウトレットはweakでかつアンラップで定義する。ストリートボードからプロパティを自動生成する場合はprivate修飾子を追記する。
ビューを親ビューからリムーブしてかつ継続的にこのビューを利用したい場合はweakではなくstrongで記述する。
このプロパティを外部クラスから利用したい場合はprivate修飾子を記述しない。

```Swift
// ◯ 
@IBOutlet private weak var button: UIButton!

// ☓
@IBOutlet weak var button: UIButton!

// ☓
@IBOutlet var button: UIButton!

// ☓ 
@IBOutlet weak var button: UIButton?
``` 

アクションの引数の型はアクション元のクラスを定義する。

```Swift
// ◯ 
@IBAction func buttonTapped(sender: UIButton) {
  println("button tapped!")
}

// ☓ 
@IBAction func buttonTapped(sender: AnyObject) {
  println("button tapped!")
}
``` 

## 循環参照問題

TODO:

## マクロコメント

### #pragma mark

Swift

```Swift
// MARK: - View life cycle
```

Objective-C

```Objective-C
#pragma mark -
#pragma mark View life cycle
```

### #warning

Swift

```Swift
// WARNING: Clean up this code after testing
```

Objective-C

```Objective-C
#warning Clean up this code after testing
```

### #ifdef

Build settings -> Swift Compiler Custom Flags -> -DEBUG (yes, including the -D prefix)

```Swift
// Objective-C and Swift
#if DEBUG
// DEBUG CODE
#else
// PRODUCTION CODE
#endif
```


## ログ出力

ログ出力は以下の観点よりNSLogを用いる。

- NSLogはスレッドセーフだが、printlnはスレッドセーフではない
- NSLogはデイバイスコンソールに出力されるが、printlnは出力されない

```Swift
// ◯
NSLog("%@ is %@", hoge, fuga)

// ☓
println("\(hoge) is \(fuga)")
```

ただし、printlnはNSLogに対して以下のメリットもある。

- 出力が高速である
- 変数を文字列にフォーマットしなくても、適宜文字列を出力する

## シングルトン

シングルトンはstructのstatic定数で定義する。

```Swift
// ◯
class Singleton {
    class var sharedInstance : Singleton {
        struct Static {
            static let instance : Singleton = Singleton()
        }
        return Static.instance
    }
}
```

以下の方式では記述しない。

グローバル定数で定義

```Swift
// ☓
let _SingletonSharedInstance = Singleton()

class Singleton  {
    class var sharedInstance : Singleton {
        return _SingletonSharedInstance
    }
}
```

dispatch_onceで実装

```Swift
// ☓
class Singleton {
    class var sharedInstance : Singleton {
        struct Static {
            static var onceToken : dispatch_once_t = 0
            static var instance : Singleton? = nil
        }
        dispatch_once(&Static.onceToken) {
            Static.instance = Singleton()
        }
        return Static.instance!
    }
}
```

## Reference
- [The Swift Programming Language](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/)
- [Using Swift with Cocoa and Objective-C](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/BuildingCocoaApps/index.html#//apple_ref/doc/uid/TP40014216)
- [The Official raywenderlich.com Swift Style Guide](https://github.com/raywenderlich/swift-style-
guide)
- [Transitioning from Objective-C to Swift](http://b2cloud.com.au/tutorial/transitioning-from-objective-c-to-swift/)
- [Morizotter Blog](http://blog.morizotter.com)


## 改版履歴

### v0.2 2014/10/05

- 【追加】コーディング規約の必要性について
- 【変更】アウトレットプロパティでprivateを記述 thx @mono0926
- 【変更】プロパティのアンラップについて thx @questbeat
- 【変更】ログ出力 thx @questbeat @mono0926

### v0.1 2014/10/03
- 初版公開
