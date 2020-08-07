# proxy

## configure http proxy for avd emulator
> last tested on avd - pixel, api 28, android 9
* put these tools on your path
    * `adb` - `<sdk>/platform-tools/`
    * `emulator` - `<sdk>/emulator/`
* find the target app on your phone
```sh
adb shell cmd package list packages -f | grep -i ${APP}
adb pull /data/app/.../base.apk
```
* patch the target app so you can proxy traffic
```sh
npx apk-mitm ${APP}.apk
# -> ${APP}-patched.apk
```
* prepare the device for a new cert
```sh
adb root
adb remount
```
* get your proxy cert into `/system`
```sh
openssl x509 -inform DER -subject_hash_old -in ${CERT}
# you get a HASH and the cert printed out
cp ${CERT} ${HASH}.0
adb push ${HASH}.0 /system/etc/security/cacerts
```
* run the emulator
```sh
emulator -avd ${AVD_NAME} -http-proxy http://${LAN_IP}:${PORT} -writable-system -netdelay none -netspeed full -no-boot-anim -no-audio -selinux permissive
# I like turning shit off
```

## Resources
* [avd cli command](https://medium.com/testvagrant/how-to-set-up-charles-proxy-for-mobile-and-web-apps-14163cf31908)
* [install CA in /system](https://docs.mitmproxy.org/stable/howto-install-system-trusted-ca-android/)
