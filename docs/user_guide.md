## user_guide

在 Rust 的 Cargo 專案中（例如你已經用 `cargo init aaos_bluetooth` 建立了一個專案），如果要加入文件（documentation），通常指的是以下幾種類型的文件：

1. **Rustdoc 文件**（程式碼註解生成的文件，像是 API 文件）。
2. **其他文件**（如專案的 README、說明文件或其他手動撰寫的文件）。

以下說明如何在 Cargo 專案的目錄結構中加入這些文件，以及它們應該放在哪個目錄底下。

---

### 1. **Rustdoc 文件（程式碼註解生成的文件）**
Rust 使用 `rustdoc` 工具來根據程式碼中的註解（例如 `///` 或 `//!`）生成 API 文件，這些文件通常不需要手動創建額外的目錄，因為它們會在執行 `cargo doc` 時自動生成並存放在 `target/doc` 目錄下。

#### 目錄結構與放置方式
假設你的專案 `aaos_bluetooth` 目錄結構如下（由 `cargo init aaos_bluetooth` 自動生成）：

```
aaos_bluetooth/
├── Cargo.toml
├── src/
│   ├── main.rs
│   └── lib.rs (如果選擇建立 library 專案)
```

- **程式碼註解**：你需要在程式碼中撰寫 Rustdoc 註解（`///` 用於模組外部，`//!` 用於模組內部），例如：
  ```rust
  // src/lib.rs
  //! This is the main documentation for the aaos_bluetooth crate.

  /// A function to connect to a Bluetooth device.
  pub fn connect() {
      // Implementation
  }
  ```

- **生成文件**：執行以下指令來生成 API 文件：
  ```bash
  cargo doc
  ```
  生成的文件會自動存放在：
  ```
  aaos_bluetooth/target/doc/aaos_bluetooth/index.html
  ```

- **注意事項**：
  - 你不需要手動創建 `doc` 目錄，`cargo doc` 會自動在 `target/` 下生成。
  - 如果要公開文件（例如上傳到 `docs.rs`），確保在 `Cargo.toml` 中設置正確的 metadata，例如：
    ```toml
    [package]
    name = "aaos_bluetooth"
    version = "0.1.0"
    edition = "2021"
    documentation = "https://docs.rs/aaos_bluetooth"
    ```

  - 如果需要自訂文件的樣式或額外內容，可以在專案根目錄下創建 `doc/` 目錄（見下文）。

---

### 2. **其他文件（README、專案說明等）**
如果你指的是手動撰寫的說明文件（例如 Markdown 文件、專案說明、或額外的技術文件），這些文件通常放在專案的根目錄或自訂的子目錄中。

#### 推薦的目錄結構
假設你想加入專案文件，可以在專案根目錄下創建一個 `doc/` 或 `docs/` 目錄（這是慣例，名稱可以自訂，但 `docs/` 是常見做法）：

```
aaos_bluetooth/
├── Cargo.toml
├── src/
│   ├── main.rs
│   └── lib.rs
├── docs/                # 自訂文件目錄
│   ├── README.md       # 專案主說明文件
│   ├── user_guide.md   # 使用者指南
│   ├── api.md          # API 說明（手動撰寫，非 rustdoc）
│   └── architecture.md # 系統架構說明
```

#### 說明：
- **`README.md`**：通常放在專案根目錄（`aaos_bluetooth/README.md`），作為專案的入口說明文件。如果需要更詳細的文件，可以將額外的 Markdown 文件放在 `docs/` 目錄下。
- **`docs/` 目錄**：這是用來存放專案相關文件的常見位置，例如：
  - 使用者指南（`user_guide.md`）
  - 開發者文件（`developer_guide.md`）
  - 架構設計（`architecture.md`）
  - 安裝說明（`installation.md`）

- **為什麼放在 `docs/`？**
  - 這是許多開源專案的慣例（例如 GitHub、GitLab 上的專案）。
  - 如果你使用工具像 `mkdocs` 或 `Sphinx` 來生成靜態文件網站，`docs/` 是預設的來源目錄。
  - 這些文件不會被 `cargo` 直接處理，但可以被版本控制並分享。

#### 範例：加入 `docs/` 目錄
1. 在專案根目錄下創建 `docs/` 目錄：
   ```bash
   mkdir docs
   ```

2. 創建一個文件，例如 `docs/user_guide.md`：
   ```markdown
   # User Guide for aaos_bluetooth

   This document explains how to use the aaos_bluetooth crate.

   ## Installation
   Add the following to your `Cargo.toml`:
   ```toml
   [dependencies]
   aaos_bluetooth = "0.1.0"
   ```

   ## Usage
   // Add usage examples here
   ```

3. 如果你希望這些文件被包含在 `cargo doc` 生成的網站中，可以在 `Cargo.toml` 中指定：
   ```toml
   [package.metadata.docs.rs]
   all-features = true
   rustdoc-args = ["--cfg", "docsrs"]
   ```

4. 如果需要生成靜態文件網站，可以使用工具如 `mkdocs`：
   ```bash
   brew install mkdocs
   cd aaos_bluetooth
   mkdocs new .
   ```
   這會在 `docs/` 下生成一個基本的文件結構，你可以進一步編輯。

---

### 3. **進階：整合手動文件與 Rustdoc**
如果你想將手動撰寫的 Markdown 文件整合到 `rustdoc` 生成的文件中，可以在程式碼中使用 `#[doc]` 屬性。例如：

```rust
// src/lib.rs
#[doc = include_str!("../docs/user_guide.md")]
pub mod bluetooth;
```

這會將 `docs/user_guide.md` 的內容嵌入到 `rustdoc` 生成的文件中，當執行 `cargo doc` 時，這些內容會出現在 `target/doc` 中。

---

### 4. **Cargo 專案目錄結構總覽**
以下是加入文件後的完整目錄結構範例：

```
aaos_bluetooth/
├── Cargo.toml              # 專案設定檔
├── src/                   # 原始碼目錄
│   ├── main.rs            # 可執行程式進入點
│   └── lib.rs             # 函式庫程式碼（若有）
├── docs/                  # 手動撰寫的文件
│   ├── README.md          # 專案說明
│   ├── user_guide.md      # 使用者指南
│   ├── developer_guide.md # 開發者文件
│   └── architecture.md    # 架構說明
├── target/                # 編譯輸出目錄（自動生成）
│   └── doc/               # Rustdoc 生成的 API 文件
│       └── aaos_bluetooth/
│           └── index.html # API 文件入口
```

---

### 5. **指令範例**
假設你要加入一個 `docs/` 目錄並生成文件：
```bash
cd aaos_bluetooth
mkdir docs
echo "# User Guide" > docs/user_guide.md
cargo doc  # 生成 API 文件
```

如果要檢查生成的 Rustdoc 文件：
```bash
open target/doc/aaos_bluetooth/index.html
```

---

### 6. **注意事項**
- **文件命名**：`docs/` 是慣例，但你也可以用其他名稱（如 `documentation/`），只要在專案中保持一致。
- **版本控制**：確保將 `docs/` 加入版本控制（例如 Git），但不要將 `target/` 加入（因為它是自動生成的）。
- **外部工具**：如果需要更複雜的文件管理，考慮使用 `mdBook` 或 `mkdocs` 來建立和管理專案文件：
  ```bash
  cargo install mdbook
  mdbook init
  ```

如果有更具體的需求（例如文件格式、生成工具、或特定的文件內容），請告訴我，我可以提供更詳細的指導！
