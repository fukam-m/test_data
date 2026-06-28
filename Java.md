# ☕ Java実践・深掘りマスターガイド (GitHub対応自己学習教材)

この教材は、Javaの基礎を一歩進め、実務で必須となる**オブジェクト指向の高度な概念（カプセル化、継承、ポリモーフィズム）**、モダンJavaの象徴である**ラムダ式とStream API**、そしてバグの少ないコードを書くために不可欠な**メモリ管理の仕組み**までを深く網羅した、詳細版の自己学習用教材です。

GitHubのリポジトリにそのまま配置して読みやすいよう、構造化とタイポグラフィを最適化しています。

---

## 🗂 目次
1. [🧬 オブジェクト指向の核心 (カプセル化・継承・ポリモーフィズム)](#1-オブジェクト指向の核心-カプセル化継承ポリモーフィズム)
2. [🔌 インターフェースと抽象クラス](#2-インターフェースと抽象クラス)
3. [📦 ジェネリクス (汎用型) の理解](#3-ジェネリクス-汎用型-の理解)
4. [🌊 モダンJava：ラムダ式とStream API](#4-モダンjavaラムダ式とstream-api)
5. [🧠 メモリ管理の仕組み (スタックとヒープ)](#5-メモリ管理の仕組み-スタックとヒープ)
6. [🏗️ 実務におけるパッケージ構成とアクセス修飾子](#6-実務におけるパッケージ構成とアクセス修飾子)

---

## 1. オブジェクト指向の核心 (カプセル化・継承・ポリモーフィズム)

基礎的なクラスの作成から、実務レベルの「堅牢で拡張性の高い設計」にするための3大要素を学びます。

### ① カプセル化 (Encapsulation)
クラスの内部データ（フィールド）を外部から直接書き換えられないように隠蔽（隠す）し、特定のメソッド（Getter/Setter）経由でのみアクセスを許可する仕組みです。

#### 📝 具体例
```java
public class Account {
    // private修飾子で外部からの直接アクセスを禁止
    private String owner;
    private int balance;

    // コンストラクタ
    public Account(String owner, int initialBalance) {
        this.owner = owner;
        this.balance = initialBalance;
    }

    // Getter: 値を取得する
    public int getBalance() {
        return this.balance;
    }

    // Setter: 値を変更する（不正な値をバリデーションで弾く）
    public void deposit(int amount) {
        if (amount > 0) {
            this.balance += amount;
            System.out.println(amount + "円入金しました。");
        } else {
            System.out.println("不正な金額です。");
        }
    }
}
```

---

### ② 継承 (Inheritance)
既存のクラス（親クラス / スーパークラス）の性質を引き継ぎ、新しいクラス（子クラス / サブクラス）を作る仕組みです。コードの再利用性を高めます。

#### 📝 具体例
```java
// 親クラス
public class Employee {
    protected String name;
    protected int baseSalary;

    public Employee(String name, int baseSalary) {
        this.name = name;
        this.baseSalary = baseSalary;
    }

    public int calculateSalary() {
        return this.baseSalary;
    }
}

// 子クラス（Employeeを継承）
public class Engineer extends Employee {
    private int technicalAllowance; // 技術手当

    public Engineer(String name, int baseSalary, int technicalAllowance) {
        super(name, baseSalary); // 親クラスのコンストラクタを呼び出す
        this.technicalAllowance = technicalAllowance;
    }

    // オーバーライド（親クラスのメソッドを上書きして再定義）
    @Override
    public int calculateSalary() {
        return this.baseSalary + this.technicalAllowance;
    }
}
```

---

### ③ ポリモーフィズム (Polymorphism / 多態性)
「同じ命令を送りながら、受け取るオブジェクトによって異なる振る舞いをする」仕組みです。これにより、呼び出し側のコードを書き換えることなく、新しい機能を追加できるようになります。

```java
public class CompanyApp {
    public static void main(String[] args) {
        // 親クラスの型として、子クラスのインスタンスを扱うことができる
        Employee emp1 = new Employee("サトシ", 250000);
        Employee emp2 = new Engineer("タカシ", 300000, 50000);

        // 同じメソッドを呼んでいるが、実体に応じて異なる結果が返る
        printSalary(emp1); // 250000円
        printSalary(emp2); // 350000円 (技術手当が加算される)
    }

    public static void printSalary(Employee emp) {
        System.out.println(emp.calculateSalary() + "円");
    }
}
```

---

## 2. インターフェースと抽象クラス

ポリモーフィズムをさらに徹底し、システムの「ルール（仕様）」と「実装」を切り離すための仕組みです。

### 🔌 インターフェース (Interface)
クラスが実装すべき「メソッドの形（名前、引数、戻り値）」だけを定義したものです。複数の中身が異なるクラスに対して、共通の操作インターフェースを強制できます。

#### 📝 具体例
```java
// インターフェースの定義
public interface Vehicle {
    void start(); // 抽象メソッド（処理の中身は書かない）
    void stop();
}

// インターフェースの実装クラス1
public class Car implements Vehicle {
    @Override
    public void start() { System.out.println("アクセルを踏んで加速します。"); }
    @Override
    public void stop() { System.out.println("ブレーキを踏んで停止します。"); }
}

// インターフェースの実装クラス2
public class Bicycle implements Vehicle {
    @Override
    public void start() { System.out.println("ペダルを漕いで進みます。"); }
    @Override
    public void stop() { System.out.println("ブレーキレバーを握って止まります。"); }
}
```

### 🧱 抽象クラス (Abstract Class) との違い
* **抽象クラス (`abstract class`)**: 「共通の処理（具象メソッド）」と「子クラスに変形を強制する処理（抽象メソッド）」を**混在**させたい場合に使います。単一継承しかできません。
* **インターフェース (`interface`)**: 純粋な「仕様の定義」として使います。複数のインターフェースを同時に1つのクラスに実装（多重実装）することが可能です。

---

## 3. ジェネリクス (汎用型) の理解

ジェネリクス（Generics）は、特定のデータ型に依存しない汎用的なクラスやメソッドを作成するための仕組みです。コレクションフレームワーク（`List` や `Map`）の内部でも多用されています。

#### 📝 具体例（型安全なコンテナクラス）
```java
// <T> がジェネリクスの記述。任意の型を指定可能
public class Box<T> {
    private T content;

    public void setContent(T content) {
        this.content = content;
    }

    public T getContent() {
        return this.content;
    }
}

// 利用時
public class Main {
    public static void main(String[] args) {
        // String型を扱うBox
        Box<String> stringBox = new Box<>();
        stringBox.setContent("Java");
        String val = stringBox.getContent(); // キャスト(型変換)が不要

        // Integer型を扱うBox
        Box<Integer> intBox = new Box<>();
        intBox.setContent(100);
    }
}
```
> **📌 メリット:** コンパイル時に厳密な型チェックが行われるため、実行時に予期せぬ型エラー（`ClassCastException`）が発生するリスクを大幅に減らすことができます。

---

## 4. モダンJava：ラムダ式とStream API

Java 8以降、大量のデータ処理（コレクションの操作）を極めてシンプルかつ直感的に記述できる手法が導入されました。

### 🌊 Stream APIによるデータ加工
ループ文（`for`, `if`）を重ねて書いていたデータ処理を、パイプラインのように繋げて記述できます。

#### 📝 具体例（リストから「5,000円以上」の金額だけを抽出し、2倍にして、新しいリストを作る）

**❌ 従来の書き方 (for文とif文の組み合わせ)**
```java
List<Integer> prices = List.of(2000, 6000, 1500, 8000);
List<Integer> result = new ArrayList<>();

for (int price : prices) {
    if (price >= 5000) {
        result.add(price * 2);
    }
}
```

**⭕️ Stream API と ラムダ式 を使った書き方**
```java
List<Integer> prices = List.of(2000, 6000, 1500, 8000);

List<Integer> result = prices.stream()
    .filter(p -> p >= 5000)      // 条件絞り込み (ラムダ式)
    .map(p -> p * 2)             // データの加工
    .toList();                   // 結果をリストにまとめる

System.out.println(result); // [12000, 16000]
```

---

## 5. メモリ管理の仕組み (スタックとヒープ)

Javaの動作メカニズムを知る上で、「どこにデータが置かれているか」を理解することは、パフォーマンスの最適化やメモリリーク（バグ）の防止に直結します。

Javaのメモリ空間は、主に**スタック（Stack）領域**と**ヒープ（Heap）領域**に分かれています。

* **スタック領域**: メソッドの実行情報や、ローカル変数（基本データ型、およびインスタンスへの参照ポインタ）が格納されます。メソッドの終了とともに自動的かつ高速に消去されます。
* **ヒープ領域**: `new` によって生成されたすべての「インスタンス（オブジェクト）の実体」や「配列の実体」が格納されます。どの変数からも参照されなくなったタイミングで、**ガベージコレクション（GC）**によって自動回収されます。

#### 📝 具体例による可視化
```java
public class MemoryDemo {
    public static void main(String[] args) {
        int x = 10;                // スタックに値(10)が直接入る
        User u = new User("タカシ"); // スタックに「u」というポインタが作られ、
                                   // ヒープに作られた「User実体」の住所を指す
    }
}
```

---

## 6. 実務におけるパッケージ構成とアクセス修飾子

大規模なアプリケーション開発では、クラスファイルを役割ごとにフォルダ（パッケージ）分けし、適切なアクセス制限をかけることが重要です。

### 🛡️ アクセス修飾子一覧
適切な公開範囲を設定することで、意図しない場所からの破壊的なアクセスを防ぎます。

| 修飾子 | 同じクラス内 | 同じパッケージ内 | 子クラス（継承先） | 全て（外部） | 目的・用途 |
| :--- | :---: | :---: | :---: | :---: | :--- |
| `public` | ⭕️ | ⭕️ | ⭕️ | ⭕️ | どこからでも使える公開用APIやメイン処理 |
| `protected` | ⭕️ | ⭕️ | ⭕️ | ❌ | 継承を前提とした、子クラスにだけ許す拡張用 |
| *(なし)* | ⭕️ | ⭕️ | ❌ | ❌ | パッケージ内部の隠蔽処理（パッケージプライベート） |
| `private` | ⭕️ | ❌ | ❌ | ❌ | クラス内部だけで使う変数や補助用メソッドの完全隠蔽 |

#### 📁 標準的な実務パッケージ構成（例）
```text
src/
└── main/
    └── java/
        └── com/
            └── example/
                └── app/
                    ├── Main.java              (起動用エントリーポイント)
                    ├── model/                 (データ構造・ビジネスロジック)
                    │   ├── User.java
                    │   └── Account.java
                    ├── repository/            (データベースとの通信処理)
                    │   └── UserRepository.java
                    └── controller/            (ユーザー入出力の制御)
                        └── UserController.java
```

---
🛠️ **お疲れ様でした！オブジェクト指向の高度な概念からモダンな構文、メモリ構造までを抑えれば、実務のソースコードを読むための土台は完璧です。次のステップとして、デザインパターンやフレームワーク（Spring Boot等）の学習に進んでみましょう！**