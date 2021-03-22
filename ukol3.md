lepsie sa to pozera tu: [https://github.com/melnicek/notes/blob/master/ukol3.md](https://github.com/melnicek/notes/blob/master/ukol3.md)

# Problém 1: Analýza Android API pro kryptografické operace

Aplikacia obsahuje textfield `tMessage`, button `bSign`, a textview `tv0`

![](./signing_app.png)

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button bSign = (Button) findViewById(R.id.bSign);

        // clicklistener na <Sign> button zavona funkciu sign()
        bSign.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                sign();
            }
        });

        // pri kazdom vytvoreni aktivity novy keypair, bolo by lepsie kontrolovat ci uz existuje, no som hladny
        try {
            KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance(
                    KeyProperties.KEY_ALGORITHM_EC, "AndroidKeyStore");
            keyPairGenerator.initialize(
                    new KeyGenParameterSpec.Builder(
                            "key1",
                            KeyProperties.PURPOSE_SIGN)
                            .setAlgorithmParameterSpec(new ECGenParameterSpec("secp256r1"))
                            .setDigests(KeyProperties.DIGEST_SHA256,
                                    KeyProperties.DIGEST_SHA384,
                                    KeyProperties.DIGEST_SHA512)
                            .build());
        } catch (Exception e) { // sakredy catch
            e.printStackTrace();
        }
    }
    
    protected  void sign(){
        TextView tv0 = (TextView) findViewById(R.id.tv0); // base64 vysledok sa vlozi do `tv0`
        EditText tMessage = (EditText) findViewById(R.id.tMessage); // text input
        String sign_me = tMessage.getText().toString(); // message ktoru chceme signut

        try {
            KeyStore keyStore = KeyStore.getInstance("AndroidKeyStore");
            keyStore.load(null);
            
            Signature signature = Signature.getInstance("SHA256withECDSA");
            signature.initSign((PrivateKey) keyStore.getKey("key1", null));
            signature.update(sign_me.getBytes("UTF-8")); // getBytes(), zo stringu na byte[]
            
            tv0.setText(Base64.encodeToString(signature.sign(), Base64.DEFAULT));
            
        } catch (Exception e) { // dalsi skaredy catch
            e.printStackTrace();
        }
    }
}
```

# Problém 2: Analýza aplikace pro SMPC

### k čemu tato aplikace slouží 

### jak funguje.

### Popište, jaké kryptografické operace aplikace používá

### jaké kryptografické algoritmy jsou využity. 

### Lokalizujte tyto operace ve zdrojovém kódu a diskutujte, jak dojde k jejich zavolání.

# Problém 3: Rozhraní NFC
