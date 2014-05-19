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

## Seleksi

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

Pembaca yang telah terbiasa dengan framework-framework DOM lainnya seperti [[jQuery|http://jquery.com/]] atau [[Prototype|http://www.prototypejs.org/]] harusnya segera mengenali kesamaannya dengan D3. Namun style, atribut, atau properti lainnya dapat ditentukan sebagai fungsi dari data pada D3, bukan hanya sebagai konstanta. Meskipun kesederhanaannya nampak jelas, fungsinya ternyata juga sangat powerful; fungsi d3.geo.path misalnya, memproyeksikan [koordinat geografis](http://geojson.org/) menjadi [data path SVG](http://www.w3.org/TR/SVG/paths.html#PathData). D3 menyediakan banyak fungsi-fungsi bawaan dan buatan yang dapat digunakan kembali, seperti [grafis primitif](/mbostock/d3/wiki/SVG-Shapes) untuk area, diagram garis dan pie chart.

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

Read more about data joins.

Using D3’s enter and exit selections, you can create new nodes for incoming data and remove outgoing nodes that are no longer needed.

When data is bound to a selection, each element in the data array is paired with the corresponding node in the selection. If there are fewer nodes than data, the extra data elements form the enter selection, which you can instantiate by appending to the enter selection. For example:

```
d3.select("body").selectAll("p")
    .data([4, 8, 15, 16, 23, 42])
  .enter().append("p")
    .text(function(d) { return "I’m number " + d + "!"; });
```

Updating nodes are the default selection—the result of the data operator. Thus, if you forget about the enter and exit selections, you will automatically select only the elements for which there exists corresponding data. A common pattern is to break the initial selection into three parts: the updating nodes to modify, the entering nodes to add, and the exiting nodes to remove.

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

By handling these three cases separately, you specify precisely which operations run on which nodes. This improves performance and offers greater control over transitions. For example, with a bar chart you might initialize entering bars using the old scale, and then transition entering bars to the new scale along with the updating and exiting bars.

D3 lets you transform documents based on data; this includes both creating and destroying elements. D3 allows you to change an existing document in response to user interaction, animation over time, or even asynchronous notification from a third-party. A hybrid approach is even possible, where the document is initially rendered on the server, and updated on the client via D3.

## Transformation, not Representation

D3 is not a new graphical representation. Unlike Processing, Raphaël, or Protovis, the vocabulary of marks comes directly from web standards: HTML, SVG and CSS. For example, you can create SVG elements using D3 and style them with external stylesheets. You can use composite filter effects, dashed strokes and clipping. If browser vendors introduce new features tomorrow, you’ll be able to use them immediately—no toolkit update required. And, if you decide in the future to use a toolkit other than D3, you can take your knowledge of standards with you!

Best of all, D3 is easy to debug using the browser’s built-in element inspector: the nodes that you manipulate with D3 are exactly those that the browser understands natively.

## Transitions

D3’s focus on transformation extends naturally to animated transitions. Transitions gradually interpolate styles and attributes over time. Tweening can be controlled via easing functions such as “elastic”, “cubic-in-out” and “linear”. D3’s interpolators support both primitives, such as numbers and numbers embedded within strings (font sizes, path data, etc.), and compound values. You can even extend D3’s interpolator registry to support complex properties and data structures.

For example, to fade the background of the page to black:

```
d3.select("body").transition()
    .style("background-color", "black");
```

Or, to resize circles in a symbol map with a staggered delay:

```
d3.selectAll("circle").transition()
    .duration(750)
    .delay(function(d, i) { return i * 10; })
    .attr("r", function(d) { return Math.sqrt(d * scale); });
```

By modifying only the attributes that actually change, D3 reduces overhead and allows greater graphical complexity at high frame rates. D3 also allows sequencing of complex transitions via events. And, you can still use CSS3 transitions; D3 does not replace the browser’s toolbox, but exposes it in a way that is easier to use.

Want to learn more? Read these tutorials. 