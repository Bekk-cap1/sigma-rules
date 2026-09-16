# T1552.001 — Unsecured Credentials: Credentials In Files

## Что ловит
Поиск учётных данных в файлах и реестре системными утилитами — шаг разведки
перед горизонтальным перемещением. Три независимых сценария:

- findstr.exe с ключевыми словами password / passwd / secret / credential
- powershell.exe, где одновременно присутствуют Select-String и password
- reg.exe с одновременным query и password

## Пример срабатывания
findstr.exe /si password *.xml *.ini *.txt
powershell.exe -c "Select-String -Path C:\inetpub\*.config -Pattern password"
reg.exe query HKLM /f password /t REG_SZ /s

## Ограничения
Блоки powershell и reg требуют точного слова "password" — поиск по словам
secret или credential этими утилитами правило не поймает.
Не ловит поиск через встроенные команды cmd (dir, type), так как они не
создают отдельный процесс.

## Ложные срабатывания
- Администраторы и DevOps, ищущие конфиги с параметром password при отладке
- Скрипты аудита, проверяющие наличие открытых учётных данных в системе
- Разработчики, ищущие переменные в исходном коде

## Техника
https://attack.mitre.org/techniques/T1552/001/