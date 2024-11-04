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
