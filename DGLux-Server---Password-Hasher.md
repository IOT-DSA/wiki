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

# Scenarios

There are several scenarios that may arise when changing the above configuration options. The following will elaborate on common scenarios and which configuration settings (as stored in the `server.json` file) to use in each.

## New Install - Using Default settings

The simplest configuration using the default configuration settings on a new installation. This will require no changes to the `server.json` file and will result in the following settings being applied automatically:

```json
    "passwordHasherIterations": 1000,
    "passwordHasherKeyLength": 32,
```

## New Install - Modifying Default Settings

If you are setting up a new installation, you can modify your `server.json` file to adjust the default settings. For example if you want a 64 Byte key length generated from 10,000 iterations apply these settings to your `server.json` file.

```json
    "passwordHasherIterations": 10000,
    "passwordHasherKeyLength": 64,
```

If you had started the server even once prior to this change, ensure that you stop the server, delete any existing `users.json` file (it may generate automatically if the server had been started once), then restart the server to make use of the new settings. A new default user account is created with the password hashed with the correct settings and any new users added through either the interface or the users command line tool will setup passwords with the correct hash algorithms and length.

## Existing Install - Modifying `passwordHasherIterations`

If you have an existing installation with users already in place, and decide to change (increase or decrease) the password hasher iterations, it is recommended to also use the `old_passwordHasherIterations` to enable passwords to be migrated to the new hasher implementation **as they are authenticated**. Due to the nature of the one-way hash, it is impossible to simply upgrade an existing hash. As an account tries to authenticate, it will compare the hash generated with the current settings from `passwordHasherIterations`. If successful, it grants access and no further changes are needed. If the hashed passwords do not much, it will try to hash the password against the settings from `old_passwordHasherIterations`. If this is successful, it will grant access and it will re-hash the password using the new `passwordHasherIterations` settings and store that on the file system. However if the hash from the `old_passwordHasherIterations` fails to match, it will return an authentication error.

```json
    "passwordHasherIterations": 10000,
    "old_passwordHasherIterations": 1000,
    "passwordHasherKeyLength": 32,
```

*(Note: When adding `"old_passwordHasherIterations"` it is recommended to place it immediately after the existing `"passwordHasherIterations"`. However after starting the server, it is possible that this value will be moved from that location to near the bottom of the `server.json` file. This is normal.)*

## Existing Install - Modifying `passwordHasherKeyLength`

There are two possibilities involving this setting, increasing the value or decreasing, and each have a specific recommendation.

### Decreasing `passwordHasherKeyLength`

When decreasing the key length stored by the password hash, we are still generating the same hash, but simply using less points. As such, if a password was hashed and stored with 32 bytes previously, those same bytes will remain as they are, but only the first _X_ number of bytes will be compared. For example if this value was set to 16, then only the first half of the password hash would be compared to see if it matches. **It is not recommended to set this below the default value**

### Increasing `passwordHasherKeyLength`

When increasing this value, we are indicating that the hasher should compare against more points than what currently exist in previously generated password hashes. To avoid authentication errors we should specify an `old_passwordHasherKeyLength` value to allow the migration of the shorter hash, to the longer hash length.

```json
    "passwordHasherIterations": 1000,
    "passwordHasherKeyLength": 64,
    "old_passwordHasherKeyLength": 32,
```

*(Note: When adding `"old_passwordHasherKeyLength"` it is recommended to place it immediately after the existing `"passwordHasherKeyLength"`. However after starting the server, it is possible that this value will be moved from that location to near the bottom of the `server.json` file. This is normal.)*

## Existing Install - Modifying both options

When modifying both `passwordHasherIterations` and `passwordHasherKeyLength` it is recommended to supply both `old_passwordHasherIterations` and `old_passwordHasherKeyLength` in order to ensure any previously encoded passwords can be migrated to the new hash parameters.

```json
    "passwordHasherIterations": 10000,
    "old_passwordHasherIterations": 1000,
    "passwordHasherKeyLength": 64,
    "old_passwordHasherKeyLength": 32,
```