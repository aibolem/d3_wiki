**D3.js** adalah suatu pustaka JavaScript untuk memanipulasi dokumen berbasis pada data. D3 membantu Anda menyajikan data menggunakan HTML, SVG dan CSS. Penekanan D3 pada web standards memberi Anda kemampuan penuh dari perambah web modern. tanpa mengkaitkan Anda pada framework proprietary, menggabungkan komponen visualisasi yang powerful dan pendekatan yang didorong data pada manipulasi DOM.

Download D3 versi terakhir di [sini](/mbostock/d3/releases).

Atau, dengan membuat link secara langsung ke rilis terakhir, kopi potongan kode berikut ini:

    <script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>

Situs-situs yang menggunakan HTTPS dapat me-host sendiri D3, atau menggunakan CDN yang mendukung HTTPS, seperti CDNJS. [Kode sumber lengkap dan tes](https://github.com/mbostock/d3) juga tersedia untuk [didownload](https://github.com/mbostock/d3/zipball/master) di GitHub.

## Pengantar

**D3** memungkinkan Anda untuk mengikat data pada suatu Document Object Model (DOM), dan kemudian menerapkan transformasi berdasar-data pada dokumen. Sebagai contoh, anda dapat menggunakan D3 untuk menghasilkan tabel HTML dari suatu array bilangan. Atau, menggunakan data yang sama untuk membuat diagram batang SVG interaktif dengan transisi dan interaksi yang halus.

D3 bukanlah framework monolitik yang bertujuan menyediakan berbagai macam fitur. Sebaliknya, D3 memecahkan inti masalah: manipulasi efisien dari dokumen berdasarkan data. Hal ini menghindari munculnya proprietary dan memungkinkan fleksibilitas yang luar biasa, mengeskpos kemampuan penuh dari web standards seperti CSS3, HTML5 dan SVG. Dengan biaya yang minimal, D3 sangatlah cepat, mendukung kumpulan data yang besar dan perilaku dinamis untuk interaksi dan animasi. Style fungsional dari D3 memungkinkan kode digunakan kembali melalui berbagai macam kumpulan [komponen](/mbostock/d3/wiki/API-Reference) dan [plugin](/d3/d3-plugins).

## Seleksi

Modifying documents using the W3C DOM API is tedious: the method names are verbose, and the imperative approach requires manual iteration and bookkeeping of temporary state. For example, to change the text color of paragraph elements: 

    var paragraphs = document.getElementsByTagName("p");
for (var i = 0; i < paragraphs.length; i++) {
  var paragraph = paragraphs.item(i);
  paragraph.style.setProperty("color", "white", null);
}