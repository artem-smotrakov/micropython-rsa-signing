# RSA signing on MicroPython

This is an implementation of RSA signing for MicroPython. It's writter in pure Python, and based on [python-rsa](https://github.com/sybrenstuvel/python-rsa) version 4.0.

Originally, this port was supposed to be used [for signing JWT claims on ESP8266](https://github.com/artem-smotrakov/esp32-weather-google-sheets). Since ESP8266 doesn't have too much memory, the original python-rsa library was truncated a lot. Nevertheless, it didn't work out. RSA signing worked very slow and threw exeptions due to low memory. In the end, [the port successfully run only on ESP32](https://github.com/artem-smotrakov/esp32-weather-google-sheets/blob/master/src/google/auth.py#L66).

## Loading private keys

`PrivateKey` class doesn't support loading keys in PKCS1 format (from PEM or DER files) because it would require porting [pyasn1](https://github.com/etingof/pyasn1) library. It might be too much for a tiny ESP8266 board since it doesn't have too much memory. But maybe ESP32 boards can handle it.

Instead, `PrivateKey` class can load a private key from a JSON file which contains `q`, `e`, `d`, `p` and `n`:

```
{
    "q": ... ,
    "e": ... ,
    "d": ... ,
    "p": ... ,
    "n": ...
}
```

## Security

Even though this library is based on a well-known python-rsa, there is no guarantee that the port has the same level of security as the original implementation. The library has been re-worked quite a lot. In particular, [this port doesn't use blinding to preven side-channel attacks](https://github.com/artem-smotrakov/micropython-rsa-signing/blob/master/rsa/key.py#L128), and it uses a [custom implementation of modular exponentiation](https://github.com/artem-smotrakov/micropython-rsa-signing/blob/master/rsa/common.py#L172) since MicroPython doesn't support it (at least 1.10).

Additional security research is required to prove that this port doesn't contain vulnerabilities. Use with care.
