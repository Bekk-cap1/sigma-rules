# T1218.011 — Rundll32 Proxy Execution

## Что ловит
Подозрительный запуск rundll32.exe: обход через javascript:,
запуск DLL из временных папок (Temp, AppData).

## Пример срабатывания
rundll32.exe javascript:"\..\mshtml,RunHTMLApplication ..."
rundll32.exe C:\Users\<user>\AppData\Local\Temp\evil.dll,DllMain
rundll32.exe C:\Windows\Temp\payload.dll,StartW

## Ложные срабатывания
Панель звука, блокировка ПК, диалоги печати — легальные вызовы rundll32.
Легитимные приложения, хранящие DLL в AppData (например, часть браузерных или пользовательских приложений)
Некоторые инсталляторы и обновления ПО запускают rundll32 из временных папок

## Техника
https://attack.mitre.org/techniques/T1218/011/