> **Wiki**

**D3.js** adalah pustaka JavaScript untuk manipulasi dokumen berdasarkan data. D3 membantu anda menyajikan data menggunakan HTML, SVG dan CSS. Penekanan D3 pada standard web memberi Anda kompatibilitas penuh trerhadap perambah web (_web browser_) modern tanpa mengkaitkan anda dengan _framework proprietary_, menggabungkan komponen-komponen visualisasi yang powerful dan pendekatan berdasarkan data pada manipulasi DOM (_Document Object Model_).

## Sumber Daya

* [Introduction](http://d3js.org/)
* [[Examples Gallery|Gallery]]
* [[Tutorials and Talks|Tutorials]]
* [[API Reference]]
* [Release Notes](/mbostock/d3/releases)
* [Plugins](/d3/d3-plugins)
* [d3.js on Stack Overflow](http://stackoverflow.com/questions/tagged/d3.js)
* [d3-js Google Group](http://groups.google.com/group/d3-js)
* [한국어](https://github.com/zziuni/d3/wiki)
* [日本語](/mbostock/d3/wiki/JP-Home)
* [中文](/mbostock/d3/wiki/CN-Home)
* [[API Reference (русскоязычная версия)]]


## Dukungan Perambah Web

D3 mendukung apa yang disebut sebagai perambah web “modern”, yang umumnya segala sesuatu _kecuali_ IE8 dan dibawahnya. D3 diujicoba terhadap Firefox, Chrome (Chromium), Safari (WebKit), Opera dan IE9. Beberapa bagian dari D3 mungkin dapat berjalan pada perambah yang lebih tua, karena pustaka inti (_core_) mempunyai _requirement_ minimal: JavaScript dan API [W3C DOM](http://www.w3.org/DOM/). Untuk IE8, direkomendasikan menggunakan pustaka kompatibilitas [Aight](https://github.com/shawnbot/aight). D3 memnggunakan [Selectors API](http://www.w3.org/TR/selectors-api/) Level 1, tapi Anda dapat mem-preload [Sizzle](http://sizzlejs.com/) untuk kompatibilitas. Anda akan membutuhkan perambah modern untuk menggunakan [SVG](http://www.w3.org/TR/SVG/) dan [CSS3 Transitions](http://www.w3.org/TR/css3-transitions/). D3 bukanlah suatu lapisan kompatibilitas, jadi bila perambah Anda tidak mendukung standard, Anda tidak beruntung. Mohon maaf :)


D3 works in Node.js as well. See <https://groups.google.com/forum/#!msg/d3-js/JyldAkWkTvI/n8thanJeGvAJ> for details.

## Installing

Download the latest version here:

* <https://github.com/mbostock/d3/releases>

Or, to link directly to the latest release, copy this snippet:

```html
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>
```

Or, if you want the full repository including tests:

* <https://github.com/mbostock/d3/zipball/master>

When developing locally, note that your browser may enforce strict permissions for reading files out of the local file system. **If you use [d3.xhr](wiki/Requests) locally (including d3.json et al.), you must have a local web server.** For example, you can run Python's built-in server:

    python -m SimpleHTTPServer 8888 &

or for Python 3+

    python -m http.server 8888 &

Once this is running, go to <http://localhost:8888/>.

D3 supports the asynchronous module definition (AMD) API. For example, if you use [RequireJS](http://requirejs.org/), you may load as follows:

```js
require.config({paths: {d3: "http://d3js.org/d3.v3.min"}});

require(["d3"], function(d3) {
  console.log(d3.version);
});
```

## Modifying

If you want to modify how D3 is implemented, click the "Fork" button in the top-right corner of this page, and then clone your fork from the command line by replacing *username* with your GitHub username:

```bash
git clone git://github.com/username/d3.git
```

The D3 repository should work out of the box if you just want to create new visualizations using D3. On the other hand, if you want to extend D3 with new features, fix bugs, or run tests, you should [fork the D3 repository](/mbostock/d3), and install [Node.js](http://nodejs.org/) (version 0.10.x or higher). From the root directory of this repository, you can then install D3's dependencies:

    npm install

To run the tests, use:

    make test

