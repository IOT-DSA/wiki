## Dart for DGLux Server on Windows

- Stop DGLux Server.
- Download [this file](https://gsdview.appspot.com/dart-archive/channels/stable/release/1.16.1/sdk/dartsdk-windows-x64-release.zip).
- Copy the `dart-sdk` directory that is inside the ZIP file and replace the `dart-sdk` directory in your DSA installation.
- Start DGLux Server.

## Dart for DGLux Server on Linux (x64-bit)

Run the following commands:
```
cd /opt/dsa
rm -rf dart-sdk
curl https://gsdview.appspot.com/dart-archive/channels/stable/release/1.17.1/sdk/dartsdk-linux-x64-release.zip > sdk.zip
unzip sdk.zip
rm sdk.zip
```