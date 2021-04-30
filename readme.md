https://doc.rust-jp.rs/rust-by-example-ja/index.html  
https://doc.rust-jp.rs/book-ja/title-page.html

# インストール
```
(1) Visual Studio をインストール
　・link.exe 等が必要になるため。通常の C++ 開発環境があれば良いと思われる。
　・現時点では、「.NET デスクトップ開発」、「C++ によるデスクトップ開発」の２つがインストールされている状態で
　　問題なく Rust を使用できている。

(2) 以下のサイトから Rust 本体をダウンロードしてインストール
　https://www.rust-lang.org/tools/install
```

## 更新、アンインストール等
```
・更新
> rustup update

・アンインストール
> rustup self uninstall

・バージョン
> rustc --version
```

## Cargo
```
cargo new --bin --vcs none <path>

--vcs -> none, git（デフォルト値）/ (version control system)
--bin -> バイナリコード作成
--lib -> ライブラリ作成
--help -> ヘルプ表示
```

```
cargo check
cargo build
cargo run
```

## 警告抑制
```
#[allow(non_camel_case_types)]
#![allow(non_camel_case_types)]

> rustc -W help
```

---
# デバッグプリント
```rust
#[derive(Debug)]
struct TestStruct(i32);

#[derive(Debug)]
struct TestStruct2(TestStruct);

#[derive(Debug)]
struct Person<'a> {
    name: &'a str,
    age: u8
}

#[derive(Debug)]
struct PersonTpl(String, i32);

#[derive(Debug)]
struct PersonTpl2<'a>(&'a str, i32);

fn main() {
	println!("{} {}", "Hello", "World");
	println!("{:?} {:?}", "Hello", "World");
	println!("{1:} {0:}", "Hello", "World");
	println!("{1:?} {0:?}", "Hello", "World");
		
	println!("{} {}, {name}",
		"Hello", "World",
		name = "Rust");
		
	println!("{name:?}, {} {}",
		"Hello", "World",
		name = "Rust");

	println!("{:?}", TestStruct(3));
	println!("{:#?}", TestStruct(3));

	println!("{:?}", TestStruct2(TestStruct(7)));
	println!("{:#?}", TestStruct2(TestStruct(7)));
	
	let mike = Person { name: "Mike", age: 28 };
	println!("{:?}", mike);
	println!("{:#?}", mike);
	
	let john = PersonTpl(String::from("John"), 29);
	println!("{:?}", john);
	println!("{:#?}", john);
	
	let bill = PersonTpl2("Bill", 30);
	println!("{:?}", bill);
	println!("{:#?}", bill);
}
```

---
# fmt::Display

```
{} -> fmt::Display がコールされる
{:?} -> fmt::Debug がコールされる
```
* 例１
```rust
use std::fmt;

#[derive(Debug)]
struct MinMax(i64, i64);

// type std::fmt::Result = Result<(), Error>;
impl fmt::Display for MinMax {
		fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
		write!(f, "({}, {})", self.0, self.1)
	}
}

#[derive(Debug)]
struct Point2D {
	x: f64,
	y: f64,
}

impl fmt::Display for Point2D {
	fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
		write!(f, "x: {}, y: {}", self.x, self.y)
	}
}

fn main() {
	let minmax = MinMax(0, 14);
	println!("Display: {}", minmax);
	println!("Debug: {:?}", minmax);
	println!("Debug: {:#?}", minmax);

	let big_range =   MinMax(-300, 300);
	let small_range = MinMax(-3, 3);

	println!("The big range is {big} and the small is {small}",
	small = small_range,
	big = big_range);

	let point = Point2D { x: 3.3, y: 7.2 };
	println!("Display: {}", point);
	println!("Debug: {:?}", point);
	println!("Debug: {:#?}", point);

// fmt::Binary`はされていないため {:b} はエラーになる。
// println!("binary: {:b}", point);
}
```

* 例２
```rust
use std::fmt; // Import the `fmt` module.

#[derive(Debug)]
struct List(Vec<i32>);

impl fmt::Display for List {
	fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
		let vec = &self.0;

		write!(f, "[")?;
		for (count, v) in vec.iter().enumerate() {
			if count != 0 { write!(f, ", ")?; }
			write!(f, "{}", v)?;
		}
		write!(f, "]")
	}
}

fn main() {
	let v = List(vec![1, 2, 3]);
	println!("{}", v);
	println!("{:?}", v);
}
```

* 例３
```rust
use std::fmt;

struct City {
	name: &'static str,
	lat: f32,  // Latitude
	lon: f32,  // Longitude
}

impl fmt::Display for City {
	fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
		let lat_c = if self.lat >= 0.0 { 'N' } else { 'S' };
		let lon_c = if self.lon >= 0.0 { 'E' } else { 'W' };

		write!(f, "{}: {:.3}°{} / {:.3}°{}",
			self.name, self.lat.abs(), lat_c, self.lon.abs(), lon_c)
	}
}

struct Color {
	red: u8,
	green: u8,
	blue: u8,
}

impl fmt::Display for Color {
	fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
		write!(f, "RGB 0x{:02X}{:02X}{:02X}", self.red, self.green, self.blue)
	}
}

fn main() {
	for city in [
		City { name: "Dublin", lat: 53.347778, lon: -6.259722 },
		City { name: "Oslo", lat: 59.95, lon: 10.75 },
		City { name: "Vancouver", lat: 49.25, lon: -123.1 },
	].iter() {
		println!("{}", *city);
	}
	
	for color in [
	Color { red: 128, green: 255, blue: 90 },
	Color { red: 10, green: 0, blue: 254 },
	Color { red: 0, green: 0, blue: 0 },
	].iter() {
	// Switch this to use {} once you've added an implementation
	// for fmt::Display.
	// fmt::Displayに実装を追加したら、 {} を使用するように変更してください。
	println!("{}", *color);
	}
}
```
