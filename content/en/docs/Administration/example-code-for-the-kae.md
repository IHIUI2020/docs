# Example Code for the KAE<a name="EN-US_TOPIC_0231142827"></a>

```
#include <stdio.h> 
```

```
#include <stdlib.h> 
```

```

```

```
/* OpenSSL headers */ 
```

```
#include <openssl/bio.h> 
```

```
#include <openssl/ssl.h> 
```

```
#include <openssl/err.h> 
```

```
#include <openssl/engine.h> 
```

```

```

```
int main(int argc, char **argv) 
```

```
{ 
```

```
    /* Initializing OpenSSL */ 
```

```
    SSL_load_error_strings(); 
```

```
    ERR_load_BIO_strings(); 
```

```
    OpenSSL_add_all_algorithms(); 
```

```

```

```
    /*You can use ENGINE_by_id Function to get the handle of the Huawei Accelerator Engine*/ 
```

```
    ENGINE *e = ENGINE_by_id("kae"); 
```

```
    /* Enable the accelerator asynchronization function. This parameter is optional. The value 0 indicates disabled, and the value 1 indicates enabled. The asynchronous function is enabled by default. */
```

```
    ENGINE_ctrl_cmd_string(e, "KAE_CMD_ENABLE_ASYNC", "1", 0) 
```

```
    ENGINE_init(e); 
```

```
 
```

```
    RSA*rsa=RSA_new_method(e);#Specify the engine for RSA encryption and decryption.
```

```
    /*The user code*/ 
```

```
    ...... 
```

```

```

```
; 
```

```
    ENGINE_free(e); 
```

```
; 
```

```
}
```
