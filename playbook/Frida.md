## Run frida-ps on usb devices

`frida-ps -U`

-U is for devices connected over usb

---

## Spawn an app with frida debug

`frida -U -f ${PACKAGE_ID} -l ${FRIDA_SCRIPT_PATH} --no-pause`

|param|desc|
|---|---|
|-U|Connected over USB|
|-f|apk id to be spawned|
|-l|script to be loaded after startup|
|--no-pause|does not require **%resume**, after startup|

---

## External links

[https://book.hacktricks.xyz/mobile-pentesting/android-app-pentesting/frida-tutorial/frida-tutorial-1](https://book.hacktricks.xyz/mobile-pentesting/android-app-pentesting/frida-tutorial/frida-tutorial-1 "https://book.hacktricks.xyz/mobile-pentesting/android-app-pentesting/frida-tutorial/frida-tutorial-1")