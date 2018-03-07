# Password Hasher Configuration

When using `json` or `file` [authType](DGLux-Server-Options#authtype) passwords are hashed and encoded prior to being stored in a file. There are a few options with enable you to configure the behaviour in the password hasher and some situations to be aware of when changing these configuration values.

The password hasher uses PBKDF2 hashing algorithm, with a configurable number of iterations (default is 1000). The number of bytes stored from the resulting hash key is also configurable (with a default of 32 bytes). These options are:

## passwordHasherIterations

The number of iterations of the PBKDF2 algorithm used to generate the hash key.

**Default Value**: 1000

## passwordHasherKeyLength

The length (in bytes) of the resulting key that is stored on the file system.

**Default Value**: 32

There are two associated hidden configuration parameters which can be used in the event you change the configuration settings above, after passwords have already been generated with the above settings. These will help to validate a password against the previous configuration and migrate the password hash to the new configuration settings.

## old_passwordHasherIterations

The number of iterations previously set in the configuration when passwords were encoded.

## old_passwordHasherKeyLength

The length of the key previously set in the configuration when passwords were encoded. (This is only required if the new `passwordHasherKeyLength` is a larger value than the previous one. If the key length is made shorted this can be omitted).

