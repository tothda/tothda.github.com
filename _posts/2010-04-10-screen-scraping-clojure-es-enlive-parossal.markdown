--- 
title:      Screen scraping Clojure-ben
created_at: 2010-04-10 19:29:41.464954 +01:00
layout:     post
--- 

[Cégem](http://dbx.hu) az elmúlt években jónéhány szakmai könyvet rendelt az
amazon.com-ról, ezekből szerettünk volna egy átlátható táblázatot
készíteni. A könyvek oldalaira mutató linkeket viszonylag könnyen
össze tudtam gyűjteni egy txt fájlba. Ebből kiindulva irtam egy
clojure programot, ami kibányássza a html-ből a fontos adatokat (cím,
szerző(k), kiadó, oldalszám, megjelenés éve). A program kimenete egy
html dokumentum, amely táblázatos formában tartalmazza az adatokat.

A feladat két részre bontható: 

* valahogy le kell tölteni az összes könyv weboldalát, majd ebből
  kibányászni az értékes információkat
* az összegyűjtött adatokból valami értelmes formútumó HTML
  dokumentumot létrehozni, későbbi böngészés céljából.

A megoldást mindkét problémára az
[Enlive](http://github.com/cgrand/enlive) nevű HTML templating library
szolgáltatta. Az Enlive a renderelt HTML-t nem template-be ágyazott
procedurális programokkal állítja elő, mint ahogy azt megszokhattuk az
erb, haml, smarty, stb. templating megoldások esetén. Az Enlive
template egy teljesen normál HTML dokumentum. Ezt parse-olja a cucc,
majd immáron clojure kódból CSS-hez hasonló selector-okkal
kiválasztjuk a dinamikus tartalommal feltölteni szándékozott részeket,
és tetszőlegesen módosítjuk ezen elemeken belüli tartalmat. Kicsit
szokatlannak tűnhet ez a módszer elsőre, de felettébb elegáns.

Legnagyobb előnye ennek a fajta megközelítésnek, hogy a template és a
programkód __teljesen különválik__, ezzel a designer - programozó
szeparáció 100%-os. A template HTML azon részeit, amit a program fog
majd dinamikusan kitölteni, tetszőlegesen feltölthetjük, ez a
végeredményt illetően nem oszt, nem szoroz. 

Az a tény, hogy az Enlive működésének szerves része a HTML parszolás
és létrejövő adatstruktúra CSS alapú szelekciója, alkalmassá teszi
weboldalakban elrejtett értékes adatok kinyerésére (screen
scraping). Csapjunk is bele a lecsóba, kaparjuk meg kicsit az
amazon.com-ot.

Screen-scraping
---------------


Lássunk egy példát arra, hogyan lehet, egy könyv oldaláról megkapni a címet:

{% highlight clojure %}

user> (use 'net.cgrand.enlive-html)
nil
user> (def url "http://www.amazon.com/gp/product/0596009771/ref=oss_product")
#'user/url
user> (def resource (html-resource (java.net.URL. url)))
#'user/resource
user> (select resource [:#btAsinTitle text-node])
("Oracle PL/SQL Programming, 4th Edition (Paperback)")
{% endhighlight %}

Első lépésben deklaráljuk, hogy használni szeretnénk az Enlive-et,
majd egy könyv oldalának URL-jét. A `html-resource` függvény letölti a
weboldalt, parsolja, majd az eredményt elmenti a `resource` nevű
var-ba. A `select` függvény pedig végrehajta első paramétereként kapott
dokumentumon a második paraméterként kapott selector-t. Esetünkben a
selector `[:#btAsinTitle text-node]`, ami a `btAsinTitle` id-jú
elemen belüli szöveges elemek-re illeszkedik.

{% highlight clojure %}

(def data (extract "http://www.amazon.com/gp/product/0596009771/ref=oss_product"))

data ;=>
{:year "2005", 
 :publisher " O'Reilly Media; 4 edition", 
 :image-url
 "http://ecx.images-amazon.com/images/I/416OCzh590L._BO2,204,203,200_PIsitb-sticker-arrow-click,TopRight,35,-76_AA300_SH20_OU01_.jpg", 
 :pages "1200", 
 :authors ("Steven Feuerstein" "Bill Pribyl"), 
 :title "Oracle PL/SQL Programming, 4th Edition (Paperback)", 
 :url "http://www.amazon.com/gp/product/0596009771/ref=oss_product"}
{% endhighlight %}

HTML rendering
--------------

{% highlight html %}
<body>
  <h1>DBX Library</h1>
  <table id="books">
    <thead>
     <tr>
       <th>Picture</th>
       <th>Title</th>
       <th>Author</th>
       <th>Publisher</th>
       <th>Year</th>
       <th>Pages</th>
     </tr> 
    </thead>
    <tbody>
      <tr>
        <td class="image"><img src="http://ecx.images-amazon.com/images/I/51ca4jVBOmL._SL500_AA300_.jpg" alt="front cover" /></td>
        <td class="title"><a href="http://www.amazon.com/Drools-JBoss-Rules-Developers-Guide/dp/1847195644/ref=sr_1_1?ie=UTF8&s=books&qid=1267798055&sr=8-1">Drools JBoss Rules 5.0 Developer's Guide (Paperback)</a></td>
        <td class="author">Michal Bali</td>
        <td class="publisher">Packt Publishing</td>
        <td class="year">2009</td>
        <td class="pages">320</td>      
      </tr>
    </tbody>
  </table>
</body>
{% endhighlight %}

{% highlight clojure %}

(def *template-file* "../template/books.html")
(def *book-template-file* (java.io.File. *template-file*))
(def *row-selector* [:table#books :tbody :> e/first-child])

(e/defsnippet row-template *book-template-file* *row-selector*
  [{:keys [title authors publisher year pages url image-url]}]
  [:.title :a] (e/do->
                (e/html-content title)
                (e/set-attr :href url))
  [:.author] (e/html-content (str/str-join ", " authors))
  [:.publisher] (e/html-content publisher)
  [:.year] (e/html-content year)
  [:.pages] (e/html-content pages)
  [:.image :img] (e/set-attr :src image-url))
{% endhighlight %}

{% highlight clojure %}
(print (render (e/emit* (row-template data))))

<tr>
  <td class="image"><img alt="front cover" src="http://ecx.images-amazon.com/images/I/416OCzh590L._BO2,204,203,200_PIsitb-sticker-arrow-click,TopRight,35,-76_AA300_SH20_OU01_.jpg" /></td>
  <td class="title"><a href="http://www.amazon.com/gp/product/0596009771/ref=oss_product">Oracle PL/SQL Programming, 4th Edition (Paperback)</a></td>
  <td class="author">Steven Feuerstein, Bill Pribyl</td>
  <td class="publisher"> O'Reilly Media; 4 edition</td>
  <td class="year">2005</td>
  <td class="pages">1200</td>      
</tr>
{% endhighlight %}
