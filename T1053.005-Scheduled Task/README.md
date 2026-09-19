# T1053.005 — Scheduled Task/Job

## Что ловит
Создание запланированной задачи через schtasks.exe /create,
когда процесс порождён cmd.exe или powershell.exe — типичный способ
закрепления (persistence) в системе. Системные задачи с именем
\Microsoft\Windows\ исключаются фильтром.

## Пример срабатывания
cmd.exe → schtasks.exe /create /tn "Updater" /tr "C:\Users\user\AppData\Local\Temp\payload.exe" /sc onlogon

## Ограничения
Фильтр отсекает очевидные системные задачи (\Microsoft\Windows\),
но обходится, если атакующий маскирует имя задачи под системное.
Также не сработает, если задача создаётся не из cmd/powershell,
а напрямую или через API.

## Ложные срабатывания
- Windows Defender (Verification, CacheMaintenance, Cleanup) — сканирование, обновление баз, очистка кэша
- Application Experience (Microsoft Compatibility Appraiser) — телеметрия совместимости приложений
- Google Update (GoogleUpdateTaskMachineUA/Core) — обновление Chrome и сервисов Google
- Mozilla Maintenance Service — обновление Firefox
- Adobe Acrobat Update Task — обновление продуктов Adobe

## Валидация в лаборатории

Правило проверено end-to-end в собственном стенде (Windows 10 + Sysmon + Wazuh),
атака воспроизведена через Atomic Red Team (`T1053.005` test 1).

**Sigma + Chainsaw** — прогон этого правила по собранному `tests/sysmon.evtx`:

```
chainsaw hunt sysmon.evtx --sigma . --mapping sigma-event-logs-all.yml
[+] Group: Sigma → "Scheduled Task/Job Creation"
[+] 4 Detections found on 4 documents
```
Полный вывод: [`tests/chainsaw-hits.json`](./tests/chainsaw-hits.json).

**Та же логика в Wazuh** (Sigma нативно не исполняется, правило портировано в XML):

```xml
<rule id="100100" level="10">
  <if_sid>61603</if_sid>
  <field name="win.eventdata.commandLine" type="pcre2">(?i)schtasks(\.exe)?\s+.*/create</field>
  <description>T1053.005 - Scheduled task created via schtasks</description>
  <mitre><id>T1053.005</id></mitre>
</rule>
```
Живой алерт level 10 с привязкой к MITRE — скриншот [`tests/wazuh-alert.png`](./tests/wazuh-alert.png).

## Техника
https://attack.mitre.org/techniques/T1053/005/