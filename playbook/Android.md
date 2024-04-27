## install APK with all permissions

`adb install -g ${APK_PATH}`  
  

|Param|Desc|
|---|---|
|-g|grant all permissions required by the apk|

  

## Push file to a destinantion

`adb push ${SOURCE_PATH} ${DESTINATION_PATH}`

## Opens a new shell

`adb shell`

## Restart phone as root

`adb root`

## Decompile APK

`apktool d -o ${APK_DECOMPILE_PATH} ${APK_PATH}`

## Compile Folder to an usigned apk

`apktool b ${APK_DECOMPILE_PATH}`

## Convert Smali to Java

`jadx --no-imports --show-bad-code -d ${APK_DECOMPILE_PATH} ${APK_PATH}`

|Param|Desc|
|---|---|
|--no-import|forces JADX to use FDQN of asset instead of using imports, to avoid naming conflict issues|
|--show-bad-code|show the smali code when can't convert to java|
|-d|destination path|

  
## Sign or re-sign an APK `java -Xmx256m -jar uber-apk-signer-1.2.1.jar -a ${APK_PATH} --allowResign --overwrite`

## Print all certificates that signed the APK

`apksigner verify --verbose --print-certs ${APK_PATH}`