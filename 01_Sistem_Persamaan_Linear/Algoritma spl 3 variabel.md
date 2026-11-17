# Algoritma Penyelesaian Sistem Persamaan Linear 3 Variabel
Metode: **Eliminasi Gauss dengan Pivoting Parsial** + **Substitusi Mundur**

Mata Kuliah: Komputasi Numerik

## Bentuk Sistem
```
a11 x + a12 y + a13 z = b1
a21 x + a22 y + a23 z = b2
a31 x + a32 y + a33 z = b3
```
Dalam bentuk matriks: A · x = b, dengan A matriks koefisien 3x3, x = [x, y, z], dan b vektor konstanta.

## Input
- Matriks koefisien A (3x3)
- Vektor konstanta b (3x1)
- Batas toleransi pivot nol: ε = 10⁻¹²

## Output
- Solusi (x, y, z), atau pesan bahwa sistem tidak memiliki solusi tunggal
- Residual r = A·x − b untuk verifikasi

## Langkah-langkah
Catatan: pada algoritma ini indeks baris dan kolom dimulai dari 1. Pada kode Python indeks dimulai dari 0.

1. Mulai.
2. Baca matriks A, vektor b, dan tetapkan ε = 10⁻¹².
3. Bentuk matriks augmented M = [A | b] berukuran 3x4.
4. Untuk k = 1 sampai 2 (tahap eliminasi maju):
   1. Cari baris p (dari baris k sampai 3) yang memiliki nilai |M[p][k]| terbesar (pivoting parsial).
   2. Jika |M[p][k]| < ε, hentikan: sistem tidak memiliki solusi tunggal.
   3. Jika p ≠ k, tukar baris k dengan baris p.
   4. Untuk i = k+1 sampai 3:
      - faktor = M[i][k] / M[k][k]
      - Baris i = Baris i − faktor × Baris k
      - Set M[i][k] = 0 (secara matematis sudah pasti nol; ditulis eksplisit agar tidak tersisa galat pembulatan)
5. Jika |M[3][3]| < ε, hentikan: sistem tidak memiliki solusi tunggal.
6. Substitusi mundur:
   - z = M[3][4] / M[3][3]
   - y = (M[2][4] − M[2][3] × z) / M[2][2]
   - x = (M[1][4] − M[1][2] × y − M[1][3] × z) / M[1][1]

   Secara umum, untuk i = 3 turun sampai 1:
   x_i = ( M[i][4] − jumlah dari M[i][j] × x_j untuk j > i ) / M[i][i]
7. Verifikasi: hitung residual r = A·x − b. Jika semua nilai r mendekati 0, solusi benar.
8. Tampilkan x, y, z dan residual.
9. Selesai.

## Pseudocode
```
FUNGSI GaussSPL(A, b)
    ε <- 1e-12
    M <- gabungkan A dan b menjadi matriks augmented 3x4
    UNTUK k <- 1 SAMPAI 2
        p <- indeks baris dengan |M[baris][k]| terbesar (baris k..3)
        JIKA |M[p][k]| < ε MAKA berhenti (tidak ada solusi tunggal)
        JIKA p != k MAKA tukar baris k dan baris p
        UNTUK i <- k+1 SAMPAI 3
            faktor <- M[i][k] / M[k][k]
            Baris_i <- Baris_i - faktor * Baris_k
            M[i][k] <- 0
        AKHIR UNTUK
    AKHIR UNTUK
    JIKA |M[3][3]| < ε MAKA berhenti (tidak ada solusi tunggal)
    UNTUK i <- 3 TURUN SAMPAI 1
        x[i] <- (M[i][4] - jumlah(M[i][j] * x[j], j > i)) / M[i][i]
    AKHIR UNTUK
    residual <- A * x - b
    KEMBALIKAN x, residual
AKHIR FUNGSI
```

## Diagram Alur (ringkas)
```
Mulai -> Baca A, b -> Bentuk [A | b]
      -> Ulangi k = 1..2: cari pivot terbesar -> pivot < ε? --ya--> Berhenti (singular)
                           |tidak
                           -> tukar baris (jika perlu) -> eliminasi baris di bawah pivot
      -> M[3][3] < ε? --ya--> Berhenti (singular)
      |tidak
      -> Substitusi mundur (z, y, x) -> Hitung residual -> Tampilkan hasil -> Selesai
```

## Contoh
```
 2x +  y -  z =   8
-3x -  y + 2z = -11
-2x +  y + 2z =  -3
```
Matriks augmented awal:
```
[  2   1  -1 |   8 ]
[ -3  -1   2 | -11 ]
[ -2   1   2 |  -3 ]
```
Tahap 1: pivot terbesar di kolom 1 adalah -3 (baris 2), tukar baris 1 dan 2, lalu eliminasi. Tahap 2: pivot terbesar di kolom 2 berada di baris 3, tukar baris 2 dan 3, lalu eliminasi. Hasil segitiga atas:
```
[ -3   -1      2    | -11     ]
[  0    1.6667 0.6667|  4.3333 ]
[  0    0      0.2  | -0.2    ]
```
Substitusi mundur: z = -0.2 / 0.2 = -1; y = (4.3333 − 0.6667 × (−1)) / 1.6667 = 3; x = (−11 − (−1)(3) − 2(−1)) / (−3) = 2.

Solusi: **x = 2, y = 3, z = -1**. Residual A·x − b = 0 (dalam batas galat pembulatan).

## Keterkaitan dengan Kode Python (Google Colab)
| Langkah algoritma | Bagian kode |
|---|---|
| Langkah 3 (matriks augmented) | `np.hstack(...)` pada fungsi `gauss_spl` |
| Langkah 4 (eliminasi maju + pivoting) | loop `for k in range(n - 1)` |
| Langkah 5 dan ε | pengecekan `abs(M[n-1, n-1]) < 1e-12` |
| Langkah 6 (substitusi mundur) | loop `for i in range(n - 1, -1, -1)` |
| Langkah 7 (verifikasi) | fungsi `uji_kasus` (residual, `cond(A)`, pembanding `np.linalg.solve`) |
