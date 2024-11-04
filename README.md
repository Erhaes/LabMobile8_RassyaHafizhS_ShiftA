# Tugas 7 Pertemuan 8

Nama  : Rassya Hafizh Suharjo

NIM   : H1D022068

SHIFT : A (baru)

SHIFT  : D (eldiru)

# Penjelasan Cara Kerja Login pada Ionic

## 1. Pemeriksaan Login menggunakan Guard
Pada saat membuka aplikasi Ionic cobalogin 'auth.guard.ts' dan 'auto-login.guard.ts' akan mengecek status login melalui isAuthenticated apakah true atau false. 

Pada 'auth.guard.ts' jika isAuthenticated false maka halaman akan diarahkan ke halaman login.
```ts
return authService.authenticationState.pipe(
    filter((val) => val !== null),
    take(1),
    map((isAuthenticated) => {
      if (isAuthenticated) {
        return true;
      } else {
        router.navigateByUrl('/login', { replaceUrl: true });
        return true;
      }
    })
  );
```

Pada 'auto-login.guard.ts' jika isAuthenticated false maka halaman akan diarahkan ke halaman home.
```ts
return authService.authenticationState.pipe(
    filter((val) => val !== null),
    take(1),
    map((isAuthenticated) => {
      if (isAuthenticated) {
        router.navigateByUrl('/home', { replaceUrl: true });
        return true;
      } else {
        return true;
      }
    }
    ))
```

status true atau false pada isAuthenticated didapatkan dari 'authentication.service.ts' seperti contohnya pada fungsi loadData
```ts
async loadData() {
    const token = await Preferences.get({ key: TOKEN_KEY });
    const user = await Preferences.get({ key: USER_KEY });
    if (token && token.value && user && user.value) {
      this.token = token.value;
      this.nama = user.value;
      this.isAuthenticated.next(true);
    } else {
      this.isAuthenticated.next(false);
    }
  }
```


## 2. Proses Halaman Login
Jika isAuthenticated false maka oleh 'auth.guard.ts' akan diarahkan ke halaman login. Pada halaman login terdapat form yang wajib diisi dengan benar sesuai database yang telah dibuat supaya dapat login. Terdapat sebuah button yang mengirimkan data hasil inputan ke halaman 'login-page.ts'
```html
<ion-item lines="full">
    <ion-label position="floating">Username</ion-label>
    <ion-input type="text" [(ngModel)]="username" required="required"></ion-input>
  </ion-item>
  <ion-item lines="full">
    <ion-label position="floating">Password</ion-label>
    <ion-input type="password" [(ngModel)]="password" required="required"></ion-input>
  </ion-item>
  <ion-row>
    <ion-col>
      <ion-button type="submit" color="primary" expand="block" (click)="login()">Login</ion-button>
    </ion-col>
  </ion-row>
```

Pada 'login-page.ts' pengiriman data dilakukan secara POST ke backend 'login.php'
```ts
login() {
    if (this.username != null && this.password != null) {
      const data = {
        username: this.username,
        password: this.password
      }
      this.authService.postMethod(data, 'login.php').subscribe({
        next: (res) => {
...
```

Di 'login.php' data inputan yang berbentuk json kemudian didecode supaya lebih mudah untuk dilakukan select ke database untuk dicek username dan passwordnya apakah benar atau tidak. Jika password dan username benar maka akan mengembalikan status login berhasil, username nya, serta mendapat token. Jika gagal maka hanya akan mengembalikan status login gagal.
```php
require 'koneksi.php';
$input = file_get_contents('php://input');
$data = json_decode($input, true);
$pesan = [];
$username = trim($data['username']);
$password = md5(trim($data['password']));
$query = mysqli_query($con, "select * from user where username='$username' and
password='$password'");
$jumlah = mysqli_num_rows($query);
if ($jumlah != 0) {
    $value = mysqli_fetch_object($query);
    $pesan['username'] = $value->username;
    $pesan['token'] = time() . '_' . $value->password;
    $pesan['status_login'] = 'berhasil';
} else {
    $pesan['status_login'] = 'gagal';
}
```
Setelah dilakukan pengecekan database, data dari $pesan diencode kembali kedalam format json
```php
echo json_encode($pesan);
echo mysqli_error($con);
```

