<template>
  <v-app>
    <v-container>
      <v-row align="center" no-gutters>
        <v-col cols="12" class="mb-10">
          <h1>Bantuan</h1>
          <h2 class="mt-5">Deskripsi</h2>
          <p>
            Media ini dibuat sebagai simulasi untuk cara kerja peluang yang
            berasal dari beberapa permainan seperti dadu, kartu bridge, koin,
            dan Wheel of Fortune. Peluang dapat dibagi menjadi 2 yaitu peluang
            empirik dan peluang teoritik.
          </p>
          <p>
            <b>Peluang empirik</b> adalah peluang yang dihitung dari hasil suatu
            percobaan yang telah terjadi. Dapat juga diartikan bahwa peluang
            empirik merupakan perbandingan antara banyak kejadian dengan banyak
            percobaan yang dilakukan.
          </p>
          <p>
            Sedangkan <b>peluang teoritik</b> adalah perbandingan antara
            frekuensi kejadian yang diharapkan terhadap frekuensi kejadian yang
            mungkin yang disebut ruang sampel. Peluang teoritik ditentukan tanpa
            harus melakukan percobaan, yaitu dengan mendata semua kejadiang yang
            diharapkan kemudian dibandingkan dengan seluruh kejadian yang
            mungkin terjadi.
          </p>
          <h2 class="mt-5">Cara Penggunaan</h2>
          <ul>
            <li>
              Tekan tombol <span class="text-primary">roll</span> untuk
              mengacak.
            </li>
            <li>
              Anda dapat melihat riwayat kejadian muncul dalam bentuk tabel dan
              grafik.
            </li>
            <li>
              Riwayat kejadian tersimpan di dalam memori browser sehingga
              riwayat tidak akan hilang saat browser di tutup.
            </li>
            <li>
              Tekan <span class="text-red">clear log</span> untuk membersihkan
              grafik dan tabel.
            </li>
          </ul>
          <h2 class="mt-5">Dibuat dengan</h2>
          <ul>
            <li>Nuxt JS</li>
            <li>Vue JS</li>
            <li>Vuetify</li>
          </ul>
          <h2 class="mt-5">Repository</h2>
          <p>
            <v-btn
              icon
              color="primary"
              variant="text"
              href="https://github.com/ahmadhasya/peluang"
              target="_blank"
              ><v-icon>mdi-github</v-icon></v-btn
            >
          </p>
        </v-col>
      </v-row>
    </v-container>
  </v-app>
</template>

<script></script>

<style></style>
