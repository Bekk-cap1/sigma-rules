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

## Валидация в лаборатории

Правило проверено в собственном стенде (Windows 10 + Sysmon + Wazuh),
атака воспроизведена через Atomic Red Team (`T1218.011` test 9)

**Sigma + Chainsaw** — прогон этого правила по собранному `tests/sysmon.evtx`:

```
chainsaw hunt sysmon.evtx --sigma . --mapping sigma-event-logs-all.yml
[+] 6 Detections found on 6 documents
```
Полный вывод: [`tests/chainsaw-hits.json`](./tests/chainsaw-hits.json).

## Техника
https://attack.mitre.org/techniques/T1218/011/