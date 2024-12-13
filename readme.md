# Panduan Membuat dan Mengelola Cargo Workspaces

## 1. Membuat Workspace Baru

### Membuat Direktori Utama untuk Workspace
```bash
mkdir nama_workspace
cd nama_workspace
```

### Membuat File `Cargo.toml`
Buat file `Cargo.toml` di direktori utama tanpa bagian `[package]`. Tambahkan bagian `[workspace]` dengan daftar anggota (crates):

```toml
[workspace]
members = [
    "crate_pertama",
    "crate_kedua",
]
```

### Membuat Crate Baru di Dalam Workspace
Gunakan perintah `cargo new` untuk membuat binary crate atau library crate:

```bash
cargo new crate_pertama
cargo new crate_kedua --lib
```

Struktur direktori akan terlihat seperti ini:

```
├── Cargo.toml
├── crate_pertama
│   ├── Cargo.toml
│   └── src
│       └── main.rs
├── crate_kedua
│   ├── Cargo.toml
│   └── src
│       └── lib.rs
```

### Membangun Workspace
Jalankan perintah berikut di direktori utama workspace:

```bash
cargo build
```

## 2. Menambahkan Dependensi Antar Crate dalam Workspace

Jika `crate_pertama` ingin menggunakan kode dari `crate_kedua`:

### Tambahkan Dependensi ke File `crate_pertama/Cargo.toml`

```toml
[dependencies]
crate_kedua = { path = "../crate_kedua" }
```

### Gunakan Crate di Kode Anda
Di file `crate_pertama/src/main.rs`:

```rust
use crate_kedua::fungsi_anda;

fn main() {
    println!("{}", fungsi_anda());
}
```

## 3. Menambahkan Dependensi Eksternal

Jika salah satu crate memerlukan dependensi eksternal, tambahkan ke file `Cargo.toml` crate tersebut.

### Contoh: Menambahkan `rand` di `crate_kedua`
Tambahkan dependensi di file `crate_kedua/Cargo.toml`:

```toml
[dependencies]
rand = "0.8.5"
```

### Gunakan Dependensi di Kode Anda
Di file `crate_kedua/src/lib.rs`:

```rust
use rand::Rng;

pub fn fungsi_anda() -> i32 {
    let mut rng = rand::thread_rng();
    rng.gen_range(1..101)
}
```

### Bangun Kembali Workspace

```bash
cargo build
```

## 4. Menjalankan dan Menguji Crate

### Menjalankan Binary Crate

```bash
cargo run -p crate_pertama
```

### Menjalankan Tes untuk Seluruh Workspace

```bash
cargo test
```

### Menjalankan Tes untuk Crate Tertentu

```bash
cargo test -p crate_kedua
```

## 5. Menambahkan Crate Baru ke Workspace

### Tambahkan Crate Baru
Gunakan perintah `cargo new`:

```bash
cargo new crate_baru --lib
```

### Tambahkan Crate Baru ke `Cargo.toml`
Tambahkan crate baru ke daftar `members` di file `Cargo.toml` direktori utama:

```toml
[workspace]
members = [
    "crate_pertama",
    "crate_kedua",
    "crate_baru",
]
```

### Bangun Ulang Workspace

```bash
cargo build
```

## 6. Tips Manajemen Workspace

- **Berbagi Dependensi**: Semua crate dalam workspace menggunakan satu file `Cargo.lock`, sehingga versi dependensi tetap konsisten.
- **Modularisasi**: Pecah kode menjadi crate-crate kecil berdasarkan fungsionalitas untuk mempermudah pengelolaan.
- **Versi Dependensi yang Berbeda**: Cargo akan mengelola versi yang berbeda secara otomatis jika diperlukan.
