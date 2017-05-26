**D3.js** adalah suatu pustaka JavaScript untuk memanipulasi dokumen berbasis pada data. D3 membantu Anda menyajikan data menggunakan HTML, SVG dan CSS. Penekanan D3 pada web standards memberi Anda kemampuan penuh dari perambah web modern. tanpa mengkaitkan Anda pada framework proprietary, menggabungkan komponen visualisasi yang powerful dan pendekatan yang didorong data pada manipulasi DOM.

Download D3 versi terakhir di [sini](/mbostock/d3/releases).

Atau, dengan membuat link secara langsung ke rilis terakhir, kopi potongan kode berikut ini:

```
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
```

Situs-situs yang menggunakan HTTPS dapat me-host sendiri D3, atau menggunakan CDN yang mendukung HTTPS, seperti CDNJS. [Kode sumber lengkap dan tes](https://github.com/mbostock/d3) juga tersedia untuk [didownload](https://github.com/mbostock/d3/zipball/master) di GitHub.

## Pengantar

**D3** memungkinkan Anda untuk mengikat data pada suatu Document Object Model (DOM), dan kemudian menerapkan transformasi berdasar-data pada dokumen. Sebagai contoh, anda dapat menggunakan D3 untuk menghasilkan tabel HTML dari suatu array bilangan. Atau, menggunakan data yang sama untuk membuat diagram batang SVG interaktif dengan transisi dan interaksi yang halus.

D3 bukanlah framework monolitik yang bertujuan menyediakan berbagai macam fitur. Sebaliknya, D3 memecahkan inti masalah: manipulasi efisien dari dokumen berdasarkan data. Hal ini menghindari munculnya proprietary dan memungkinkan fleksibilitas yang luar biasa, mengeskpos kemampuan penuh dari web standards seperti CSS3, HTML5 dan SVG. Dengan biaya yang minimal, D3 sangatlah cepat, mendukung kumpulan data yang besar dan perilaku dinamis untuk interaksi dan animasi. Style fungsional dari D3 memungkinkan kode digunakan kembali melalui berbagai macam kumpulan [komponen](/mbostock/d3/wiki/API-Reference) dan [plugin](/d3/d3-plugins).

## Selections

Memodifikasi dokumen menggunakan [W3C DOM API](http://www.w3.org/DOM/DOMTR) itu membosankan: nama method yang bertele-tele, dan pendekatan imperatif mengharuskan iterasi manual dan pengawasan langkah sementara. Sebagai contoh, untuk mengubah warna teks dari elemen paragraf:

```
var paragraphs = document.getElementsByTagName("p");
for (var i = 0; i < paragraphs.length; i++) {
  var paragraph = paragraphs.item(i);
  paragraph.style.setProperty("color", "white", null);
}
```

D3 menggunakan pendekatan deklaratif, mengoperasikan sekumpulan node yang disebut seleksi (_selection_). Contoh, Anda dapat menulis ulang loop di atas sebagai:

```
d3.selectAll("p").style("color", "white");
```

Namun, Anda masih dapat memanipulasi masing-masing node sebagaimana dibutuhkan:

```
d3.select("body").style("background-color", "black");
```

Selektor didefinisikan oleh [W3C Selectors API](http://www.w3.org/TR/selectors-api/) dan didukung oleh perambah-perambah web modern. Kompatibilitas ke belakang oleh perambah-perambah tua dapat disediakan menggunakan [Sizzle](http://sizzlejs.com/). Contoh di atas memilih (_select_) node berdasarkan nama tag (masing-masing "p" dan "body"). Elemen-elemen dapat diseleksi menggunakan berbagai macam perdikat, termasuk _containment_, nilai atribut, class dan ID.

D3 menyediakan banyak sekali metode untuk mengubah node: mengeset atribut atau style; meregister _event listeners_; menambahkan, menghapus atau menyortir node; dan mengubah isi HTML atau teks. Semua hal tersebut cukup untuk sebagian besar kebutuhan. Akses langsung ke pokok dari DOM juga dimungkinkan, sebagaimana setiap seleksi D3 hanyalah array dari node. 

## Dynamic Properties

Pembaca yang telah terbiasa dengan framework-framework DOM lainnya seperti [[jQuery|http://jquery.com/]] atau [[Prototype|http://www.prototypejs.org/]] harusnya segera mengenali kesamaannya dengan D3. Namun style, atribut, atau properti lainnya dapat ditentukan sebagai fungsi dari data pada D3, bukan hanya sebagai konstanta. Meskipun kesederhanaannya nampak jelas, fungsinya ternyata juga sangat powerful; fungsi d3.geo.path misalnya, memproyeksikan [koordinat geografis](http://geojson.org/) menjadi [data path SVG](http://www.w3.org/TR/SVG/paths.html#PathData). D3 menyediakan banyak fungsi-fungsi bawaan dan _function factory_ yang dapat digunakan kembali, seperti [grafis primitif](/mbostock/d3/wiki/SVG-Shapes) untuk area, diagram garis dan pie chart.

Sebagai contoh, untuk secara acak memberi warna pada paragraf:

```
d3.selectAll("p").style("color", function() {
  return "hsl(" + Math.random() * 360 + ",100%,50%)";
});
```

Untuk alternatif warna abu-abu untuk node genap dan ganjil:

```
d3.selectAll("p").style("color", function(d, i) {
  return i % 2 ? "#fff" : "#eee";
});
```

Properti yang dihitung sering mengacu pada data terikat. Data ditentukan sebagai array nilai, dan setiap nilai diteruskan sebagai argumen pertama (d) pada fungsi seleksi. Dengan default join-by-index, elemen pertama dalam array data diteruskan pada node pertama dalam seleksi, elemen kedua ke node kedua, dan seterusnya. Sebagai contoh, bila Anda mengikat suatu array bilangan ke elemen paragraf, Anda dapat menggunakan bilangan-bilangan tersebut untuk menghitung ukuran font secara dinamis:

```
d3.selectAll("p")
    .data([4, 8, 15, 16, 23, 42])
    .style("font-size", function(d) { return d + "px"; });
```

Pada saat data telah diikat pada dokumen, Anda dapat mengabaikan operator data; D3 akan mendapatkan kembali data yang sebelumnya diikat. Hal ini memungkinkan Anda untuk menghitung kembali properti tanpa mengikat ulang.

## Enter and Exit

Menggunakan seleksi enter dan exit D3, Anda dapat membuat node baru untuk data yang masuk dan menghapus node keluar yang tidak lagi dibutuhkan.

Ketika data diikatkan pada seleksi, setiap elemen dalam array data dipasangkan dengan node yang berhubungan dalam seleksi. Bila terdapat lebih sedikit node dibandingkan data, elemen data ekstra membentuk seleksi enter, yang dapat anda instansiasi dengan menambahkan seleksi enter. Sebagai contoh:

```
d3.select("body").selectAll("p")
    .data([4, 8, 15, 16, 23, 42])
  .enter().append("p")
    .text(function(d) { return "I’m number " + d + "!"; });
```

Mengubah node adalah seleksi default-hasil dari operator data. Jadi, jika Anda lupa mengenai seleksi enter dan evit, Anda akan secara otomatis memilih hanya elemen di mana terdapat data yang terhubung. Pola yang umum adalah dengan memecah seleksi awal menjadi tiga bagian: node yang diupdate untuk memodifikasi, node yang masuk untuk ditambahkan, dan node yang keluar untuk dihapus.

```
// Update…
var p = d3.select("body").selectAll("p")
    .data([4, 8, 15, 16, 23, 42])
    .text(String);

// Enter…
p.enter().append("p")
    .text(String);

// Exit…
p.exit().remove();
```

Dengan menangani ketiga kasus tersebut secara terpisah, Anda menentukan secara tepat operasi mana berjalan pada node yang mana. Hal ini memperbaiki performansi dan menawarkan kontrol yang lebih besar pada transisi. Sebagai contoh, dengan diagram batang Anda dapat menginisialisasi bar yang masuk dengan skala lama, dan kemudian transisi bar yang masuk ke skala yang baru bersamaan dengan mengupdate batang bersamaan dengan mengupdate dan mengeluarkan batang.

D3 memungkinkan Anda mentransformasi dokumen berdasarkan data; hal ini termasuk baik membuat maupun menghancurkan elemen-elemen. D3 memungkinkan Anda untuk mengubah dokumen eksisting untuk merespon interkasi pengguna, animasi melalui waktu, atau bahkan notifikasi asinkronos dari pihak ketiga. Suatu pendekatan hibrid bahkan dimungkinkan, dimana dokumen awalnya dirender di server, dan diupdate pada klien melalui D3.

## Transformation, not Representation

D3 bukanlah representasi grafis baru. Tidak seperti [[Processing|http://processing.org/]], [[Raphaël|http://raphaeljs.com/]], atau [[Protovis|http://vis.stanford.edu/protovis/]], perbendaharaan tanda langsung berasal dari web standard: HTML, SVG dan CSS. Sebagai contoh, Anda dapat membuat elemen SVG menggunakan D3 dan memberi style menggunakan stylesheet eksternal. Anda dapat menggunakan efek filter komposit, garis dash dan kliping. Bila pembuat perambah memperkenalkan fitur baru esok, Anda akan bisa menggunakannya langsung tanpa dibutuhkan update toolkit. Dan, bila Anda memutuskan di masa yang akan datang untuk menggunakan toolkit bukan D3, Anda dapat membawa pengetahuan akan standard bersama Anda!

Yang terbaik dari semuanya, D3 mudah untuk di-debug menggunakan inspektor elemen bawaan perambah: node yang Anda manipulasi dengan D3 adalah persis apa yang perambah mengerti secara _native_.

## Transition

Fokus D3 pada transformasi diperluas secara alamiah pada animasi transisi. Transisi secara bertahap menginterpolasi atau menyisipkan style dan atribut seiring dengan berjalannya waktu. _Tweening_ dapat dikendalikan melalui pelonggaran fungsi seperti “elastic”, “cubic-in-out” dan “linear”. Interpolator D3 mendukung primitif, seperti bilangan dan bilangan yang dilekatkan dalam string (font sizes, path data, dll.), dan nilai-nilai gabungan. Anda bahkan dapat memperluas interpolator registry D3 untuk mendukung properti dan data struktur kompleks.

Sebagai contoh, untuk memudarkan latar belakang halaman menjadi hitam:

```
d3.select("body").transition()
    .style("background-color", "black");
```

Atau, untuk mengubah ukuran lingkaran dalam suatu map simbol dengan delay:

```
d3.selectAll("circle").transition()
    .duration(750)
    .delay(function(d, i) { return i * 10; })
    .attr("r", function(d) { return Math.sqrt(d * scale); });
```

Dengan hanya memodifikasi atribut yang sesungguhnya mengubah, D3 mengurangi biaya dan memungkinkan kompleksitas grafis yang lebih pada _frame rate_. D3 juga memungkinkan merangkaikan transisi kompleks melalui event. Dan, Anda masih dapat menggunakan transisi CSS; D3 tidak menggantikan toolbox perambah, namun mengeksposnya dalam cara yang mudah untuk digunakan.

Ingin belajar lebih? Bacalah tutorial-tutorial berikut ini.