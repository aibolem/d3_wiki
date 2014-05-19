**D3.js** adalah suatu pustaka JavaScript untuk memanipulasi dokumen berbasis pada data. D3 membantu Anda menyajikan data menggunakan HTML, SVG dan CSS. Penekanan D3 pada web standards memberi Anda kemampuan penuh dari perambah web modern. tanpa mengkaitkan Anda pada framework proprietary, menggabungkan komponen visualisasi yang powerful dan pendekatan yang didorong data pada manipulasi DOM.

Download D3 versi terakhir di [sini](/mbostock/d3/releases).

Atau, dengan membuat link secara langsung ke rilis terakhir, kopi potongan kode berikut ini:

    <script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>

Situs-situs yang menggunakan HTTPS dapat me-host sendiri D3, atau menggunakan CDN yang mendukung HTTPS, seperti CDNJS. [Kode sumber sepenuhnya dan tes](https://github.com/mbostock/d3) juga tersedia untuk [didownload](https://github.com/mbostock/d3/zipball/master) di GitHub.

### Pengantar

D3 allows you to bind arbitrary data to a Document Object Model (DOM), and then apply data-driven transformations to the document. For example, you can use D3 to generate an HTML table from an array of numbers. Or, use the same data to create an interactive SVG bar chart with smooth transitions and interaction.

D3 is not a monolithic framework that seeks to provide every conceivable feature. Instead, D3 solves the crux of the problem: efficient manipulation of documents based on data. This avoids proprietary representation and affords extraordinary flexibility, exposing the full capabilities of web standards such as CSS3, HTML5 and SVG. With minimal overhead, D3 is extremely fast, supporting large datasets and dynamic behaviors for interaction and animation. D3’s functional style allows code reuse through a diverse collection of components and plugins. 