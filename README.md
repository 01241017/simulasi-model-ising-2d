# 🔬 Simulasi Model Ising 2D via Algoritma Metropolis
### Tugas Studi Kasus Komprehensif - Case Based 2 | Fisika Komputasi

Selamat datang di repositori portofolio komputasi fisika kami. Proyek ini menyajikan implementasi mandiri mengenai **Model Ising 2 Dimensi (2D)** dan fenomena **Transisi Fasa Makroskopis** menggunakan pendekatan statistik Monte Carlo berbasis **Algoritma Metropolis**.

---

## 👥 Anggota Kelompok
* **Juan Prayoga** * **Muhammad Afrizal Ma'arif** * **Muhammad Zahid Tegar Alamsyah** ---

## 📖 Latar Belakang Teoretis & Formulasi Fisis

Model Ising 2D digunakan untuk mempelajari perilaku kolektif partikel yang memicu terjadinya magnetisasi spontan pada material feromagnetik. Sistem dimodelkan sebagai kisi dua dimensi berukuran $N \times N$ dengan spin diskret $s_{i,j} \in \{+1, -1\}$.

### ⚡ Perhitungan Energi Lokal ($\Delta E$)
Untuk mengoptimalkan efisiensi komputasi, perubahan energi ketika satu spin pada posisi $(i, j)$ dibalik (*flipped*) dihitung secara lokal tanpa menjumlahkan ulang energi total seluruh kisi pada setiap iterasi:
$$\Delta E = 2 s_{i,j} \sum s_{\text{tetangga}}$$

Kami menerapkan **Kondisi Batas Periodik (Periodic Boundary Conditions)** menggunakan operasi modulo (`%`) agar spin di tepi kisi tetap berinteraksi dengan spin di tepi seberangnya:
$$\sum s_{\text{tetangga}} = s_{(i+1)\%N, j} + s_{(i-1)\%N, j} + s_{i, (j+1)\%N} + s_{i, (j-1)\%N}$$

### 🎲 Kriteria Penerimaan Metropolis
Probabilitas penerimaan perubahan keadaan dinyatakan oleh faktor Boltzmann:
$$\mathcal{R} = e^{-\frac{\Delta E}{T}}$$
* Jika $\Delta E \le 0$, konfigurasi baru **langsung diterima**.
* Jika $\Delta E > 0$, konfigurasi baru diterima hanya jika angka acak seragam $r \in [0, 1)$ memenuhi syarat $r < \mathcal{R}$.

---

## 💻 Implementasi Kode Python (Algoritma Metropolis)

Berikut adalah potongan kode utama yang bersih dan teroptimasi untuk menjalankan simulasi:

```python
import numpy as np

def metropolis_simulation(N=20, T=2.27, steps=200000):
    # Inisialisasi konfigurasi acak (Hot Start) untuk entropi maksimum
    lattice = np.random.choice([1, -1], size=(N, N))
    magnetizations = []
    
    for step in range(steps):
        # Pilih spin secara acak
        i = np.random.randint(0, N)
        j = np.random.randint(0, N)
        
        s_current = lattice[i, j]
        
        # Perhitungan tetangga dengan Periodic Boundary Conditions (Modulo)
        s_neighbors = (lattice[(i + 1) % N, j] +
                       lattice[(i - 1) % N, j] +
                       lattice[i, (j + 1) % N] +
                       lattice[i, (j - 1) % N])
        
        # Hitung selisih energi lokal
        dE = 2 * s_current * s_neighbors
        
        # Kriteria penerimaan Metropolis
        if dE <= 0 or np.random.rand() < np.exp(-dE / T):
            lattice[i, j] = -s_current
            
        # Hitung magnetisasi rata-rata secara berkala (setiap 100 langkah)
        if step % 100 == 0:
            M_avg = np.mean(lattice)
            magnetizations.append(M_avg)
            
    return lattice, magnetizations
