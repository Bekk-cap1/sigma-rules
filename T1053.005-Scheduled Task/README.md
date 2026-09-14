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

## Техника
https://attack.mitre.org/techniques/T1053/005/